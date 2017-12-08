*********************
Geoservicios realtime
*********************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	 X Noviembre 2017    * Victor Pascual 
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Creación de un servicio realtime para compartir la ubicación
============================================================

Crearemos un servicio que permita compartir la ubicación de los usuarios y ver que usuarios están en linea. Para ello utilizaremos la librería Socket.io [#]_ que permite la comunicación en tiempo real en dos direcciones cliente-servidor (tipo pull) y servidor-cliente (tipo push).

Para mostrar los datos en el mapa utilizaremos la libreria Leaflet [#]_. Para obtener la ubicación de los usuarios podemos usar el plugin *leaflet-locatecontrol* en nuestro caso vamos en lugar de utilizar la ubicación del usuario vamos a simular la ubicación del usuario haciendo click sobre el mapa. 


Creación del mapa
-----------------

#. 




Referencias
###########

.. [#] https://socket.io/
.. [#] http://leafletjs.com/
.. [#] https://github.com/domoritz/leaflet-locatecontrol

