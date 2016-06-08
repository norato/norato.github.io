---
layout: post
title:  "Using Bower in Ruby on Rails"
date:   2016-06-07 23:37:51 -0300
categories: rails bower
image: "/assets/rails-bower.png"
---

![My helpful screenshot]( /assets/rails-bower.png)

Era uma vez uma época distante onde os nossos heróis quando precisavam de mais poderes
eles tinham que recorrer a arquivos mágicos que os davam certas habilidades. Porém
estas habilidades eram pesadas para serem carregadas para todo canto e difícil de
compartilhar com os outros heróis. Foi então que os heróis se juntaram para resolver
este problema e foram criados os gerenciadores de pacotes.
  
A maioria das linguagens/frameworks possuem seus gerenciador de pacotes. E isso trás vários
benefícios:

* Realiza a instalação dos pacotes necessários para a aplicação
* Reduz a quantidade de arquivos no controle de versão
* Evita a inclusão do arquivo de bibliotecas no projeto
* Mantém atualizado os pacotes
* Realiza o controle interno de dependências para cada pacote

### O Bower

Para realizar o gerenciamento das dependências de front-end uma equipe do Twitter
criou o [Bower](http://bower.io/ "Bower"), que possibilita este gerenciamento, que
anteriormente era caótico para a comunidade Javascript, a.k.a., litros de arquivos
minificados.


A instalação do bower é realizada a partir do *npm*, que também é um gerenciador de
pacotes Javascript para node (Use sudo para linux se for necessário):


```bash
npm install -g bower
```

Na pasta root de seu projeto cria o arquivo  de configuração **_.bower.json_** com o 
comando:

```bash
bower init
```

O conteúdo será algo semelhante a isso:

``` json
{
  "name": "meu-projeto",
  "version": "1.0.0",
  "main": "caminho/para/index.js",
  "ignore": [
    ".jshintrc",
    "**/*.txt"
  ],
  "dependencies": {
  }
}
```
Existem uma lista de comandos para pesquisar, listar pacotes e muito mais coisas, porém o comando mais importante é o de instalação:

```bash
bower install <nome-do-pacote> --save
```

Ao passar o argumento **--save** o pacote é adicionado automaticamente no **.bower.json**
contentendo o nome do pacote e a versão que foi instalada.

Para instalar todos os pacotes já listados **.bower.json**:

```bash
bower install
```

### Bower + Rails

Bom, eu trabalho com Rails e sei que o **Rubygems** já faz isso. Por que vou passar a usar o
Bower para gerenciar as dependências de front-end?

1. Porque ele foi feito para isso, e faz muito bem!

2. A maioria das gems para front-end são um hook para a instalação das
bibliotecas que já podem ser instaladas com o uso do Bower. 

3. Resolve os problemas de dependências entre os pacotes.

4. Mantém os pacotes atualizados, inclusive aqueles que não são gems e que foi necessário
fazer um **cp** no arquivo minificado dentro do app. 


#### Configuração do Bower para o Rails

Primeiro devemos configurar em qual diretório os componentes serão armazenados. Isto será
realizado no arquivo **_.bowerrc_**, que será definido o path vendor para destino.


``` json
{
  "directory":"vendor/assets/bower_components"
}
```

Para que os arquivos sejam carregados e tranquilamente importados é necessário adicionar o path do 
bower no asset pipeline do Rails no arquivo **_config/application.rb_**:

``` ruby
config.assets.paths << Rails.root.join("vendor","assets","bower_components")
```

Deste modo os pacotes porem ser chamados no **application.js** e **application.css** normalmente.

Agora para reescrever o path relativo dos assets nos componentes é necessário instalar a gem bower.

``` bash
gem 'bower-rails' # Gemfile
```
E criar um arquivo de configuração **_config/bower.rb_**:

``` ruby
BowerRails.configure do |bower_rails|
  # Invokes rake bower:resolve before precompilation. Defaults to false
  bower_rails.resolve_before_precompile = true
end
```
PS: A gem [bower-rails](https://github.com/rharriso/bower-rails "bower-rails") trabalha um pouco
diferente do command line do bower.

PS: Pode ser necessário trocar o nome da pasta do pacote se esta possuir um . (ponto/dot).
Pois o asset pipeline do Rails não consegue encontrar o componente, então a instalação segue 
da seguinte maneira:

``` shell
bower install <nome-da-pasta>=<url-do-pacote>
```

Referências:
  
<http://growingdevs.com/stop-using-rubygems-and-start-using-bower.html>

<https://coderwall.com/p/hhaxlg/bower-and-rails-friends-forever>
  
<http://makandracards.com/makandra/28945-bower-rails-can-rewrite-your-relative-asset-paths>