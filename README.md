# Despliegue-AWS

Para desplegar una instancia desde Amazon Web Services, primero debemos acceder a la ruta:
```
https://www.awsacademy.com/vforcesite/LMS_Login
```
Y loguearnos con nuestro user y contraseña. (student login)  
Una vez dentro vamos a:
```
Cursos >> Modulos >> Laboratorio de aprendizaje >> Iniciar el laboratorio
```
Cuando el laboratorio se haya iniciado nos aparecerá el nombre AWS con un punto rojo al lado, que indica el estado Terminado y al lado derecho distintos botones, vamos a:
```
Start Lab
```
Nos aparecerá una pantalla de carga y veremos como el punto rojo cambia a color amarillo y posteriormente a verde.
```
Esperando...
```
Cuando el punto cambie a color verde, hacemos clic en el nombre del laboratorio y accederemos a el.  
Cuando hayamos ingresado, vamos a la barra de busqueda o a nuestros marcadores en este caso y ecribimos:
```
EC2 o Servidores virtuales en la nube
```
Nos cargará la pagina de inicio de EC2.  
Localizaremos lo siguiente:
```
Instancias >> Lanzar instancia
```
Esto nos dejara configurar la instancia que queremos lanzar, sus datos y caracteristicas. Las cuales en este caso serán:
```
Nombre: [next-prod-app]
Imagenes de aplicaciones y sistemas operativos: [Amazon linux]
Arquitetura: 64bits x86
Tipo de instancia: t2.micro (apto para la capa gratuita)
```
Tambien debemos crear el par de claves, muy importante para poder conectarnos. Podemos usar las que vienen por defecto o generar unas nuevas
```
Par de claves:
Buscar [vockey rsa] o
Crear un nuevo par de claves >> [nombre][RSA][.pem]
(se descarga)
```
Tambien debemos revisar esto:
```
Asignar automaticamente la ip publica >> Habilitar
Permitir trafico [ssh], [https] y [http] (elegimos)
```
Despues de revisar todos los apartados, lanzamos la instancia.  
Volvemos a instancias, refrescamos y veremos que nos aparece la que acabamos de crear y lanzar.  

# Conectarse al servidor
Una vez lanzada nuestra instancia debemos conectarnos a ella, para ello vamos a:
```
Detalles >> Ip pública (la copiamos)
```
Buscamos el .pem que descargamos y nos aseguramos de darle los permisos necesarios (400)
```
chmod 400 [.pem]
```
Volvemos a la pagina de la instancia y:
```
Hacemos clic en el ID instancia >> conectarse >> Cliente SSH
```
Seguimos los pasos y ejecutamos el comando para conectarnos
```
ssh -i "next-prod-app.pem" [ec2-user@ec2-3-88-194-175.compute-1.amazonaws.com]
```

# Dentro del servidor
```
!! Recordar que los comandos son similares a linux pero no iguales)
```
## Instalamos git 
```
sudo yum update -y
sudo yum install git -y
```
Verificamos la version con 
```
git --version
```
Creamos la carpeta donde estara nuestro repositorio y nos movemos a ella
```
mkdir [nombre]
```
Clonamos el repositorio
```
git clone [url]
```
## Instalamos docker
```
sudo yum install docker -y
```
Lo iniciamos y habilitamos
```
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```
Creamos/revisamos los dockerfile y creamos el docker-compose
## Instalamos nginx
```
sudo yum install nginx -y
```
## Añadir Nginx a nuestros contenedores
Primero debemos crear un directorio llamado nginx y dentro de el la carpeta certs y el archivo default.conf o nginx.conf
```
mkdir nginx (dentro de nuestra carpeta donde esta el docker-compose)
```
Para generar certificados que nos sirvende momento ejecutamos el siguiente codigo
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 
  -keyout nginx/certs/selfsigned.key 
  -out nginx/certs/selfsigned.crt 
  -subj "/C=US/ST=State/L=City/O=Organization/OU=Department/CN=localhost"
```
Revisamos que aparezcan dentro de la carpeta certs y proseguimos con el archivo default.conf que debe contener lo siguiente
```
server {
  listen 80;

  # Servir React
  location / {
    #Esto redirige las solicitudes a la app React en ese contenedor y puerto
    proxy_pass http://react-app:3000;  # El nombre del servicio de React en docker-compose.yml
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }

  # Servir Node.js 
  location /tasks/ {
    proxy_pass http://api-node:5000;  # El nombre del servicio de Node.js en docker-compose.yml
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```
Una vez creado todo este directorio volvemos al docker-compose del directorio raiz y lo modificamos añadiendole el servicio de nginx, quedando asi:
```
nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    volumes:
    #Aqui deben copiarse tanto la configuracion como los certificados
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./nginx/certs:/etc/nginx/certs
    ports:
    #Exponemos los puertos para http y https
      - '80:80'
      - '443:443'
    depends_on:
      - react-app
      - api-node
    networks:
      - pro-app
```
## Hacer dockerbuild
Construimos los contenedores y quedan listos para levantarse
```
docker compose build
```
## Levantar los contenedores
Al levantarlos añadiendo el -d en el comando se dejan en segundo plano y podremos seguir utilizando la consola
```
docker compose up -d
```
## Comprobar estado
Podemos comprobar el estado de nuestro contenedores utilizando:
```
docker ps (para ver los contenedores que tenemos activos)
docker ps -a (pR ver todos los contenedores activos e inactivos)
docker logs [nombrecontenedor] para ver sus logs
```
Tambien debemos acceder desde el navegador del localhost añadiendo el puerto expuesto 3000
```
localhost:3000
```
