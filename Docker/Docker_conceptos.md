# Conceptos para entender Docker.
Videos : 
- Docker in an 1h and half, by JavaScriptMastery -  [Link](https://www.youtube.com/watch?v=GFgJkfScVNU)

## El problema de los entornos de software
### Entorno de software:
- SO
- DB
- Framework
- Librerias
- API
- Servidor

### Entorno de desarrollo.

### Entorno de pruebas.
Pruebas unitarias: codigo fuente, clases y funciones
De integración: de de los componentes APIs etc ..
Pruebas de aceptación. usuario

### Entorno de preproducción

### Entorno de producción.

## Virtualización.
### Viene a resolver un poco el problema de entornos de software.
- Sistema anfitrión.
- Sistema invitado.

### Soluciones de virtualización:
- VMWare vSphere: Profesional
- VMWare workstation
- VMWare fusion (para MAC)
- Oracle VM VirtualBox (pequeñas empresas o académicas)
- Microsoft Hyper-V: Usada para sistemas producción
- Docker : Tecnologia de contenedores Más eficientes y rapidos que MV.
- Docker Hub y Docker Registry hay colecciones de imagenes preconstruidas y oficiales.
- Docker ideal para microservicios.
- Seguridad y gestión: Herramientas Docker Trusted Registry y Docker Security Scannning ...
- Docker Desktop: Cliente gráfico.

## Docker.
### Glosario:
- DockerFile: Plantilla para crear la imagenes
- Imagen : Un paquete inmutable que posee todos lo necesario para ejecutar la aplicación. A partir de aquí puedes generar contenedores.
- Contenedor: Una instancia de una imagen que se ejecuta de manera aislada.
- Docker Engine: La tecnologia central de Docker que permite la creación  y ejecución de contenedores.
- Docker Hub: registor publico central Docker ...
- Volumen: Mecanismo para persistir datos generados/o usados por contenedores Docker. Los volumnes permiten que los datos persistan más allà de ciclo de vida.
- Docker Compose: Herramienta para definir y ejecutar aplicaciones Docker multicontenedor. Un docker con MySQL, otro con Node.js, otro ...
- Docker Desktop: Aplicación de escritorio para gestionar imagenes y contenedores de Docker.

### Instalación en Linux:
https://docs.docker.com/desktop/install/linux-install/
Docker desktop para interfaz grafica de usuario

### CLI
`docker run hello-world`
Cada vez que ejecutamos este comando se crea un nuevo contenedor.

`docker ps` : me lista 
`docker images` : me llista las imagenes
`docker rmi hello-world` eliminar una imagen. ( necesita que no existan ningun contenedor asociado a la imagen )
`docker rm nombre o ID del contenedor`
`docker run hello-world`
`docker pull`  descarregar imatge docker hub
`docker build -t <NOMBER_IMAGE>:<TAG>  <RUTA_DOCKERFILE>` : Construir un fitxer 

Interectuar amb contenidors:
`docker ps` Llista contenidors actius.
`docker ps -a` Llista de contenidors ejecució y aturats.
`docker run <opciones> <imagen>`
`docker stop`
`docker start`

DOCKER CLI
DOCKER HOST
DOCKER REGISTRY (DUCKER HUB)


### DockerFile define una imagen.
Instruccions per exemple
FROM (especifica la imatge base per la nova imatge)
WORDIR (On posarem tot)
COPY (src to )
RUN (executa comandes en shell mentres es contrueix el container)
EXPOSE <ports>
ENV key=value
ARGS 
VOLUME /myvol   (defineix el punt de muntatge per els volumns)
CMD (executa quan container arranca, executable per defecte que es pot variar)
ENTRYPOINT (lo mateix però no pot ser sobreescrit, punt d'arrancada fixe )

