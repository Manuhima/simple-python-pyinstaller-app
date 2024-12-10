# README
## Despliegue de una Aplicación Python con Jenkins y Terraform

*Miembros: Manuel Antonio Hidalgo Mayén y Álvaro Jaén Seiva*

## Introducción
Este documento explica cómo desplegar una aplicación Python utilizando Jenkins y Terraform. Abarca desde la configuración del entorno hasta la ejecución y creación del artefacto python.

## Requisitos Previos
*Herramientas Instaladas:*
- Docker.
- Terraform.
- Git.
- Python 3.12.0 o superior.

## Repositorio Forkeado:
1. Forkea el repositorio de la aplicación desde *simple-python-pyinstaller-app.*
2. Trabaja en la rama *main*.

# Paso 1: Configuración de la Imagen Personalizada de Jenkins
## Crear la Imagen Docker para Jenkins
Crea un archivo llamado Dockerfile con el siguiente contenido:

``` Dockerfile
FROM jenkins/jenkins:2.479.2-jdk17
USER root
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow token-macro json-path-api"
```

## Construir la Imagen Personalizada
Ejecuta el siguiente comando para construir la imagen personalizada de Jenkins:

``` bash
docker build -t myjenkins-blueocean .
```

# Paso 2: Configuración del Despliegue con Terraform

## Archivos de Configuración
Crea un archivo llamado *main.tf* con el contenido correspondiente.

## Ejecutar Terraform
Inicializa y aplica la configuración de Terraform:

``` bash
terraform init
terraform plan
terraform apply
```
*Nota*: Confirma el despliegue cuando se solicite.

# Paso 3: Configuración de Jenkins
## Acceso al Panel de Jenkins

1. Accede a Jenkins en *http://localhost:8080.*

2. Obtén la contraseña inicial con el siguiente comando:

``` bash
docker logs jenkins-blueocean
```

3. Ingresa la contraseña en la interfaz de Jenkins.

## Configurar Jenkins

1. Completa la configuración inicial.

2. Instala los plugins recomendados.

3. Crea un usuario.

# Paso 4: Creación del Pipeline en Jenkins
## Crear un Nuevo Pipeline

1. En el panel de Jenkins, selecciona *New Item* y crea un pipeline.
2. Configura el pipeline para utilizar un *Jenkinsfile* desde el repositorio.
3. Enlaza tu repositorio forkeado en la rama *main*.

## Agregar el Jenkinsfile
Asegúrate de tener un archivo *Jenkinsfile* en la ruta correspondiente, en nuestro caso en *docs/Jenkinsfile* en el repositorio.

# Paso 5: Prueba del Pipeline
1. En el panel de Jenkins, ejecuta el pipeline.

2. Verifica que las siguientes etapas se completen correctamente:

- Build: Compilación de los archivos Python.
- Test: Ejecución de pruebas unitarias.
- Deliver: Generación del artefacto ejecutable utilizando PyInstaller.

## Verificación del Artefacto
Si el pipeline se ha ejecutado correctamente, encontraremos el artefacto *add2vals* generado correctamente en la pestaña de *Status* de Jenkins.