#¿Qué es MongoDB Sharding y las mejores prácticas?

## Necesidad
En el momento que tenemos nuestra base de datos en producción, se empiezan a generar más trafico de datos y el almacenamiento se llena. Por eso llega un momento donde nuestro cluster no rinde adecuadamente, ya sea por CPU, RAM o Disco Duro.

##  Solución
La solución más usada en MongoDB es el escalamiento horizontal de las bases de datos o fragmentación (sharding en inglés). Esto nos permite agregar nuevos clusters, es decir más máquinas, en nuestro ecosistema de modo que podemos hacer crecer nuestra capacidad de procesado y almacenamiento. Esto resulta más efectivo que el escalamiento vertical, que se basaria en aumentar la capacidad del hardware de esa única máquina. Aún así, actualmente, los mejores servidores en la actualidad no tienen más de 256 GB de RAM o 16 TB de disco duro, por lo que el escalamiento vertical es una opción limitada por hardware.




