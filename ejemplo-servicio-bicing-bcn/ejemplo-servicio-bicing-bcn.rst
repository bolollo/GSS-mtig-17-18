*********************************
Ejemplo servicio bicing Barcelona
*********************************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	 X Noviembre 2017    * Victor Pascual 
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Acceso al servicio de datos del Bicing de Barcelona
===================================================

En el portal Open data del Ayuntamiento de Barcelona podemos encontrar un dataset (conjunto de datos) que contiene las `estaciones del servicio de Bicing <http://opendata-ajuntament.barcelona.cat/data/es/dataset/bicing>`_ 

Si bien el Ayuntamiento de Barcelona no ofrece el acceso a los datos del Bicing como un servicio se pueden acceder a los datos del `mapa de disponibilidad <https://www.bicing.cat/es/mapa-de-disponibilidad>`_ . Para ellos abrimos el mapa de disponibilidad en el navegador y presionamos la tecla F12. 

Con la tecla F12 desplegamos la barra de herramientas para desarrolladores. Esta barra entre otras cosas nos permite ver las peticiones que hace una página web. Para ver la url del servicio de estaciones de Bicing seleccionamos la pestaña de red (Network) y filtramo por XHR. Si no vemos ninguna petición recargamos la página presionando la tecla F5. 

Al refrescar la página veremos dos peticiones, la que nos interesa es la que dice *getJsonObject*. La seleccionamos y copiamos la URL que sería https://www.bicing.cat/availability_map/getJsonObject.


		.. |bicing_bcn| image:: _images/bicing.png
		  :align: middle
		  :alt: capturar url servicio de bicing

		+--------------+
		| |bicing_bcn| |
		+--------------+





Referencias
###########