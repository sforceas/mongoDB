# MongoDB
Curso de mongodb de platzi.

## Base de datos no relacionales (NoSQL)

Las bases de datos NoSQL tienen 4 grandes familias: Key Value Stores, basadas en grafos, columnares y basadas en documentos.

* **Key Value Stores**: Guardan la información en formato de llaves y valores. Las usamos para guardar cache, información de sesión de los usuarios o cosas muy sencillas. Son muy rápidas de consultar pero no podemos usarlas en casos más complejos donde necesitamos estructuras más especiales. El mejor ejemplo de estas bases de datos es Redis.

* **Graph Databases**: Bases de datos basadas en Grafos. Nos permiten establecer conexiones entre nuestras entidades para realizar consultas de una forma más eficiente que en bases de datos relacionales (así como Twitter o Medium donde cada publicación tiene diferentes relaciones entre sus usuarios, likes, etc). Por ejemplo: Neo4j o JanusGraph.

* **Wide-column** Stores: Bases de datos columnares. Tienen una llave de fila y otra de columnas para hacer consultas muy rápidas y guardar grandes cantidades de información pero modelar los datos se puede volver un poco complicado. Las usamos en Big Data, IoT, sistemas de recomendaciones, entre otras. Por ejemplo: Cassandra o HBase.

* **Document Databases**: Bases de datos basadas en documentos. Nos permiten guardar documentos dentro de colecciones, tiene muy buena performance y flexibilidad que nos permite modelar casos de la vida real de forma sencilla y efectiva. Por ejemplo: MongoDB o CouchBase.

## Funcionamiento de MongoDB
### Base de datos:
* Contenedor físico de colecciones.
* Cada base de datos tiene su archivo propio en el sistema de archivos.
* Un cluster puede tener múltiples bases de datos.

### Colecciones:
* Agrupación de documentos.
* Equivalente a una tabla en las bases de datos relacionales.
* No impone un esquema, por lo que los documentos pueden tener diferentes campos.

### Documentos:
* Un registro dentro de una colección.
* Es análogo a un objeto JSON (BSON).Es su codificación en binario y admite otros datos como fechas.
* La unidad básica dentro de MongoDB.
* Se identifican por un campo oculto ```_id: 123456789``` que es obligatorio y único. Si no se introduce en la creación del documento, se asigna automaticamente.
* Máximo de 16MB.

![comparacion entre conceptos SQL y MongoDB](https://i.pinimg.com/originals/b2/17/79/b21779c78f9474ae2adf947ac6cca20d.png)

Ejemplo de un documento en formato JSON.
```
{   _id:"123456789", 
    item: "canvas",
    qty: 100, 
    tags: ["cotton"], 
    size: { 
        h: 28, 
        w: 35.5, 
        uom: "cm"
        } 
}
````

### Tipos de datos:

#### Strings
Nos sirven para guardar textos.

#### Boolean
Información cierta o falsa (true y false).

#### ObjectId: 
Utilizan el tiempo exacto en el que generamos la consulta para siempre generan IDs únicos. Existen en BSON pero no en JSON.

#### Date 
Nos sirven para guardar fechas y hacer operaciones de rangos entre ellas.

#### Números
Doubles, Integers, Integers 64 bits y Decimals.

#### Documentos Embebidos
Documentos dentro de otros documentos ({}).

#### Arrays []
Arreglos o listas de cualquier otro tipo de datos, incluso, de otras listas.

Más tipos en https://docs.mongodb.com/manual/reference/bson-types/

### Esquemas y relaciones:
Los **esquemas** son la forma en que organizamos nuestros documentos en nuestras colecciones. MongoDB no impone ningún esquema pero podemos seguir buenas prácticas y estructurar nuestros documentos de forma parecida (no igual) para aprovechar la flexibilidad y escalabilidad de la base de datos sin aumentar la complejidad de nuestras aplicaciones.

Las **relaciones** son la forma en que nuestras entidades o documentos sen encuentran enlazados unos con otros. Por ejemplo: Una carrera tiene multiples cursos y cada curso tiene multiples clases.

#### Relaciones 1 a 1
Los **documentos embebidos** nos ayudan a guardar la información en un solo documento y nos ahorra el tiempo que tardamos en consultar diferentes documentos a partir de referencias a colecciones separadas.

```
Colección: inventario

{   _id:"123456789", 
    item: "canvas",
    qty: 100, 
    tags: ["cotton"], 
    size: { 
        h: 28, 
        w: 35.5, 
        uom: "cm"
        } 
}

```

#### Relaciones 1 a muchos
**Documentos embebidos** cuando la información no va a cambiar muy frecuentemente y **referencias** cuando si. 

```
Colección: inventario
{   _id:123456789, 
    item: "canvas",
    tags: ["cotton"], 
    size: { 
        h: 28, 
        w: 35.5, 
        uom: "cm"
        } 
}


Colección: stock
{
    _id:00000001,
    inventory_id: 123456789
    quantity: 100
}
```


#### Tipos de esquemas
La arquitectura de la base de datos supone la principal decisión del desarrollo. Tenemos que tener en cuenta varios puntos antes de empezar a realizar el código:

* Qué datos serán consultados con mayor y menor frecuencia. (_El nombre de un articulo se muestra mas frecuentemente que su proveedor._)

* Qué datps serám actualizados con mayor y menor fecuencia. (_El stock de un articulo se actualiza mas frecuentemente que su marca._)

* Que tipo de consultas serán las más y menos frecuentes (y cuál será su coste de procesamiento). (_Se más frecuentemente antes las listas de varios articulos que modificar detalles individuales de uno de ellos. La lista deberia tener una mayor optimización._)


* Por dónde se expande la BD a medida que escala y si llegara al tamaño maximo de 16MB por documento.
(_Los almacenes o contenedores tienen una capacidad de articulos limitada. Pero a medida que se escala se pueden ir añadiendo (lenamente) almacenes con nuevos articulos. El numero de articulos crece mucho más rápido que el de almacenes o contenedores._)

* Que tipo de nuevos datos se prevee incluir a medida que se añaden funciones. (_Podemos requerir de una colección de "usuarios" para acceder al sistema (con contraseña,nombre,apellidos,telefono,email...)._)


##### Modo MongoDB con Documentos Embebidos
Fácil de implementar y acceso a todos los datos de golpe. A medida que crezca la BD los documentos serán más pesados. Esto dificulta actualizar datos de manera frecuente como el stock.
```
{
    "_id": "ALM0201",
    "ubicación": "Calle Ejemplo 23",
    "responsable": "Cristina Aguado",
    "telefono": "999888777",
    "contenedores":[
        {
        "_id": "CNT05123",
                "tamaño":"40ft",
                "color": "verde",
                "articulos": [
                    {
                    "_id": "ART661234",
                    "nombre": "Laptop",
                    "marca":"superPC",
                    "proveedor":"UAMZ Inc",
                    "peligroso": false,
                    "stock": 240
                    },                
                    {
                    "_id": "ART903452",
                    "nombre": "BateríaLaptop",
                    "marca":"superPC",
                    "proveedor":"UAMZ Inc",
                    "peligroso": true,
                    "stock": 350
                    }

                ]
                    
        },
        {
            "_id": "CNT02012",
                    "tamaño":"20ft",
                    "color": "rojo",
                    "articulos": [                                      
                        {
                        "_id": "ART523642",
                        "nombre": "Cable HDMI",
                        "marca":"superCable",
                        "proveedor":"ExIo SL",
                        "peligroso": false,
                        "stock": 1032
                        }    
                    ]
        }
    ]   
},
{
    "_id": "ALM0111",
    "ubicación": "Calle Recuerdo 12",
    "responsable": "Pepe Villuela",
    "telefono": "666111666",
    "contenedores":[]
}
```


##### Modo “SQL” con Referencias
Las consultas directas por id son muy eficientes. Si se quieren mostrar conjuntos de documentos (como una lista de articulos con su stock) se deben realizar uniones que aumentan el costo de procesado.
```
//colección: almacenes
{
    "_id": "ALM0201",
    "ubicación": "Calle Ejemplo 23",
    "responsable": "Cristina Aguado",
    "telefono": "999888777",
    "contenedores":["CNT05123","CNT02012"]
},
{
    "_id": "ALM0111",
    "ubicación": "Calle Recuerdo 12",
    "responsable": "Pepe Villuela",
    "telefono": "666111666",
    "contenedores":[]
}

// colección: contenedores
{
    "_id": "CNT05123",
    "tamaño":"40ft",
    "color": "verde",
    "articulos": ["ART661234","ART903452"]
},
{
    "_id": "CNT02012",
    "tamaño":"20ft",
    "color": "rojo",
    "articulos": ["ART523642"]
},

//colección: artículos
{
    "_id": "ART661234",
    "nombre": "Laptop",
    "marca":"superPC",
    "proveedor":"UAMZ Inc",
    "peligroso": false,
    "stock": 240
},                
{
    "_id": "ART903452",
    "nombre": "BateríaLaptop",
    "marca":"superPC",
    "proveedor":"UAMZ Inc",
    "peligroso": true,
    "stock": 350
},
{
    "_id": "ART523642",
    "nombre": "Cable HDMI",
    "marca":"superCable",
    "proveedor":"ExIo SL",
    "peligroso": false,
    "stock": 1032
}
```

##### Combinación de documento embedido y referencias:
Mejor opción si tenemos datos con consulta (nombre) update frecuente (stock), y datos con consulta puntual (marca, proveedor…). Nos quiamos todo el costo de cargar los detalles cuando no son necesarios.
```
//colección: almacenes
{
    "_id": "ALM0201",
    "ubicación": "Calle Ejemplo 23",
    "responsable": "Cristina Aguado",
    "telefono": "999888777",
    "contenedores":[
        {
        "_id": "CNT05123",
                "tamaño":"40ft",
                "color": "verde",
                "articulos": [
                    {
                    "_id": "ART661234",
                    "nombre": "Laptop",
                    "stock": 240
                    },                
                    {
                    "_id": "ART903452",
                    "nombre": "BateríaLaptop",
                    "stock": 350
                    }

                ]
                    
        },
        {
            "_id": "CNT02012",
                    "tamaño":"20ft",
                    "color": "rojo",
                    "articulos": [                                      
                        {
                        "_id": "ART523642",
                        "nombre": "Cable HDMI",
                        "stock": 1032
                        }    
                    ]
        }
    ]   
},
{
    "_id": "ALM0111",
    "ubicación": "Calle Recuerdo 12",
    "responsable": "Pepe Villuela",
    "telefono": "666111666",
    "contenedores":[]
}

//colección: artículos
{
    "_id": "ART661234",
    "nombre": "Laptop",
    "marca":"superPC",
    "proveedor":"UAMZ Inc",
    "peligroso": false,
},                
{
    "_id": "ART903452",
    "nombre": "BateríaLaptop",
    "marca":"superPC",
    "proveedor":"UAMZ Inc",
    "peligroso": true,
},
{
    "_id": "ART523642",
    "nombre": "Cable HDMI",
    "marca":"superCable",
    "proveedor":"ExIo SL",
    "peligroso": false,
}
```


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

## Divers de MongoDB para otros lenguajes
Los drivers las librerías que utilizamos para comunicar nuestra aplicación con nuestra base de datos.
Sin nuestros drivers no podemos trabajar con nuestros cluster de base de datos.
Para agregallos, usamos un gestor de dependencias. 
* Python: 
```bash 
python -m pip install pymongo
```
* Node.js: 
```bash 
npm install mongodb --save
```
* Ruby: 
```bash 
gem install mongoid
```
* GO: 
```bash 
dep ensure -add go.mongodb.org/mongo-driver/mongo
```

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
    2. Escojer el tipo de máquiuna (tier) en función de las necesidades.
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
    3. Usar el comando: ```mongo "mongodb+srv://cluster0.wujbs.mongodb.net/<nombredelabasedatos>" --username <nombredelusuarioadmin>```.
    4. Autentificarse con la contraseña del usuario.
    
## Interfaz gráfica MongoDB Compass
  1. Instalar MongoDB Compass desde https://www.mongodb.com/download-center/compass
  2. Crear nueva conexión.
      1. Introducir la url de la base de datos.
      2. Activar SRV record (conexion segura).
      3. Autenticarse con el usuario creado en Atlas.
  
# Creando una base de datos
## Comandos básicos de MongoDB
* Conexión con el cluster de MongoDB Atlas (recuerda añadir tu IP a la lista de IPs permitidas): ```mongo "URL DE NUESTRO CLUSTER",```
* Mostrar las bases de datos exitentes:```show dbs```
* Mostra la base de datos en la nos encontramos: ```db```
* Usar una nueva base de datos (o crearla si no existe):  ```use basedatos-db```
* Listar todos los posibles comandos que podemos ejecutar: ```db.NOMBRE_COLECCIÓN.help()```

## Operaciones CRUD (Create, Read, Update, Delete)
**Version de MongoDB: 4.4**
Para más información sobre otras versiones: https://docs.mongodb.com/manual/crud/


### Crear (insert)
Las operaciones de creación o inserción agregan nuevos documentos a una colección. Si la colección no existe actualmente, las operaciones de inserción crearán la colección.

MongoDB proporciona los siguientes métodos para insertar documentos en una colección:
```
db.collection.insertOne () Nuevo en la versión 3.2
db.collection.insertMany () Nuevo en la versión 3.2
```
En MongoDB, las operaciones de inserción tienen como objetivo una sola colección. Todas las operaciones de escritura en MongoDB son atómicas al nivel de un solo documento.

* Crear una colección (opcional) y añadir un elemento en formato JSON. La base de datos responde true si la operación fue exitosa y crea el campo irrepetible de _id si nosotros no lo especificamos: ```db.NOMBRE_COLECCIÓN.insertOne({ ... }) ```

* Crear una colección (opcional) y añadir algunos elementos en formato JSON. Recibe un array de elementos y devuelve todos los IDs de los elementos que se crearon correctamente.
```db.NOMBRE_COLECCIÓN.insertMany([{ ... }, { ... }])```

![InsertOne](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-insertOne.bakedsvg.svg)

### Leer (find)

Las operaciones de lectura recuperan documentos de una colección; es decir, consultar una colección de documentos. MongoDB proporciona los siguientes métodos para leer documentos de una colección. Puede especificar filtros de consulta o criterios que identifiquen los documentos a devolver.

```
db.collection.find ()
```

* Encontrar elementos en una colección: 
```db.NOMBRE_COLECCIÓN.find()``` 
* Podemos aplicar filtros y mostrar todos los elementos: 
```db.NOMBRE_COLECCIÓN.find({filtroA:"valor1", filtroB:"valor2"})```
* Podemos aplicar filtros y mostrar uno de los elementos:
```db.NOMBRE_COLECCIÓN.find({filtroA:"valor1",filtroB:"valor2"}).limit(1)```
* Leer solo ciertos campos del elemento (se escribe un 1): 
```db.NOMBRE_COLECCIÓN.find({filtro:valorfiltro}, {campoA: 1, campoC: 1)```
* Leer todos los campos de un elemento excluyendo (se escribe un 0): 
```db.NOMBRE_COLECCIÓN.find({filtro:valorfiltro}, {campoB: 0)```

* Si queremos encontrar un **elemento por su id** es importante aplicar una transformación de tipo de variable de "string" a ObjectId("string").
 ```db.NOMBRE_COLECCIÓN.find{'_id': ObjectId(curso['_id'])}```
* Para limitar el numero de resultados de la busqueda se usa el métodod .limit(n) 

![FindOne](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-find.bakedsvg.svg)


### Actualizar (set)
Las operaciones de actualización modifican los documentos existentes en una colección. MongoDB proporciona los siguientes métodos para actualizar documentos de una colección:
```
db.collection.updateOne () Nuevo en la versión 3.2
db.collection.updateMany () Nuevo en la versión 3.2
db.collection.replaceOne () Nuevo en la versión 3.2
```
En MongoDB, las operaciones de actualización tienen como objetivo una sola colección. Todas las operaciones de escritura en MongoDB son atómicas al nivel de un solo documento.

Puede especificar criterios o filtros que identifiquen los documentos a actualizar. Estos filtros utilizan la misma sintaxis que las operaciones de lectura.

Esquema: 
```db.cursos.updateOne({filtros}, {$set: {campo:valor}}).modified_count```
```db.cursos.updateMany({filtros}, {$set: {campo:valor}}).modified_count```

Ejemplo:
```db.cursos.updateOne({'_id': ObjectId(curso['_id'])}, {'$set': {'nombre': curso['nombre'], 'descripcion': curso['descripcion'], 'clases': curso['clases']}}).modified_count```

![UpdateMany](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-updateMany.bakedsvg.svg)


### Eliminar
Las operaciones de eliminación eliminan documentos de una colección. MongoDB proporciona los siguientes métodos para eliminar documentos de una colección:
```
db.collection.deleteOne () Nuevo en la versión 3.2
db.collection.deleteMany () Nuevo en la versión 3.2
```
En MongoDB, las operaciones de eliminación tienen como objetivo una sola colección. Todas las operaciones de escritura en MongoDB son atómicas al nivel de un solo documento.

Puede especificar criterios o filtros que identifiquen los documentos que se eliminarán. Estos filtros utilizan la misma sintaxis que las operaciones de lectura.

![DeleteMany](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-deleteMany.bakedsvg.svg)

## Operadores de consulta

### Operadores de comparació
* **$eq** : Operador igual =.
* **$gt** : Operador mayor que >.
* **$gte** : Operador mayor o igual que >=.
* **$lt** : Operador menor que >.
* **$lte** : Operador manor o igual que >=.
* **$ne** : Operador distinto de.
* **$in** : Valores dentro de un arreglo.
* **$nin** : Valores que no están dentro de un arreglo.
### Operadores lógicos
* **$and** : Une queries con un and lógico (similar al uso de una coma en un filtro).
* **$not** : Invierte el efecto de una query.
* **$nor** : Une queries con un nor lógico.
* **$or** : Une queries con un or lógico (si una condición se cumple nos trae el documento) ```db.inventory.find( { $or: [ { status: “A” }, { qty: { $lt: 30 } } ] } )```.
### Operadores por elemento
* **$exist** : Documentos que cuentan con un campo específico (Ejemplo: campo edad).
* **$type** : Documentos que cuentan con un campo de un tipo específico (Ejemplo: tipo datetime).
### Operadores para arreglos
* **$all** : Arreglos que contengan todos los elementos de la query ```db.articles.find( { tags: { $all: [ [ “ssl”, “security” ] ] } } )```.
* **$elemMatch** : Documentos que cumplen la condición del $elemMatch en uno de sus documentos (condiciones dentro de subdocumentos) ```db.inventory.find( { “instock”: { $elemMatch: { qty: 5, warehouse: “A” } } } )```
* **$size** : Documentos que contienen un campo tipo arreglo de un tamaño específico.

https://docs.mongodb.com/manual/tutorial/query-documents/
