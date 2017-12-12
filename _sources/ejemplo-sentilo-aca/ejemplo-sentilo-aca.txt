*******************
Ejemplo Sentilo ACA
*******************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Acceso al servicios de sensores Sentilo de la ACA
-------------------------------------------------

En la página de la ACA [#]_ en el apartado de consulta de datos podemos encotrar un subapartado de datos en tiempo real, estos datos los sirven utilizando la plataforma Sentilo [#]_. En esta caso los sensores dan información sobre los diferentes embalses que hay en Cataluña.

En la página existe un acceso a un mapa con los datos de los diferentes sensores [#]_ este mapa es que ofrece la plataforma de Sentilo y está basado en tecnología de Google Maps. 

También encontramos la documentación [#]_ para usar la API, lo que nos permite acceder a los datos y generar nuestro propio visor.

Creación de un visor 
--------------------

Para crear un visor de mapas utilizaremos la librería de mapas Leaflet [#]_.  

#. En nuestro ordenador creamos una carpeta con el nombre de *visor-aca*.
#. Dentro de esta carpeta creamos un archivo con el nombre de *index.html*.
#. Abrimos el archivo index.html con un editor de texto y copiamos el siguiente código. ::

		<!DOCTYPE html>
		<html>
		<head>
			<meta charset="UTF-8">
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <meta http-equiv="X-UA-Compatible" content="ie=edge">
			<title>Ejemplo Sentilo ACA</title>
			<link rel="stylesheet" href="https://unpkg.com/leaflet@1.2.0/dist/leaflet.css" />
			<style>
		        #map {
		            height: 100%;
		            width: 100%;
		            position: absolute;
		        }
		    </style>
		</head>
		<body>

			<div id="map">

		    </div>

			<script src="https://unpkg.com/leaflet@1.2.0/dist/leaflet.js"></script>
			<script>
				var map = L.map('map');

		        map.setView([41.5087, 2.1777], 8);  

		        L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
		            attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
		        }).addTo(map);
			</script>
		</body>
		</html>
#. Abrimos el archivo index.html en el navegador y vemos que se nos carga un mapa centrado en Cataluña.
#. Si miramos la documentación de la API nos dice que la url de descripción del servicio es http://aca-web.gencat.cat/sdim2/apirest/catalog. Abrimos esta url en un navegador y vemos que nos responde un JSON con la información de los diferentes sensores.
#. El JSON de salida tiene una propiedad *location* que nos indica las coordenadas de la ubicación del sensor. A pesar de que el JSON tiene coordenadas no es un GeoJSON y por lo tanto no lo podemos pintar automaticamente en nuestro mapa.
#. Para cargar este JSON en nuestro mapa utilizaremos un plugin de Leaflet llamado *leaflet-ajax* [#]_. Este plugin nos permite hacer una llama AJAX a un servicio que retorne un JSON y cargar la respuesta en un mapa. Para cargar este plugin debemos agregar lo siguiente justo después de donde cargarmos el leaflet ::

		<script src="https://calvinmetcalf.github.io/leaflet-ajax/dist/leaflet.ajax.js"></script>
#. Para agregar la capa al mapa llamaremos a la API de la ACA utilizando el plugin. Para ello al final de nuestro código agregamos lo siguiente: ::

		var geojsonLayer = new L.GeoJSON.AJAX('http://aca-web.gencat.cat/sdim2/apirest/catalog').addTo(map);
#. Recargamos la página y vemos que no aparece ninguna información en el mapa. Si abrimos la consola de desarrollador del navegador (Ctrl+F12) podemos ver que nos aparece un mensaje de error *XMLHttpRequest cannot load ...* esto es debido a que estamos llamado a un servicio que no está en nuestro dominio y nos da un error de CORS [#]_. Para evitar el error de CORS necesitamos un proxy [#]_ en nuestro servidor web que pueda hacer la llamada al servicio de bicing y que nos devuelva el contenido.

#. Creación del proxy. En este caso crearemos un proxy utilizando Node.js [#]_

#. Instalación de Node. Nos descargamos la última versión LTS (en este momento es la 8.9.1 LTS) y lo instalamos con las opciones por defecto. Una vez instalado el Node abrimos la consola para verificar que se ha instalado correctamente escribimos ::

		node -v

#. En la consola navegamos hasta nuestra carpeta *visor-bicing*. Con este comando estamos creando el archivo *package.json*. Este comando solicita varios elementos como, por ejemplo, el nombre y la versión de la aplicación. Por ahora, sólo hay que pulsar ENTER para aceptar los valores predeterminados. Una vez en la carpeta escribimos: ::

		npm init

#. Instalar las dependencias para crear nuestro servicio de proxy. En este caso utilizaremos Express [#]_ como servidor web y el módulo http-proxy [#]_ .

	#. Instalar el express y guardarlo en la lista de dependencias ::

			npm install express --save

	#. Instalar el http-proxy y guardarlo en la lista de dependencias ::

			npm install http-proxy --save

#. Al ejecutar estos comandos vemos que se nos crea una carpeta llamada *node_modules* que es donde se guardan los módulos que hemos instalado.

#. En nuestra carpeta creamos un archivo llamado *app.js* que contendrá nuestra aplicación que servirá de proxy con el servicio de la ACA. Para ello copiremos lo siguiente en este archivo. ::

		var express  = require('express');
		var app      = express();
		var httpProxy = require('http-proxy');
		var apiProxy = httpProxy.createProxyServer();
		var serverAca = 'http://aca-web.gencat.cat/sdim2/apirest/catalog';
		
		app.get('/', function(req, res){
			res.sendFile(__dirname + '/index.html');
		});

		app.all("/aca/*", function(req, res) {
		    console.log('redirecting to Server1');
		    apiProxy.web(req, res, {
		    	target: serverAca,
		    	changeOrigin: false, 
		    	ignorePath: true
		    });
		});

		app.listen(3000);
#. Para probar que nuestro proxy está funcionando vamos a la consola y escribimos: ::

		node app.js

#. Abrimos la url de nuestro proxy http://localhost:3000/aca/ en el navegador.
#. En el navegador escribimos http://localhost:3000 y debemos ver nuestro mapa.
#. Modificamos el archivo index.html para que llame al proxy que hemos creado. Para ello cambiamos la url de la capa geojsonLayer *http://aca-web.gencat.cat/sdim2/apirest/catalog* por nuestro proxy *http://localhost:3000/aca/* (como el proxy y la aplicación están en el mismo servidor podríamos usar */aca/*). Recargamos la aplicación con Ctrl+F5 y vemos que el error ha desaparecido.
#. Continuamos sin ver ningún dato en nuestro mapa. Esto es debido a lo que ya mencionamos que lo que retorna la API no es un GeoJSON. Pero lo tanto tenemos que convertir la respuesta de la API en un GeoJSON, para ello utilizaremos la opción *middleware* que ofrece la capa GeoJSON.AJAX. Esta opción nos permite crear un funcion donde podemos manipular los datos antes de agregarlos al mapa. 
#. Creamos una función que transforma los datos de Sentilo de la ACA en un GeoJSON. Al final de nuestro código escribimos ::

		function sentiloAca2geoJSON(data){
        	var geojson = {
        		type: "FeatureCollection",
        		features: []
        	};
        	var sensors = data.providers[0].sensors;
        	for (var i = sensors.length - 1; i >= 0; i--) {
        		var sensor = sensors[i];
        		var location = sensor.location.split(" ");
        		var feature = {
			        type: 'Feature',
			        properties: {
			            description: sensor.description,
			            id: sensor.component,
			            nom: sensor.componentDesc,
			            info: sensor.componentAdditionalInfo,
			            unit: sensor.unit
			        },
			        geometry: {
			            type: 'Point',
			            coordinates: [location[1], location[0]]
			        }
			    };
			    geojson.features.push(feature);
        	}
        	return geojson;
        }
#. Luego modficamos la capa geojsonLayer para que el middleware llame a nuestra función de transformación. Cambiamos el código de la capa por lo siguiente: ::

		var geojsonLayer = new L.GeoJSON.AJAX('http://localhost:3000/aca/', {
	    	middleware:function(data){
	    		return sentiloAca2geoJSON(data);
	        }
	    }).addTo(map);
#. Recargarmos el mapa y ahora debemos ver los puntos de los embalses en el mapa.
#. Para mostrar la información del punto vamos a agregar el evento click en cada unos de los puntos. Para ellos utilizaremos la opción de *onEachFeature* que ofrece las capa GeoJSON de Leaflet. Esta opción permite ejecutar una función en la creación de cada uno de los elementos de la capa. Es muy útil para agregar popups a los elementos o para agregar eventos en los elementos.
#. Creamos una función llamada *eachFeature* que recibe como parámetros un feature (elemento del GeoJSON) y un layer (elemento de Leaflet). Nuestra función sería la siguiente ::

		function eachFeature(f,l){
        	l.on('click',function(ev){
        		console.log(f);
        		console.log(l);
			});
		}
#. Recargamos el mapa y hacemos click sobre un elemento. En la consola de desarrollador debemos ver que nos aparecen 2 entradas una que corresponde al feature y otra al layer.
#. Ahora tendríamos que llamar a la API de la ACA para pedir la última lectura del sensor y así obtener la información. La url para obtener la última lectura es http://aca-web.gencat.cat/sentilo-catalog-web/component/map/EMBASSAMENT-EST.*.<id_sensor>*/lastOb/. Por ejemplo ::

		http://aca-web.gencat.cat/sentilo-catalog-web/component/map/EMBASSAMENT-EST.L25075-72-00003/lastOb/
#. Como estamos llamando una url que está fuera de nuestro dominio tenemos el mismo problema de CORS. Por lo tanto tendremos que modificar nuestro proxy. En nuestro archivo app.js escribimos justo debajo de la declaración de la variable *serverAca* ::

		var serverAcaLastOb = 'http://aca-web.gencat.cat/sentilo-catalog-web/component/map/EMBASSAMENT-EST.';
#. Ahora agregamos justo antes del app.listen el código que nos va a ser de proxy. Copiamos lo siguiente ::

		app.all("/acalast/:id", function(req, res){
			console.log('redirecting to Server2' + req.params.id);	
			apiProxy.web(req, res, {
		    	target: serverAcaLastOb+req.params.id+'/lastOb/',
		    	changeOrigin: false, 
		    	ignorePath: true
		    });
		});
#. Para reiniciar nuestro servidor de node vamos a la consola y presionamos Crtl+c. Luego volvemos a escribir node app.js.
#. En nuestro abrimos http://localhost:3000/acalast/L25075-72-00003 para comprovar que el proxy está funcionando correctamente.
#. Una vez tenemos el proxy que nos permite obtener la última lectura del sensor tendremos que modificar la función que se llama al hacer click sobre un elemento del mapa para que llame a nuestro proxy. Para hacer esta llamada ajax al proxy utilizaremos la librería JQuery [#]_. Para ellos escribiremos los siguiente justo debajo de donde cargarmos la librería de leaflet.ajax ::

		<script src="https://code.jquery.com/jquery-3.2.1.min.js" integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4=" crossorigin="anonymous"></script>
#. Modificamos la función *eachFeature* con el siguiente código ::

		function eachFeature(f,l){
        	l.on('click',function(ev){
        		var url = 'http://localhost:3000/acalast/'+f.properties.id;
        		$.ajax({
				  url: url,
				}).done(function(result) {
				  console.log(result);
				});
			});
		}
#. Recargamos el mapa y al hacer click sobre un elemento vemos que en la consola del desarrollador nos aparece un objeto que contiene la respuesa del sensor con la información de la última lectura.
#. Para mostrar esta información en el mapa creamos una función *popUp* que recibe como parámetros un layer de Leaflet y unos datos del sensor. Esta función muestra un popup asociado al elemento con la información del sensor. Despues de la función eachFeature debemos escrbir ::

		function popUp(l, data){
			var out = [];
			out.push('<strong>'+data.componentDesc+'</strong>');
		    if (data.sensorLastObservations){
		    	for (var i = data.sensorLastObservations.length - 1; i >= 0; i--) {
		    		var observ = data.sensorLastObservations[i];
		    		out.push(observ.sensorType+": "+observ.value+" "+observ.unit);
		    	}
		    }
		    l.unbindPopup();
		    l.bindPopup(out.join("<br />")).togglePopup();
		}
#. Dentro de la función que se llama en el *done* de la llamada ajax debemos llamar a la función popUp. Para ellos escribimos ::

		.done(function(result) {
		  console.log(result);
		  popUp(l, result);
		});
#. Recargamos la aplicación y al hacer click nos debe aparecer un popup con la información de la última lectura del sensor.

		.. |sentilo_aca| image:: _images/sentilo_aca.png
		  :align: middle
		  :alt: captura ejemplo sentilo ACA

		+---------------+
		| |sentilo_aca| |
		+---------------+


Referencias
###########

.. [#] http://aca-web.gencat.cat/aca/appmanager/aca/aca?_nfpb=true&_pageLabel=P56600137761453129970599
.. [#] http://www.sentilo.io/wordpress/
.. [#] http://aca-web.gencat.cat/sentilo-catalog-web/component/map
.. [#] http://aca-web.gencat.cat/aca/documents/Consulta_dades/dades_obertes_tempsreal/us_serveis_dades_API_REST.pdf
.. [#] http://leafletjs.com/
.. [#] https://github.com/calvinmetcalf/leaflet-ajax
.. [#] https://developer.mozilla.org/es/docs/Web/HTTP/Access_control_CORS
.. [#] https://es.wikipedia.org/wiki/Servidor_proxy
.. [#] https://nodejs.org/es/
.. [#] http://expressjs.com/
.. [#] https://github.com/nodejitsu/node-http-proxy
.. [#] https://jquery.com/
