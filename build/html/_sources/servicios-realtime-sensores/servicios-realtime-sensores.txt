***************************
Servicios realtime sensores
***************************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	 X Noviembre 2017    * Victor Pascual 
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Algunos problemas que nos encontraremos al trabar con sensores
==============================================================

- Si bien existe el estandar SOS de la OGC [#]_, muchos de los desarrolladores de los sensores utilizan formatos propios, lo que dificulta la integración de sensores de diferentes provedores en un sólo sistema.

- Datos erroneos, puede haber datos falsos, ya sea por una mala lectura o por que el sensor no está funcionando correctamente. No tenemos la seguridad de saber si el dato es bueno. Ejemplo: http://sensors.portdebarcelona.cat/widget/?name=thermometer&service=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fsos%2Fjson&offering=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fdef%2Fweather%2Fofferings%2310m&feature=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fdef%2Fweather%2Ffeatures%23P6&property=http%3A%2F%2Fsensors.portdebarcelona.cat%2Fdef%2Fweather%2Fproperties%2332&refresh_interval=120&footnote=Nota%20al%20pie%20de%20ejemplo%20en%20el%20widget%20Term%C3%B3metro&lang=es


Ejemplo de buenas prácticas
---------------------------

Un buen ejemplo de servicios de sensores son los servicios de datos sobre embalses ofrecidos por la ACA http://aca-web.gencat.cat/aca/appmanager/aca/aca?_nfpb=true&_pageLabel=P56600137761453129970599. El servicio está bien documentado con ejemplos, el acceso es libre y gratuito, también tiene salida en un mapa para los ususarios que no sean desarrolladores. 
 
Ejemplo de salida
http://aca-web.gencat.cat/sdim2/apirest/catalog?componentType=embassament

Si bien el formato de salida es un JSON donde tiene una propiedad *location* no es un formato geográfico que podamos utilizar directamente para poner en un mapa, para ello tendríamos que hacer una transfromación a algún formato geo tipo GeoJSON.


Ejemplo de no tan buenas prácticas
----------------------------------

Un ejemplo de no tan buenas prácticas es el caso del servicio de la DIBA https://www.diba.cat/es/web/smartregion/obtenir-acces-a-sentilo-diba para acceder a los servicios es necesaria una API Key, para obtenerla hay que enviar un email con nuestros datos y el motivo de uso. El simple hecho de tener que registrarse ya es una barrera. El acceso a la aplicación http://sentilo.diba.cat/sentilo-catalog-web/ no está fácil de encontrar y no hay ninguna documentación.

Referencias
###########

.. [#] http://www.opengeospatial.org/standards/sos
