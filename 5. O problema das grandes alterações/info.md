1. O problema das grandes alterações:

Para entender o problema que a Uber identificou, primeiro precisamos compreender como funciona um Monorepo.

Um Monorepo é uma estrutura na qual vários projetos ou serviços diferentes ficam armazenados dentro de um mesmo repositório de código.

Por exemplo, imagine uma empresa que possui serviços separados para pagamentos, autenticação, pedidos e entregas. Cada um possui suas próprias responsabilidades, mas todos podem compartilhar bibliotecas e componentes em comum.

Isso traz vantagens para o desenvolvimento, porque facilita o compartilhamento de código e a manutenção de padrões entre os serviços.

Entretanto, também cria um desafio: uma alteração em um componente compartilhado pode afetar diversos serviços ao mesmo tempo.

Vamos imaginar que a Uber tenha uma biblioteca responsável pela comunicação entre seus microsserviços. Essa biblioteca é utilizada por centenas ou até milhares de serviços.

Agora, suponha que um desenvolvedor faça uma alteração nessa biblioteca para melhorar o desempenho das requisições.

A alteração passa pelos testes automatizados, é integrada ao repositório principal e, como a Uber possui um processo de Continuous Deployment, começa a ser distribuída automaticamente para os serviços afetados.

O problema é que os testes nem sempre conseguem reproduzir todas as situações que acontecem em produção. Portanto, mesmo uma alteração aparentemente simples pode introduzir um erro que só será percebido quando vários serviços começarem a utilizá-la.

Foi justamente esse tipo de situação que a Uber investigou.

A equipe analisou aproximadamente 500 mil commits do seu monorepo Go e descobriu que 1,4% das alterações afetavam mais de 100 serviços, enquanto 0,3% afetavam mais de mil serviços.

Esses percentuais podem parecer pequenos, mas representam alterações com um potencial de impacto muito grande.

Portanto, o desafio não era apenas conseguir publicar código rapidamente, mas garantir que uma alteração problemática não fosse distribuída para centenas ou milhares de serviços antes que alguém percebesse o problema.

2. Blast Radius

Para entender melhor o risco dessas alterações, existe um conceito chamado Blast Radius (raio de explosão em português).

Esse termo representa a extensão que uma falha pode atingir dentro de um sistema.

Quanto mais componentes dependem de algo que apresentou um problema, maior pode ser o impacto dessa falha.

Por exemplo, digamos que a Uber tenha mil microsserviços utilizando uma mesma biblioteca responsável por realizar requisições HTTP.

Um desenvolvedor atualiza essa biblioteca, mas, sem perceber, introduz um erro que faz algumas requisições falharem.

No primeiro cenário, a nova versão é distribuída praticamente ao mesmo tempo para todos os mil serviços.

Como resultado, muitos serviços podem começar a apresentar erros simultaneamente. Isso pode afetar pagamentos, pedidos, entregas e outras funcionalidades que dependem desses serviços.

Além de prejudicar os usuários, fica mais difícil identificar a origem do problema, porque diversos componentes começam a apresentar falhas ao mesmo tempo.

Esse é um exemplo de Blast Radius elevado.

Agora, em um outro cenário, no qual a implantação acontece progressivamente.

Em vez de atualizar todos os mil serviços de uma vez, a nova versão começa sendo distribuída para um grupo menor de serviços.

Durante essa primeira etapa, o sistema monitora indicadores como erros, disponibilidade e consumo de recursos.

Se os resultados forem satisfatórios, a implantação pode avançar para os próximos grupos.

Porém, se o sistema identificar um aumento significativo de falhas, a implantação pode ser interrompida antes que a alteração alcance os demais serviços.

Dessa forma, o problema fica limitado a uma parcela menor da infraestrutura, enquanto os serviços que ainda não receberam a atualização continuam utilizando a versão anterior.

Essa é a ideia central do segundo artigo da Uber: controlar a propagação das alterações para reduzir o impacto potencial de uma falha.

E, a partir desse problema, surge a pergunta: como decidir quais serviços devem receber uma atualização primeiro e quais devem ser atualizados somente depois?

É justamente isso que a Uber resolve com seu mecanismo de implantação progressiva, que será explicado a seguir.
