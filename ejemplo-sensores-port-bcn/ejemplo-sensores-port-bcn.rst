*******************************
Ejemplo sensores port Barcelona
*******************************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	 X Noviembre 2017    * Victor Pascual 
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Acceso al servicio de datos de sensores del Port de Barcelona
=============================================================

El `portal de sensores del Port de Barcelona <http://sensors.portdebarcelona.cat/?lang=es>` contiene una serie de componentes gráficos o widgets que permiten acceder a los datos de los diferentes sensores SOS [#]_. También podemos acceder directamente al servicio http://sensors.portdebarcelona.cat/sos/json si queremos implementar nuestros propios componentes o procesar los datos directamente.

Estos widgets implementan un cliente SOS que soporta la versión 2.0 de estándard, y, en estos momentos, necesitan un endpoint en formato JSON, que resulta no ser un requisito del estándar, sino una funcionalidad opcional que proporciona la implementación de servidor SOS de 52 north en su versión 4.0.0 o superior.

La ventaja de utilizar los widgets es que agregan una capa de abstracción que hace el trabajo sucio y nos evita trabajar directamente con el servicio.

La implementación de los widgets es open source [#]_ y extensible lo que nos permite poder desarrollar nuestros propios widgets.

Creación de un visor 
--------------------

Para crear un visor de mapas utilizaremos el widget de Mapa [#]_ que está basado en la librería de mapas Leaflet [#]_.

#. En nuestro ordenador creamos una carpeta con el nombre de *visor-port*.
#. Dentro de esta carpeta creamos un archivo con el nombre de *index.html*.
#. Abrimos el archivo index.html con un editor de texto y copiamos el siguiente código.::
		<!DOCTYPE html>
		<html>
		<head>
			<title>Sensores Port de Barcelona</title>
			style>
		        #map-container {
		            position: absolute;
		            top: 0;
		            left: 0;
		            bottom: 0;
		            right: 0;
		        }
		    </style>
		</head>
		<body>
			<div id="map-container"></div>

		    <script src="http://sensors.portdebarcelona.cat/js/SensorWidgets.js"></script>
		    <script type="text/javascript">

		    	SensorWidget('map', {
				   "service": "http://sensors.portdebarcelona.cat/sos/json",
				   "offering": "http://sensors.portdebarcelona.cat/def/weather/offerings#30m",
				   "swap_axis": true,
				   "features": [],
				   "properties": []
				}, document.getElementById('map-container'));
				
		    </script>
		</body>
		</html>  
#. Abrimos el archivo index.html en el navegador y vemos que se nos carga un mapa del mundo. Este mapa no contiene ninguna información.
#. Agregamos algunas features (sensores) en la matriz de features. Para obtenter la features tendríamos que hacer una peticón de GetFeatureOfInterest [#]_ .Para agregar algunos elementos en nuestro mapa reemplazamos la propiedad features por lo siguiente: ::
		"features": [
		    "http://sensors.portdebarcelona.cat/def/weather/features#01",
		    "http://sensors.portdebarcelona.cat/def/weather/features#02",
		    "http://sensors.portdebarcelona.cat/def/weather/features#03",
		    "http://sensors.portdebarcelona.cat/def/weather/features#P4",
		    "http://sensors.portdebarcelona.cat/def/weather/features#10",
		    "http://sensors.portdebarcelona.cat/def/weather/features#P5",
		    "http://sensors.portdebarcelona.cat/def/weather/features#P6"
		],
#. Recargamos el mapa y vemos que nos aparecen unos puntos en el puerto de Barcelona.
#. Ya tenemos algunos sensores en nuestro mapa pero no tenemos ningún dato. Para ver algunos valores tendremos que indicar que propiedades queremos observar. Esto lo indicaremos en la matriz de properties de nuestro mapa. Para ver la temperatúra agregamos las siguientes propiedades: ::
		"properties": [
	        "http://sensors.portdebarcelona.cat/def/weather/properties#32M",
	        "http://sensors.portdebarcelona.cat/def/weather/properties#32",
	        "http://sensors.portdebarcelona.cat/def/weather/properties#32N"
	    ],
#. Ahora si recargamos el mapa y pasamos el cursor sobre algún elemento vemos que se despliega un panel con las temperaturas.
#. En nuestro mapa podemos combinar varios widgets por ejemplo podemos hacer que al hacer click sobre un elemento se muestre un popup con un widget. Pare ellos debemos utilizar la opción *popup_widget* del mapa. En este caso cargaremos un widget de tipo serie de tiempoi con las temperaturas. En nuestro mapa justo debajo de las propertities escribimos lo siguiente: ::
		"popup_widget": {
		    "name": "timechart",
		    "title": "Temperatures",
		    "properties": [
		        "http://sensors.portdebarcelona.cat/def/weather/properties#32M",
		        "http://sensors.portdebarcelona.cat/def/weather/properties#32",
		        "http://sensors.portdebarcelona.cat/def/weather/properties#32N"
		    ],
		    "time_start": "2015-09-03T05:05:40Z",
		    "time_end": "2015-09-03T08:05:40Z"
		}
#. Recargamos la página y al hacer click sobre un elemento se nos muestra un popup con una serie temporal de las temperaturas.
#. También podemos hacer que al clicar sobre un elemento se nos muestre un widget en un elemento fuera del mapa. Para ellos vamos a crear un nuevo div. En nuestro html juesto debajo de donde declaramos el div del mapa escribimos ::
		<div id="info-container"></div>
#. En nuestro apartado de estilo creamos un nuevo estilo para este elemento. ::
		#info-container {
        	position: absolute;
            top: 0;
            left: 0;
            width: "200px";
            z-index: 9000;
            background-color: rgba(255,255,255,0.7);
        }
#. En nuestro mapa quitaremos la opción de *popup_widget* y en este caso utilizaremos la opción de *on_click* que llama a una función que se ejecuta al hacer click sobre un elemento del mapa. Debajo de las properties copiamos lo siguiente: ::
		"on_click": function(el){
   			console.log(el);
   		}
#. Recargamos el mapa, abrimos la consola de desarrollador del navegador y hacer click sobre un elemento. Podemos observar que no pasa nada. Esto es debido a que esta funcionalidad no está implementada en la librería SensorWidgets que estamos utilizando. Para que esto nos funcione vamos a utilizar una nueva versión de esta librería que está en la página del desarrollador de los Widgets (Oscar Fonts). Para ellos en nuestro html debemos cargar **http://sensors.fonts.cat/js/SensorWidgets.js** en lugar de *http://sensors.portdebarcelona.cat/js/SensorWidgets.js*.
#. Volvemos a cargar la página y ahora al hacer click sobre un elemento en la consola de desarrollador nos aparece la información del elemento clicado.
#. Creamos una función que dado el id de un elemento nos cree un widget de tipo termómetro. Antes de donde cerramos el tag de script escribimos ::
		function showTermometro(feature){
			SensorWidget('thermometer', {
			   "service": "http://sensors.portdebarcelona.cat/sos/json",
			   "offering": "http://sensors.portdebarcelona.cat/def/weather/offerings#30m",
			   "feature": feature,
			   "property": "http://sensors.portdebarcelona.cat/def/weather/properties#32",
			   "refresh_interval": 120,
			   "footnote": "A sample footnote for Thermometer widget"
			}, document.getElementById('info-container'));
		}
#. Dentro de la función del *on_click* llamaremos a nuestra nueva función. Luego del console.log escribimos: ::
		showTermometro(el.feature.id);
#. Ahora si recargamos nuestro mapa y hacemos click sobre un elemento se desplega el widget del termómetro con la temperatura actual.

		.. |port_bcn| image:: _images/sensores_port_bcn.png
		  :align: middle
		  :alt: capturar mapa de sensores del port de barcelona

		+------------+
		| |port_bcn| |
		+------------+


Referencias
###########

.. [#] http://sensor-widgets.readthedocs.io/es/latest/sos.html
.. [#] https://github.com/oscarfonts/sensor-widgets
.. [#] http://sensor-widgets.readthedocs.io/es/latest/widgets.html#mapa-map
.. [#] http://leafletjs.com/
.. [#] http://sensor-widgets.readthedocs.io/es/latest/sos.html#getfeatureofinterest

 