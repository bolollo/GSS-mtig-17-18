***************************
Servicios realtime sensores
***************************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Problemas frecuentes al trabajar con sensores
---------------------------------------------

- Existe el estandar SOS de la OGC [#]_, pero tiene poca implementación, debido a que algunos de los fabricantes de sensores utilizan formatos propios, lo que dificulta la integración de sensores de diferentes provedores en un sólo sistema.

- Datos erróneos y falsos, ya sea por una mala lectura ó porque el sensor no está funcionando correctamente. No tenemos forma de saber si el dato es correcto. `Ejemplo <http://sensors.portdebarcelona.cat/widget/?name=thermometer&service=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fsos%2Fjson&offering=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fdef%2Fweather%2Fofferings%2310m&feature=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fdef%2Fweather%2Ffeatures%23P6&property=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fdef%2Fweather%2Fproperties%2332&refresh_interval=120&footnote=Nota%20al%20pie%20de%20ejemplo%20en%20el%20widget%20Term%C3%B3metro&lang=es>`_


Ejemplo de buenas prácticas
---------------------------

El servicio de datos sobre embalses ofrecidos por la ACA (Agencia Catalana del Agua) http://aca-web.gencat.cat/aca/appmanager/aca/aca?_nfpb=true&_pageLabel=P56600137761453129970599. Es un ejemplo de buenas prácticas porque está bien documentado y contiene ejemplos. El acceso es libre y gratuito y tiene salida en un mapa para los ususarios que no sean desarrolladores. 
 
Ejemplo de salida
http://aca-web.gencat.cat/sdim2/apirest/catalog?componentType=embassament

Si bien el formato de salida es un JSON donde tiene una propiedad *location* no es un formato geográfico que podamos utilizar directamente para poner en un mapa, para ello tendríamos que hacer una transformación hacia algún formato geográfico tipo GeoJSON.


Ejemplo de malas prácticas
--------------------------

El servicio de la DIBA https://www.diba.cat/es/web/smartregion/obtenir-acces-a-sentilo-diba ya que para acceder a los servicios es necesaria una API Key y para obtenerla hay que enviar un email con nuestros datos y el motivo de uso. El simple hecho de tener que registrarse ya es una barrera. 

El acceso a la aplicación http://sentilo.diba.cat/sentilo-catalog-web/ no es fácil de encontrar y no hay ninguna documentación.

Referencias
###########

.. [#] http://www.opengeospatial.org/standards/sos
