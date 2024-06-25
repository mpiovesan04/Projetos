# Especificação Técnica da Camada de Dados (dataLayer)

<br />

## Enhanced Ecommerce

Última atualização: 13/02/2019  <br />

<br />

## Objetivo

Este documento tem como objetivo instruir a implementação da camada de dados para utilização de recursos do enhanced ecommerce referentes ao ambiente do site de Biuti.

<br />

## Overview e Descrições Técnicas

### Camada de dados (DataLayer)
> É um array de objetos javascript utilizado pelo Google Tag Manager para receber em seus atributos, dados importantes do site.
Para implementar o dataLayer no site, o desenvolvedor pode utilizar formas diferentes para preencher os dados. Essas formas são dependentes da ação estabelecida na documentação e também do nível da interação.

**Instalação**<br />
Inserir a camada de dados antes do snippet de instalação do Google Tag Manager. Exemplo:

```html
<script>
	window.dataLayer = window.dataLayer || [];
	window.dataLayer = [{
		'atributo1': 'valor1',
		'atributo2': 'valor2'
	}];
</script>
```

 OU

```html
<script>
	window.dataLayer = window.dataLayer || [];
	window.dataLayer.push({
		'atributo1': 'valor1',
		'atributo2': 'valor2'
	});
</script>
```

## Implementação

A documentação foi descrita para algumas áreas especificas do ambiente [](http://).

---

## Especificações Globais:

**Itens Gerais:**<br />

Todas as informações entre colchetes `[[  ]]` são variáveis dinâmicas que devem ser preenchidas com seus respectivos valores;<br />
Todos os valores enviados ao Google Analytics devem estar sanitizados, ou seja, sem espaços, acentuação ou caracteres especiais;<br />
Caso a informação solicitada não estiver disponível retornar 'nao_disponivel'.

### Dimensões Globais:

**Dimensões Customizadas para todas as páginas:**<br />
Deve ser disparado um push de dataLayer no momento de carregamento de todas as páginas do site (Considerar também todas as trocas de Path, quando o conteúdo da página é alterado mas a página não recarrega).<br />

```html
<script>
	window.dataLayer = window.dataLayer || [];
	dataLayer.push({
		'dimension1': '[[User ID]]'
	});
</script>
```


| Variável 				| Exemplo 				| Descrição 									|
| :--------------------	| :-------------------- | :-------------------------------------------	|
| [[User ID]]			| '012345'				| Retornar o ID único do usuário logado			|

---

### Especificação de Micro-conversões:


**No clique dos botões que tenham a ação para sair e deslogar da minha conta:**<br />

- **Onde:**  No clique de 'sair' do menu superior em minha conta; No clique de sair dentro de minha conta;
- **Título ou nome do botão/link:** 'Sair'.

```html
<!-- Use se o elemento for um link -->
<a href="#"
	class="gtm-link-event"
	data-gtm-event-category="biuti:conta"
	data-gtm-event-action="login"
	data-gtm-event-label="sair"
>Link</a>

<!-- Use se o elemento não for um link -->
<i 	class="gtm-element-event"
	data-gtm-event-category="biuti:conta"
	data-gtm-event-action="login"
	data-gtm-event-label="sair"
>Botão</i>
```

<br />

---

### Especificação de Conversões:

**No callback de sucesso de todos os logins de usuário no ecommerce:**<br />

- **Onde:** Após clicar em entrar na sessão 'Eu já estou registrado' do site ou no modal flutuante de "Acesse Já". 

```html
<script>
	dataLayer.push({
		'event': 'login',
		'eventCategory': 'biuti:[[conta || modal]]',
		'eventAction': 'login',
		'eventLabel': 'sucesso'
	});
</script>
```


| Variável 				| Exemplo 				| Descrição 									|
| :--------------------	| :-------------------- | :-------------------------------------------	|
| [[conta ou modal]]	| 'conta' ou 'modal'	| Retornar em qual tela o usuário realizou o login.	|

<br />


**No callback de sucesso do primeiro cadastro do usuário no ecommerce:**<br />

- **Onde:** Na página deslogada na sessão de registre-se para cadastro de usuário no site. 

```html
<script>
	dataLayer.push({
		'event': 'conversion',
		'eventCategory': 'biuti:conta',
		'eventAction': 'cadastro',
		'eventLabel': 'sucesso'
	});
</script>
```

<br />

---

## Enhanced Ecommerce.

### Product Impression:

**No carregamento das páginas que contem vitrines de produtos - Páginas de Lista de produtos:**

- **Onde:** Em todas as páginas do site que exibem vitrines com diversos produtos - Home, Categorias, Marcas, Etc, e subcategorias correspondentes.

```html
<script>
dataLayer.push({
	'event': 'productImpression',
	'eventCategory': 'biuti:[[sessao]]',
	'eventAction': 'enhanced-ecommerce',
	'eventLabel': 'lista-de-produtos',
	'ecommerce': {
		'impressions': [{
			'name': '[[nome-produto]]',				
			'id': '[[id-produto]]',
			'price': '[[preco-produto]]',
			'brand': '[[marca-produto]]',
			'category': '[[categoria-produto]]',
			'variant': '[[variacao-produto]]',
			'list': '[[lista-produto]]',
			'position': '[[posicao-produto]]'
		}]
	}
});
</script>
```

<br />

| Variável 					| Exemplo 						| Descrição 								|
| :------------------------ | :------------------------ 	| :---------------------------------------- |
| [[sessao]] 				| 'home', 'categorias' e etc. 	| Retornar a sessão atual do usuário.		|
| [[nome-produto]] 			| 'batom-avon-vermelho' 		| Retornar o nome do produto. 				|
| [[id-produto]] 			| 'i17mcjf106-771-2'			| Retornar o ID do produto. 				|
| [[preco-produto]] 		| '139.99'						| Retornar o preço do produto.				|
| [[marca-produto]] 		| 'avon'	 					| Retornar a marca do produto. 	 			|
| [[categoria-produto]] 	| 'categorias/corpo'			| Retornar a categoria do produto. 			|
| [[variacao-produto]]		| '125g'						| Retornar o tamanho do produto. 			|
| [[lista-produto]] 		| 'corpo'						| Retornar o nome da lista de produtos. 	|
| [[posicao-produto]] 		| '2'							| Retornar a posição que o produto aparece na lista iniciando pelo número '1' 	|

<br />

### Product Click:

**No clique dos produtos que abrem um produto especifico exibido na vitrine de produtos - Páginas de Lista de produtos:**

- **Onde:** Em todas as páginas do site que exibem vitrines com diversos produtos - Home, Categorias, Marcas, Etc, e subcategorias correspondentes.
- **Título ou nome do botão/link:** Imagem ou Descrição do produto que abre a página de detalhe do produto.

```html
<script>
dataLayer.push({
	'event': 'productClick',
	'eventCategory': 'biuti:[[sessao]]',
	'eventAction': 'enhanced-ecommerce',
	'eventLabel': 'clique-produtos',
	'ecommerce': {
		'click': {
			'actionField': {'list': '[[lista-produto]]'},
			'products': [{
				'name': '[[nome-produto]]',				
				'id': '[[id-produto]]',
				'price': '[[preco-produto]]',
				'brand': '[[marca-produto]]',
				'category': '[[categoria-produto]]',
				'variant': '[[variacao-produto]]',
				'position': '[[posicao-produto]]'
			}]
		}
	}
});
</script>
```

<br />

| Variável 					| Exemplo 						| Descrição 								|
| :------------------------ | :------------------------ 	| :---------------------------------------- |
| [[sessao]] 				| 'home', 'categorias' e etc. 	| Retornar a sessão atual do usuário.		|
| [[nome-produto]] 			| 'batom-avon-vermelho' 		| Retornar o nome do produto. 				|
| [[id-produto]] 			| 'i17mcjf106-771-2'			| Retornar o ID do produto. 				|
| [[preco-produto]] 		| '139.99'						| Retornar o preço do produto.				|
| [[marca-produto]] 		| 'avon'	 					| Retornar a marca do produto. 	 			|
| [[categoria-produto]] 	| 'categorias/corpo'			| Retornar a categoria do produto. 			|
| [[variacao-produto]]		| '125g'						| Retornar o tamanho do produto. 			|
| [[lista-produto]] 		| 'corpo'						| Retornar o nome da lista de produtos. 	|
| [[posicao-produto]] 		| '2'							| Retornar a posição que o produto aparece na lista iniciando pelo número '1' 	|

<br />

### Product Detail:

**No carregamento das páginas de um produto especifico - Página de detalhe de produto:**

- **Onde:** Em todas as páginas de detalhe dos produtos. 

```html
<script>
dataLayer.push({
	'event': 'productDetail',
	'eventCategory': 'biuti:produtos',
	'eventAction': 'enhanced-ecommerce',
	'eventLabel': 'detalhes-do-produto',
	'ecommerce': {
		'detail': {
			'actionField': {'list': '[[lista-produto]]'},
			'products': [{
				'name': '[[nome-produto]]',				
				'id': '[[id-produto]]',
				'price': '[[preco-produto]]',
				'brand': '[[marca-produto]]',
				'category': '[[categoria-produto]]',
				'variant': '[[variacao-produto]]'
			}]
		}
	}
 });
</script>
```

<br />

| Variável 					| Exemplo 						| Descrição 								|
| :------------------------ | :------------------------ 	| :---------------------------------------- |
| [[nome-produto]] 			| 'batom-avon-vermelho' 		| Retornar o nome do produto. 				|
| [[id-produto]] 			| 'i17mcjf106-771-2'			| Retornar o ID do produto. 				|
| [[preco-produto]] 		| '139.99'						| Retornar o preço do produto.				|
| [[marca-produto]] 		| 'avon'	 					| Retornar a marca do produto. 	 			|
| [[categoria-produto]] 	| 'categorias/corpo'			| Retornar a categoria do produto. 			|
| [[variacao-produto]]		| '125g'						| Retornar o tamanho do produto. 			|
| [[lista-produto]] 		| 'corpo'						| Retornar o nome da lista de produtos. 	|

<br />

### AddToCart:

**No clique dos botões que tenham a ação para adicionar o produto na sacola:**

- **Onde:** Em todas as páginas de detalhe dos produtos ou em paginas que exibem vitrines de produtos.
- **Título ou nome do botão/link:** 'Adicionar na Sacola'.

```html
<script>
dataLayer.push({
	'event': 'addToCart',
	'eventCategory': 'biuti:[[sessao]]',
	'eventAction': 'enhanced-ecommerce',
	'eventLabel': 'adicao-ao-carrinho',
	'ecommerce': {
		'add': {
			'products': [{
				'name': '[[nome-produto]]',				
				'id': '[[id-produto]]',
				'price': '[[preco-produto]]',
				'brand': '[[marca-produto]]',
				'category': '[[categoria-produto]]',
				'variant': '[[variacao-produto]]',
				'quantity': '[[quantidade-produto]]'
			}]
		}
	}
});
</script>
```

<br />

| Variável 					| Exemplo 									| Descrição 								|
| :------------------------ | :------------------------ 				| :---------------------------------------- |
| [[sessao]] 				| 'produtos', 'home', 'categorias' e etc. 	| Retornar a sessão atual do usuário.		|
| [[nome-produto]] 			| 'batom-avon-vermelho' 					| Retornar o nome do produto. 				|
| [[id-produto]] 			| 'i17mcjf106-771-2'						| Retornar o ID do produto. 				|
| [[preco-produto]] 		| '139.99'									| Retornar o preço do produto.				|
| [[marca-produto]] 		| 'avon'	 								| Retornar a marca do produto. 	 			|
| [[categoria-produto]] 	| 'categorias/corpo'						| Retornar a categoria do produto. 			|
| [[variacao-produto]]		| '125g'									| Retornar o tamanho do produto. 			|
| [[quantidade-produto]] 	| '1'										| Retornar a quantidade do produto.			|

<br />

### Checkout:

**No carregamento das páginas do fluxo de checkout correspondete ao carrinho, escolha de entrega e  meios de pagamento:**
 
- **Onde:** Nas páginas do fluxo de checkout.

```html
<script>
dataLayer.push({
	'event': 'checkout',
	'eventCategory': 'biuti:checkout',
	'eventAction': 'enhanced-ecommerce',
	'eventLabel': 'checkout-etapa-[[passo-checkout]]',
	'ecommerce': {
		'checkout': {
			'actionField': {'step': '[[checkout-index]]'},
			'products': [{
				'name': '[[nome-produto]]',				
				'id': '[[id-produto]]',
				'price': '[[preco-produto]]',
				'brand': '[[marca-produto]]',
				'category': '[[categoria-produto]]',
				'variant': '[[variacao-produto]]',
				'quantity': '[[quantidade-produto]]'
			}]
		}
	}
});
</script>
```
 
<br />
 
| Variável 					| Exemplo 									| Descrição 								|
| :------------------------ | :------------------------ 				| :---------------------------------------- |
| [[checkout-index]] 		| '1', '2' ou '3' 							| Retornar de acordo com a página de checkout que o usuário está (Página de Carrinho = 1/ Página de entrega = 2 / Página de pagamento = 3)		|
| [[passo-checkout]] 		| 'carrinho', 'entrega' ou 'pagamento' 		| Retornar de acordo com a página de checkout que o usuário está.	|	
| [[nome-produto]] 			| 'batom-avon-vermelho' 					| Retornar o nome do produto. 				|
| [[id-produto]] 			| 'i17mcjf106-771-2'						| Retornar o ID do produto. 				|
| [[preco-produto]] 		| '139.99'									| Retornar o preço do produto.				|
| [[marca-produto]] 		| 'avon'	 								| Retornar a marca do produto. 	 			|
| [[categoria-produto]] 	| 'categorias/corpo'						| Retornar a categoria do produto. 			|
| [[variacao-produto]]		| '125g'									| Retornar o tamanho do produto. 			|
| [[quantidade-produto]] 	| '1'										| Retornar a quantidade do produto.			|
 
<br />
 
### Purchase:

**No carregamento de sucesso da página de compra do pedido. :**
 
- **Onde:** Nas páginas de resumo do pedido finalizado.
 
```html
<script>
dataLayer.push({
	'event': 'purchase',
	'eventCategory': 'biuti:checkout',
	'eventAction': 'enhanced-ecommerce',
	'eventLabel': 'purchase',
	'ecommerce': {
		'purchase': {
			'actionField': {
				'id': '[[id-transacao]]',
				'revenue': '[[valor-total-transacao]]',
				'shipping': '[[frete-transacao]]'
			},
			'products': [{
				'name': '[[nome-produto]]',				
				'id': '[[id-produto]]',
				'price': '[[preco-produto]]',
				'brand': '[[marca-produto]]',
				'category': '[[categoria-produto]]',
				'variant': '[[variacao-produto]]',
				'quantity': '[[quantidade-produto]]',
			}]
		}
	}
});
</script>
```

<br />
 
| Variável 					| Exemplo 									| Descrição 								|
| :------------------------ | :------------------------ 				| :---------------------------------------- |
| [[id-transacao]] 			| '000011652' 								| Retornar o ID único da transação.			|
| [[valor-total-transacao]]	| '139.99' 									| Retornar o valor total da transação incluindo fretes e taxas.	|
| [[frete-transacao]]		| '0.00'									| Retornar o valor do frete da transação. 	|
| [[nome-produto]] 			| 'batom-avon-vermelho' 					| Retornar o nome do produto. 				|
| [[id-produto]] 			| 'i17mcjf106-771-2'						| Retornar o ID do produto. 				|
| [[preco-produto]] 		| '139.99'									| Retornar o preço do produto.				|
| [[marca-produto]] 		| 'avon'	 								| Retornar a marca do produto. 	 			|
| [[categoria-produto]] 	| 'categorias/corpo'						| Retornar a categoria do produto. 			|
| [[variacao-produto]]		| '125g'									| Retornar o tamanho do produto. 			|
| [[quantidade-produto]] 	| '1'										| Retornar a quantidade do produto.			|
 
<br />
 
---
 
## Considerações Finais
 
> Recomendações do Google:
> 1. [Enhanced Ecommerce - Product Impressions](https://developers.google.com/tag-manager/enhanced-ecommerce#product-impressions)
> 2. [Enhanced Ecommerce - Product Clicks](https://developers.google.com/tag-manager/enhanced-ecommerce#product-clicks)
> 3. [Enhanced Ecommerce - Product Detail](https://developers.google.com/tag-manager/enhanced-ecommerce#details)
> 4. [Enhanced Ecommerce - AddToCart / Remove From Cart](https://developers.google.com/tag-manager/enhanced-ecommerce#cart)
> 5. [Enhanced Ecommerce - Checkout](https://developers.google.com/tag-manager/enhanced-ecommerce#checkout)
> 6. [Enhanced Ecommerce - Purchase](https://developers.google.com/tag-manager/enhanced-ecommerce#purchases)
 
 
<script>
  document.addEventListener("DOMContentLoaded", function(event) {
    document.querySelectorAll("h1 a")[0].style.display = 'none';
  });
</script>
