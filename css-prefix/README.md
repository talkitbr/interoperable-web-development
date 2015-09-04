Detecção de Features
========================================
Neste minicurso iremos corrigir problemas relacionados com prefixos CSS. 

Os prefixos CSS são comumente utilizados para o desenvolvedor usufruir de recursos CSS em seu site antes mesmo destes se tornarem [recomendações](http://www.w3.org/Consortium/Process/Process-19991111/tr.html#RecsCR). 

Nesse caso o desenvolvedor assume certos riscos em utilizar recursos que podem vir a não funcionar em alguns browsers, principalmente naqueles que não estão sempre sendo atualizados. Pode ocorrer, por exemplo, de certo conteúdo ser apresentado de forma errada para o usuário em determinadas circunstâncias.

Para mitigar estes problemas, neste minicurso veremos:

1. [Identificando problemas de uso de prefixos CSS](#Task1)
1. [Usando ferramenta de para adicionar prefixos CSS automaticamente](#Task2)
1. [Revisão](#Review)

Para executar as tarefas, vamos abrir o projeto Web. Para tanto, vamos usar o **Netbeans** e acessar a pasta [`code\begin`](./code/begin) contido no repositório.

Depois disso, execute o projeto num servidor Web local usando o próprio **Netbeans**:

![Run Netbeans Project](./images/site_local_webserver.png)

> Você pode usar a ferramenta de desenvolvimento Web que desejar. Acesse a [etapa de configuração](../_setup) deste minicurso para mais detalhes.

<p name="Task1" />
##Identificando problemas de uso de prefixos CSS

Voltando para os resultados obtidos pelo Site scan (realizado no [minicurso 1](../sitescan-rendermode/)), observe que nossa página `index.html` possui o seguinte problema de prefixo CSS (há vários outros problemas identificados também no CSS jquery.mobile mas que trataremos mais tarde):

    "cssprefixes": {
      "testName": "cssprefixes",
      "passed": false,
      "data": [
		{
		  "cssFile": "embed",
          "selectors": [
            {
              "selector": ".fadeIn",
              "lineNumber": 3,
              "styles": [
                "-webkit-animation"
              ]
            }
          ]
        }
      ]
    }

Para corrigir este problema, recomenda-se especificar o CSS tanto sem prefixo como com prefixos específicos de outros browsers. Exemplo:

	-webkit-appearance: button; /* WebKit */
	-moz-appearance: button; /* Mozilla */
	-o-appearance: button; /* Opera */
	-ms-appearance: button; /* Internet Explorer */
	appearance: button; /* CSS3 */

No nosso caso, vamos localizar o seletor `.fadeIn` onde foi detectado o erro e então incluir as seguintes propriedades:

	.fadeIn {
        -webkit-animation: fadeIn 3s forwards; /* WebKit */
        -moz-animation: fadeIn 3s forwards; /* Mozilla */
        -o-animation: fadeIn 3s forwards; /* Opera */
        -ms-animation: fadeIn 3s forwards; /* Internet Explorer */
        animation: fadeIn 3s forwards;
    }

Percebam que agora incluímos todos os prefixos para animation. Isso dar maior garantias de que a propriedade funcionará adequadamente nos browsers modernos.

Se testarmos de novo o site após feita essa alteração, você irá notar que o problema encontrado anteriormente não estará mais lá. Somente estarão sendo apontados os problemas nos arquivos externos.
  
<p name="Task2" />
##Usando ferramenta de para adicionar prefixos CSS automaticamente

Existem várias situações em que a inclusão de prefixos CSS manualmente é impeditivo além de que pode resultar em erros por mais que tenhamos cuidado.

Por exemplo, o Site Scan apontou vários problemas no CSS das bibliotecas que usamos. Nesse caso, o ideal é utilizarmos uma ferramenta que adiciona automaticamente os prefixos de forma que não afete o resultado final e garanta que tudo esteja especificado conforme recomendado.

Existem várias ferramentas que adicionam automaticamente os prefixos CSS. Aqui usaremos o autoprefixer disponibilizado no repositório GitHub [https://github.com/postcss/autoprefixer](https://github.com/postcss/autoprefixer).

O Autoprefixer pode executado de várias maneiras, seja por linha de comando, no processo de build ou mesmo através de ferramentas de edição de texto como Visual Studio de SublimeText.

Segue exemplo usando o postcss-cli, ferramenta de linha de comando do [PostCSS](https://github.com/postcss/postcss).
 
1. Instalar via npm o postcss com o plugin autoprefixer através do seguinte comando:

	`npm install --global postcss-cli autoprefixer`

2. Ainda usando linha de comando, executar o postcss na pasta onde estão os arquivos CSS que queremos adicionar automaticamente os prefixos CSS:

	`postcss --use autoprefixer jquery.mobile-1.2.0.css -d .`
	`postcss --use autoprefixer jquery.mobile.structure-1.2.0.css -d .`

Pronto! Agora é para os prefixos estarem adicionados corretamente.

<p name="Review"/>
##Conclusão

Leituras Recomendadas:

1. [Prefixos CSS no Microsoft Edge](http://talkitbr.com/2015/08/19/prefixos-css-no-microsoft-edge/): Aprenda a usar Prefixos CSS;
2. [Teste seu site em diferentes versões de browsers](http://talkitbr.com/2015/09/01/teste-seu-site-em-diferentes-versoes-de-browsers/): Veja como testar seu site em diferentes versões de browsers, desde o IE até Chrome e Firefox. 

Você pode obter o código final desse minicurso [aqui](./code/end).

