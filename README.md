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
Nos aparecerá una pantalla de cargay veremos como el punto rojo cambia a color amarillo y posteriormente a verde.
```
Esperando...
```
Hacemos clic en el nombre del laboratorio y accederemos a el.  
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
volvemos a instancias, refrescamos y veremos que nos aparece la que acabamos de crear y lanzar.  

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
ssh -i "next-prod-app.pem" ec2-user@ec2-3-88-194-175.compute-1.amazonaws.com
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
mkdir <nombre> 
```
Clonamos el repositorio
```
git clone <url>
```

## Instalamos docker
```
sudo yum install -y
```
Lo iniciamos y habilitamos
```
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```
Creamos los repositorios y el dockercompose


