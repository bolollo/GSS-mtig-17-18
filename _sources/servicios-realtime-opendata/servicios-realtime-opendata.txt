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


Algunos problemas que nos encontraremos al trabar con servicios Open Data
-------------------------------------------------------------------------

- Al trabajar con servicios Open Data nos encotraremos con infinidad de diferentes implementaciones desde datos en formatos cerrados o no reutilizables como el pdf, hasta servicios bien pensados que ofrecen soluciones a los diferentes niveles de usuarios ya sea mediante aplicaciones finales como mapas o gráficas hasta APIs y ejemplos para desarrolladores.

- Otro problema común es el cambio de las direcciones de los recursos. Es muy frecuente que por un cambio de nombre de servidor o al cambiar la técnología del portal las URLs cambien. Esto hace que todas las aplicaciones y servicios que consumen estos datos dejen de funcionar.

- Falta de cuidado en los datos, inconsistencia, falta de normalización, datos erroneos, etc. Ejemplo https://analisi.transparenciacatalunya.cat/Urbanisme-infraestructures/Equipaments-de-Catalunya/8gmd-gz7i


Ejemplo de buenas prácticas
---------------------------

Un buen ejemplo de servicios realtime Open Data son los servicios de notificación de terremotos ofrecidos por el USGS. El USGS ofrece una serie de servicios que están muy bien documentados y tienen salida en múltiples formatos y son gratuitos y de libre acceso. 

En nuestro caso nos centraremos en el GeoJSON Summary Format https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php vemos que está muy bien documentado.

Ofrece diferentes salidas desde salidas ya procesadas como mapas, gráficos, etc, para los no desarrolladores hasta una API para los desarrolladores. 


Ejemplo de no tan buenas prácticas
----------------------------------

Un ejemplo de no tan buenas prácticas es el caso del servicio OpenData de la AEMET http://www.aemet.es/es/datos_abiertos/AEMET_OpenData para acceder a los servicios es necesaria una API Key, para obtenerla hay que dar una dirección de email y resolver un captcha. Si bien no parece haber restricciones de uso, el simple hecho de tener que registrarse ya es una barrera.  

En este caso hay una diferencia clara entre desarrolladores y acceso general. En ninguno de los casos hay un uso fácil ni claro de los recuros. Al solicitar un recurso te retorna un json que apunta a otro recurso. Ejemplo

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
