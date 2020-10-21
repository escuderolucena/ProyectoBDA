# ProyectoBDA
Proyecto aconsejador de alquileres por barrios con menos contaminación.

# Nombre del Producto

	Recomendador de alquileres de Airbnb por barrios con menos contaminación.


### Estrategia del DAaaS

	Se pasará un reporte cada 2 horas de los mejores 50 pisos para alquilar
	con Airbnb en Madrid en función a su menor contaminación. Se incorporarán
	un dataset público de Airbnb y otro	dataset scrapyado de Aqicn.org.
	Se limpiarán y transformarán los datos y se unirán con una query en la nube.


### Arquitectura

	Arquitectura Cloud basada en Scrapy, Google Cloud Storage, Trifacta, HIVE, 
	Dataproc y Gmail.

	A traves de un activador de Google Cloud Function se ejecutará un Scrapy
	leyendo los datos de contaminación de Aqicn. Dichos datos se transformarán
	en la nube con Google Dataprep a través	de Trifacta transformando el CSV
	en uno más manejable.

	El dataset de Airbnb se limpiará y transformará con Trifacta en Google Dataprep.
  
	Ambos CSV's se guardarán en el mismo segmento de Google Cloud.
  
	Con Google Cloud se creará un cluster de 3 contenedores y se usará HIVE para 
	hacer una query	en la que se hará un Join con los datos de geolocalización de
	ambos CSV's para llevar a cabo una comparación que indique los mejores pisos
	en alquiler en función a su distancia a	puntos menos contaminantes y con una
	nota media de al menos 8. El resultado se guardará en el mismo segmento de
	Google Storage.


### Operating Model

	El operador será una Raspberry Pi que cada hora ejecutará el activador de
	Google Cloud Functions,	guardando el archivo en el segmento en el directorio
	llamado "input/crawl.csv". En el mismo segmento siempre habrá otro archivo
	llamado "airbnbmadrid.csv". Además, se ha programado a Trifacta	con Google
	Dataprep para que cada hora el archivo "crawl.csv" lo prepare para la query
	dejando un archivo llamado "aqicnCSV.csv". Se lanzará la query en HIVE.

	Se enviarán las siguientes tareas manualmente en HIVE:
	- 	Crear tabla de Airbnb.
	- 	Crear tabla de Aqicn.
	- 	load data inpath 'gs://.../aqicnCSV.csv' into table aqicn;
	- 	load data inpath 'gs://.../airbnb.csv/' into table airbnb;
	- 	Se ejecuta la Query (Ver en el link de Desarrollo)

	O se activará, tras encender el Cluster, una tarea de Dataproc configurada
	tipo HIVE que ejecuta las ordenes anteriores automáticamente.

	Se apagará el Cluster.

	El resultado se descargará de Google Storage y se enviará por Gmail siempre
	que se quieran conocer los resultados.


### Desarrollo

	- Scrapy en la Google Cloud Function: 
	https://drive.google.com/open?id=1v7VpQ7pmZZnM0ZAwq02j5HeWV-omOFg0

	- Activador Google Cloud Function:
	https://us-central1-calcium-spanner-264710.cloudfunctions.net/function-8

	- Query de HIVE:
	https://drive.google.com/file/d/1OtmB0bx2WZTF2VD1f3el433zmtUYO2cZ/view?usp=sharing

	- Pantallazo final:
	https://drive.google.com/file/d/1zS8QutSCe-vWootiI19f-QXjSdVh7pdE/view?usp=sharing


### Diagrama

	https://docs.google.com/drawings/d/1weTpodYp5jCgk4Jj5W2ebC_uQ3kZ2auzKC5PcHXPLAc/edit?usp=sharing
