
## Clúster de Hadoop en Docker y MapReduce en Azure 
En este readme, se muestra una guia para poder desplegar un cluster de Hadoop usando Docker y Docker Compose, y ejecutar un ejemplo como WordCount con MapReduce.
### Requisitos 
- **Cuenta de Azure** donde crearemos la MV (maquina virtual).
- **Docker y Docker Compose**, estos archivos debes de tenerlos en tu sistema, en este repositorio de github los puedes encontrar.
- Un **directorio** de trabajo donde poder alojar todos los archivos necesarios, incluyendo docker-compose.yml, dockerfile y hadoop-mapreduce-examples-3.2.1.jar .
### Máquina Virtual en Azure
En esta parte, os mostrare como crear y configuarar la Maquina Virtual en Azure con Ubuntu 20.04 LTS.
#### 1. Creación
Para crear la maquina virtual de Azure con Ubuntu, necesitaremos seguir estos pasos:
##### 1. Accede al [Portal de Azure](https://portal.azure.com/) con tu cuenta
![Foto de proyecto](assets/1.png)
##### 2. Haz click en la opción "Crear un recurso" y selecciona el apartado que pone "Maquina Virtual", (Literalmente pulsa el nombre "Maquina virtual", ni crear ni nada más)
![Foto de proyecto](assets/2.png)
![Foto de proyecto](assets/3.png)
##### 3. Configura los detalles necesarios para la Maquina Virtual:
- **Gruppo de recursos:** Crea uno nuevo o usa uno existente, (normalmente crearemos uno nuevo ya que solemos borrarlos por el consumo).
![Foto de proyecto](assets/4.png)
(Donde encontrarlo)
![Foto de proyecto](assets/5.png)
(Seleccion de un grupo ya existente)
![Foto de proyecto](assets/6.png)
(Creacion de un nuevo grupo)
- **Nombre de la Maquina:** Escribe un nombre para la maquina, por ejemplo, `MaquinaHadoop`.
![Foto de proyecto](assets/7.png)
- **Region:** como recomendacion, selecciona la region mas cercana a ti, para evitar cobros muy elevados.
![Foto de proyecto](assets/8.png)
- **Imagen:** en la imagen escoge "Ubuntu Server 20.04 LTS", no sale de normal, por lo que tendremos que pulsar "ver todas las imagenes"
![Foto de proyecto](assets/9.png)
![Foto de proyecto](assets/9.1.png)
Buscamos Ubuntu Minimal 20.04 LTS Gen 2
![Foto de proyecto](assets/9.2.png)
![Foto de proyecto](assets/9.3.png)
- **Tamaño:** selecciona un tamaño adecuado, en este caso intenta elegir el mas bajo para avaratar costes ya que no es necesario una maquina muy potente para esta practica.
![Foto de proyecto](assets/10.png)
En resumen, la maquina deberia de quedar parecido a esto:
![Foto de proyecto](assets/11.png)
![Foto de proyecto](assets/12.png)
![Foto de proyecto](assets/13.png)
##### 4. Configuracion SHH:
Ahora, configuraremos la autenticicacion con SSH, para ello:
- **Nombre Usuario:** escribe un nombre de usuario adecuado como puede ser `azureuser` (viene por defecto).
- **Tipo de autenticicación:** Selecciona "Clave pública SSH"
- **Clave pública SSH:** Pega tu clave pública, o generala, (selecciona la opcion que mejor se adapte a ti).
(Posteriormente cuando creemos la maquina nos saldra este mensaje.)
![Foto de proyecto](assets/24.png)
- **Nombre Claves:** añade un nombre a las claves que vas a generar, en este caso `parClaves`
![Foto de proyecto](assets/14.png)
(Como aclaracion, en esta parte suele venir todo por defecto, solo convendria poner nombre a las claves)
##### 5. Revisión:
En esta parte, debes revisar y comprobar que las opciones que hemos puesto antes estan correctamente.
Una vez recisadas, iremos avanzando en las siguientes pestañas dejandolo todo por defecto.
![Foto de proyecto](assets/15.png)
![Foto de proyecto](assets/16.png)
![Foto de proyecto](assets/17.png)
![Foto de proyecto](assets/18.png)
![Foto de proyecto](assets/19.png)
![Foto de proyecto](assets/20.png)
![Foto de proyecto](assets/21.png)
(Revisamos y Creamos la MV)
![Foto de proyecto](assets/22.png)
![Foto de proyecto](assets/23.png)
(Descargamos la clave y creamos el recurso)
![Foto de proyecto](assets/24.png)
(Se nos descargaran las claves en nuestro PC)
![Foto de proyecto](assets/25.png)
(Y por ultimo ya tendremos creada nuestra maquina virtual)
![Foto de proyecto](assets/26.png)
#### 2. Conexion desde Terminal Local
Despues de haber creado la MV correctamente siguiendo los paso anteriores, deberemos conectarnos a ella usando SSH con el comando: (claves que se nos han descargado en nuestro PC de forma local)
```bash
ssh -i ruta/a/clave/descargada azureuser@<dirección_IP_pública_de_tu_VM>

#Por ejemplo (Desde wsl):

ssh -i /mnt/c/Users/user/Desktop/PowerBI/Azure-Hadoop-Docker/.ssh/parClaves.pem azureuser@4.233.149.75

```

![Foto de proyecto](assets/41.png)

##### Posible Fallo:

**ASEGURATE DE ESTAR EN LA TERMINAL DE BASH EN VEZ DE WSL**
Comandos clave para bash:
**cd <nombreDirectorio>:** entrar en un directorio
**cd .. :** retroceder al directorio anterior
**dir:** ver el contenido de un directorio
**mkdir:** crear directorio
**rmdir:** eliminar directorio

Es posible que al ejecutar este comando, no nos deje acceder, debido a que el usuario tiene demasiados permisos, nos saldrá algo como esto:
![Foto de proyecto](assets/27.png)
Primero ponemos este comando sobre el archivo para poner solo peromisos de lectura:
```bash
sudo chmod 400 parClaves.pem

```

Despues,  lo tendremos que hacer desde la interfaz grafica de windows.
Primero localizamos la clave:
![Foto de proyecto](assets/28.png)
Pulsamos propiedades:
![Foto de proyecto](assets/29.png)
![Foto de proyecto](assets/30.png)
Pulsamos seguridad y opciones avanzadas:
![Foto de proyecto](assets/31.png)
Pulsamos deshabilitar herencia y quitar los permisos heredados:
![Foto de proyecto](assets/32.png)
![Foto de proyecto](assets/33.png)
Ahora tenemos que agregarlos, para un usuario concreto, para ello:
Pulsa agregar:
![Foto de proyecto](assets/34.png)
Ahora seleccionar entidad
![Foto de proyecto](assets/35.png)
Escribe el nombre del usuario y compruebalo:
![Foto de proyecto](assets/36.png)
![Foto de proyecto](assets/37.png)
Te tiene que salir algo como esto, (en caso de error sale una alerta):
![Foto de proyecto](assets/38.png)
Pulsamos aceptar:
![Foto de proyecto](assets/39.png)
Aplicamos y aceptamos:
![Foto de proyecto](assets/40.png)

#### 3. Instalacion de Docker y Docker Compose en la Maquina Virtual:
(**TODO ESTO SE HACE DENTRO DE LA MAQUINA**)
##### 1. Actualizar
Primero debemos de actualizar los paquetes e instalar Docker:
```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker

```
![Foto de proyecto](assets/42.png)
##### 2. Agregar Usuario
Ahora, agregamos nuestro usuario al grupo docker
```bash
sudo usermod -aG docker $USER

```
![Foto de proyecto](assets/43.png)
##### 3. Instalar Docker Compose
Lo siguiente que hay que hacer es instalar el Docker Compose correspondiente:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

```
![Foto de proyecto](assets/44.png)
Una vez hayamos hecho estos comandos, Docker y Docker Compose estan configurados en la MV.

##### 4. Habilita la salida o entrada de puertos
Para habilitar la salida o entrada de puertos, nos metemos en azure:
![Foto de proyecto](assets/47.png)
Pulsamos en configuracion de red y pulsamos crear ACL del puerto:
![Foto de proyecto](assets/48.png)
Despues pulsamos regla de puerto de entrada
![Foto de proyecto](assets/49.png)
Configuramos la entrada de la siguiente manera
![Foto de proyecto](assets/50.png)
![Foto de proyecto](assets/51.png)
![Foto de proyecto](assets/52.png)
### Ejecución el Ejemplo
#### Preliminares
Es posible que nano no este ni instalado, para ello pon el comando:
```bash
sudo apt install nano

```

Para poder levantar el cluster, primero tienes que tener el docker compose, para ello al estar en una MV de Azure, es mejor hacer un nano para crear el archivo y copiar el contenido del `docker-compose` que se encuentra en este repositorio.
```bash
nano docker-compose.yml

```

#### 1. Levantar Cluster
Ejecuta este comando para lvantar los servicios del cluster de Hadoop. Usa `-d` para ejecutarlos en segundo plano. 
```bash
sudo docker-compose up -d

```
![Foto de proyecto](assets/45.png)

#### 2. Verificacion
Ahora verificamos que los Servicios estan Corriendo, para ello escribimos el siguiente comando.
(Se mostraran todos los contenedores activos)
```bash
sudo docker ps

```
(Con este se mostraran todos los que hay, incluso los inactivos)
```bash
sudo docker ps -a

```
(Con este se mostraran todos los que hay inactivos)
```bash
docker ps -f "status=exited"


```
(En caso de que haya algun contenedor inactivo que haga falta activar se pone)
```bash
docker start <nombre/id_Contenedor>

```
![Foto de proyecto](assets/53.png)

En esta parte deberias de observar los contenedores: namenode, datanode1, datanode2, datanode3, resourcemanager, nodemanager, y historyserver en la lista.

#### 3.0. Configuracion de Permisos
Para evitar problemas, asegurate de que los permisos del directorio de trabajo sean totales para el usuario. (Esto es para no usar sudo)

1. Cambia la propiedad del directorio: 
```bash
sudo chown -R $USER:$USER /ruta/a/tu/directorio/MaquinaVirtual
#Ejemplo
sudo chown -R $USER:$USER /home/azureuser

```
2. Otorga permisos de lectura, escritura y ejecución al usuario:
```bash
chmod -R u+rwx /ruta/a/tu/directorio/MaquinaVirtual
#Ejemplo
chmod -R u+rwx /home/azureuser
```
Esto garantiza que puedes ejecutar los comandos sin necesidad de sudo.
#### 3.1. Subida de archivo de entrada a HDFS

##### Preliminar

Para subir el archivo de entrada a HDFS, copia el archivo archivo.txt al contenedor del Namende.

Para poder subir el archivo txt primero lo tienes que crear, usa nano y haz copia y pega del archivo de este mismo repositorio.
```bash
nano archivo.txt

```
Una vez el archivo lo tengas en la MV, se debe de copiar el archivo al contenedor NameNode (Contenedor Principal)
(Asegurate de estar en el directorio esperado)
```bash
sudo docker cp archivo.txt namenode:/archivo.txt

```

Después, crea los directorios necesarios en HDFS:
```bash
sudo docker exec -it namenode hdfs dfs -mkdir -p /user/Alumno_AI

```
Finalmente, sube el archivo `.txt` que hemos creado a HDFS:
```bash
sudo docker exec -it namenode hdfs dfs -put /archivo.txt /user/Alumno_AI/

```
(La mayoria de respuestas a los comandos anteriores son nulas, es decir que no hay respuesta y se ve tal que asi:)
![Foto de proyecto](assets/54.png)
#### 4. Ejecutar el Ejemplo de WordCount
Para ejecutar el ejemplo de WordCount usando MapReduce, (cuyo fin es contar las palabras del `archivo.txt`) escribiremos lo siguiente.

```bash
sudo docker exec -it resourcemanager yarn jar /opt/hadoop-3.2.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar wordcount /user/Alumno_AI/archivo.txt /user/Alumno_AI/output

```
#### 5. Verificar los resultados
Para verificar los resultados del ejemplo de WordCount, puedes listar los archivos del directorio de salida en HDFS:
```bash
sudo docker exec -it namenode hdfs dfs -ls /user/Alumno_AI/output

```
Luego, vemos el contenido del archivo de resultados:
```bash
sudo docker exec -it namenode hdfs dfs -cat /user/Alumno_AI/output/part-r-00000

```
![Foto de proyecto](assets/55.png)

Una vez hallamos terminado esto, habremos terminado de haacer todos los pasos y esta practica, en resumen, hemos conseguido hacer un ejemplo de WordCount dentro de una maquina virtual de Azure.