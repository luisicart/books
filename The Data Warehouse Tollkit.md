# The Data Warehouse Tollkit - Ralph Kimball 

The Data Warehouse Tollkit - The Definitive Guide to Dimensional Modeling. Third Edition. 
    Ralph Kimbal e Margy Ross - 2013

## Capítulo 1

**DW é o sistema de suporte de decisão da Empresa**

Um DW precisa ser:
    1. Óbvio
    2. Fácil
    3. Confiável (Se duas métricas tem os mesmos nomes, elas precisam ter valores iguais)
    *Simplicidade é crítica - Começar simples para terminar simples*

|          Fatos              |             Dimensões            | 
| --------------------------  | -------------------------------- |
| Longas                      | Largas                           | 
| Representam números         | Podem ter até 100 colunas        |
| Se tiver texto será único   | Who, what, where, when, why, how |
| Dado mais granular possível | Amigável                         | 

Não podemos normalizar dados, ou seja, criar dimensões de dimensões. 

Bus Architeture: Conceito de que todos embarcam nos mesmos *conceitos*, leia-se onibus. Não temos uma informação diferente para cada time.

**DW é um grande Restaurante:**
* Pessoas buscam a melhor comida
* Um menu variado e da preferência de quem vai comer, não de quem serve
* Conforto com os padrões de dados
* Entrega
* Custo

Nesse capítulo são apresentadas também alternativas ao método de DW criado por Kimball, porém em conclusão eles não são boas opções no geral.

Para prevenir retrabalho, é importante pensar no menor grão possível para não ter que dar dive em coisas toda hora.
É importante que esse processo seja feito pensando no processo de mensuramento do negócio e não em métricas específicas e dashboards.

**A grande jóia do DW são suas dimensões** por proporcionarem diversas quebras importantes para as análises.

## Capítulo 2

Existem 4 passos para o design de um modelo dimensional:
1. Selecionar o processo de negócio a ser mensurado
2. Encontrar o grain (grão)
3. Identificar todas as possíveis dimensões
4. Identificar as fatos

*Curiosidade é identificar primeiramente as dimensões existentes e posteriormente as fatos. Isso faz sentido quando vemos a construção de um modelo dimensional onde contruímos primeiro as dimensões e depois as fatos*

**Processo de Negócio:** Localizar todo processo ou evento que um fato ocorra e possa ser mensurado.
**Grão:** Para evitar retrabalhos, é importante ser o menor grão possível de acordo com o negócio.
**Dimensões:** Sempre que possível deve ter uma relação de 1 valor apenas para cada linha. Podemos adicionar dimensões desde que seja possível apenas criar uma nova coluna de chave na tabela de fato, o mesmo se aplica para criar uma nova coluna na dimensão.
**Fatos:** Precisam ser consistentes no grão e suas alterações devem ser resolvidas apenas incluindo novas colunas

### Fatos:
> Nulos são bem-vindo, exceto nas chaves primárias
> Transactions fact tables referem-se a mensuração de um evento
> Periodic Snapshot Fact table é onde o menor grão é o período e como os valores estavam naquele momento
### Dimensões:
> São mais poderosas quando usamos verbos em seus atributos
* Surrogate Keys são chaves primárias criadas no dw iniciando em 1. Criadas para evitar quando chaves naturais são más administradas e quando chaves naturais do negócio fiquem multiplicadas por cada alteração no tempo.
* Elas podem ter hierarquias, como por exemplo, formas de pagamento e intermediadores
* Flags podem ser substituídas por verbos
* Nulos em uma tabela de dimensão devem ser efeitados e trocados por 'Não informado' 
* Conformed Dimensions são dimensões compartilhadas através de duas ou mais fatos
* Shrunken Dimensions só serão usadas com um grupo de linhas ou colunas de acordo com a fato.

**Bus Architecture:** Representa desmembrar o DW em partes dirigidas para focar no processo do negócio
**Bus Matrix:** Uma matriz onde as linhas são as métricas e as colunas dimensões
**Stakeholder Matrix:** Uma matriz onde as as linhas são as métricas e as colunas as áreas

### Tipos de Dimensões

Type 0 - Valor que nunca muda ou se altera
Type 1 - Quando precisamos atualizar uma linha e sobreescrevemos ela
Type 2 - Quando precisamos atualizar uma linha e criamos outra linha com outro código para preservar o anterior. Deve ter 3 colunas ao menos, data de inicio, data de fim e valor durante essas datas.
Type 3 - É criado um novo atributo (coluna) na dimensão, possibilitando agrupar pelo valor anterior ou atual
Type 4 - É usada quando queremos manter um histórico de alguma coisa, splitamos em 2 dimensões. 1 dimensão terá uma chave primária e o valor atual que será preenchido em uma coluna da fato durante todo o período. Outra tabela será criada onde o valor antigo terá preservado e também será adicionado o novo onde linkaremos com a fato através de outra coluna. Nesse caso a fato terá duas colunas que poderão ser conectadas com qualquer dessas dimensões. **É usada para atributos que alteram-se com frequência**
Type 5 - Criamos na tabela 1 do exemplo anterior uma nova chave sobre o valor mais recente, e criamos uma mini dimensão onde trazemos atributos desse valor atual para facilitar a conexão e nao precisar procurar na type 4
Type 6 - É uma junção do type 1 2 3. Onde realizamos o mesmo processo do Type 2 sendo que criamos uma nova linha com o novo atributo e sua data inicial e final. E além disso usamos o type 3 para criar uma coluna com o atributo anterior
Type 7 - É onde uma surrogate key é atribuida na fato para evidenciar o que realmente aconteceu ali, usando a técnica de type 2. E temos outra chave natural ou de bussiness que conecta com o valor atual do atributo.

[Documentação Simplificada](https://www.kimballgroup.com/2013/02/design-tip-152-slowly-changing-dimension-types-0-4-5-6-7/)

### Tipos de Fatos

Fatos são voltadas para números. Caso existam números que são geralmente filtrados ou usado para grupamentos eles podem gerar uma dimensão. Ainda é possível deixa-los na fato mas ainda sim é interessante criar uma dimensão para eles.

Header/Line Fact Table - Fala a respeito que diversas fatos possuem uma mesma ocorrência associada a várias linhas na fato. Um exemplo disso seria uma fato de pedidos onde cada linha é um produto do pedido.
Multivalued Dimension and Birdge Tables - Essa técnica deve ser usada quando é possível que um fato seja realmente associado a diversos pontos da mesma dimensão. Um exemplo é um paciente com diganóstico que pode possuir diversas doenças ao mesmo tempo.
Behavior Tag Time Series - **Preciso ver exemplos para que fique mais claro**

*Extra: Error Event Schema pode ser um jeito de salvar todos os erros encontrados durante o processo de modelagem*

## Retail Sales
