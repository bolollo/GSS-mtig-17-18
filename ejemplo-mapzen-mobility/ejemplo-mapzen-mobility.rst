***********************
Ejemplo Mapzen Mobility
***********************

.. note::

	=================  ====================================================
	Fecha              Autores
	=================  ====================================================
	 8 Noviembre 2017    * Wladimir Szczerban
	=================  ====================================================

	©2017 Wladimir Szczerban

  Excepto donde quede reflejado de otra manera, la presente documentación se halla bajo licencia: Creative Commons (Creative Commons - Attribution - Share Alike: http://creativecommons.org/licenses/by-sa/3.0/deed.es)


Acceso a los servicios de Mapzen Mobility
-----------------------------------------

Mapzen Mobility [#]_ es un kit de herramientas para el transporte multimodal, está basado en software libre y usa datos abiertos (principalmente datos de OpenStreetMap). Consta de varias herramientas como son:
- Mapzen Turn-by-Turn: es un servicio de navegación para el mundo, ya sea en automóvil, bicicleta, pie y combinaciones multimodales que implican caminar y andar en transporte público.
- Mapzen Matrix: servicio que calcula la tabla de distancia y tiempo entre diferentes lugares.
- Mapzen Isochrone: servicio para obtener un cálculo de las áreas que se pueden alcanzar dentro de períodos de tiempo específicos desde una ubicación o un conjunto de ubicaciones.
- Mapzen Map Matching: servicio que permite convertir una ruta en una ruta con instrucciones narrativas. Esto lo hace observando las coordenadas que coincide con las coordenadas de las carreteras conocidas y así obtener los valores de los atributos de esa línea coincidente.

Otro servicio interesante de Mapzen es el servicio de búsqueda que permite geocodificar direcciones, o buscar puntos de interés. [#]_

Mapzen tiene una librería Mapzen JavaScript SDK [#]_ que está basada en Leaflet [#]_ que facilita el uso de los diferentes servicios de Mapzen. Aqui [#]_ se puede ver un ejemplo del cálculo de Isócronas utilizando la librería js de Mapzen.

En nuestro caso crearemos un visor utilizando Leaflet y llamando directamente al servicio de Mapzen.

Creación de un visor que permita el cáculo de Isócronas
-------------------------------------------------------

#. En nuestro ordenador creamos una carpeta con el nombre de *visor-port*.
#. Dentro de esta carpeta creamos un archivo con el nombre de *index.html*.
#. Abrimos el archivo index.html con un editor de texto y copiamos el siguiente código.::

		<!DOCTYPE html>
		<html>
		<head>
			<meta charset="UTF-8">
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <meta http-equiv="X-UA-Compatible" content="ie=edge">
			<title>Ejemplo Isócronas Mapzen</title>
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

		        map.setView([41.3887, 2.1777], 13);  

		        L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
		            attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
		        }).addTo(map);
			</script>
		</body>
		</html>
#. Abrimos el archivo index.html en el navegador y vemos que se nos carga un mapa centrado en Barcelona.
#. En la documentación de la API del servicio de Mapzen Isochrone [#]_ vemos que es necesario tener una API key para poder utilizarlo. Para crear una API key hay que darse de alta en la página de Mapzen [#]_, tiene una verisón gratuita que tiene unas cuotas de uso. Si se quiere hacer un uso más intesivo del servicio hay que pagar.
#. En nuestro código javascript crearemos una variable donde guardaremos nuestra API key. Antes de la declaración de nuestro mapa escribimos ::

		var API_KEY = 'TU_API_KEY';
#. Consultado la documentación observamos que la respuesta del servicio es un GeoJSON, para cargar este GeoJSON en nuestro mapa utilizaremos un plugin de Leaflet llamado *leaflet-ajax* [#]_. Este plugin nos permite hacer una llama AJAX a un servicio que retorne un JSON y cargar la respuesta en un mapa. Para cargar este plugin debemos agregar lo siguiente justo después de donde cargarmos el leaflet ::

		<script src="https://calvinmetcalf.github.io/leaflet-ajax/dist/leaflet.ajax.js"></script>
#. Para agregar una capa de Isócronas al mapa llamaremos a la API de Mapzen utilizando el plugin. Para ello al final de nuestro código agregamos lo siguiente: ::

		var geojsonLayer = new L.GeoJSON.AJAX('https://matrix.mapzen.com/isochrone?json={"locations":[{"lat":41.40024,"lon":2.180442}],"costing":"pedestrian","contours":[{"time":15,"color":"ff0000"}]}&api_key=TU_API_KEY').addTo(map);
#. Si recargamos el mapa vemos que nos aparece una línea azul que representa la isócrona del punto dado. En este caso el punto seleccionado es fijo y está escrito en nuestro código. 
#. Modificar nuestra aplicación para que se haga el cálculo de la Isócrona cuando el usuario haga click en un punto del mapa. Para esto primero tendremos que detectar el evento click en el mapa. Luego de donde declaramos la capa geojson escribimos ::

		map.on('click', function(e){
            console.log(e);
		});
#. Recargamos la aplicación y abrimos la consola del desarrollador, al hacer click sobre el mapa, en la consola aparece el objeto del evento click. Si inspeccionamos este objeto vemos que tiene una propiedad llamada latlng que contine las coordenadas donde se ha hecho el click.
#. Crearemos una función que tenga como parámetro una posición (coordenada lat lon) y nos genere una url de llamada al servicio de isócronas de Mapzen para que haga el cálculo en la coordenada indicada. Al final de nuestro código copiamos lo siguiente: ::

		function crearUrlIsochrona(latlng){
            var lat = latlng.lat;
            var lng = latlng.lng;
            var url = 'https://matrix.mapzen.com/isochrone?json=';
            var json = {
                locations: [{"lat":lat, "lon":lng}],
                costing: "pedestrian",
                contours: [{"time":15,"color":"ff0000"}]
            };
            url += escape(JSON.stringify(json));
            url+= '&api_key='+API_KEY;
            return url;
        }
#. Esta función la llamaremos cuando se hace click en el mapa. Al final de la función del click escribimos ::

		var url = crearUrlIsochrona(e.latlng);
		console.log(url);       
#. Recargamos la página hacemos click sobre el mapa y vemos que en la consola nos aparece una url. Si abrimos esta url en el navegador nos responde con un GeoJSON que contiene la isócrona.
#. Actualizamos la capa geojsonLayer con la nueva url, para actualizar la capa utilizaremos el metodo refresh. Debajo de donde declaramos la variable url escribimos ::

		geojsonLayer.refresh(url);
#. Refrescamos el mapa y al hacer click sobre el mapa vemos que se nos dibuja una nueva línea isócrona.
#. Modificamos la capa geojsonLayer para que se inicialice vacía sin ningún elemento. Para ello modificamos la declaración de la capa y borramos la url que llama a la API de Mapzen. Nos quedaría así: ::

		var geojsonLayer = new L.GeoJSON.AJAX('').addTo(map);
#. Vemos que por defecto nos pinta la línea de color azul a pesar de que en la llamada a la API le estamos diciendo que el color es rojo (ff0000). Esto es debido a que el Leaflet no sabe de que color pintar la línea y utiliza el color por defecto. En la respuesta del servicio podemos ver que los elementos que nos retorna tienen unas propiedades (properties) en donde se listan una serie de estilos, uno de ellos es el color que si corresponde con el rojo. Ahora lo que debemos hacer es decirle al leaflet que utilice esa propiedad para dar el color a la línea. Para ellos escribiremos lo siguiente en nuestra capa geojsonLayer. ::

		var geojsonLayer = new L.GeoJSON.AJAX('',{
            style: function(geoJsonFeature){
                return {color: geoJsonFeature.properties.color};
            }
        }).addTo(map);
#. Ahora si recargamos el mapa y hacemos click vemos que las líneas son de color rojo.
#. Hasta el momento pedíamos una isócrona de 15 minutos ahora añadiremos para que nos calcule también la isócrona de 30 minutos. Para ellos debemos modificar el parámetro *contours* de nuestra función. Le agregaremos otro objeto a la matriz de countours pero en este caso el tiempo (time) será de 30 y la línea será de color verde. Nos quedaría de la siguiente forma: ::

		contours: [{"time":15,"color":"ff0000"}, {"time":30,"color":"00ff00"}]
#. Recargamos el mapa y vemos que ahora al hacer click nos aparecen 2 isócronas.


Agregar un buscador de direcciones y puntos de interés al mapa
--------------------------------------------------------------

Para agregar un buscador utilizaremos el plugin de Leaflet *leaflet-geocoder* [#]_ desarrollado por Mapzen que permite de una forma fácil y rápida hacer llamadas al servicio de búsqueda de Mapzen.

#. Cargamos la librería en nuestra aplicación. Para ellos debemos copiar lo siguiente justo despúes de la carga del plugin de leaflet.ajax ::

		<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-geocoder-mapzen/1.9.4/leaflet-geocoder-mapzen.js"></script>
#. Agregamos el control al mapa. Para utilizar el servicio de búsqueda tambíen es necesario pasar nuestra API key. Antes de la declaraciṕn de nuestra función *crearUrlIsochrona* agregamos lo siguiente: ::

		var geocoder = L.control.geocoder(API_KEY).addTo(map);
#. Recargamos el mapa y vemos que nos aparece el control de búsqueda. Vemos que funciona pero que no tiene estilo, esto es debido a que no hemos cargado el estilo del control. Para esto debemos escribir lo siguiente justo debajo de donde cargamos el estilo de Leaflet ::

		<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet-geocoder-mapzen/1.9.4/leaflet-geocoder-mapzen.css">
#. Volvemos a recargar y ahora si nos aparece el control con estilo.
#. Calcular las isócronas al seleccionar un resultado de la búsqueda. Para ello utilizaremos el evento *select* del control geocoder ::

		geocoder.on('select', function (e) {
            console.log(e);
        });
#. Si refrescamos el mapa y abrimos la consola de desarrollados vemos que al seleccionar un resultado de la búsqueda no aparece un objeto en la consola. Si inspeccionamos este objeto vemos que tiene una propiedad latlng que es lo que necesitamos para calcular las isócronas.
#. En la función del evento select debemos llamar a nuestra función *crearUrlIsochrona* para generar la url y luego refrescar la capa de *geojsonLayer*, esto ya que hemos hecho cuando el usuario hace click en el mapa. Por lo tanto copiamos lo siguiente en la función ::

		var url = crearUrlIsochrona(e.latlng);
        geojsonLayer.refresh(url);
#. Refrescamos la página y al seleccionar un resultado de búsqueda nos calculá las isócronas desde ese punto.

		.. |isocrones| image:: _images/mapzen_isocrones.png
		  :align: middle
		  :alt: capturar mapa cálculo de isócronas.

		+-------------+
		| |isocrones| |
		+-------------+


Referencias
###########

.. [#] https://mapzen.com/documentation/mobility/
.. [#] https://mapzen.com/products/search/geocoding/
.. [#] https://mapzen.com/documentation/mapzen-js/
.. [#] http://leafletjs.com/
.. [#] https://bl.ocks.org/rfriberg/38694be3e8ffb30ac6ac8302960c7ebd
.. [#] https://mapzen.com/documentation/mobility/isochrone/api-reference/
.. [#] https://mapzen.com/developers/sign_up
.. [#] https://github.com/calvinmetcalf/leaflet-ajax
.. [#] https://github.com/mapzen/leaflet-geocoder
