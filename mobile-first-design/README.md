Mobile First com Design Responsivo
========================================
Uma parte importante do desenvolvimento de sites atualmente é a de Web Design. E quando falamos de Web Design, temos que pensar que o design deve ser responsivo para atender a todos os usuários, não importando qual tela estiverem usando.

Para facilitar esse trabalho recomenda-se começar pela tela menor e então ir ajustando conteúdo para atender telas maiores. Daí nesse caso pode-se até incluir novas informações. A isso dá-se o nome **Mobile First**. E para o ajuste do design de acordo com o tamanho da tela dá-se o nome de **Design Responsivo**.  

Este minicurso inclui:

1. [Mobile First](#Task1)
1. [Design Responsivo](#Task2)
	1. [Viewport](#Task21)
	2. [Estilos](#Task22)
	3. [URI Schema](#Task23)
	4. [Imagens Responsivas](#Task24)
2. [Revisão](#Task3)

<p name="Task1"></p>
##Mobile First##

O termo **Mobile First** foi concebido por [Luke Wroblewski](http://www.lukew.com/presos/preso.asp?26) que destacou a necessidade de se priorizar dispositivos móveis para criar experiências do usuário. O **Mobile First** se baseia na entrega do conteúdo para mais pessoas, forçando o design de páginas com foco no conteúdo principal e funcionalidade do site, permitindo aos designers inovar e tirar proveito das novas tecnologias.

Ele envolve:

- **Web Design Responsivo:** A interface do usuário se encaixa nas telas dos dispositivos que os usuários estão utilizando. A mesma interface pode então se modificar quando a tela é maior. E isto é feito com base em várias propriedades como tamanho ou resolução de tela. Por exemplo, num telefone podemos ver o conteúdo numa coluna simples. Já no Desktop este mesmo conteúdo poderá ser apresentado em duas ou mais colunas, tirando maior proveito do espaço disponível na tela.

- **Melhoria Progressiva:** visa prover funcionalidades básicas para a aplicação e progressivamente incluir novos recursos com base no dispositivo em que ela está executando.

No exemplo utilizado nos minicursos, o **Contoso Movies** foi desenvolvido como uma aplicação para dispositivos móveis. Neste caso podemos usar **melhoria progressiva** para adicionar novos recursos para telas maiores, como desktops..

<p name="Task2"></p>
##Design Responsivo##

Agora vamos detalhar aqui alguns aspectos relacionados com o Design Responsivo para Web.

<p name="Task21"></p>
###**Viewport**###

O Viewport permite especificar como se dará a visualização do conteúdo no browser de um dispositivo móvel. E isso se é feito através do uso da tag `meta` `viewport` que instrui o browser sobre como tratar o dimensionamento e escala do site.

Recomenda-se especificar essa tag com o conteúdo `"width=device-width"` indicando que a página deve ser dimensionada para o tamanho do dispositivo. Além disso, recomenda-se especificar o valor `initial-scale` conforme segue:

1. Abrir o arquivo `index.html`.

2. Localizar a tag `<meta name="viewport">` dentro da tag `<head>`.

3. Dentro do atributo `content` dessa tag, incluir o valor `initial-scale=1`:

	````HTML
	<meta name="viewport" content="width=device-width, initial-scale=1" />
	````

	> Note que parâmetros diferentes dentro do atributo `content` devem ser separados por vírgula.

4. Vamos fazer a alteração nos demais arquivos HTML do nosso projeto: `contato.html`, `postcard.html` e `sobre.html`.

<p name="Task22"></p>
###**Estilos**###

Quando escrevemos CSS, temos que focar o layout para que seja o mais leve e fluído possível. Existem muitos dispositivos com vários tamanhos de tela. E mesmo quando não sabemos exatamente o tamanho da tela de um dispositivo, temos que ter um conteúdo que se ajuste adequadamente ao conteúdo disponível.

Seguem algumas dicas:

**Ter folhas de estilos separadas para telas grandes**

Recomenda-se criar dois arquivos CSS separados - style.css and enhanced.css - com o intuito de prover estilos básicos para telas pequenas e estilos para telas grandes.

E quando for especificar tamanhos de imagens e fontes, sempre usar unidades relativas como `em` e `%`.Neste caso o valor vai ser relativo ao padrão do browser no dispositivo. Por exemplo: Se eu especificar o tamanho da fonte de um tópico `<h1>` como sendo 2em, isso quer dizer que ele terá 2 vezes o tamanho padrão herdado pelo elemento. Dessa forma, se o tamanho de um tópico `<h1>` no desktop for 16 pontos e no dispositivo móvel for 10 pontos, o valor final ficará 32 e 20 pontos, respectivamente. O valor será relativo e não absoluto. 

Vamos então ao código:

1. Criar um novo arquivo **enhanced.css** dentro da pasta **Content**. Este arquivo irá conter estilos para resoluçõe smaiores de telas.

2. Já no `index.html` vamos adicionar o seguinte conteúdo dentro da tag `<head>`:

	````HTML
	<link rel="stylesheet" type="text/css" href="~/Content/enhanced.css" media="screen  and (min-width: 40.5em)" />
	````

	> As folhas de estilo funcionam como camadas de estilos uma sobre a outra. Definimos o enhanced.css após a inclusão do CSS Styles.css para sobrescrever propriedades básicas do site com aquelas para telas maiores.
	> Note também que usamos o atributo `media` para determinar o tamanho da tela mínimo para o qual será incluído o arquivo de estilo `enhanced.css`.	

3. Vamos fazer a alteração nos demais arquivos HTML do nosso projeto: `contato.html`, `postcard.html` e `sobre.html`.

**Estilos Mobile First & Media Queries**

Ao invés de declarar regras de estilo primeiro para telas grandes e depois sobrescrevê-las para telas pequenas, é melhor definir primeiro as regras para telas pequenas. É mais fácil organizar o conteúdo quando se tem mais espaço do que o contrário.

Para tanto vamos usar o recurso de Media Queries.

> É importante ressaltar que alguns browsers de dispositivos móveis não suportam Media Queries. Portanto, definir por padrão estilos para dispositivos móveis garante que estes serão atendidos. 

1. No arquivo **enhance.css**, adicionado recentemente, vamos incluir o seguinte conteúdo:

	````CSS
	#primaryContent {
	     float: right;
	     width: calc(100% - 220px);
	}

	.navigationList {
	     float: left;
	     width: 200px;
	}	
	````

	> Os estilos acima serão adicionados a página somente se a tela ter no mínimo 40.5em, conforme especificado na tag `<link>`. Quando a tela for maior, o menu de navegação ficará a esquerda da tela com tamanho fixo de 200 pixels. 
	
	 
	> Podemos também definir essa regra no próprio CSS da seguinte forma:
	
	````CSS
	@media screen and (min-width: 28.75em) {

	}
	````

2. Além disso, vamos voltar par a página `index.html` e incluir a lista de links dentro de uma `<div>` com a classe `navigationList`:

	````HTML
	<div class="navigationList">
        <ul data-role="listview" style="margin:10px 0 10px 0;" data-inset="true">
            <li data-role="list-divider">Navigation</li>
            <li><a href="./sobre.html">About</a></li>
            <li><a href="./contato.html">Contact</a></li>
            <li><a href="./postcard.html">Postcard</a></li>
        </ul>
    </div>
	````

3. Vamos fazer a alteração nos demais arquivos HTML do nosso projeto: `contato.html`, `postcard.html` e `sobre.html`.

4. Quando rodarmos novamente nosso site, teremos dois efeitos visuais diferentes dependendo do tamanho do browser:

	![Site em tela grande](./images/mobilefirstdesign_largescreen.png?raw=true)

	_Site para tela grande_

5. Quando diminuímos o tamanho da janela do browser, observamos layout diferente:

	![Site em tela pequena](./images/mobilefirstdesign_smallscreen.png?raw=true)

	_Site para tela pequena_

<p name="Task23"></p>
###**O tel: URI Scheme**###

Quando criamos sites para dispositivos móveis, temos que levar em consideração que eles foram projetados para fazer ligações. Além disso, desktops podem ter aplicativos como Skype, que permitem fazer ligações. Para ambos os casos, podemos usar o URI Scheme de telefone ( o `tel`) que permite prover recurso que integra o site com o recurso de ligação:

````HTML
<a href="tel:+18005555555">1-800-555-5555</a>
````

1. Abrir a página `contato.html`.
2. Localizar o último elemento `div` que contem a classe CSS `contact`.
3. Substitua o número de telefone de modo que fique como mostrado abaixo:

	````HTML
	<div class="contact">
		You can call us at
			<a href="tel:+18005555555">
				1-800-555-5555
			</a>
	</div>
	````
4. Execute novamente a aplicação.
5. Vá para a página `contato.html`.
6. Clique o link com o número do telefone. Será mostrada uma tela com aplicativos que permitem fazer ligação.

	![Fazendo ligação a partir da página Web](./images/mobilefirstdesign_telscheme.png?raw=true)

	_Fazendo ligação a partir da página_

7. Ao abrirmos a mesma página no dispositivo móvel, se clicarmos no link, será mostrado o discador.

<p name="Task24" ></p>
###**Imagens Responsivas**###

Sugere-se para imagens definir as otimizadas e menores como padrão e então carregar imagens maiores sob demanda. Há várias técnicas para prover imagens responsivas tanto do lado do cliente como do lado do servidor. Algumas dessas ténicas foram abordadas eno minicurso de [atualização do site](../update/).

<p name="Task3" ></p>
#Revisão#

Atualmente, para criar sites que abrangem a maioria dos usuários, deve-se focar o design no **Mobile First**, que significa projetar seu site tendo como base o dispositivo móvel e então progressivamente ir incluindo novos recursos visuais e conteúdos para telas maiores e dispositivos com mais recursos. A este conceito dá-se o nome de **Melhoria Progressiva**.

Para criar um design web responsivo deve-se, entre outras coisas:

- **Usar viewport** para acomodar as páginas em telas de dispositivos móveis.
- **Media queries** Usar media queries para adaptar a tela para diferentes tamanhos.
- **Imagens e mídias responsivos** para ter o conteúdo apropriado para qualquer resolução.
- **Grids Fluido** que se ajustam com o tamanho do dispositivo.
