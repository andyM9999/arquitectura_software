# Tutorial_5_TDD_Docker

## Conceptos previos 

Un **Dockerfile** es un documento de texto que contiene todos los comandos que un usuario puede llamar en la línea de comandos para ensamblar una imagen. Al utilizar la compilación de Docker, los usuarios pueden crear una compilación automatizada que ejecute varias instrucciones de línea de comandos en sucesión. 

**Test Driven Development** es un proceso de desarrollo de software que se basa en la repetición de un ciclo de desarrollo muy corto: los requisitos se convierten en casos de prueba muy específicos, luego el software se mejora para pasar las nuevas pruebas, únicamente. 
Serverspec es esencialmente pruebas RSpec para la configuración de sus servidores. Serverspec prueba el estado real de su servidor.

## Vamos a TDD 
```
.

├── Gemfile

├── Gemfile.lock

├── LICENSE

├── README.md

└── spec

1 directory, 4 files
```
Entonces, de la manera habitual de TDD, comenzaremos creando nuestra primera prueba (fallida). Crearemos *sspec/Dockerfile_spec.rb*  con el siguiente contenido:
```
require "serverspec"

require "docker"

describe "Dockerfile" do

	before(:all) do

		@image = Docker::Image.build_from_dir('.')

		@image.tag(repo: 'jadametz/serverspec', tag: 'latest')

		set :os, family: :alpine

		set :backend, :docker

		set :docker_image, @image.id

		set :docker_container_create_options, { 'Entrypoint' => ['ash'] }

	end

	it "should have the maintainer label" do

		expect(@image.json["Config"]["Labels"].has_key?("maintainer"))

	end

end
```
Estamos usando algunas gemas de terceros y hemos comenzado a establecer el contexto para nuestras pruebas. Sin embargo, antes de ejecutar cualquier prueba, necesitamos configurar.

*Docker :: Image.build_from_dir ('.')* Va a compilar nuestro *Dockerfile* en una imagen y posteriormente etiquetarlo como *jadametz / serverspec: latest.*

Después de eso, Serverspec necesita saber algo de información sobre cómo ejecutar las pruebas. Con qué sistema operativo se está ejecutando, que el backend para las pruebas será Docker y, como resultado, qué imagen usar.

*: docker_container_create_options* es algo especial en este caso y es cierto que tomó un poco de tiempo averiguarlo. Debido a que nuestra imagen va a tener un *ENTRYPOINT*, necesitamos anularlo durante la prueba para que no se ejecute cuando Serverspec ejecute un contenedor desde nuestra imagen.

## ¡Nuestra primera prueba!

Con la configuración fuera del camino, si ejecutamos la prueba (L15-17) veremos que falla ya que ni siquiera tenemos un *Dockerfile* todavía.
```
~/../docker-serverspec ➭ bundle exec rspec

F

Failures:

1) Dockerfile should have the maintainer label

Failure/Error: @image = Docker::Image.build_from_dir('.')

Docker::Error::ServerError:

Cannot locate specified Dockerfile: Dockerfile

# ./spec/Dockerfile_spec.rb:6:in `block (2 levels) in <top (required)>'

# ------------------

# --- Caused by: ---

# Excon::Error::InternalServerError:

# Expected([200, 201, 202, 203, 204, 301, 304]) <=> Actual(500 InternalServerError)

# ./spec/Dockerfile_spec.rb:6:in `block (2 levels) in <top (required)>'

Finished in 0.10805 seconds (files took 0.38076 seconds to load)

1 example, 1 failure

Failed examples:

rspec ./spec/Dockerfile_spec.rb:15 # Dockerfile should have the maintainer label
```
Para completar este ciclo, necesitamos escribir un código que satisfaga la prueba. Usaremos la imagen *ruby: 2.4.1-alpine* y agregaremos una etiqueta de `maintainer`.
```
~/../docker-serverspec ➭ cat Dockerfile

FROM ruby:2.4.1-alpine

LABEL maintainer="jesseadametz@gmail.com"

~/../docker-serverspec ➭ bundle exec rspec

.

Finished in 0.51922 seconds (files took 0.38438 seconds to load)

1 example, 0 failures
```
## Hay dos "flavors" de pruebas

Antes de continuar, debo señalar que vamos a utilizar dos estilos diferentes de pruebas.
Nuestra prueba para la etiqueta de mantenedor es lo que llamaremos prueba "config". Estas pruebas utilizan *rspec* puro para analizar la salida JSON de docker inspect en nuestra imagen.
```
[
	{
		"Id":"sha256:128e503101d05a0e428f87aaecc161cc21068aa13d261a9a58fa210e3252348f",
	"RepoTags": [
		"jadametz/serverspec:latest"
	],
	"Config": {
		"Cmd": [
			"irb"
		],
		"Entrypoint": null,
		"Labels": {
			"maintainer": "jesseadametz@gmail.com"
		}
	}
}
]
```
El otro tipo de prueba es lo que hemos estado eludiendo todo el tiempo, Serverspec. Recuerde, estas pruebas se utilizan para verificar el estado real de la imagen. Hay muchas cosas que puede hacer, pero por ahora, solo verificaremos la instalación de algunos paquetes y nos aseguraremos de que existan algunos archivos.

## Instalación de un paquete apt
```
~/.../docker-serverspec ➭ git diff spec/Dockerfile_spec.rb
@@ -15,4 +15,8 @@ describe "Dockerfile" do
it "should have the maintainer label" do
expect(@image.json["Config"]["Labels"].has_key?("maintainer"))
end
+
+ describe package('build-base') do
+ it { should be_installed }
+ end
end
~/.../docker-serverspec ➭ bundle exec rspec
.F
Failures:
1)Dockerfile Package "build-base" should be installed
Failure/Error: it { should be_installed }
expected Package "build-base" to be installed
# ./spec/Dockerfile_spec.rb:20:in `block (3 levels) in <top (required)>'
Finished in 0.64045 seconds (files took 0.38026 seconds to load)
2 examples, 1 failure
Failed examples:
rspec ./spec/Dockerfile_spec.rb:20 # Dockerfile Package "build-base" should be installed
```
Agregamos la prueba y está fallando. Por suerte para nosotros, este es un ciclo de una línea. Simplemente agregue el paquete base de compilación a la imagen y listo.
```
~/.../docker-serverspec ➭ git diff Dockerfile
@@ -1,2 +1,5 @@
FROM ruby:2.4.1-alpine
LABEL maintainer="jesseadametz@gmail.com"
+
+RUN apk add --update --no-cache build-base
+
~/.../docker-serverspec ➭ bundle exec rspec
..
Finished in 0.64843 seconds (files took 0.37674 seconds to load)
2 examples, 0 failures
```

## Instalación de gemas de nuestro Gemfile
```
~/.../docker-serverspec ➭ git diff spec/Dockerfile_spec.rb
@@ -19,4 +19,24 @@ describe "Dockerfile" do
   describe package('build-base') do
     it { should be_installed }
   end
+
+  [
+    'Gemfile',
+    'Gemfile.lock',
+  ].each do | file |
+    describe file("/#{ file }") do
+      it { should exist }
+    end
+  end
+
+  [
+    'docker-api',
+    'rspec',
+    'rspec_junit_formatter',
+    'serverspec',
+  ].each do | gem |
+    describe package(gem) do
+      it { should be_installed.by('gem') }
+    end
+  end
 end
 
 ~/.../docker-serverspec ➭ bundle exec rspec
 ..FFFFFF

Failures:

  # TRUNCATED - I know I promised code samples. But you're smart, you get it...

Finished in 1.73 seconds (files took 0.38617 seconds to load)
8 examples, 6 failures

Failed examples:

rspec ./spec/Dockerfile_spec.rb[1:3:1] # Dockerfile File "/Gemfile" should exist
rspec ./spec/Dockerfile_spec.rb[1:4:1] # Dockerfile File "/Gemfile.lock" should exist
rspec ./spec/Dockerfile_spec.rb[1:5:1] # Dockerfile Package "docker-api" should be installed by "gem"
rspec ./spec/Dockerfile_spec.rb[1:6:1] # Dockerfile Package "rspec" should be installed by "gem"
rspec ./spec/Dockerfile_spec.rb[1:7:1] # Dockerfile Package "rspec_junit_formatter" should be installed by "gem"
rspec ./spec/Dockerfile_spec.rb[1:8:1] # Dockerfile Package "serverspec" should be installed by "gem"
```

Necesitamos COPIAR nuestro Gemfile y Gemfile.lock, luego usar la instalación del paquete para obtener todas las gemas necesarias.
```
~/.../docker-serverspec ➭ git diff Dockerfile
@@ -3,3 +3,6 @@ LABEL maintainer="jesseadametz@gmail.com"
 RUN apk add --update --no-cache build-base
+COPY Gemfile Gemfile.lock ./
+RUN bundle install
+
~/.../docker-serverspec ➭ bundle exec rspec
........
Finished in 13.32 seconds (files took 0.37055 seconds to load)
8 examples, 0 failures
```
## Y por último pero no menos importante…

Al final del proceso, el Dockerfile necesita (debería tener) instrucciones sobre cómo ejecutarse. Vamos a hacer que nuestro ENTRYPOINT sea responsable de ejecutar rspec. A continuación, el usuario le dará la ruta a sus especificaciones. Si no es así, la CMD predeterminada imprimirá el menú de ayuda con -h.
```
~/.../docker-serverspec ➭ git diff spec/Dockerfile_spec.rb
@@ -39,4 +39,12 @@ describe "Dockerfile" do
it { should be_installed.by('gem') }
end
end
+
+ it "should execute rspec" do
+ expect(@image.json["Config"]["Entrypoint"][0]).to eq("rspec")
+ end
+
+ it "should pass the -h option by default" do
+ expect(@image.json["Config"]["Cmd"][0]).to eq("-h")
+ end
end
~/.../docker-serverspec ➭ bundle exec rspec
.FF.......
Failures:
# TRUNCATED -- I'm trying to save _you_ room on the page!
Finished in 1.77 seconds (files took 0.39947 seconds to load)
10 examples, 2 failures

Failed examples:

rspec ./spec/Dockerfile_spec.rb:43 # Dockerfile should execute rspec
rspec ./spec/Dockerfile_spec.rb:47 # Dockerfile should pass the -h option by default
```
Y con las dos últimas instrucciones de Dockerfile agregadas, estamos "¡todas las pruebas pasan!"
```
~/.../docker-serverspec ➭ git diff Dockerfile
@@ -6,3 +6,6 @@ RUN apk add --update --no-cache build-base
COPY Gemfile Gemfile.lock ./
RUN bundle install
+ENTRYPOINT ["rspec"]
+CMD ["-h"]
+
~/.../docker-serverspec ➭ bundle exec rspec
..........
Finished in 2.37 seconds (files took 0.38096 seconds to load)

10 examples, 0 failures
```
## ¡La mitad del trabajo ya está hecho!

Debido a que lo que creamos aquí es una imagen de especificación de servidor, puede comenzar a usarla inmediatamente para probar sus imágenes. No necesita Serverspec, no necesita la gema de docker-api (aunque deberá requerirlos en su especificación). Solo necesita su propio archivo Dockerfile_spec.rb.

```
# Here's that framework we talked about!
# It's perfect for ephemeral build servers.
# Why install Serverspec everywhere?
# Use a container to test your container(s)!
$ docker run --rm --name integration-test \
➭ -v /var/run/docker.sock:/var/run/docker.sock \
➭ -v $(pwd):/test \
➭ -w /test \
➭ jadametz/serverspec \
➭ spec/
```

