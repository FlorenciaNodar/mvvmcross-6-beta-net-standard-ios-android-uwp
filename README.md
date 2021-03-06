# mvvmcross-6-beta-net-standard-ios-android-uwp
Código de ejemplo de la charla del día 21/03/2018

<h1>Tutorial de como crear una aplicación con Xamarin Classic para IOS , Android y UWP usando las vistas nativas.</h1>

<p>En este tutorial vamos a crear un proyecto desde 0 para UWP, IOS y Android usando MvvmCross 6 beta, que hace uso de Net Standard.</p>

<p>Como vamos a crear para UWP, lo haremos desde Windows y Visual Studio 2017, en mi caso estoy usando la última* versión la 15.6.0 </p>


<p><strong>Observación:</strong></p>
<p>Xamarin Classic (native) es más complejo que Xamarin Forms, requiere tener conocimiento sobre todas las plataformas, explicaremos de la forma más detallada posible.</p>



<h1>¡Empezamos!</h1>

<h2>Paso 1: Crear una solución vacía:</h2>

<p>Abrimos visual studio 2017, en el menú superior seleccionamos => Nuevo => Proyecto. </p>

<p>Elegimos la opción “Otros tipos de proyectos”, y creamos un proyecto solución en blanco. </p>


<img src="/Img/1.PNG"/> 

<h2>Paso 2: Agregar los proyectos para las plataformas y uno para el Core</h2>

<p> Seleccionamos con el 2º botón la solución vacía y elegimos, <strong>agregar</strong> => <strong>nuevo proyecto.</strong> </p>

<img src="/Img/2.png"/> 

<p>Debemos agregar 4 proyectos: </p>
<ul>
	<li>Core</li>
	<li>Android</li>
	<li>IOS</li>
	<li>UWP</li>
</ul>

	
<p>*Todos los proyectos de plataforma deben tener el proyecto de Core como referencia.</p>

<h3>Agregando el proyecto de Core:</h3>


<img src="/Img/3.PNG"/> 

<h3>Agregando el proyecto de IOS:</h3>

<img src="/Img/4.PNG"/> 

<h3>Agregando el proyecto de Android:</h3>

<img src="/Img/5.PNG"/> 

<h3>Agregando el proyecto de UWP:</h3>

<img src="/Img/6.PNG"/> 


<p>No todas las versiones de UWP son compatible con Net Standard, se debe tener como mínimo la versión “Fall Creators”.</p>

<img src="/Img/7.PNG"/> 
<p>Se puede ver más detalles aquí: <a href="https://blogs.msdn.microsoft.com/dotnet/2017/10/10/announcing-uwp-support-for-net-standard-2-0/">enlace a microsoft</a></p>


<p>Una vez creado todos los proyectos debemos agregar la referencia al proyecto de Core, para esto tenemos que seleccionar con el 2º botón sobre referencia, en el menú que se muestra elegir la opción “<strong>agregar referencia</strong>”, dentro de solución se debe seleccionar el proyecto <strong>Classic.Core</strong> y hacer clic en el botón <strong>aceptar</strong>.</p>

<img src="/Img/8.PNG"/> 

<p>Debemos realizar la misma operación para Android y IOS. El resultado final debe ser similar a este:</p>

<img src="/Img/9.PNG"/> 

<h2>Paso 3: Agregar MvvmCross 6.0.5 (beta)</h2>

<p>Al ser una versión de beta, el paquete que configura casi todo de forma automática no existe en nuget. Vamos a tener que configurar de forma manual.</p>

<p>Pero de cara a futuro, revise si el paquete MvvmCross.StarterPack se encuentra disponible para la versión 6. </p>

<img src="/Img/10.PNG"/>

<p>Para instalar MvvmCross debemos seleccionar la solución con el 2º botón y elegir la opción “administrar paquetes de nuget para la solución”, el siguiente paso es buscar por MvvmCross con la opción “incluir versión preliminar” habilitada. </p>


<img src="/Img/11.PNG"/>

<p>Una vez elegido la versión de MvvmCross 6.0.5, debemos indicar que queremos instalar en todos los proyectos de nuestra solución. El último paso es hacer clic en instalar. </p>

<img src="/Img/12.PNG"/>

<p>Aceptamos y toca esperar que finalice la instalación.</p>



<h2>Paso 4: Configurar MvvmCross en Core</h2>


<p>Antes de empezar a configurar debemos crear las siguientes carpetas dentro del proyecto Core:</p>
<ul>
<li>Constants</li>
<li>Converters
<ul>
<li>Json</li>
</ul>
</li>
<li>Models
<ul>
<li>Base</li>
<li>Movie</li>
</ul>
</li>
<li>Services
<ul>
<li>Connectores</li>
<li>WebServices</li>
</ul>
</li>
<li>ViewModels
<ul>
<li>Base</li>
</ul>
</li>
</ul>

<h3>Paso 4.1 Agregar paquetes nuget</h3>

<p>Nuestra aplicación usará la Api de Themoviedb para esto es necesario crear una cuenta en esta página: https://www.themoviedb.org y obtener la key para consumir datos de la API.</p>

<p>El siguiente paso es instalar el paquete Newtosoft.Json en el proyecto Core que será el responsable de deserializar el json del servicio.</p>

<img src="/Img/13.PNG"/>

<p>El último paquete que vamos a instalar en el proyecto de Core, solo tiene sentido si vamos a dar soporte a versiones inferior a 5 de Android, sirve para trabajar con el httpClient nativo de cada plataforma mejorando los tiempos de respuesta. *Se puede hacer lo mismo configurando el httpClient desde el proyecto de plataforma.</p>

<p>ModernhttpClient , se debe instalar en todos los proyectos. </p>

<img src="/Img/14.PNG"/>


<h3>Paso 4.2 : Agregar el archivo de configuración</h3>

<p>Dentro de la carpeta Constants, vamos a crear una clase con el siguiente nombre ConfigConstants. Usaremos este archivo para guardar la configuración de la aplicación, se puede ver el código aquí: </p>

<p><a target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/Constants/ConfigConstants.cs">[Código]</a></p>

<h3> 4.3: Crear los modelos</h3>

<p>Vamos a crear 3 modelos:</p>

<ul>
	<li>BaseResponse: Nos ayudará a comunicar si la petición al servicio salió bien o no.</li>
<li>	MoviResponse y ResultMovie: El resultado de las últimas películas de la API. Para agilizar el proceso, copie la respuesta del JSON en esta página: http://json2csharp.com , ella genera todas las propiedades de forma automática. </li>


<p><a target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Img/servicios.json">[Json del servicio]</a></p>

<p><a target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/Models/Base/BaseResponse.cs" target="_black">[Código BaseResponse]</a></p>

<p><a target="_blank"  href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/Models/Movie/MovieResponse.cs">[Código MoviResponse]</a></p>
<p><a target="_blank"  href="https://github.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/blob/master/Classic/Classic.Core/Models/Movie/ResultMovie.cs">[Código ResultMovie]</a></p>

<h3>Paso 4.4: Crear los Servicios</h3>

<p>Vamos a crear una interface para registrar el conector httpclient de la forma más simples posible.</p>

<p><a target="_blank"  href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/Services/Connectors/IWebClientService.cs">[Código de la interfaz]</a></p>

<p><a target="_blank"  href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/Services/Connectors/WebClientService.cs">[Código de la implementación]]</a></p>


<p>Creamos una nueva interfaz para agregar todos los servicios de la API de Themoviedb.</p>

<p><a  target="_blank"  href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/Services/WebServices/IMovieWebService.cs">[Código de la interfaz]</a></p>
<p><a  target="_blank"  href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/Services/WebServices/MovieWebService.cs">[Código de la implementación]</a></p>



En breve explicaré como usar.


<h3>Paso 4.5: Crear los ViewModels</h3>

<p>Dentro de la carpeta ViewModels => Base, vamos a crear el archivo BaseViewModel. Todas las propiedades comunes de los viewmodels van en este archivo.</p>


<p><a  target="_blank"  href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/ViewModels/Base/BaseViewModel.cs">[Código aquí]</a></p>

<p>En BaseViewModel podemos destacar 3 elementos: </p>

<ul>
<li>Se herenda de MvxViewModel&lt;object&gt;</li>
<li>Los servicios se inicializa por el constructor</li>
<li>Contiene 2 propiedades
<ul>
<li>IsBusy para indicar la carga</li>
<li>Title para pintar el titulo de la pantalla.</li>
</ul>
</li>
</ul>


<p>En la carpeta ViewModels, vamos agregar nuestra p&aacute;gina principal: HomeViewModel</p>
<p>En este archivo podemos destacar:</p>
<ul>
<li>Herenda de BaseViewModel</li>
<li>Al cargar la p&aacute;gina (ViewAppeared), se llama el servicio y el resultado se agrega en una propiedad publica que notifica la vista.</li>
</ul>
<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/ViewModels/HomeViewModel.cs">[Código aquí]</a></p>
<p>&nbsp;</p>

<h3>Paso 4.6: Agregar el arranque de la aplicación</h3>

<p>Debemos crear el archivo App.cs, este archivo es el responsable por arrancar la aplicación en el viewmodel indicado, aparte de registrar los servicios.</p>

<p>Todos los servicios terminados por service, se registra de forma automática por MvvmCross. Este es el caso de nuestros 2 servicios (IWebClientService y IMovieWebService). </p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Core/App.cs">[Código aquí]</a></p>


<h2>Paso 5: Configurar UWP</h2>

<p>Lo primero que debemos crear es un archivo Setup.cs dentro del proyecto Classic.UWP</p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/Setup.cs">[Código aquí]</a></p>

<p>Este archivo es el responsable por arrancar MvvmCross. </p>

<p>El siguiente paso es eliminar el archivo MainPage.Xaml, debemos crear páginas que herende directamente de MvvmCross.</p>

<p>El próximo para es una carpeta con el nombre de Views, agregaremos nuestras páginas en esta carpeta.</p>

<p>Modificar el archivo App.xaml.cs, debemos agregar la configuración de MvvmCross para controla la navegación.</p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/App.xaml.cs">[Código aquí]</a></p>

<p>Haga clic con el 2º botón sobre la carpeta Views, elija agregar nuevo elemento. Tenemos que crear una página vacía.</p>

<img src="/Img/20.PNG"/>

<p>Una vez creado debemos agregar modificar la herencia del fichero, las páginas que usan MvvmCross debe heredar de <strong>views:MvxWindowsPage</strong></p>

<img src="/Img/21.PNG"/>


<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/Views/HomeView.xaml">[Código aquí Xaml]</a></p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/Views/HomeView.xaml">[Código aquí Xaml]</a></p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/Views/HomeView.xaml">[Código aquí .CS]</a></p>

<p>Para usar el cargando, debemos crear un converte que indique cuando mostrar o ocultar el <strong>ProgressBar</strong></p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/Converters/BooleanToVisibilityConverter.cs">[Código aquí .CS]</a></p>

<p> El último paso será crear la vista AboutView, los pasos a seguir son los mismos que para la vista HomeView</p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/Views/AboutView.xaml">[Código aquí Xaml]</a></p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.UWP/Views/AboutView.xaml.cs">[Código aquí .CS]</a></p>


<h2>Paso 5: Configurar Android</h2>

<p>Lo primero que debemos hacer es eliminar el archivo <strong>MainActivity.cs</strong> que se crea de forma automática al crear un proyecto de Xamarin Android.</p>

<p>El siguiente paso es crear el archivo responsable de arrancar MvvmCross. Dentro del proyecto Android debemos crear el archivo Setup.cs</p>

<img src="/Img/30.PNG"/>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Droid/Setup.cs">[Código aquí]</a></p>


<p>El siguiente paso es agregar los siguientes paquetes nuget:</p>

<ul>
	<li>MvvmCross.Droid.Support.V7.RecyclerView, usaremos en los listados.</li>
</ul>

<img src="/Img/17.PNG"/>

<ul>
	<li>Xamarin.FFImageLoading, usaremos para trabajar con las imagenes.</li>
</ul>

<img src="/Img/18.PNG"/>

<ul>
	<li>NewtosoftJson - MvvmCross en android necesita de este paquete</li>
</ul>

<img src="/Img/15.PNG"/>



<p>Una vez instalado todos los paquetes, debemos agregar como referencia <strong>Mono.android.Export</strong></p>

<img src="/Img/16.PNG"/>


<p> El siguiente paso es crear la vista, vamos a crear un carpeta con el nombre <strong>Views</strong> y agregaremos muestas vistas ahí dentro. Abajo captura con todas las vistas creadas.</p>

<img src="/Img/31.PNG"/>


<p>Para crear la vista hacemos clic sobre la carpeta Views y elegimos la opción agregar nuevo elemento.</p>

<img src="/Img/32.PNG"/>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Droid/Views/HomeView.cs">[Código de la vista .cs]</a></p>


<p>La vista va asociado a un archivo AXML que debemos crear, dentro de la carpeta Resources => layout debemos hacer clic con el 2 botón sobre la carpeta y debemos elegir la opción  agregar nuevo elemento.</p>

<img src="/Img/33.PNG"/>


<p>En nuestra vista va a contener 2 elementos principales, un elemento de lista y un progressBar para indicar sí el servicio está cargando. La lista hace referencia a un template que debemos crear.</p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Droid/Resources/layout/HomeView.axml">[Código de la vista AXML]</a></p>

<p>Vamos ahora a crear el template de la lista, el proceso es el mismo que el anterior, dentro de la carpeta Resources => layout debemos hacer clic con el 2 botón sobre la carpeta y debemos elegir la opción  agregar nuevo elemento. </p>

<img src="/Img/34.PNG"/>

<p> En este template, vamos a pintar los valores que queremos pintar por cada ítem de la lista. En este caso he puesto la imagen y el título de la película. Fijate que estoy usando el plugin de la imagen que hemos instalado antes.</p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Droid/Resources/layout/ItemList.axml">[Código del TEMPLATE AXML]</a></p>


<p>Ahora falta crear la vista del detalle, es muy similar a los pasos anteriores, se puede ver el código abajo.</p>


<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Droid/Views/AboutView.cs">[Código vista About.cs]</a></p>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.Droid/Resources/layout/AboutView.axml">[Código vista AboutView.axml]</a></p>




<h2>Paso 6: Configurar iOS</h2>

<p>Abrimos el AppDelegate, este es el punto de partida de la Aplicación en iOS, aqui controlamos el ciclo de vida de la Aplicación, el proposito es trabajar sin los famosos Storyboards y hacer una aplicacion mas escalable y con un mejor control del flujo</p>

<img src="/Img/ImgAppDelegate.png"/> 

<h2>Paso 2: Agregar las vistas que pintarán los datos segun el modelo de datos</h2>

<p>En este paso creamos dos vistas como patron de diseño Master - Detail, para poder realizar las vista controladores debemos crear seleccionar un nuevo archivo en la carpeta en donde estarán todas nuestras vistas(Views), en la seleccion de el tipo de interfaz que necesitamos buscamos una Vista controlador, esta clase nos creará tanto el .XIB como su relación, para más adelante poder tener acceso a las distintas instancias de los objetos declarados.</p>

<img src="/Img/ImgNuevoArchivo.png"/> 
<br></br>
<img src="/Img/ImgSeleccionVC.png"/> 


<h2>Paso 3: Creación de los objetos en la Vista</h2>

<p>En este paso tenemos que incorporar en la vista los objetos que posteriormente se encargarán de pintar lo que el Modelo de datos nos indique, en este paso es necesario abrir XCode, este IDE de Apple sólo nos servirá para colocar los objetos dentro de la vista y los "constrains" que permitirá que se ajusten a cualquier dispositivo de Apple.</p>

<p>En las imagenes vais a ver que se abre un proyecto de Classic.IOS, no se debe tocar nada de eso, solo y exclusivamente las vistas.</p>

<img src="/Img/ImgSeleccionXCODE.png"/> 
<br></br>
<img src="/Img/ImgVistaMaster.png"/> 
<br></br>
<img src="/Img/ImgVistaDetalle.png"/> 

<h2>Paso 4: Creación de las instancias de los objetos con los que el controlador podrá de manera Reactiva informar a las subVistas que deben cambiar o modificarse</h2>

<p>En este paso, debemos abrir ahora el .XIB en Visual Studio, y alli, observar el esquema de documento, seleccionamos el objeto al que le crearemos la instancia para poder trabajar con él</p>


<img src="/Img/ImgInstanciaUno.png"/> 
<br></br>
<img src="/Img/ImgInstanciaDos.png"/> 
<br></br> 

<p>Con el objeto seleccionado nos vamos a la paleta de Propiedades y seleccionamos la propiedad <b>"Name"</b>, alli creamos la conexión del objeto, en otras palabras es el <b>"IBOutlet"</b> nativo de iOS.</p>
<br></br>
<img src="/Img/ImgInstanciaCuatro.png"/> 
<br></br>

<p>Desplegamos el fichero .designer.cs que se ha creado con nuestra Vista controlador y observamos que de manera automatica se han creado los <b>"IBOutlets"</b></p>
<img src="/Img/ImgInstanciaCinco.png"/> 

<h2>Paso 5: Implementación de MVVM en la vista</h2>

<p>Creamos la tabla de manera dinamica para proveerle de datos</p>

<img src="/Img/ImgTableViewSource.png"/> 
<br></br>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.IOS/DataSource/SimpleDynamicTableViewSource.cs">[Código aquí SimpleDynamicTableViewSource]</a></p>

<p>Creamos la celda que esta dentro de la tabla y que pintará los datos de cada uno de los objetos del array</p>

<img src="/Img/ImgCustomView.png"/> 
<br></br>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.IOS/Views/Cell/CustomCell.cs">[Código aquí CustomCell]</a></p>


<p>Abrimos la clase HomeView.cs y escribimos el siguiente codigo</p>

<img src="/Img/ImgHomeView.png"/> 
<br></br>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.IOS/Views/HomeView.cs">[Código aquí HomeView]</a></p>

<p>Abrimos la clase AboutView.cs y escribimos el siguiente codigo</p>

<img src="/Img/ImgAboutView.png"/> 
<br></br>

<p><a  target="_blank" href="https://raw.githubusercontent.com/elbrinner/mvvmcross-6-beta-net-standard-ios-android-uwp/master/Classic/Classic.IOS/Views/AboutView.cs">[Código aquí AboutView]</a></p>
