# MongoDB
Curso de mongodb de platzi.

## Base de datos no relacionales (NoSQL)

Las bases de datos NoSQL tienen 4 grandes familias: Key Value Stores, basadas en grafos, columnares y basadas en documentos.

* **Key Value Stores**: Guardan la información en formato de llaves y valores. Las usamos para guardar cache, información de sesión de los usuarios o cosas muy sencillas. Son muy rápidas de consultar pero no podemos usarlas en casos más complejos donde necesitamos estructuras más especiales. El mejor ejemplo de estas bases de datos es Redis.

* **Graph Databases**: Bases de datos basadas en Grafos. Nos permiten establecer conexiones entre nuestras entidades para realizar consultas de una forma más eficiente que en bases de datos relacionales (así como Twitter o Medium donde cada publicación tiene diferentes relaciones entre sus usuarios, likes, etc). Por ejemplo: Neo4j o JanusGraph.

* **Wide-column** Stores: Bases de datos columnares. Tienen una llave de fila y otra de columnas para hacer consultas muy rápidas y guardar grandes cantidades de información pero modelar los datos se puede volver un poco complicado. Las usamos en Big Data, IoT, sistemas de recomendaciones, entre otras. Por ejemplo: Cassandra o HBase.

* **Document Databases**: Bases de datos basadas en documentos. Nos permiten guardar documentos dentro de colecciones, tiene muy buena performance y flexibilidad que nos permite modelar casos de la vida real de forma sencilla y efectiva. Por ejemplo: MongoDB o CouchBase.


## Ecosistema de MongoDB
MongoDB es una base de datos gratis y de código abierto No Relacional basada en documentos que nos permite guardar una gran cantidad de documentos de forma distribuida. Mongo también es el nombre de la compañía que desarrolla el código de esta base de datos.

Una de sus principales características es que nos permite guardar nuestras estructuras o documentos en formato **JSON** (no exactamente JSON, pero si algo muy parecido, lo veremos más adelante) para tener una gran flexibilidad a la hora de modelar situaciones de la vida real.

Por ser una base de datos distribuida podemos hablar no de uno sino de varios servidores, lo que conocemos como el Cluster de MongoDB. Gracias a esto obtenemos una gran escalabilidad de forma horizontal (escalabilidad en cantidad de servidores).

![Escalabilidad vertical de servidores](https://www.oscarblancarteblog.com/wp-content/uploads/2017/03/escalamiento-vertical.png)
![Escalabilidad horizontal de servidores](https://www.oscarblancarteblog.com/wp-content/uploads/2017/03/escalamiento-horizontal.png)

MongoDB es **“Schema Less”** lo que permite que nuestros documentos tengan estructuras diferentes sin afectar su funcionamiento, algo que no podemos hacer con las tablas de las bases de datos relacionales. Su lenguaje para realizar queries, índices y agregaciones es muy expresivo.

## MongoDB Atlas (cloud)
Tenemos varios proveedores que nos permiten utilizar o alquilar MongoDB como servicio y en este caso vamos a usar MongoDB Atlas por ser desarrollado por las mismas personas que desarrollan MongoDB.
https://www.mongodb.com/cloud/atlas

MongoDB Atlas tiene las siguientes características:
* Aprovisionamiento automático de clusters con MongoDB
* Alta disponibilidad
* Altamente escalable
* Seguro
* Disponible en AWS, GCP y Microsoft Azure
* Fácil monitoreo y optimización

# Instalación y ejecución
## Instalación en macOS
Para realizar la instalación directamente desde bash es necesario tener instalado el paquete brew. 
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

NOTA: en este caso se usa la versión de mongodb comunity 4.4. La versión se indica tras el símbolo @, de modo que se puede usar otra versió modificando este valor.

```bash
brew tap mongodb/brew
brew install mongodb-community@4.4

```
## Ejecución en macOS

Puede ejecutar MongoDB como un servicio de macOS usando brew, o puede ejecutar MongoDB manualmente como un proceso en segundo plano. Se recomienda ejecutar MongoDB como un servicio macOS, ya que al hacerlo se configuran los valores ulimit correctos del sistema automáticamente (consulte la configuración ulimit para obtener más información).

Para ejecutar MongoDB (es decir, el proceso mongod) como un servicio macOS, emita lo siguiente:
```bash
brew services start mongodb-community@4.4
```

Para detener la ejecución de un mongod como servicio macOS, utilice el siguiente comando según sea necesario:

```bash
brew services stop mongodb-community@4.4
```

Para ejecutar MongoDB (es decir, el proceso mongod) manualmente como un proceso en segundo plano, emita lo siguiente:

```bash
mongod --config /usr/local/etc/mongod.conf --fork
```

Para inicializar el shell de MongoDB, emita lo siguiente:
```bash
mongod
```

## Configuración de un cluster en MongoDB Atlas
1. Iniciar sesión en Atlas.
2. Crear nuevo cluster (Build a Cluster).
    1. Seleccionar proveedor y region más próxima a los usuarios.
    2. Escojer el tipo de máqiuna (tier) en función de las necesidades.
    3. Escojer la version de Atlas.
    4. Nombrar el cluster.
    5. Esperar a que se despliegue el cluster (varios minutos).
3. En la pestaña Security, click en Añadir Nuevo Usuario.
    1. Nombre y contraseña seguros (guardar credenciales).
    2. Permisos de usuario (admin/lectura/escritura).
4. En la pestaña Security, click en Añadir IP en WhiteList.
    1. Escribir la ip desde la que nos conectamos para que permita la conecxión.
5. Click en Connect.
    1. Click en Connect via Mongo Shell.
    2. Verificar que Mongo Shell está instalado.
    3. Usar el comando: bash```mongo "mongodb+srv://cluster0.wujbs.mongodb.net/<nombredelabasedatos>" --username <nombredelusuarioadmin>```.
    4. Autentificarse con la contraseña del usuario.
    
## Interfaz gráfica MongoDB Compass
  1. Instalar MongoDB Compass desde https://www.mongodb.com/download-center/compass
  2. Crear nueva conexión.
      1. Introducir la url de la base de datos.
      2. Activar SRV record (conexion segura).
      3. Autenticarse con el usuario creado en Atlas.
  
# Creando una base de datos
## Comandos básicos de MongoDB
* Mostrar las bases de datos exitentes bash:``` show dbs```
* Mostra la base de datos en la nos encontramos: bash```db```
* Usar una nueva base de datos (o crearla si no existe): bash ```use basedatos-db```

## Operaciones CRUD (Create, Read, Update, Delete)


