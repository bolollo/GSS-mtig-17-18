****************************
Ejemplo Geoservicio realtime
****************************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Creación de un servicio realtime para compartir la ubicación
------------------------------------------------------------

Crearemos un servicio que permita compartir la ubicación de los usuarios y ver que usuarios están en linea. Para ello utilizaremos la librería Socket.io [#]_ que permite la comunicación en tiempo real en dos direcciones cliente-servidor (tipo pull) y servidor-cliente (tipo push). Esto lo hace gracias a un socket [#]_ .

Para mostrar los datos en el mapa utilizaremos la libreria Leaflet [#]_ . Para obtener la ubicación de los usuarios podemos usar el plugin *leaflet-locatecontrol* [#]_ en nuestro caso vamos en lugar de utilizar la ubicación del usuario vamos a simular la ubicación del usuario haciendo click sobre el mapa. 


Creación del mapa
-----------------

#. En nuestro ordenador creamos una carpeta con el nombre de *user-realtime*.
#. Dentro de esta carpeta creamos un archivo con el nombre de *index.html*.
#. Abrimos el archivo index.html con un editor de texto y copiamos el siguiente código. ::

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

#. Abrimos el archivo index.html en el navegador y vemos que se nos carga un mapa centrado en Barcelona.
#. Capturamos el click en el mapa. Para esto tenemos que capturar el evento click. Luego de la declaración de nuestra capa vamos a escribir los siguiente: ::

		map.on('click', function(e){
            console.log(e);
		});
#. Recargamos la aplicación y abrimos la consola del desarrollador, al hacer click sobre el mapa, en la consola aparece el objeto del evento click. Si inspeccionamos este objeto vemos que tiene una propiedad llamada latlng que contine las coordenadas donde se ha hecho el click.

Creación del servicio que comparte la ubicación de los usuarios
---------------------------------------------------------------

Para la creación del servicio utilizaremos Nodejs [#]_ para implementar nuestro servidor web y utilizaremos el módulo de socket.io para establecer la comunicación entre el cliente y nuestro servidor.

#. Instalación de Node. Nos descargamos la última versión LTS (en este momento es la 8.9.1 LTS) y lo instalamos con las opciones por defecto. Una vez instalado el Node abrimos la consola para verificar que se ha instalado correctamente escribimos ::

		node -v

#. En la consola navegamos hasta nuestra carpeta *user-realtime*. Con este comando estamos creando el archivo *package.json*. Este comando solicita varios elementos como, por ejemplo, el nombre y la versión de la aplicación. Por ahora, sólo hay que pulsar ENTER para aceptar los valores predeterminados. Una vez en la carpeta escribimos: ::

		npm init

#. Instalar las dependencias para crear nuestro servicio de proxy. En este caso utilizaremos Express [#]_ como servidor web y el módulo socket.io.

	#. Instalar el express y guardarlo en la lista de dependencias ::

			npm install express --save

	#. Instalar el socket.io y guardarlo en la lista de dependencias ::

			npm install socket.io --save

#. Al ejecutar estos comandos vemos que se nos crea una carpeta llamada *node_modules* que es donde se guardan los módulos que hemos instalado.

#. En nuestra carpeta creamos un archivo llamado *app.js* que contendrá nuestra aplicación que servirá de proxy con el servicio de Bicing. Para ello copiremos lo siguiente en este archivo. ::

		var express  = require('express');
		var app = express();
		var http = require('http').Server(app);
		var io = require('socket.io')(http);

		app.get('/', function(req, res){
			res.sendFile(__dirname + '/index.html');
		});

		io.on('connection', function(socket){
			console.log('a user connected');
		});

		http.listen(3000, function(){
		  console.log('listening on *:3000');
		});

#. Para probar que nuestro proxy está funcionando vamos a la consola y escribimos: ::

		node app.js

#. Abrimos la url de nuestro proxy http://localhost:3000/ en el navegador y vemos nuestro mapa. 

#. En nuestro archivo html agregamos la librería cliente de socket.io. Justo debajo de donde cargamos el leaflet escribimos ::

		<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.0.4/socket.io.js"></script>

#. Al inicio de nuestro código antes de la declaración del mapa declaramos nuestra variable que va a tener el objeto socket.io. Para ello escribimos los siguiente: ::

		var socket = io();

#. Ahora recarmos la página y podemos ver en la consola que nos aparece el mensaje de *a user connected*.

#. En la funcion que se llama al hacer click sobre el mapa enviaremos un evento al servidor, este evento lo llamaremos *user_click*. A este evento le pasaremos como parámetro la posición del click. Para ello copiamos lo siguiente en la función del on click. ::

		socket.emit('user', e.latlng); 

#. En nuestra aplicación del servidor tenemos que escuchar al evento *user_click*. Dentro de la función que se llama en el *io.on* es donde se crea el socket de conexión, por lo tando escribiremos nuestro código dentro de la misma. Debajo de donde escribimos el mensaje de *a user connected* escribimos lo siguiente: ::

		socket.on('user_click', function(msg){
   		 	console.log(msg);
  		});

#. Para reiniciar nuestro servidor de node vamos a la consola y presionamos Crtl+c. Luego volvemos a escribir node app.js.

#. Ahora recargamos la página y si hacemos click sobre el mapa vemos en la consola que nos aparecen las coordenadas del click. Con esto ya hemos logrado la comunicación cliente-servidor.   

#. Para lograr la comunicación servidor-cliente y que el servidor notifique a todos los cliente debemos emitir un evento en nuestro servidor. Este evento lo llamaremos *new_user*. Para emitir el evento dentro de la función que se llama en el evento *user_click* copiamos los siguiente. ::

		io.emit('new_user', msg);

#. En nuestro cliente ahora debemos escuchar el evento *new_user*. Al final de nuestro código del html escribimos ::

		socket.on('new_user', function(msg){
      		console.log(msg);
    	});

#. Volvemos a recargar el servidor y recargamos la página. Ahora al clicar sobre el mapa vemos las coordenadas del click tanto en el la consola del servidor como en la consola de desarrolladores del navegador.

#. Vamos a mostrar un marcador en el mapa en la posición donde el usuario hace click. En nuestro html en la función que escucha el evento *new_user* agregamos el siguiente código ::

		L.marker([msg.lat, msg.lng]).addTo(map);

#. Recargamos nuestra aplicación y abrimos otra pestaña con nuestra aplicación para simular que somos dos usuarios distintos. Si hacemos click en nuestro mapa en cualquiera de las pestañas vemos que nos aparece el marcador en ambas pestañas.


Referencias
###########

.. [#] https://socket.io/
.. [#] https://es.wikipedia.org/wiki/Socket_de_Internet
.. [#] http://leafletjs.com/
.. [#] https://github.com/domoritz/leaflet-locatecontrol
.. [#] https://nodejs.org/es/
.. [#] http://expressjs.com/
