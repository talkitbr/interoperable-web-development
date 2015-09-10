Atualizando seu site e corrigindo problemas
===========================================
Quando desenvolvemos para a Web Moderna, é inevitável que alguns recursos e propriedades que desejamos utilizar não funcionem em browsers antigos. Para contornar este problema, temos que adotar formas de atender a browsers antigos priorizando a funcionalidade. Neste caso importa para o usuário conseguir usar a funcionalidade.

Neste contexto, existem algumas propriedades que você pode abrir mão e recursos que você pode substituir por outros que permitem ainda assim que seu site funcione. E a essa estratégia da-se o nome de *Graceful Degradation*. 

Neste minicurso veremos como atualizar nosso site para suportar browsers antigos e ainda estar aderente a Web Moderna.

Este minicurso inclui:
1. [Recursos JavaScript](#Task1)
1. [Imagens SVG](#Task2)
1. [Imagens @2X](#Task3)
1. [Media Queries](#Task4)
1. [Propriedades CSS3](#Task5)
1. [Elementos HTML5](#Task6)

Neste minicurso iremos usar ténicas vistas nos minicursos anteriores, como o de [Feature Detection](../feature-detection) e [Prefixos CSS](../css-prefix) para corrigir problemas que identificamos ao [testar nosso site em diferentes browsers](../testing).

Para esta tarefa iremos usar a Ferramenta do Desenvolvedor (F12) do Internet Explorer para emular versões anteriores do IE, como o IE8. Seguem os passos para isso:

1. Abrir o projeto [Contoso Industries](code/begin) no **NetBeans**
1. Executar o projeto e abrir a página inicial no Internet Explroer.
1. Abrir a Ferramenta do Desenvolvedor pressionando a tecla F12.
1. Abrir a aba do Emulador (Emulation) e modar o **Document mode** para **8**.

	
	![Mudando o Document Mode para IE8](images/update_setrendermodeie8.png)

	_Mudando o Document Mode para IE8_

1. O site será recarregado no emulando o IE8. 

	> Não feche a Ferramenta do Desenvolvedor. Ao fazê-lo, essa configuração será desfeita e o site volatrá a ser exibido no Document Mode padrão.

<p name="Task1" />
#### Recursos Javascript ####
Primeiramente vamos procurar problemas relativos ao Javascript e HTML5 que não são suportados pelo IE8.

Ao executarmos a página inicial, já iremos nos deparar com um primeiro problema de visualização no IE8: o uso do 

> A função attachEvent era usada para definir código a ser executado quando ocorrer um determinado evento. O attachEvent, tornou-se obsoleto e foi totalmente removido do IE11. Além disso ele não existe em nenhum dos browsers modernos, como Microsoft Edge e Google Chrome.  As you can see, the error surfaces now that the app is running in Edge.

Após o passo 1, se executarmos nosso site iremos observar que ocorre um erro Javascript (visualizado através da ferramenta do desenvolvedor F12) relacionado com o uso da função `attachEvent`:

![Erro usando função attachEvent](./images/featuredetection_attacheevent_error.png)

> Esse é um exemplo clássico de problema Javascript quando estamos atualizando sites. A função `attachEvent` foi substituida pela função `addEventListener`. Mas é muito comum encontrá-la nas páginas Web que foram desenvolvidas para versões anteriores do Internet Explorer. Quando usamos um browser moderno ou a nova versão do IE11, o código Javascript simplesmente falha ao tentar executar essa função.

Para corrigir, devemos alterar o código Javascript para verificar se a função attachEvent está disponível. Na página `index.html`, vamos alterar o código da tag script localizado no final da tag `div` com id `mainContent`:

	<script>
		if (window.attachEvent) {
            window.attachEvent("onload", function () {
                setTimeout(function () {
                    jwplayer().play(true);
                }, 200);
            });
        }
        else {
            window.addEventListener("load", function () {
                setTimeout(function () {
                    jwplayer().play(true);
                }, 200);
            });
        }
	</script>

Dessa forma, conseguiremos obter o resultado esperado em todos os casos, mesmo quando o usuário estiver usando um browser anterior do Internet Explorer que implementa somente a função `attachEvent`.

Para testar numa versão anterior do IE, vamos fazer o seguinte:

1. Abrir o Internet Explorer
2. Acessar nosso site
3. Abrir a ferramenta do desenvolvedor
4. Acessar a aba Emulation
5. Selecionar o Document Mode IE9, por exemplo.
6. Na aba Debugger, colocar um ponto de parada dentro do bloco condicional que verifica a existência da função `attachEvent`:

![Erro usando função attachEvent](./images/featuredetection_attacheevent_running_ie9.png)

> Observe que o código de `attachEvent` foi executado quando definimos o Document Mode para IE9.

Ainda em relação ao addEventListener, temos outros trechos de código que são usam ele e não o `attachEvent`. E isso pode ser também um problema pois o usuário pode estar usando um browser antigo. Neste caso podemos verificar se a função addEventListener existe antes de utilizarmos ela. Por exemplo:  

	<script>
		if (window.addEventListener)
	        window.addEventListener("mobileinit", function () {
	            $.mobile.ajaxEnabled = false;
	        });
		}
    </script>

> O evento `mobileinit` é disparado a partir de navegadores web de smartphones e estes não possuem versões anteriores de navegadores Web, como o IE8. Portanto, nesse caso não precisamos considerar o uso da função `attachEvent`.

Agora vamos rever as demais páginas do site localizando trechos de código que usam a função `addEventListener` para incluir a alteração acima.


1. Press **F5** to start debugging and when the website is launched in IE, set IE to emulate IE8 (press **F12** to open the Developer tools, then **Ctrl+8** to change to the Emulator tab, then change the **Document mode** to **8**).

	The home page will reload in the browser now emulating IE8. Before the Home page loads completely you will get an error message dialog box in Visual Studio. This is one of the errors you saw when running the website in BrowserStack, only this time you are running the debugger and are prompted to review it first.

	![AddEventListener Error in IE8](images/addeventlistener-error-in-ie8.png?raw=true)

	_addEventListener Error Message in IE8_

1. Click **Continue**.

	![Second AddEventListener Error in IE8](images/second-addeventlistener-error-in-ie8.png?raw=true)

	_Second addEventListener Error in IE8_ 
	
1. Click **Continue**. 

	Notice that IE finishes loading the page but does not display the video.

	![Website Loaded in IE8 with errors](images/website-loaded-in-ie8-with-errors.png?raw=true)

	_Website loaded in IE8 with errors_

1. Stop debugging.

1. Open **Index.cshtml** (in the **Views\Home** folder) and find the `script` section that contains the following:

	````JavaScript
	<script>
		  window.addEventListener("load", function () {
				var player = document.getElementById("promoVideo");
				player.play();
		  });
	</script>
	````

	This code was displayed in the first error encountered. It is failing in IE8 because _addEventListener_ is not supported, as it was introduced into a later version of JavaScript than the one supported . Looking at what the code does you see that it is causing the video to start playing. A bit of investigation around the html definition of the video (up in the Index.cshtml file) indicates the `video` tag is used to display it. Further investigation online determines that the `video` tag, a newly introduced element in HTML5, is not supported in IE8 (see [the W3Schools page for video](http://www.w3schools.com/tags/tag_video.asp)), so it will need to be replaced in order for the video to play in IE8. You will learn more about other HTML5 elements in an upcoming [section of this lab](#Task2.6).

	In the next steps you will fix both issues by:
	* augmenting the code in which _addEventListener_ is used by checking whether _addEventListener_ exists first. If it doesn't, _attachEvent_ will be used. 
	* providing a fallback for the `video` tag by adding back the code to use _jwplayer_. This was the original way in which the video was displayed, and it was updated in Task 3 in the [Best Practices lab](../best-practices#Task3). Now you will reintroduce this code but apply it only to the case of IE8.

1. Update **Index.cshtml** so that this section of the code looks as follows:
	<!-- mark:2,7-11 -->
	````JavaScript
	<script>
		 if (window.addEventListener) {
			  window.addEventListener("load", function () {
					var player = document.getElementById("promoVideo");
					player.play();
			  });
		 } else if (window.attachEvent) {
			  window.attachEvent("onload", function () {
					setTimeout(function () { jwplayer().play(true); }, 500);
			  });
		 }
	</script>
	````

	This fixes one of the places where _addEventListener_ was used without fallback for IE8. Notice the fallback code uses the _jwplayer_ object, which will be added shortly. First you will fix the other place where _addEventListener_ was used without fallback for IE8.

1. Open **_Layout.cshtml** (in the **Views\Shared** folder) and find the script section that contains the following:

	````JavaScript
	  <script>
		 window.addEventListener("mobileinit", function () {
			  $.mobile.ajaxEnabled = false;
		 });
	  </script>
	````

1. Update the code so that it looks as follows:
	<!-- mark:2,6-11 -->
	````JavaScript
        <script>
            if (window.addEventListener) {
                window.addEventListener("mobileinit", function () {
                    $.mobile.ajaxEnabled = false;
                });
            } else if (window.attachEvent) {
                window.attachEvent("mobileinit", function () {
                    $.mobile.ajaxEnabled = false;
                });
            }
        </script>
	````

	Again, you have added a query to check whether _addEventListener_ is supported, and if not, used the older _attachEvent_ method to register the event handler function.

	Now that you have fixed all places where _addEventListener_ was being used without fallback for IE8, it's time to provide a fallback for the video tag. You will do so before testing the app because it won't run without this fix.

1. Open **Index.cshtml** again and find the `video` tag. Update it so that it looks as follows:
	<!-- mark:2 -->
	````HTML
		 <video id="promoVideo" width="100%" controls src="http://wams.edgesuite.net/media/SintelTrailer_MP4_from_WAME/sintel_trailer-1080p_3400.mp4">
			  <div id="myElement">Loading the player...</div>
		 </video>

	````
	This provides a fallback object (the div with id=_myElement_) that will be displayed if the `video` tag is not available. Now you will add the definition for the _jwplayer_ object and include other necessary files.

1. Copy and paste the following code in **Index.cshtml**, right before the first `script` tag already defined:

	````JavaScript
	<script type="text/javascript">
		 if (!window.addEventListener) {
			  jwplayer("myElement").setup({
					file: "http://wams.edgesuite.net/media/SintelTrailer_MP4_from_WAME/sintel_trailer-1080p_3400.mp4",
					width: "100%",
					aspectratio: "16:9",
					primary: "flash"
			  });
		 }
	</script>
	````
1. Open the **_Layout.cshtml** file and add the **Render** statement for the flashplayer inside the **head** tag. This is shown in the following code:

	<!-- mark:10 -->
	````JavaScript
    <head>
        <meta charset="utf-8" />
        <title>@ViewBag.Title</title>
        <meta name="viewport" content="width=device-width" />

        <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
        <link href='http://fonts.googleapis.com/css?family=Lobster' rel='stylesheet' type='text/css'>
        @Styles.Render("~/Content/mobileCss", "~/Content/css")
        @Scripts.Render("~/bundles/modernizr")
        @Scripts.Render("~/bundles/flashplayer")
    </head>
	````

1. Open the **BundleConfig.cs** file located in the **App_Start** folder and add the definition of the flashplayer script bundle, shown in the following code:

	````C#
	bundles.Add(new ScriptBundle("~/bundles/flashplayer").Include(
            "~/Scripts/jwplayer/jwplayer.js"));
	````

1. Press **F5** to start debugging. Again, after IE is launched, change IE to emulate IE8 (press **F12** to open the Developer Tools and then **Ctrl+8** to switch to the Emulator tab, where you will change the **Document mode** to **8**).

	The website will reload, emulating IE8. Notice that now there are no errors thrown, and after a few of seconds the video starts playing. 

	![Website displays in IE8 with no loading errors](images/website-displays-in-ie8-with-no-loading-error.png?raw=true)

	_Website displays in IE8 without loading errors_

1. Play around with the page, and click the movie title to display the synopsis.
 
	Notice you will get an error since the [classList property](https://developer.mozilla.org/en-US/docs/Web/API/element.classList) is not available in IE8 (or IE9). As it stands, css animations are not supported either. 

	![Error message when clicking the movie title](images/error-message-when-clicking-the-movie-title.png?raw=true)
	_Error Message when clicking the movie title_

	You will now update the code to use _Feature Detection_, as you did in the [Feature Detection lab](../edge-mode-and-feature-detection). Additionally, as part of graceful degradation, the fix will arrive to the same end result (semi-transparent title and synopsis displayed) but without the nice slideOut animation available in IE8 and without using the _classList_ property.

1. Dismiss the error message dialog box and stop debugging.

1. Open **Index.cshtml** and scroll until you find a script tag with the same code displayed at the error point:

	````JavaScript
	<script>
		 var wrapper = document.querySelector(".wrapper");
		  wrapper.onclick = function () {
				wrapper.classList.toggle("animation");
		  };
	</script>
	````

1. Update the code so it looks as follows:
	<!-- mark:3-4,7-20 -->
	````JavaScript
	<script>
		 var wrapper = document.querySelector(".wrapper");
		 if (Modernizr.cssanimations) {
			  wrapper.onclick = function () {
					wrapper.classList.toggle("animation");
			  };
		 } else {
			  wrapper.onclick = function () {
					var synopsis = document.getElementById("synopsis");
					var title = document.getElementById("movietitle");
					if (synopsis.style.display != "block") {
						 synopsis.style.left = "0px";
						 synopsis.style.display = "block";
						 title.style.filter = "progid:DXImageTransform.Microsoft.Alpha(Opacity=10)";
					} else {
						 synopsis.style.display = "none";
						 title.style.filter = "progid:DXImageTransform.Microsoft.Alpha(Opacity=100)";
					}
			  };
		 }
	</script>
	````

	The code is now using _Modernizr.cssanimations_ to determine whether animations are available. In the _else_ block there is an alternate code for the _onclick_ event handler function. This alternative function manually displays the synopsis by setting the display mode and location and sets the opacity for the title using the filter property (the way it was done in IE8).

1. Press **F5** to start debugging. Again, after IE is launched, change IE to emulate IE8 (press **F12** to open the Developer Tools and then **Ctrl+8** to switch to the Emulator tab, where you will change the **Document mode** to **8**).

	The website will reload, emulating IE8. Notice that now when you click the movie title header no errors are thrown: the opacity of the title changes and the synopsis is displayed. If you click again, the synopsis is hidden and the title is shown in black. In short, the same end result is achieved albeit without the animation. 

	![Website running in IE8 with synopsis displayed](images/website-running-in-ie8-with-synopsis-displaye.png?raw=true)

	_Website running in IE8 with the fallback for the synopsis_

1. Click the **About** button in the navigation bar.

	The About page loads. Notice the logo is not being displayed. You will learn why and how to fix it in the next steps.

1. Stop debugging.

<a name="Task2.2" />
#### SVG images ####
[SVG](http://www.w3schools.com/svg/svg_intro.asp) (Scalable Vector Graphics) is an XML-based vector image format for two-dimensional graphics with support for interactivity and animation. SVG graphics are scalable and do not lose any quality if zoomed or resized, and since they are defined in pure XML they can be created and edited in any text editor, and searched. It's those advantages as well as compliance with other standards that make them attractive, but lack of browser support has limited its use. The workaround for these browsers in which SVG is not supported is to serve a different, equivalent image in a different format. 

In this section you will fix the About page (which is not displaying the Sintel logo because the image format is SVG) by using _Modernizr_ to detect whether the browser supports SVG, and if not provide a PNG fallback. 

![About page not displaying the logo](images/about-page-not-displaying-the-logo.png?raw=true)

1. Open **About.cshtml** in the **Views\Home** folder.

1. Scroll to the bottom and add the following code:

	````JavaScript
	<script>
		 if (!Modernizr.svg) {
			  var logo = document.getElementById("sintelLogo");
			  logo.src = '/Content/images/Sintel_logo.png';
		 }
	</script>
	````

	This code is setting the image source to a png image if svg is not supported.

1. Press **F5** to start debugging and when the website is launched in IE, change it to emulate IE8 (press **F12** to open the Developer tools and then press **Ctrl+8** to change to the Emulator tab where you will set the **Document mode** to **8**).

	The home page will reload in IE8. If the About page is not displayed, click the **About** button in the Navigation bar.

	Notice that the logo is now visible. Upon inspection of the html in the Developer Tools, you can see that the image loaded is in PNG format.

	![Logo visible in About page in IE8 mode](images/logo-visible-in-about-page-in-ie8-mode.png?raw=true)

	_Logo is visible in IE8 after fix_

1. Switch back to Visual Studio and stop debugging.

<a name="Task2.3" />
#### @2X images ####
With newer retina displays and devices, the images normally used look grainy and of low quality. The solution adopted by the modern websites is to serve two sets of images: a normal version and a "2x" version that is twice as big. This 2x version needs to be displayed only if the device is a retina device.

To detect retina devices and serve this "2x" image, you can use a media query to detect the window width for responsive layouts. You will learn more about responsive design in the [Responsive design lab](../mobile-first-design). 

To test this in IE11 you will need to have a retina display or device. You can also use the Google Chrome Developer Tools, as they allow emulation of high resolution displays. You can read more about the Google Chrome screen emulator [here](https://developer.chrome.com/devtools/docs/device-mode#screen-emulator).

1. Open **Contact.cshtml** in the **Views\Home** folder.

1. Update the `style` tag to add the media query for high-resolution, so it looks like this:
	<!-- mark:14-19 -->
	````CSS
	<style>
		 .contact {
			  padding: 5px 10px 15px 0px;
			  font-family: Georgia;
			  font-size: 16px;
			  text-align: justify;
		 }

		 .highqualityimage {
			  background: url("/content/images/sintel_logo.png") no-repeat;
			  height: 140px;
		 }

		 @@media(-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi) {
			  .highqualityimage {
					background: url("/content/images/sintel_logo@2x.png") no-repeat;
					background-size: 140px;
			  }
		 }
	</style>
	````
1. Right-click the **images** folder under **Content** and select **Add existing item**. In the dialog box that opens, browse to where the sintel_logo@2x.png file is located (you can find it [here](code/end/ContosoIndustries/Content/images/sintel_logo%402x.PNG)).

1. In the toolbar, change to debug in Google Chrome and then press **F5**.

	![Change browser to Chrome](images/change-browser-to-chrome.png?raw=true)

	_Change browser to Chrome_

1. Once the website launches in Chrome and you are in the Contact page, press **F12** to open the Developer Tools.

1. In the top bar, change the **Device** to a retina display device, like **Notebook with HiDPI screen**.

	![Change device to HiDPI screen](images/change-device-to-hidpi-screen.png?raw=true)

	_Change the device to a HiDPI screen_

	The screen will change to show the page emulating the selected device.

1. On the bottom pane of the **Developer Tools**, find the html tag for the image (you can locate the image by clicking the search button and then the image).

	Once the html for the image is selected on the left, the Styles tab on the right will display the CSS rules applied to the element. As you can see, the @2x image is used.

	![@2X image used in high resolution mode](images/2x-image-used-in-high-resolution-mode.png?raw=true)

	_The @2x image is used in high dpi mode_

1. Stop debugging.

<a name="Task2.4" />
#### Media Queries ####
CSS3 media queries used to build responsive websites are not supported in IE8 and other older browsers. If you are developing mobile-first, which is the preferred way to build responsive sites, you will need to work around this issue. 

There are 2 options:

* using a polyfill, like Respond.js, to add support for media queries in IE8 so the site looks as intended.

	[Respond.js](https://github.com/scottjehl/Respond) is a polyfill script that adds media query support in IE8. It runs through the CSS and picks out the styles that are referenced inside media queries, returning back to the browser those styles that apply without the media queries. That way, the browser can interpret them correctly. It is very fast and lightweight, and installing it on a page is as simple as referencing it in a script tag in the head. It's most commonly used inside a conditional comment that only executes on browser versions lower than IE9.

	````HTML
	<!--[if lt IE 9]>
		 <script src="http://cdnjs.cloudflare.com/ajax/libs/respond.js/1.1.0/respond.min.js"></script>
	<![endif]-->
	````
* using conditional stylesheets, so a stylesheet specifically tailored for IE8 is used.
 
	Conditional stylesheets are stylesheets specifically written for a browser, e.g. IE8, that are referenced using conditional statements.
	The way you would link up the stylesheets using this method looks like this:

	````HTML
	<!--[if (lt IE 9) & (!IEMobile)]>
		 <link href="ie8.css" rel="stylesheet">
	<![endif]-->
	 
	<!--[if (gte IE 9) | (IEMobile)]><!-->
		 <link href="style.css" rel="stylesheet">
	<!--<![endif]-->
	````

	[This article](http://seesparkbox.com/foundry/structuring_and_serving_styles_for_older_browsers) provides a good approach to automatically generating an IE8 stylesheet free of media queries, by using mixins and variables.

<a name="Task2.5" />
#### CSS3 Properties
Support for CSS3 properties varies among browsers. Some CSS3 properties are  supported in all browsers. Others are supported when vendor prefixes are used. And in some cases, the vendor prefixes properties are not supported at all. 

> Note: Vendor prefixes have been covered in the [Best Practices lab](../best-practices#Task2), but for the sake of completion they will be revisited here briefly.

Graceful degradation for older browsers that do not support a property or even the prefixed version of a property, can result in doing nothing if the experience is not completely broken. In this website, the Contoso Movies header is supposed to have a gradient going from blue to black. This gradient is not displayed in IE8, a flat color background is displayed. However, if you revisit the BrowserStack testing section, you will remember that the gradient was also not showing in the latest version of Safari.

You will fix that now:

1. Open **Site.css** in the **Content** folder.

1. Find the CSS rule for the header:

````CSS
header[role=banner] {
    background: linear-gradient(#1f2887, #000000);
}
````

1. Update the rule as follows:

````CSS
header[role=banner] {
    background: #1f2887;
    background: -webkit-linear-gradient(#1f2887, #000000);
    background: -o-linear-gradient(#1f2887, #000000);
    background: -moz-linear-gradient(#1f2887, #000000);
    background: linear-gradient(#1f2887, #000000);
}
````

As before, you are providing vendor prefix versions for the property and if all fails, display a flat blue background. It is important to always provide the CSS2 property or value before the new CSS3 property, so older browsers will still get some style applied if the better one is not supported.

Testing this in Safari should now display a gradient in the header. However, testing this in IE8 using the emulator provided by the Developer Tools is not displaying a blue background. You will learn why and how to fix it in the next section.

![Background for the top header not blue in IE8](images/background-for-the-top-header-not-blue-in-ie8.png?raw=true)

_Background for the top header is not blue in IE8_

<a name="Task2.6" />
#### HTML5 Elements
[HTML5](http://www.w3schools.com/html/html5_intro.asp) has introduced new elements, some of which you have seen before in this lab (SVG image format, `video` tag) and learned how to provide a fallback for them in older browsers. Among other new elements, HTML5 introduced semantic elements like _\<header>_, _\<footer>_, _\<article>_ and _\<section>_.

1. Open **_Layout.cshtml** in the **Views\Shared** folder and find the part in the `body` where the page header is defined. You will find this code:

<!-- mark:3,15 -->
````HTML
    <body>
        <div data-role="page" data-theme="b">
            <header data-role="header">
                @if (IsSectionDefined("Header"))
                {
                    @RenderSection("Header")
                }
                else
                {
                    <div class="header-container">
                        <img class="logo" src="~/Content/images/movieIcon.png" />
                        <div class="title">@ViewBag.Title</div>
                    </div>
                }
            </header>
		...
		</div>
   </body>
````

As you can see, the top header is written using the new HTML5 `header` tag. This HTML5 element is not supported by IE8. 

The way to fix it is by providing a "_shiv_" (or _HTML5 Enabling JavaScript_), that allows IE8 to interpret HTML5 properties and elements. Placing a shiv in the head of the document makes those HTML5 elements "real" in IE8's eyes by creating them with JavaScript. 

1. Still in **_Layout.cshtml**, update the head element by copying and pasting the following code inside the head of the page:

	````HTML
		  <!--[if lt IE 9]>
		  <script src="https://code.google.com/p/html5shiv/"></script>
		  <![endif]-->
	````

	You are including the _shiv_ using a conditional statement, so it is only included if the browser version is IE8 or lower.

	The whole `head` element will look like this:

	<!-- mark:11-13 -->
	````HTML
		 <head>
			  <meta charset="utf-8" />
			  <title>@ViewBag.Title</title>
			  <meta name="viewport" content="width=device-width" />

			  <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
			  <link href='http://fonts.googleapis.com/css?family=Lobster' rel='stylesheet' type='text/css'>
			  @Styles.Render("~/Content/mobileCss", "~/Content/css")
			  @Scripts.Render("~/bundles/modernizr")
			  @Scripts.Render("~/bundles/flashplayer")
			  <!--[if lt IE 9]>
			  <script src="https://code.google.com/p/html5shiv/"></script>
			  <![endif]-->
		 </head>
	````

1. Press **F5** to start debugging and change IE to emulate IE8 when the website is launched (press **F12** to open the Developer tools and then press **Ctrl+8** to change to the Emulator tab where you will set the **Document mode** to **8**).

	The home page will reload in IE emulating IE8. Notice that now the header has changed: while it is not displaying a gradient because the feature is not supported, it is blue.

	![Website running in IE8 with shiv applied](images/website-running-in-ie8-with-shiv-applied.png?raw=true)

	_Website running in IE8 with the shiv applied_

	You can now sign in to BrowserStack again and follow the same steps as in the previous section to verify all the fixes are indeed working.

1. Stop debugging.


##Summary##
In this lab you have learned about different ways of testing websites that don't involve a lab with physical machines or devices that you manually interact with. Also, you have seen how to change your website to gracefully support older versions of different browsers.
