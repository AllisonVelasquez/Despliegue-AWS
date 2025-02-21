# Despliegue-AWS
(podemo sañadir capturas)
entras
login
laboratorio
vamos a cursos
modulos >> laboratorio de aprendizaje >> Iniciar
Start lab

Vamos a Ec2 (Servidores virtuales en la nube)
Instancias (no hay)
Lanzar instancia


nombre next-prod-app
imagenes de aplicaciones y sistemas operativos Amazon linux
arquitetura 64bits x86
tipo de instancia t2.micro apto para la capa gratuita
creamos par de claves >> nombre .pem (se descarga) || vockey rsa
asignar automaticamente la ip publica Habilitar
permitir trafico ssh, https y http
lanzar instancia
volvemos a instancias, refrescamos y veremos que ya nos aparece
LISTO
Ahora grupos de seguridad
vemos los detalles de la instancia
copiamos la ip publica
buscamos el .pem
vamos a la terminal y cambiamos los permisos de ese archivo >> 400.
clic en ID instancia >> conectarse >> Cliente SSH
Seguimos los pasos y ejecutamos el comando para conectarnos
ssh -i "next-prod-app.pem" ec2-user@ec2-3-88-194-175.compute-1.amazonaws.com
DENTRO (recordar que los comandos son similares a linux pero no iguales)
Instalamos git 
```
sudo yum install git -y
```
Verificamos la version con 
```
git --version
```
Clonamos el repositorio

Instalamos docker


