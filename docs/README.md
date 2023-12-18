# Generar una imagen Docker con Jenkins y Blue Ocean

Para generar la imagen de Jenkins, creamos el siguiente ``Dockerfile``:

```
FROM jenkins/jenkins:2.426.2-jdk17
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
RUN jenkins-plugin-cli --plugins "blueocean:1.27.9 docker-workflow:572.v950f58993843"
```
Y ejecutamos el comando:
```
docker build -t myjenkins-blueocean:2.426.2-1 .
```
# Desplegar contenedores en Terraforms y configuración inicial de Jenkins
Creamos el directorio de trabajo con el archivo de configuración ```Terraform.tf``` en el directorio ```docs``` de este repositorio:

```terraform init```

Y luego lanzamos los contenedores:

```terraform apply```

Una vez hecho, buscamos dentro del contenedor de jenkins la contraseña para poder inicializarlo,  se encontrará en el log de este, con un formato tal que así:

```
2023-12-18 20:50:24 
2023-12-18 20:50:24 Jenkins initial setup is required. An admin user has been created and a password generated.
2023-12-18 20:50:24 Please use the following password to proceed to installation:
2023-12-18 20:50:24 
2023-12-18 20:50:24 56f4a1a847c94165bdbd30b8d489bd4e
2023-12-18 20:50:24 
2023-12-18 20:50:24 This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
2023-12-18 20:50:24 
2023-12-18 20:50:24 *************************************************************
2023-12-18 20:50:24 *************************************************************
2023-12-18 20:50:24 *************************************************************
2023-12-18 20:50:24
```
Creamos un usuario y contraseña y habremos terminado la configuración inicial.

# Creacion del Pipeline

- Creamos un nuevo trabajo 
[![1.png](https://i.postimg.cc/gjdsz3SL/1.png)](https://postimg.cc/JD6ZKHWR)


- Y seleccionamos la opción de ```Pipeline``` y damos OK
[![2.png](https://i.postimg.cc/7PVM1GtX/2.png)](https://postimg.cc/Hjrydxg7)

- En la siguiente página, en el apartado de Pipeline, seleccionamos ```Pipeline script fomr SCM``` y añadimos el URL a nuestro repositorio (y seleccionar tanto la rama como localización del Jenkinsfile)
[![3.png](https://i.postimg.cc/Vky6d49h/3.png)](https://postimg.cc/VdR1hqV9)
[![4.png](https://i.postimg.cc/fy9ssnPR/4.png)](https://postimg.cc/T5fFjSGz)

- Lanzamos y, si lo tenemos todo bien escrito y configurado, deberíamos obtener este resultado:
[![5.png](https://i.postimg.cc/L41hRrLP/5.png)](https://postimg.cc/XXnnk12N)
