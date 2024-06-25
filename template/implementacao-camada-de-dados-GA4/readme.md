# Documento de Especificação Técnica

## Volkswagem - Ofertas
23/04/2018 <br />

## Objetivo
Este documento tem como objetivo instruir a implementação do Google Tag Manager e da camada de dados e data attributes para utilização de recursos de monitoramento do Google Analytics referentes a [Volkswagem Ofertas](https://ofertas.vw.com.br/).

<br />

--- 

### Descrição Geral - Snippet Principal (GTM)

O `snippet` do Google Tag Manager é um pequeno trecho de código javascript ou non-javascript, através do uso de um iframe quando o javascript não está disponível, que é inserido nas páginas do site, tornando possível que a configuração das tags sejam realizadas via interface.


### **Posicionamento do Código - Google Tag Manager**

#### 1. Copie o seguinte JavaScript e cole-o o mais próximo da tag `<head>` de abertura possível em todas as páginas do seu site.

```html

<html>
  <head>
    <!-- Google Tag Manager -->
    <script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start': new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0], j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src='https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f); })(window,document,'script','dataLayer','GTM-5XJ32T8');</script>
    <!-- End Google Tag Manager -->
    //...
  </head>
```

#### 2. Copie o seguinte trecho e cole-o imediatamente após a marcação `<body>` de abertura em cada página do seu site.

```html
<body>
  <!-- Google Tag Manager (noscript) -->
  <noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-5XJ32T8" height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
  <!-- End Google Tag Manager (noscript) -->
  //...
  </body>
</html>
```

Link de referência: [Documentação Oficial Google Tag Manager](https://developers.google.com/tag-manager/quickstart)

---

## Overview e Descrições Técnicas - Recursos

### Camada de dados (DataLayer)

> É um array de objetos javascript utilizado pelo Google Tag Manager para receber em seus atributos, dados importantes do site.
Para implementar o dataLayer no site, o desenvolvedor pode utilizar formas diferentes para preencher os dados. Essas formas são dependentes da ação estabelecida na documentação e também do nível da interação.

**Instalação**<br />
Inserir a camada de dados antes do snippet de instalação do Google Tag Manager. Exemplo:

```html
<script>
	window.dataLayer = window.dataLayer || [];
	window.dataLayer = [{
		'atributo1': '[[valor1]]',
		'atributo2': '[[valor2]]'
	}];
</script>
```

OU

```html
<script>
	window.dataLayer = window.dataLayer || [];
	window.dataLayer.push({
		'atributo1': '[[valor1]]',
		'atributo2': '[[valor2]]'
	});
</script>
```

### Atributos HTML (Data Attributes)

> São atributos customizados inseridos nos elementos HTML da página que permite a inclusão de dados adicionais.

**Instalação**
1. Elementos de link: ```<a href="..." class="minha_classe">Link</a>``` <br />
Elementos do tipo link que foram mapeados, precisam receber a classe **gtm-link-event** e os data attributes em sua estrutura.

```html
<a href="http://www.meudominio.com.br/page2"
	class="minha_classe gtm-link-event"
 	data-gtm-event-category="e[[xemplo:valor-categoria]]"
  	data-gtm-event-action="[[exemplo:valor-acao]]"
  	data-gtm-event-label="[[exemplo:valor-rotulo]]">Texto do link</a>
```

2. Elementos comuns: ```<div class="minha_classe">Elemento</div>``` <br />
Todos os elementos comuns do html que não são links e que foram mapeados, precisam receber a classe **gtm-element-event** em sua estrutura.

```html
<div 	class="minha_classe gtm-element-event"
  	data-gtm-event-category="[[exemplo:valor-categoria]]"
 	data-gtm-event-action="[[exemplo:valor-acao]]"
 	data-gtm-event-label="[[exemplo:valor-rotulo]]">Texto do elemento</div>
```

---

<br />

## Implementação - Recursos

A documentação foi descrita para algumas áreas especificas do ambiente [Volkswagem Ofertas](https://ofertas.vw.com.br/).

## Especificações Globais:

**Itens Gerais:**<br />
Todas as informações entre colchetes `[[  ]]` são variáveis dinâmicas que devem ser preenchidas com seus respectivos valores;<br />
Todos os valores enviados ao Google Analytics devem estar sanitizados, ou seja, sem acentuação ou caracteres especiais, e os espaços nos modelos dos veículos devem ser substituídos por underline;<br />
Não retornar a palavra 'novo' na variável [[modelo-veiculo]] ou quando for preencher algum campo com o modelo do veículo;<br />
Caso a informação solicitada não estiver disponível retornar 'nao_disponivel'.<br />

### Dimensões Globais:

**Dimensões Customizadas para todas as páginas:**<br />
Deve ser disparado um push de dataLayer no momento de carregamento de todas as páginas do site, antes do snippet do GTM. Considerar também todas as trocas de Path, quando o conteúdo da página é alterado mas a página não recarrega (History Changes).<br />

```html
<script>
	window.dataLayer = window.dataLayer || [];
	dataLayer.push({
		'dimension1': '[[ambiente]]',
		'dimension2': '[[pageType]]',
		'dimension3': '[[regional]]',
		'dimension4': '[[estado]]',
		'dimension5': '[[cidade]]',
	});
</script>
```

<br />

| Variável		| Exemplo 				| Descrição								|
| :------------	| ---------------------	| :------------------------------------ |
| [[ambiente]] 	| 'volkswagem-ofertas'	| Nome do ambiente 						|
| [[pageType]] 	| 'Home'				| Nome amigável da sessão página atual	|
| [[regional]] 	| '02'					| ID da região do uusário				|
| [[estado]] 	| 'sp'					| Estado informado pelo usuário			|
| [[cidade]] 	| 'sao-paulo'			| Cidade informada pelo usuário			|

*PageType*
- Retornar os valores na variável 'pageType' de acordo com cada página conforme exeplificado a seguir:

| Exemplo		| Página																		|
| :------------	| -----------------------------------------------------------------------------	|
| 'page404'		| Em todas as páginas não encontradas (erro404)									|
| 'localizacao' | Na exibição do modal de localização 											|
| 'home' 		| No carregamento da Home 														|
| 'comparativo' | No carregamento da página de comparativo										|
| 'oferta'		| No carregamento da página de detalhes de uma oferta							|
| 'formulario'	| Na exibição dos formulários "Agende um test drive" ou "Estou Interessado"		|

<br />

### Especificação de Micro-conversões:

#### Importante:
> Todos os links e botões citados nesta documentação devem ter em sua estrutura `html` a classe `gtm-link-event` se for um link `<a>` ou `gtm-element-event` se o elemento não for um link `<a>` conforme demonstraremos nos exemplos específicos.<br />
> Devem ter também os data-attributes `data-gtm-event-category`, `data-gtm-event-action` e `data-gtm-event-label` preenchidos conforme instruções específicas.

**Menu lateral**<br />

- **Onde:** Nos botões do menu lateral em todas as páginas;
- **Título ou nome do botão/link:** "Carros", "Ofertas", "Monte o seu", etc.

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="[[pageType]]"
	data-gtm-event-action="menu-lateral"
	data-gtm-event-label="[[item-clicado]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="[[pageType]]"
	data-gtm-event-action="menu-lateral"
	data-gtm-event-label="[[item-clicado]]"
>Botão</i>
```

<br />

| Variável 			| Exemplo 		| Descrição						|
| :----------------	| :------------	| :----------------------------	|
| [[pageType]] 		| 'home'		| Nome amigável da página atual	|
| [[item-clicado]]	| 'monte-o-seu'	| Texto do item clicado			|

<br />

**Logo Volkswagem**<br />

- **Onde:** No logo da volkswagem localizado no canto superior esquerdo em todas as páginas;
- **Título ou nome do botão/link:** "Logo Volkswagem".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="logo-vw"
	data-gtm-event-action="topo"
	data-gtm-event-label="home"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="logo-vw"
	data-gtm-event-action="topo"
	data-gtm-event-label="home"
>Botão</i>
```

<br />

**Icone - Localização**<br />

- **Onde:** No ícone para alterar a localização do usuário em todas as páginas;

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="localizacao"
	data-gtm-event-action="topo"
	data-gtm-event-label="mudar"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="localizacao"
	data-gtm-event-action="topo"
	data-gtm-event-label="mudar"
>Botão</i>
```

<br />

**Preenchimento do e-mail - Formulário Radar de Ofertas**<br />

- **Onde:** Após o clique para preenchimento do campo "E-mail" localizado dentro do formulário "Radar de Ofertas".

```html
<script>
	dataLayer.push({
		'event': 'event',
		'eventCategory': 'formulario-radar',
		'eventAction': 'interacao',
		'eventLabel': 'e-mail'
	});
</script>
```

<br />

**Seleção de modelos - Formulário Radar de Ofertas**<br />

- **Onde:** Após o clique para cada modelo selecionado no campo "Modelos" localizado dentro do formulário "Radar de Ofertas".

```html
<script>
	dataLayer.push({
		'event': 'event',
		'eventCategory': 'formulario-radar',
		'eventAction': 'modelo',
		'eventLabel': '[[modelo-veiculo]]'
	});
</script>
```

<br />

| Variável 				| Exemplo 	| Descrição			 			|
| :--------------------	| :-------- | :----------------------------	|
| [[modelo-veiculo]]	| 'up'		| Modelo de veículo selecionado	|

<br />

**Botão Enviar - Formulário Radar**<br />

- **Onde:** No botão para enviar o formulário dentro do Formulário "Radar de Ofertas";
- **Título ou nome do botão/link:** "Enviar".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="formulario-radar"
	data-gtm-event-action="clique"
	data-gtm-event-label="enviar"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="formulario-radar"
	data-gtm-event-action="clique"
	data-gtm-event-label="enviar"
>Botão</i>
```

<br />

**Filtros - Aplicar ou limpar**<br />

- **Onde:** Nos botões para aplicar filtros ou limpar os filtros aplicados dentro da página de filtros;
- **Título ou nome do botão/link:** "Aplicar filtros" e "limpar filtros".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="filtros"
	data-gtm-event-action="clique"
	data-gtm-event-label="[[limpar ou aplicar]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="filtros"
	data-gtm-event-action="clique"
	data-gtm-event-label="[[limpar ou aplicar]]"
>Botão</i>
```

<br />

| Variável 				| Exemplo 				| Descrição								|
| :--------------------	| :--------------------	| :------------------------------------	|
| [[limpar ou aplicar]]	| 'limpar' ou 'aplicar'	| Retornar de acordo com nome do botão	|

<br />

**Categoria dos filtros**<br />

- **Onde:** Após o clique nos botões para selecionar uma categoria principal de filtro.
- **Título ou nome do botão/link:**	"Buscar por Modelo", "Filtrar por Tecnologia", "Hatch", "Cross", "SUV/Utilitario", etc.

```html
<script>
	dataLayer.push({
		'event': 'event',
		'eventCategory': 'filtros',
		'eventAction': '[[modelo ou categoria ou tecnologia]]',
		'eventLabel': 'clique'
	});
</script>
```

<br />

| Variável 								| Exemplo 									| Descrição			 										|
| :------------------------------------	| :---------------------------------------- | :--------------------------------------------------------	|
| [[modelo ou categoria ou tecnologia]]	| 'modelo' ou 'categoria' ou 'tecnologia'	| Retornar de acordo com a categoria de filtro selecionada	|

<br />

**Filtros**<br />

- **Onde:** Após o clique nos filtros que serão aplicados.
- **Título ou nome do botão/link:**	"Hatch" , "Ar Condicionado", "Fusca", etc.

```html
<script>
	dataLayer.push({
		'event': 'event',
		'eventCategory': 'filtros',
		'eventAction': '[[modelo ou categoria ou tecnologia]]',
		'eventLabel': '[[item-clicado]]'
	});
</script>
```

<br />

| Variável 								| Exemplo 									| Descrição			 										|
| :------------------------------------	| :---------------------------------------- | :--------------------------------------------------------	|
| [[modelo ou categoria ou tecnologia]]	| 'modelo' ou 'categoria' ou 'tecnologia'	| Retornar de acordo com a categoria de filtro selecionada	|
| [[item-clicado]]						| 'direcao-hidraulica'						| Retornar de acordo com o filtro selecionado	 				|

<br />

**Ordenar**<br />

- **Onde:** Nos botões para ordernar a exibição de veículos de acordo com um critério selecionado;
- **Título ou nome do botão/link:** "Menor preço", "Mais vistos" e "Menor preço".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="ordenar"
	data-gtm-event-action="clique"
	data-gtm-event-label="[[item-clicado]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="ordenar"
	data-gtm-event-action="clique"
	data-gtm-event-label="[[item-clicado]]"
>Botão</i>
```

<br />

| Variável 			| Exemplo 										| Descrição				|
| :----------------	| :--------------------------------------------	| :--------------------	|
| [[item-clicado]]	| 'menor-preco', 'mais-vistos' ou 'menor-preco'	| Texto do item clicado	|

<br />

**Comparativo - Card-home**<br />

- **Onde:** No botão para adicionar um veículo para comparação localizado dentro do card do veículo na Home;
- **Título ou nome do botão/link:** "Comparar Ofertas".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="card-home"
	data-gtm-event-action="comparar"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="card-home"
	data-gtm-event-action="comparar"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Botão</i>
```

<br />

| Variável 				| Exemplo 							| Descrição										|
| :--------------------	| :--------------------------------	| :--------------------------------------------	|
| [[modelo-veiculo]]	| 'up'								| Modelo do veículo								|
| [[id-oferta]]			| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]	| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]		| '2017'							| Ano do veículo								|
| [[cor-veiculo]		| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]	| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]		| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|

<br />

**Mais detalhes**<br />

- **Onde:** Nos botões do exibição de acessórios e mais detalhes do veículo dentro da página de detalhes do veículo;
- **Título ou nome do botão/link:** "Performance", "Tecnologia", "Segurança" e "Design".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="detalhes"
	data-gtm-event-action="opcoes:[[item-clicado]]"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="detalhes"
	data-gtm-event-action="opcoes:[[item-clicado]]"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Botão</i>
```

<br />

| Variável 				| Exemplo 							| Descrição										|
| :--------------------	| :--------------------------------	| :--------------------------------------------	|
| [[item-clicado]]		| 'performance'						| Texto do item clicado							|
| [[modelo-veiculo]]	| 'up'								| Modelo do veículo								|
| [[id-oferta]]			| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]	| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]		| '2017'							| Ano do veículo								|
| [[cor-veiculo]		| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]	| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]		| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|

<br />

**Galeria de fotos**<br />

- **Onde:** No botão para visualizar fotos do veículo dentro da página de detalhes do veículo;
- **Título ou nome do botão/link:** "Galeria de fotos".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="expansao"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="expansao"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Botão</i>
```

<br />

| Variável 				| Exemplo 							| Descrição										|
| :--------------------	| :--------------------------------	| :--------------------------------------------	|
| [[modelo-veiculo]]	| 'up'								| Modelo do veículo								|
| [[id-oferta]]			| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]	| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]		| '2017'							| Ano do veículo								|
| [[cor-veiculo]		| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]	| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]		| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|

<br />

**Alterar fotos**<br />

- **Onde:** Nos ícones para alterar as imagens exibidas do veículo dentro da galeria de fotos;
- **Título ou nome do botão/link:** "<" e ">".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="setas:[[direita ou esquerda]]"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="setas:[[direita ou esquerda]]"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Botão</i>
```

<br />

| Variável 					| Exemplo 							| Descrição										|
| :------------------------	| :--------------------------------	| :--------------------------------------------	|
| [[direita ou esquerda]]	| 'interior' ou 'exterior'			| Retornar de acordo com texto do botão			|
| [[modelo-veiculo]]		| 'up'								| Modelo do veículo								|
| [[id-oferta]]				| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]		| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]			| '2017'							| Ano do veículo								|
| [[cor-veiculo]			| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|

<br />

**Fotos - Interior ou Exterior**<br />

- **Onde:** Nos botões para visualizar fotos internas ou externas dos veículos dentro da galeria de fotos;
- **Título ou nome do botão/link:** "Exterior" e "Interior".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="clique:[[interior ou exterior]]"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="clique:[[interior ou exterior]]"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Botão</i>
```

<br />

| Variável 					| Exemplo 							| Descrição										|
| :------------------------	| :--------------------------------	| :--------------------------------------------	|
| [[interior ou exterior]]	| 'interior' ou 'exterior'			| Retornar de acordo com texto do botão			|
| [[modelo-veiculo]]		| 'up'								| Modelo do veículo								|
| [[id-oferta]]				| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]		| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]			| '2017'							| Ano do veículo								|
| [[cor-veiculo]			| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|

<br />

**Fechar Galeria**<br />

- **Onde:** No botão para fechar a galeria de fotos;
- **Título ou nome do botão/link:** "X".

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="fechar"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="galeria"
	data-gtm-event-action="fechar"
	data-gtm-event-label="[[modelo-veiculo]]-[[id-oferta]]"
	data-gtm-dimension6="[[categoria-veiculo]]"
	data-gtm-dimension7="[[modelo-veiculo]]"
	data-gtm-dimension8="[[id-oferta]]"
	data-gtm-dimension9="[[ano-veiculo]]"
	data-gtm-dimension10="[[cor-veiculo]]"
	data-gtm-dimension11="[[versao-veiculo]]"
	data-gtm-dimension12="[[trim-veiculo]]"
>Botão</i>
```

<br />

| Variável 					| Exemplo 							| Descrição										|
| :------------------------	| :--------------------------------	| :--------------------------------------------	|
| [[modelo-veiculo]]		| 'up'								| Modelo do veículo								|
| [[id-oferta]]				| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]		| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]			| '2017'							| Ano do veículo								|
| [[cor-veiculo]			| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|

<br />

### Especificação de Conversões:

**Virtual Pageview**

- **Onde:** Todas as vezes que o modal de localização for exibido ao usuário.

```html
<script>
	dataLayer.push({
		'event': 'virtual-pageview',
		'page': '/vpv/localizacao',
		'dimension1': '[[ambiente]]',
		'dimension2': '[[pageType]]',
		'dimension3': '[[regional]]',
		'dimension4': '[[estado]]',
		'dimension5': '[[cidade]]',
	});
</script>
```

<br />

| Variável		| Exemplo 				| Descrição								|
| :------------	| ---------------------	| :------------------------------------ |
| [[ambiente]] 	| 'volkswagem-ofertas'	| Nome do ambiente 						|
| [[pageType]] 	| 'home'				| Nome amigável da sessão página atual	|
| [[regional]] 	| '02'					| ID da região do uusário				|
| [[estado]] 	| 'sp'					| Estado informado pelo usuário			|
| [[cidade]] 	| 'sao-paulo'			| Cidade informada pelo usuário			|

<br />

**Comparar ofertas**<br />

- **Onde:** Após o clique no botão "Comparar Ofertas" que direcione o usuário para a página de Comparativos.

```html
<script>
	dataLayer.push({
		'event': 'event',
		'eventCategory': 'comparar',
		'eventAction': 'clique',
		'eventLabel': '[[numero-de-modelos]]',
		'dimension13': '[[id-comparativo]]',
		'dimension14': '[[id-modelo-1]]',
		'dimension15': '[[id-modelo-2]]',
		'dimension16': '[[id-modelo-3]]',
		'dimension17': '[[modelo-completo-1]]',
		'dimension18': '[[modelo-completo-2]]',
		'dimension19': '[[modelo-completo-3]]'
	});
</script>
```

<br />

| Variável 					| Exemplo 						| Descrição								|
| :------------------------	| :----------------------------	| :------------------------------------	|
| [[numero-de-modelos]]		| '3'							| Número de modelos comparados			|
| [[id-comparativo]]		| '2310510'						| ID do comparativo						|
| [[id-modelo-1]]			| 'gol-123'						| ID único da primeira oferta			|
| [[id-modelo-2]]			| 'polo-123'					| ID único da segunda oferta			|
| [[id-modelo-3]]			| 'fox-123'						| ID único da terceira oferta			|
| [[modelo-completo-1]]		| 'gol-track-1.0-2015'			| Modelo completo do primeiro veículo	|
| [[modelo-completo-2]]		| 'polo-highline-200sti-2017'	| Modelo completo do segundo veículo	|
| [[modelo-completo-3]]		| 'fox-pepper-mpi-2016'			| Modelo completo do terceiro veículo	|

<br />

**Download Ficha Técnica**<br />

- **Onde:** Após o clique no botão "Download Ficha técnica" dentro da página de detalhes do veículo.

```html
<script>
	dataLayer.push({
		'event': 'event',
		'eventCategory': 'detalhes',
		'eventAction': 'ficha-tecnica',
		'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
		'dimension6': '[[categoria-veiculo]]',
		'dimension7': '[[modelo-veiculo]]',
		'dimension8': '[[id-oferta]]',
		'dimension9': '[[ano-veiculo]]',
		'dimension10': '[[cor-veiculo]]',
		'dimension11': '[[versao-veiculo]]',
		'dimension12': '[[trim-veiculo]]'
	});
</script>
```

<br />

| Variável 				| Exemplo 							| Descrição										|
| :--------------------	| :--------------------------------	| :--------------------------------------------	|
| [[modelo-veiculo]]	| 'up'								| Modelo do veículo que a ficha foi baixada		|
| [[id-oferta]]			| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]	| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]		| '2017'							| Ano do veículo								|
| [[cor-veiculo]		| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]	| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]		| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|

<br />

**Formulário Radar de Ofertas - Sucesso**<br />

- **Onde:** No sucesso de envio do formulário Radar de Ofertas para cada modelo selecionado.

```html
<script>
	dataLayer.push({
		'event': 'conversion',
		'eventCategory': 'formulario:radar',
		'eventAction': 'envio:sucesso',
		'eventLabel': '[[modelos-veiculos]]:[[numero-de-modelos]]'
	});
</script>
```

<br />

| Variável 				| Exemplo 		| Descrição											|
| :--------------------	| :------------	| :------------------------------------------------	|
| [[modelos-veiculos]]	| 'up/amarok'	| Modelos solicitados separados por '/'				|
| [[numero-de-modelos]]	| '2'			| Quantidade de modelos enviados no formulário		|

<br />

---

## Especificação de conversões - Enhanced Ecommerce:

### Product Impression:

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no carregamento de uma página de lista de veiculos - Home e Comparativo.

```html
<script>
dataLayer.push({
	'event': 'productImpression',
	'eventCategory': '[[home ou comparativo]]',
	'eventAction': 'visualizacao',
	'eventLabel': 'lista-de-veiculos',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'impressions': [{
			'name': '[[modelo-veiculo]]',
			'id': '[[id-oferta]]',
			'price': '[[preco-veiculo]]',
			'category': '[[categoria-veiculo]]',
			'variant': '[[versao-veiculo]]',
			'position': '[[posicao-veiculo]]'
		}]
	}
});
</script>
```

*Descrição*

| Variável 					| Exemplo 							| Descrição 										|
| :------------------------	| :--------------------------------	| :------------------------------------------------	|
| [[home ou comparativo]] 	| 'home' ou 'comparativo'			| Retornar de acordo com a página atual do usuário 	|
| [[categoria-veiculo]]		| 'hatch'							| Categoria do veículo								|
| [[modelo-veiculo]]		| 'up'								| Modelo do veículo									|
| [[id-oferta]]				| '7350'							| ID único da oferta								|
| [[ano-veiculo]]			| '2017'							| Ano do veículo									|
| [[cor-veiculo]			| 'preto'							| Cor do veículo									|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'				| Versão do veículo									|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'		|
| [[preco-veiculo]] 		| '46760.00'						| Preço do veiculo									|
| [[posicao-veiculo]] 		| '2'								| Posição que o veiculo aparece na lista 			|

<br />

### Product Click:

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no clique nas imagens e no botão "Ver mais detalhes" que estejam dentro dos cards dos veículos nas páginas Home e Comparativo.

```html
<script>
dataLayer.push({
	'event': 'productImpression',
	'eventCategory': 'card-[[home ou comparativo]]',
	'eventAction': '[[imagem ou informacoes]]',
	'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'click': {
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]',
				'position': '[[posicao-veiculo]]'
			}]
		}
	}
});
</script>
```

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no clique no botão "Ver mais detalhes" próximo ao rodapé na página Comparativo.

```html
<script>
dataLayer.push({
	'event': 'productImpression',
	'eventCategory': 'comparativo-fim',
	'eventAction': 'informacoes',
	'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'click': {
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]',
				'position': '[[posicao-veiculo]]'
			}]
		}
	}
});
</script>
```

*Descrição*

| Variável 				| Exemplo 							| Descrição 									|
| :--------------------	| :--------------------------------	| :--------------------------------------------	|
| [[modelo-veiculo]] 	| 'up'								| Modelo do veículo								|
| [[id-oferta]]			| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]	| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]		| '2017'							| Ano do veículo								|
| [[cor-veiculo]		| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]	| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]		| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|
| [[preco-veiculo]] 	| '46760.00'						| Preço do veiculo								|
| [[posicao-veiculo]] 	| '2'								| Posição que o veiculo aparece na lista 		|

<br />

### Product Detail:

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no carregamento da página de detalhes de um veículo.

```html
<script>
dataLayer.push({
	'event': 'productDetail',
	'eventCategory': 'detalhes',
	'eventAction': 'visualizacao',
	'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'detail': {
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]'
			}]
		}
	}

});
</script>
```

*Descrição*

| Variável 				| Exemplo 							| Descrição 									|
| :--------------------	| :--------------------------------	| :--------------------------------------------	|
| [[modelo-veiculo]] 	| 'up'								| Modelo do veículo								|
| [[id-oferta]]			| '7350'							| ID único da oferta							|
| [[categoria-veiculo]]	| 'hatch'							| Categoria do veículo							|
| [[ano-veiculo]]		| '2017'							| Ano do veículo								|
| [[cor-veiculo]		| 'preto'							| Cor do veículo								|
| [[versao-veiculo]]	| 'up-cross-1.6-tsi'				| Versão do veículo								|
| [[trim-veiculo]]		| 'up_up-cross-1.6-tsi'				| Modelo e versão do veículo separados por '_'	|
| [[preco-veiculo]] 	| '46760.00'						| Preço do veiculo								|

<br />

**Preenchimento de Campos  - Formulários Agende um test drive / Estou Interessado**<br />

- **Onde:** Após o clique nos campos para preenchimento dos dados pessoais dentro dos formulários "Estou Interessado" e "Agende um test drive".

```html
<script>
	dataLayer.push({
		'event': 'event',
		'eventCategory': 'formulario-[[nome-do-formulario]]',
		'eventAction': 'interacao',
		'eventLabel': '[[nome-do-campo]]',
		'dimension6': '[[categoria-veiculo]]',
		'dimension7': '[[modelo-veiculo]]',
		'dimension8': '[[id-oferta]]',
		'dimension9': '[[ano-veiculo]]',
		'dimension10': '[[cor-veiculo]]',
		'dimension11': '[[versao-veiculo]]',
		'dimension12': '[[trim-veiculo]]'
	});
</script>
```

*Descrição*

| Variável 					| Exemplo 										| Descrição										|
| :------------------------	| :--------------------------------------------	| :--------------------------------------------	|
| [[nome-do-formulario]]	| 'estou-interessado' ou 'agende-um-test-drive'	| Nome do formulário que está sendo preenchido	|
| [[nome-do-campo]]			| 'e-mail'										| Nome do campo que será preenchido				|
| [[categoria-veiculo]]		| 'hatch'										| Categoria do veículo							|
| [[modelo-veiculo]]		| 'up'											| Modelo do veículo								|
| [[id-oferta]]				| '7350'										| ID único da oferta							|
| [[ano-veiculo]]			| '2017'										| Ano do veículo								|
| [[cor-veiculo]			| 'preto'										| Cor do veículo								|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'							| Versão do veículo								|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'							| Modelo e versão do veículo separados por '_'	|

<br />

### AddToCart:

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no clique do botão "Estou Interessado" localizado dentro dos cards dos veículos nas páginas Home e Comparativo.

```html
<script>
dataLayer.push({
	'event': 'addToCart',
	'eventCategory': 'card-[[home ou comparativo]]',
	'eventAction': 'estou-interessado',
	'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'add': {
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]',
				'quantity': '[[quantidade-veiculo]]'
			}]
		}
	}
});
</script>
```

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no clique do botão "Estou Interessado" localizado no topo e no rodapé da página de detalhes do veículo.

```html
<script>
dataLayer.push({
	'event': 'addToCart',
	'eventCategory': 'oferta-[[fim ou inicio]]',
	'eventAction': 'estou-interessado',
	'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'add': {
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]',
				'quantity': '[[quantidade-veiculo]]'
			}]
		}
	}
});
</script>
```

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no clique do botão "Estou Interessado" localizado próximo ao rodapé da página de Comparativo.

```html
<script>
dataLayer.push({
	'event': 'addToCart',
	'eventCategory': 'comparativo-fim',
	'eventAction': 'estou-interessado',
	'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'add': {
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]',
				'quantity': '[[quantidade-veiculo]]'
			}]
		}
	}
});
</script>
```

*Descrição*

| Variável 					| Exemplo 					| Descrição 										|
| :------------------------	| :------------------------	| :------------------------------------------------	|
| [[home ou comparativo]] 	| 'home' ou 'comparativo'	| Retornar página atual do usuário					|
| [[fim ou inicio]] 		| 'fim' ou 'inicio'			| Retornar a região da página que ocorreu o clique	|
| [[modelo-veiculo]] 		| 'up'						| modelo do veículo									|
| [[id-oferta]]				| '7350'					| ID único da oferta								|
| [[categoria-veiculo]]		| 'hatch'					| Categoria do veículo								|
| [[ano-veiculo]]			| '2017'					| Ano do veículo									|
| [[cor-veiculo]			| 'preto'					| Cor do veículo									|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'		| Versão do veículo									|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'		| modelo e versão do veículo separados por '_'		|
| [[preco-veiculo]] 		| '46760.00'				| Preço do veiculo									|
| [[quantidade-veiculo]]	| '1' 						| Quantidade do veículo 							|

<br />

### Checkout:

**Formulários - Estou Interessado / Agende um Test Drive**

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado uma única vez no carregamento dos formulários "Estou Interessado" e "Agende um test Drive".

```html
<script>
dataLayer.push({
	'event': 'checkout',
	'page': '/vpv/form/[[nome-do-formulario]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'checkout': {
			'actionField': {'step': '1'},
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]',
				'quantity': '[[quantidade-veiculo]]'
			}]
		}
	}
});
</script>
```

*Descrição*

| Variável 					| Exemplo 										| Descrição 									|
| :------------------------	| :--------------------------------------------	| :--------------------------------------------	|
| [[nome-do-formulario]] 	| 'agende-um-test-drive' ou 'estou-interessado'	| Retornar nome do formulário exibido			|
| [[categoria-veiculo]]		| 'hatch'										| Categoria do veículo							|
| [[modelo-veiculo]]		| 'up'											| Modelo do veículo								|
| [[id-oferta]]				| '7350'										| ID único da oferta							|
| [[ano-veiculo]]			| '2017'										| Ano do veículo								|
| [[cor-veiculo]			| 'preto'										| Cor do veículo								|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'							| Versão do veículo								|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'							| Modelo e versão do veículo separados por '_'	|
| [[preco-veiculo]] 		| '46760.00'									| Preço do veiculo								|
| [[quantidade-veiculo]]	| '1' 											| Quantidade do veículo 						|

<br />

### Purchase:

- **Onde:** O objeto javascript (dataLayer) abaixo deve ser disparado no callback de sucesso de envio dos formulários "Agende um test drive" e "Estou Interessado" para cada concessionária escolhida.

```html
<script>
dataLayer.push({
	'event': 'purchase',
	'eventCategory': 'formulario-[[nome-do-formulario]]',
	'eventAction': 'envio:sucesso',
	'eventLabel': '[[modelo-veiculo]]-[[id-oferta]]',
	'dimension6': '[[categoria-veiculo]]',
	'dimension7': '[[modelo-veiculo]]',
	'dimension8': '[[id-oferta]]',
	'dimension9': '[[ano-veiculo]]',
	'dimension10': '[[cor-veiculo]]',
	'dimension11': '[[versao-veiculo]]',
	'dimension12': '[[trim-veiculo]]',
	'ecommerce': {
		'purchase': {
			'actionField': {
				'id': '[[id-lead]]',
				'revenue': '[[valor-total-veiculo]]',
			},
			'products': [{
				'name': '[[modelo-veiculo]]',
				'id': '[[id-oferta]]',
				'price': '[[preco-veiculo]]',
				'category': '[[categoria-veiculo]]',
				'variant': '[[versao-veiculo]]',
				'quantity': '[[quantidade-veiculo]]'
			}]
		}
	}
});
</script>
```

*Descrição Event:*

| Variável 					| Exemplo 										| Descrição 									|
| :------------------------	| :--------------------------------------------	| :--------------------------------------------	|
| [[nome-do-formulario]] 	| 'agende-um-test-drive' ou 'estou-interessado'	| Retornar nome do formulário enviado			|
| [[modelo-veiculo]] 		| 'up'											| Modelo do veículo								|
| [[id-oferta]]				| '7350'										| ID único da oferta							|
| [[categoria-veiculo]]		| 'hatch'										| Categoria do veículo							|
| [[ano-veiculo]]			| '2017'										| Ano do veículo								|
| [[cor-veiculo]			| 'preto'										| Cor do veículo								|
| [[versao-veiculo]]		| 'up-cross-1.6-tsi'							| Versão do veículo								|
| [[trim-veiculo]]			| 'up_up-cross-1.6-tsi'							| modelo e versão do veículo separados por '_'	|
| [[id-lead]] 				| '8948465'										| ID único do lead								|
| [[valor-total-veiculo]] 	| '46760'	 									| Valor total do veículo						|
| [[preco-veiculo]] 		| '46760.00'									| Preço do veiculo								|
| [[quantidade-veiculo]]	| '1' 											| Quantidade do veículo 						|

<br>

---

<script>
  document.addEventListener("DOMContentLoaded", function(event) {
    document.querySelectorAll("h1 a")[0].style.display = 'none';
  });
</script>
