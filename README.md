#AngularJS

![Logo do AngularJS](https://cldup.com/cWbFHP8Pol-1200x1200.png)

##Directivas

![Exército de clones](https://cldup.com/04QHfHjmPv.thumb.png)

As directivas são marcadores em um elemento DOM (como um atributo, o nome do elemento, comentário ou classe CSS) que informam ao compilador HTML do AngularJS ($compile) para anexar um comportamento específico para o elemento DOM ou mesmo transformar o elemento DOM e seus filhos.

Basicamente utilizamos as diretivas para encapsular a lógica de apresentação de um módulo, qualquer tipo de interação no DOM deve ser feita única e exclusivamente na directiva, se você estiver fazendo isso no *Controller* tenho péssimas notícias para você.

![Você está errado, mas continue tentando.](https://cldup.com/kQb7cU5bJg-3000x3000.jpeg)

O AngularJS vem com um conjunto de directivas internas, como `ngBind`, `ngModel` e `ngClass`. Assim como você pode criar *Controllers* e *Services*, também pode criar suas próprias diretivas para Angular usar.

*O que significa "compilar" um template de HTML? Para AngularJS, "compilação" significa anexar listeners de eventos no HTML para torná-lo interativo.*

###Como criar?

![Como criar? Imagem da patente de uma campainha](http://chestofbooks.com/crafts/electricity/Electricity-For-Boys/images/Fig-49-Electric-Bell.png)

Já levando em consideração boas práticas esse é o código para iniciarmos a programação de uma diretiva:

```js
(function () {

  var directive = function () {
    return {

    };
  };

  angular.module('moduleName')
    .directive('directiveName', directive);

}());
```

No código anterior, a função `directive()` registra uma nova directiva em nosso módulo. O primeiro argumento para esta função é o nome da directiva. O segundo argumento é uma função que retorna um objeto de definição da directiva.

O segredo mora no que retornamos nesse objeto e para isso temos uma API para trabalhar com as diretivas.

E todo o código foi encapsulado em uma [IIFE](http://benalman.com/news/2010/11/immediately-invoked-function-expression/) para que não haja "vazamentos" no módulo.

![WAT](https://cldup.com/I-XX4sQNwq.jpg)

Isso vazamento, para que seus dados não *vazem* para o escopo global, então com essa IIFE criamos um escopo local que nos garante o encapsulamento do dados.

###API

A API da directiva pode ser um pouco complexa no início, mas com o tempo os conceitos vão se enraizando e começa a ficar um pouco mais fácil, vamos ver do que é composta essa API:

```js
var myModule = angular.module('moduleName'); 
myModule.directive('directiveName', function (injectables) {
  return {
    restrict: 'ACME',
    template: '<div></div>',
    templateUrl: 'directive.html',
    replace: false,
    priority: 0,
    transclude: false,
    scope: false,
    terminal: false,
    require: false,
    controller: function($scope, $element, $attrs, $transclude, otherInjectables) { ... },
    compile: function compile(tElement, tAttrs, transclude) {
      return {
        pre: function preLink(scope, iElement, iAttrs, controller) { ... },
        post: function postLink(scope, iElement, iAttrs, controller) { ... }
      }
    },
    link: function postLink(scope, iElement, iAttrs) { ... }
  };
});
```

![Will falando THE FUCK?](https://cldup.com/zZtuN3sHf0-1200x1200.jpeg)

Ela pode assustar no começo mas nada que meses de estudo não nos ajude, vamos conhecer cada opção, porém começaremos com os mais utilizados.

Para iniciar usaremos esse exemplo simples de diretiva:

```js
(function () {

  var directive = function () {
    return {
      restrict: 'AECM',
      replace: 'true',
      template: '<h3>Hello World!!</h3>'
    };
  };

  angular.module('moduleName')
    .directive('helloWorld', directive);

}());
```

####restrict

Para criar seu elemento da diretiva, no template, podemos utilizar 4 formas diferentes:

- A: via atributo
- C: via classe
- M: via comentário
- E: via elemento

##### A

```html
<div hello-world></div>
```

##### C

```html
<div class="hello-world"></div>
```

##### M

```html
<!-- directive:hello-world  -->
```

##### E

```html
<hello-world></hello-world>
```

> Para validar seu código em HTML5 use o prefixo **data**, exemplo: data-hello-world.

As mais comumente utilizadas são a `A` e `E` pois as de classe e comentário podem causar confusões em pessoas que não estão acostumadas com AngularJs e na minha opinião pessoal são péssimas para demonstrar que aquele código é uma diretiva.

Exemplo: http://plnkr.co/edit/Wlnul9IGuDnZwOFjJdC3?p=preview

![eu não quero](http://memeblender.com/wp-content/uploads/2012/08/ecce-homo-jesus-painting-meme.jpg)

Perceba que o nome da directiva em camelCase, `helloWorld` pode ser usado das seguintes formas no template:

```html
<hello-world></hello-world>

<hello:world></hello:world>
```

###CUIDADO!

Caso você esteja usando o `restrict` como `E` dessa forma:

```html
<hello />
<world />
```

```js
.directive("hello", function(){
  return {
    restrict: 'E',
    template: 'Hello'
  };
})
.directive("world", function(){
  return {
    restrict: 'E',
    template: 'World'
  };
})
```

Exemplo: http://plnkr.co/edit/n9Az6LdPJjI9QQXtW8u1?p=preview


Ele não irá compilar corretamente mostrando apenas o `Hello`.

![](https://cldup.com/d6fnc_Dq9j-3000x3000.png)

Vamos ver como o HTML foi compilado:

```
<hello>Hello</hello>
```

Então podemos perceber que ele terminou a compilação na primeira tag que não foi corretamente fechada.

Discussão sobre em [https://github.com/angular/angular.js/issues/1953](https://github.com/angular/angular.js/issues/1953).

> self-closing or void elements as the html spec defines them are very special to the browser parser. you can't make your own, so for your custom elements you have to stick to non-void elements (<foo></foo>).
> 
> this can't be changed in angular. closing.

**Dica:** se você deseja compor um elemento com mais de um comportamento, pode por exemplo agrupar directivas do tipo `A` em um único elemento.


```js
(function () {

  var directiveSuperman = function () {
    return {
      restrict: "A",
      link: function(){
         alert("Superman ao resgate!");
       }
    },
    directiveFlash = function () {
    return {
      restrict: "A",
      link: function(){
         alert("FLASHHHHHHH!");
       }
    };
  angular.module('moduleName')
    .directive('superman', directiveSuperman)
    .directive('flash', directiveFlash);

}());
```

Exemplo: [http://plnkr.co/edit/Wlnul9IGuDnZwOFjJdC3?p=preview](http://plnkr.co/edit/Wlnul9IGuDnZwOFjJdC3?p=preview)

####replace

Usado para especificar se o template gerado irá substituir o elemento HTML em que a directiva está ligado. No caso utilizado uma directiva como `<hello-world> </hello-world>`, a substituição é definida como `true`. Assim, após a diretiva ser compilada, o template produzido substitui <hello-world> </hello-world>. O resultado final é `<h3> Hello World!!</h3>`. Se você definir `replace` como `false`, o padrão, o template será inserido no elemento em que a directiva é invocado.

O código gerado com `replace: false`:

```html
<hello-world>
  <h3>Hello World!!</h3>
</hello-world>
```

E agora gerado com `replace: true`:

```html
<h3>Hello World!!</h3>
```

Então ele basicamente substitiu o seu elemento da diretiva pelo resultado *compilado* dela. Com o `replace` setado como `true` você necessariamente precisa ter um elemento como raíz agrupando o conteúdo do template.

Exemplo correto:

```html
<div><b>Hello</b> World!</div>
```

Exemplo errado:

```html
<b>Hello</b> World!
```

Exemplo: [http://plnkr.co/edit/0obL6YZJuGjGODh9yRLZ?p=preview](http://plnkr.co/edit/0obL6YZJuGjGODh9yRLZ?p=preview)

Caso você se depare com um erro assim:

```js
 Template for directive 'directiveName' must have exactly one root element. 
```

Já sabe que é porque você não encapulou seu template em uma tag.

Exemplo: [http://plnkr.co/edit/n9Az6LdPJjI9QQXtW8u1?p=preview](http://plnkr.co/edit/n9Az6LdPJjI9QQXtW8u1?p=preview)

![Fácil não?](https://cldup.com/KwOd022vK8-3000x3000.jpeg)

Mas em busca de mais material para estudar sobre esse atributo encontrei uma informação que diz que ele será depreciado.

> docs($compile): deprecate `replace` directives
> **BREAKING CHANGE:**

> The `replace` flag for defining directives that replace the element that they are on will be removed in the next major angular version. This feature has difficult semantics (e.g. how attributes are merged) and leads to more problems compared to what it solves.
> Also, with WebComponents it is normal to have custom elements in the DOM.

Então melhor se acostumar a deixar ele como `false`.

####template

Define o conteúdo que deve ser usado pela directiva. Pode incluir HTML, expressões de data-binding e até mesmo outras directivas.

```js
(function () {

  var directive = function () {
    return {
      restrict: 'AECM',
      template: '<h3>Hello World!!</h3>'
    };
  };

  angular.module('moduleName')
    .directive('directiveName', directive);

}());
```

Para usarmos os valores de escopo do *Controller* onde a directiva esta precisamos apenas chamar a variável como no template:

```html
 <div superman flash></div>
```

```js
(function () {

  var directiveSuperman = function () {
    return {
      restrict: "A",
      link: function(){
         alert("Superman ao resgate!");
       }
    };
  },
  directiveFlash = function () {
    return {
      restrict: "A",
      link: function(){
         alert("Superman ao resgate!");
       }
    };
  }

  angular.module('moduleName')
    .directive('superman', directiveSuperman)
    .directive('flash', directiveFlash);

}());
```

Exemplo: [http://plnkr.co/edit/kmHvPtrUTY6rQkU4seAI?p=preview](http://plnkr.co/edit/kmHvPtrUTY6rQkU4seAI?p=preview)

Também podemos utilizar outras directivas dentro, como por exemplo o `ng-repeat`:

```js
return {
  restrict: 'AE',
  template: '<ul><li ng-repeat="l in languages">{{ l }}</li></ul>'
  };
```

E no *Controller* precisamos apenas setar o array `languages`:

```js
  $scope.languages = ['javascript', 'php', 'python', 'ruby'];
```

Exemplo: [http://plnkr.co/edit/FkC9H2AtUEdMtUtJGDcv?p=preview](http://plnkr.co/edit/FkC9H2AtUEdMtUtJGDcv?p=preview)

####templateUrl

Fornece o caminho para o template que deve ser utilizado pela directiva. Aqui podemos tanto chamar um HTML como podemos chamar uma rota no servidor que sirva esse template renderizado, comumente utilizado em sistemas [MEAN](http://bemean.com.br/) onde o AngularJs consome uma *view* em Jade renderizada pelo Node.js e servida via Express por uma rota.

```js
return {
  restrict: 'AE',
  templateUrl: 'linguagens.html'
  };
```

Ou acessando um rota no servidor:

```js
return {
  restrict: 'AE',
  templateUrl: '/expose/linguagens'
  };
```

Vamos passar a lógica que estava no nosso `template` para um arquivo HTML:

```html
<!-- linguagens.html -->
<ul><li ng-repeat="l in languages">{{ l }}</li></ul>
```

Ou Jade:
```jade
<!-- linguagens.jade -->
ul
  li(ng-repeat="l in languages"){{ l }}
```

####priority

![Tudo é prioridade](https://cldup.com/K2QgqX7BRL-1200x1200.jpeg)
Esta opção diz ao AngularJS para ordenar as directivas por prioridade então uma directiva que tem maior prioridade será compilada ligadas antes das outras. A razão para ter essa opção é para podermos realizar seleção condicionada à saída da directiva compilada anteriormente.

No exemplo abaixo, eu quero adicionar classe `btn-primary` somente o elemento tem `btn` classe nele.

```js
.directive("btn", function(){
  return {
    restrict: 'A',
    priority: 1,
    link: function(scope, element, attrs) {
      element.addClass('btn');
      element.text('btn');
    }
  };
})
.directive("primary", function(){
  return {
    restrict: 'A',
    priority: 0,
    link: function(scope, element, attrs) {
      if (element.hasClass('btn')) {
        element.addClass('btn-primary');
        element.text('btn-primary');
      }
    }
  };
});

```

Exemplo: [http://plnkr.co/edit/S1oFSjk50Iq0zwQIDZ8R?p=preview](http://plnkr.co/edit/S1oFSjk50Iq0zwQIDZ8R?p=preview)

Agora ordenando corretamente a prioridade:

```js
app.directive("btn", function(){
  return {
    restrict: 'A',
    priority: 1,
    link: function(scope, element, attrs) {
      element.addClass('btn');
      element.text('btn');
    }
  };
});

app.directive("primary", function(){
  return {
    restrict: 'A',
    priority: 2,
    link: function(scope, element, attrs) {
      if (element.hasClass('btn')) {
        element.addClass('btn-primary');
      element.text('btn-primary');
      }
    }
  };
});
```

Exemplo: [http://plnkr.co/edit/YSxjvRzP8TK5BJos7inB?p=preview](http://plnkr.co/edit/YSxjvRzP8TK5BJos7inB?p=preview)

Você só deve usar essa propriedade caso uma directiva dependa da outra para algo.

####scope

Ao contrário dos outros frameworks MVC, AngularJS não tem classes ou funções específicas para a criação de *models*. Em vez disso, AngularJS estende os objetos JavaScript com métodos e propriedades personalizados. Esses objetos, também conhecido como *scope*, trabalham como uma cola entre a *view* e outras partes (*directives*, *controllers* e *services*) dentro da aplicação.

Quando a aplicação é iniciada, um objeto `rootScope` é criado. Cada *scope* criado por *directives*, *controllers* e *services* são prototipicamente herdados de rootScope.

Essa opção é usada para criar um novo *scope* filho ou um *scope* isolado, elas aceita 3 valores:

- false (padrão)
- true
- isolate (não esse valor, veremos logo abaixo)

#####scope: true

Cria um novo *scope*, mas prototipicamente herda o *scope* pai. Logo o seu *scope* pai será o *scope* do *Controller*, não o `$rootScope`.

```js
angular.module('myapp', []);

angular.module('myapp')
.run(function($rootScope) {
  $rootScope.autor = 'Ninguém';
})
.controller('MainCtrl', function($scope, $http, $rootScope) {
  $scope.autor = 'Suissa';
})
.directive("comScopeTrue", function(){
  return {
    restrict: 'E',
    scope: true,
    template: 'Autor: {{$parent.autor}}'
  };
});
```

Exemplo: http://plnkr.co/edit/C0zlV1XnpbFHDPnbb5YS?p=preview

#####scope: false

É a opção padrão a qual não cria um novo *scope* para a diretiva, mas a faz compartilhar as propriedades com o *scope* pai, nesse caso o `$rootScope`.

```js
var app = angular.module('myapp', []);

app
.run(function($rootScope) {
  $rootScope.autor = 'Ninguém';
})
.controller('MainCtrl', function($scope, $http, $rootScope) {
  $scope.autor = 'Suissa';
})
.directive("comScopeFalse", function(){
  return {
    restrict: 'E',
    scope: false,
    template: 'Autor: {{$parent.autor}}'
  };
});
```

Exemplo: http://plnkr.co/edit/tGPmiagxXLuDHup7rNqi?p=preview

#####scope: isolate

Cria um *scope* isolado que não herda prototipicamente do *scope* pai, mas você pode acessar escopo pai usando scope.$parent.

####terminal

####controller

Utilizado para definir o *Controller* que será associado ao template da directiva.

Pode ser tratada como uma sala de controle de directiva. Você pode vincular as propriedades / métodos para US $ âmbito disponível ou essa palavra-chave. Os dados ligados a este estará acessível em outras directivas, injetando o controlador usando exigir opção. 

Você pode pensar nesse método como a sala de controle da directiva, você pode adicionar propriedades/métodos ao `$scope` desse *Controller* e ele pode ser acessado em outras directivas injetando o *Controller* usando a opção `require`.
No exemplo abaixo, vamos alternar o estado de uma lâmpada de modo que as directivas filhas saberão sobre o estado atual.

```js
return {
  restrict: 'A',
  controller: function($scope, $element, $attrs) {
    $scope.state = true;

    $scope.toggle = function() {
      $scope.state = !$scope.state;
    };

  },
  link: function($scope, element, attrs) {
    angular.element(this).click(this.toggle);
  }
};
```

E no HTML:

```html
<button power-switch ng-click="toggle()">lampada</button>
<span>{{state}}</span>

```

Nesse exemplo criamos um *Controller* específico para essa directiva onde adicionamos propriedade e método, esse o qual pôde ser chamado no método `link` para atrelar a função `toggle` ao evento de click desse elemento usando a directiva `ng-click="toggle()"`. Então a cada click nesse botão ele inverte o `$scope.state`, simples não?

Exemplo: [http://plnkr.co/edit/AYi0Fh9tsHo428DoMXZd?p=preview](http://plnkr.co/edit/AYi0Fh9tsHo428DoMXZd?p=preview)

Além de criamos um *Controller* para a directiva também podemos utilizar um já existe.

```html
<produto-titulo></produto-titulo>
<produto-descricao></produto-descricao>
```

```js
.controller('ProductCtrl', function($scope, $http) {
  $scope.Product = { name: 'Produto teste', 
  price: 666,
  description: 'Testando controller das diretivas'};
})
.directive("produtoTitulo", function(){
  return {
    restrict: 'AE',
    controller: 'ProductCtrl',
    template: '<h1>{{ Product.name }}</h1>'
  };
})
.directive("produtoDescricao", function(){
  return {
    restrict: 'AE',
    controller: 'ProductCtrl',
    template: '<p>{{ Product.description }}<br />Preço: {{ Product.price }}</p>'
  };
})
```

Nesse caso cada directiva recebe sua própria instância do *Controller*, porém estamos compartilhando a lógica entre elas e usando seus dados no template das directivas.

Exemplo: http://plnkr.co/edit/Yk2D9yD567Rh1McjNCzv?p=preview

Caso você queira compartilhar a mesma instância do *Controller* você deve usar o **require**.

####require

![](https://cldup.com/yMjyuh51Cq-2000x2000.jpeg)

Essa opção permite que você compartilhe a mesma instância do *Controller* em várias directivas.

O nome pode ser prefixado com:

> ? – Não acusará nenhum erro se a directiva mencionada não existir.
> ^ - Vai procurar pela directiva nos elementos pai, se não estiver disponível no mesmo elemento.

Use colchetes para requisitar múltiplas directivas:

> [‘directive1′, ‘directive2′, ‘directive3′]


####compile

![](http://www.angularjshub.com/code/examples/customdirectives/01_CompileLinkFunctions/images/directives-compile-link-calls.png)

A função `compile` recebe 2 parâmetros:

- $elem: é um objeto jqLite contendo o nó do DOM que está sendo compilado (então se a directiva estiver dentro de um elemento div, então o objeto jqLite é o nó que está dentro desse div)
- $attrs: é um objeto e cada atributo no nó do DOM corresponde a uma propriedade dentro do objeto attrs (note que o nome da propriedade é a versão normalizada do nome do atributo, por exemplo, se meu-atributo é especificado no nó DOM, em seguida, os objeto attrs terá a propriedade meuAtributo e seu valor será o valor real atribuído ao atributo no DOM)

Neste exemplo vamos ver como as directivas são processadas pelo AngularJS quando encontra-los em um template HTML e como podemos escrever nossas próprias diretivas personalizadas.

Exemplo: [http://plnkr.co/edit/B8zFNUcwARkjONxZhohX?p=preview](http://plnkr.co/edit/B8zFNUcwARkjONxZhohX?p=preview)

#####Fases de inicialização, compilação e linking

Quando AngularJS analisa o template HTML para processar as directivas, podemos identificar três fases principais que cada directiva passa por:

- inicialização: isso acontece quando uma directiva é encontrada pela primeira vez na passagem de árvore DOM (assim acontece apenas uma vez, mesmo que a directiva apareça várias vezes no template HTML) e permite que a directiva  inicialize-se internamente, se necessário
- compilação: nesta fase o AngularJS manipula o DOM do template HTML e cada directiva tem uma chance de fazer algum processamento para cada nó em que ela aparece (por isso, se a mesma directiva aparece em vários nós DOM, a compilação da directiva terá cha para cada node); na fase de compilação de uma directiva também tem a chance de modificar o nó DOM antes de um escopo é ligado a ele
- nesta fase o AngularJS atribui *listeners* de eventos para o template HTML para torná-lo interativo e atribui um *scope* da directiva e ele faz isso para cada nó do DOM em que a directiva aparece; a fase de linking ocorre após a compilação de todo o template HTML foi executado

####link
Função usada para manipulação do DOM.

Exemplo de como manipular um elemento colocando uma cor de fundo nele via CSS.

```html
<input type="text" ng-model="color" placeholder="Enter a color" />
<hello-world/>
```

```js
return {
  restrict: 'AE',
  replace: true,
  template: '<p style="background-color:{{color}}">Hello World',
  link: function($scope, $elem, $attrs) {
    $elem.bind('click', function() {
      $elem.css('background-color', 'white');
      $scope.$apply(function() {
        scope.color = "white";
      });
    });
    $elem.bind('mouseover', function() {
      $elem.css('cursor', 'pointer');
    });
  }
};
```

Como vemos a  função `link` recebe 3 parâmetros:

- $scope: é o *scope* associado a nossa directiva
- $elem: é equivalente ao mesmo parâmetro a função `compile`
- $attrs: é equivalente ao mesmo parâmetro a função `compile`

E também podemos receber como quarto parâmetro um *Controller*.


####transclude
