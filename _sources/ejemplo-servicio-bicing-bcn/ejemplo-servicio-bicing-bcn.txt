*********************************
Ejemplo servicio bicing Barcelona
*********************************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Acceso al servicio de datos del Bicing de Barcelona
---------------------------------------------------

En el portal Open data del Ayuntamiento de Barcelona podemos encontrar un dataset (conjunto de datos) que contiene las `estaciones del servicio de Bicing <http://opendata-ajuntament.barcelona.cat/data/es/dataset/bicing>`_ 

Si bien el Ayuntamiento de Barcelona no ofrece explicitamente el acceso a los datos del Bicing como un servicio, si que tiene un servicio de datos en tiempo real. La url la podemos encontrar presionando el botón de Descargar del recurso bicing.json 

		.. |bicing_bcn| image:: _images/bicing.png
		  :align: middle
		  :alt: capturar url servicio de bicing

		+--------------+
		| |bicing_bcn| |
		+--------------+


Al abrir la url http://wservice.viabicing.cat/v2/stations en nuestro navegador observaremos que la respuesta es un archivo json con un conjunto de elementos que tienen las coordenadas de la localización de la estación de bicing, la disponibilidad de bicis, las estaciones más cercanas, etc.

Mapa que utiliza este servicio, `Ejemplo creado en la plataforma Instamaps <https://www.instamaps.cat/instavisor/1611695/dc769e48513f5df888691d2048005934/Estacions_bicing_i_carrils_bici_a_BCN_.html?3D=false#14/41.3962/2.1714>`_

El archivo json que retorna el servicio tiene coordenadas pero no es un fichero GeoJSON [#]_.

Para ver estos datos sobre un mapa crearemos un visor utilizando Leaflet [#]_.

Creación de un visor
--------------------

#. Crer una carpeta con el nombre de *visor-bicing*.
#. Crear un archivo con el nombre de *index.html* dentro de la carpeta
#. Abrir el archivo index.html con un editor de texto y copiar el siguiente código. ::

		<!DOCTYPE html>
		<html>
		<head>
			<title>Servicio de Bicing realtime</title>
			<link rel="stylesheet" href="https://unpkg.com/leaflet@1.2.0/dist/leaflet.css" />
		    <style>
		        #map {
		            position: absolute;
		            top: 0;
		            left: 0;
		            bottom: 0;
		            right: 0;
		        }
		    </style>
		</head>
		<body>
			<div id="map"></div>

		    <script src="https://unpkg.com/leaflet@1.2.0/dist/leaflet.js"></script>
		    <script type="text/javascript">
		    	var map = L.map('map');

		    	map.setView([41.3887, 2.1777], 13);
			    

				L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
				    attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
				}).addTo(map);
				
		    </script>
		</body>
		</html>

#. Abrir el archivo index.html en el navegador para ver que carga un mapa centrado en Barcelona.

#. Agregar el plugin para cargar datos en tiempo real. Para ellos utilizaremos el plugin Leaflet Realtime [#]_.  Copiar lo siguiente justo después de cuando carguemos la libreria de Leaflet. ::

		<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-realtime/2.1.0/leaflet-realtime.min.js"></script>

#. Agregar la capa de realtime del bicing a nuestro mapa. Siguiendo el ejemplo básico del plugin para cagar una capa, copiar lo siguiente al final de nuestro código de javascript. ::

		var realtime = L.realtime({
	        url: 'http://wservice.viabicing.cat/v2/stations',
	        crossOrigin: true,
	        type: 'json'
	    }, {
	        interval: 3 * 1000
	    }).addTo(map);

#. Recargar la página para visualizar nuestra capa de bicing. Observaremos que no aparece ningún dato, esto es debido a que estamos llamando a un servicio que no está en nuestro dominio y nos da un error de CORS [#]_. Abrir la consola de desarrollador del navegador presionando F12 y veremos que cada 3 segundos aparecerá un error. Para evitar el error de CORS necesitamos un proxy [#]_ en nuestro servidor web que pueda hacer la llamada al servicio de bicing y que nos devuelva el contenido.


Creación del proxy
------------------

#. Instalar Node.js [#]_. Descargar la última versión LTS (en este momento es la 8.9.1 LTS) y lo instalaremos con las opciones por defecto. Una vez instalado el Node abrir la consola para verificar que se ha instalado correctamente. Escribir ::

		node -v

#. Navegar hasta nuestra carpeta *visor-bicing* y escribir: ::

		npm init

		Con este comando estaremos creando el archivo *package.json*. Este comando solicita varios elementos como, por ejemplo, el nombre y la versión de la aplicación. Por ahora, sólo hay que pulsar ENTER para aceptar los valores predeterminados.		

#. Instalar las dependencias para crear nuestro servicio de proxy. En este caso utilizaremos Express [#]_ como servidor web y el módulo http-proxy [#]_ .

	#. Instalar el express y guardarlo en la lista de dependencias ::

			npm install express --save

	#. Instalar el http-proxy y guardarlo en la lista de dependencias ::

			npm install http-proxy --save

	Al ejecutar estos comandos veremos que se crea una carpeta llamada *node_modules* donde se guardan los módulos instalados.

#. Crear un archivo llamado *app.js* que servirá de proxy con el servicio de Bicing. Copiar lo siguiente en este archivo. ::

		var express  = require('express');
		var app      = express();
		var httpProxy = require('http-proxy');
		var apiProxy = httpProxy.createProxyServer();
		var serverBicing = 'http://wservice.viabicing.cat/v2/stations';


		app.use(express.static('public'));
		 
		app.all("/bicing/*", function(req, res) {
		    console.log('redirecting to Server1');
		    apiProxy.web(req, res, {
		    	target: serverBicing,
		    	changeOrigin: false, 
		    	ignorePath: true
		    });
		});

		app.listen(3000);

#. 	Probar que nuestro proxy está funcionando, escribiendo: ::

		node app.js

#. Abrir la url de nuestro proxy http://localhost:3000/bicing/ en el navegador.

#. Crear una carpeta llamada *public* dentro de nuestra carpeta y mover el archivo index.html dentro de esa carpeta. Con esto ya podemos ver nuestra aplicación del mapa servida desde un servidor web y no abriendola directamente como habíamos hecho hasta ahora. 

#. Escribir en el navegador http://localhost:3000 para ver nuestro mapa.

#. Modificar el archivo index.html para que llame al proxy que hemos creado. Cambiar la url del servicio de bicing *http://wservice.viabicing.cat/v2/stations* por nuestro proxy *http://localhost:3000/bicing/* (como el proxy y la aplicación están en el mismo servidor podríamos usar */bicing/*). Recargar la aplicación con Ctrl+F5 y veremos que el error que nos da ahora es diferente.

	En este caso el error es *Error: Invalid GeoJSON object.*. Este error es debido a lo que ya comentamos; la respuesta del servicio de Bicing no es un GeoJSON.

#. Crear una variable llamada geojson que será la que contendrá el GeoJSON resultante de la transformación, antes de la declaración de nuestra capa de realtime ::

		var geojson = {
            type: 'FeatureCollection',
            features: []
        };

#. Modificar la aplicación para transformar la respuesta del bicing en un GeoJSON. Modificar nuestra capa realtime con el siguiente código ::

		var realtime = L.realtime(function(success, error) {
	        fetch('/bicing/')
	        .then(function(response) { 
	        	return response.json(); 
	        })
	        .then(function(data) {
	        	var stations = data.stations;
	        	for (var i = stations.length - 1; i >= 0; i--) {
	        		var station = stations[i];
	        		var feature = {
				        type: 'Feature',
				        properties: {
				            altitude: station.altitude,
				            bikes: station.bikes,
				            id: station.id,
				            nearbyStations: station.nearbyStations,
				            slots: station.slots,
				            status: station.status,
				            streetName: station.streetName,
				            streetNumber: station.streetNumber,
				            type: station.type
				        },
				        geometry: {
				            type: 'Point',
				            coordinates: [station.longitude, station.latitude]
				        }
				    };
				    geojson.features.push(feature);
	        	}
	            success(geojson);
	        })
	        .catch(error);
	    }, {
	        interval: 3 * 1000
	    }).addTo(map);

#. Recargar la aplicación y veremos los puntos de las estaciones de bicing. Si vamos a la pestaña de red (network) en la consola de desarrollador del navegador podremos ver que cada 3 segundos se hace una llamada a nuetro proxy.

#. Crear un popup para ver la información de la estación al seleccionarla. Escribir justo después de donde definimos el intervalo ::

		,onEachFeature(f, l) {
            l.bindPopup(function() {
                return '<h3>' + f.properties.id + '</h3>' +
                    '<p>' + f.properties.streetName +
                    '<br/>bike: <strong>' + f.properties.bikes + '</strong></p>' +
                    '<p>slots: ' + f.properties.slots + '</p>';
            });
    	} 

#. Recargar la página y hacer click sobre alguna estación para ver su información en tiempo real.


		.. |mapa_bicing_bcn| image:: _images/mapa_bicing.png
		  :align: middle
		  :alt: mapa de servicio de bicing

		+-------------------+
		| |mapa_bicing_bcn| |
		+-------------------+


Referencias
###########

.. [#] https://es.wikipedia.org/wiki/GeoJSON
.. [#] http://leafletjs.com/
.. [#] https://github.com/perliedman/leaflet-realtime
.. [#] https://developer.mozilla.org/es/docs/Web/HTTP/Access_control_CORS
.. [#] https://es.wikipedia.org/wiki/Servidor_proxy
.. [#] https://nodejs.org/es/
.. [#] http://expressjs.com/
.. [#] https://github.com/nodejitsu/node-http-proxy