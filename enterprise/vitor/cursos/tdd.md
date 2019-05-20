### Seção 1 - Aula 3 - Tipos de teste

#### Unitário
Testa a menor parte possível de código. Geralmente é um método apenas

#### Integração
Utiliza mais de uma classe ou serviço para testar

#### Sistema (código legado)
Teste feito em um sistema já completo

Nem sempre, em um sistema legado, é possível fazer testes unitários ou de integração. Geralmente, esses sistemas já estão prontos, sem uma suite de testes e design totalmente acoplado.

Fazendo TDD, o design do código da aplicação é feito de uma forma que "aceita" testes. Ao pensar primeiro no teste, o design da aplicação tende a melhorar.

Em geral, quanto mais integrado o teste for, mais caro (computacionalmente) e mais difícil de ser escrito ele é. De acordo com a pirâmide do Martin Fowler, é interessante (da maior quantidade pra menor) testes unitários, testes de integração e testes de aceitação (UI).


### Seção 1 - Aula 4 - TDD
TDD significa Test Driven Development, ou seja, desenvolvimento orientado a testes.

O fluxo do TDD é Red -> Green -> Refactor:
1. Escreva um teste que falha (red)
2. Escreva um código para que o teste passe (green)
3. Elimine a redundância (refactor)
Repita.


### Seção 1 - Aula 5 - Conhecendo o RSpec
Para instalar o `rspec`, utilize `gem install rspec`. Para iniciar um projeto usando o RSpec puro, utilize o comando `rspec --init`. O RSpec criará uma pasta `spec` e, dentro dela, um arquivo chamado `spec_helper.rb`. É dentro da pasta `spec` que todos os testes serão feitos.

O comando `rspec --help` pode ser utilizado para listar todos os parâmetros de `rspec`. O próprio comando `rspec` já roda todos os testes da suite.

O RSpec também já adiciona a pasta `/lib` ao `$LOAD_PATH`. A pasta `lib`, em geral, é onde se situa todos os arquivos de um projeto Ruby. 