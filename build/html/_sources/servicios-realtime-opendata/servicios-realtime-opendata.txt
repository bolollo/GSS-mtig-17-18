****************************
Servicios realtime Open data
****************************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Problemas frecuentes al trabajar con servicios Open Data
--------------------------------------------------------

- Ausencia de normalización u homogeneidad en los portales y servicios.

- Datos en formatos cerrados o no reutilizables como el pdf.

- Cambio de las direcciones de los recursos. Por ejemplo, suele ocurrir que cuando se cambia el nombre de un servidor ó la técnología del portal ocasiona un cambio en las URLs y dejan de funcionar las aplicaciones y servicios que consumen esos datos.

- Datos poco fiables, erróneos, inconsistentes. Falta de  normalización y homogeneidad también en los datos. Ejemplo https://analisi.transparenciacatalunya.cat/Urbanisme-infraestructures/Equipaments-de-Catalunya/8gmd-gz7i


Ejemplo de buenas prácticas
---------------------------

Un buen ejemplo de servicios realtime Open Data son los servicios de notificación de terremotos de el USGS porque están muy bien documentados, tienen salida en múltiples formatos, son gratuitos y de libre acceso y ofrecen diferentes niveles de usuarios (programadores y no programadores)

https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php



Ejemplo de malas prácticas
--------------------------

En el servicio OpenData de la AEMET http://www.aemet.es/es/datos_abiertos/AEMET_OpenData observamos que nos solicitan una API Key para acceder a los servicios y para obtenerla hay que dar una dirección de email y resolver un captcha, lo que constituye una barrera de entrada.  

Luego, existe una diferencia clara entre desarrolladores y el acceso general. Y en ninguno de los perfiles de usuarios hay un acceso fácil, directo y claro a la información. Lo vemos cuando solicitamos un recurso, que nos retorna un json apuntando a otro recurso. Ejemplo

https://opendata.aemet.es/opendata/api/prediccion/especifica/municipio/diaria/08001?api_key=eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJib2xvc2lnQGdtYWlsLmNvbSIsImp0aSI6ImFkMzFlYjhmLTYxYmQtNGUxMi05Y2E0LTE4MGU4M2UzYzkwNSIsImlzcyI6IkFFTUVUIiwiaWF0IjoxNTExOTgzOTI2LCJ1c2VySWQiOiJhZDMxZWI4Zi02MWJkLTRlMTItOWNhNC0xODBlODNlM2M5MDUiLCJyb2xlIjoiIn0.YYQ93aedA5RM6WTp8XR-gDw3XyMeMxYrCEddDbSpwhU

Retorna ::

		{
		  "descripcion" : "exito",
		  "estado" : 200,
		  "datos" : "https://opendata.aemet.es/opendata/sh/36188a6b",
		  "metadatos" : "https://opendata.aemet.es/opendata/sh/dfd88b22"
		}

Datos
https://opendata.aemet.es/opendata/sh/36188a6b

Metadatos
https://opendata.aemet.es/opendata/sh/dfd88b22
