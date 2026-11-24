1. Implantação progressiva por criticidade

Ficou a pergunta: se um commit afeta mil serviços, quais recebem a mudança primeiro? A resposta da Uber é: os que menos importam.

A Uber já classificava seus serviços por criticidade, em tiers de 0 a 5. O tier 0 é o mais crítico, aquele que não pode cair. O tier 5 é o menos crítico, onde uma falha incomoda pouca gente.

O segundo artigo usa essa classificação para controlar o deploy das grandes alterações. Quando um commit afeta muitos serviços, os serviços afetados são separados em grupos conforme o tier, e o deploy segue do menos crítico para o mais crítico:

- Primeiro, só os serviços do tier 5 recebem o commit.
- Quando uma parte deles fez o deploy com sucesso, o tier 4 é liberado.
- E assim por diante, até chegar ao tier 0.

Enquanto isso, os serviços dos tiers ainda não liberados continuam rodando a versão anterior.

Voltando ao exemplo da biblioteca de comunicação: o erro aparece primeiro em serviços pouco críticos. Se ele for grave, a mudança para ali, antes de chegar aos serviços de pagamento ou de pedido de corrida.

Como isso funciona por dentro? Cada commit grande ganha um estado próprio, que diz até qual tier ele já foi liberado. Um processo roda periodicamente, olha o resultado do deploy de todos os serviços afetados e decide: avança para o próximo tier, continua esperando ou marca o commit como problemático.

Esse controle entra no Up CD como mais um gate. Os gates que vimos antes perguntam se o tempo de maturação passou, se está dentro da janela de deploy e se não há alerta. Agora tem mais uma pergunta: o tier deste serviço já foi liberado para este commit?

Um detalhe de escala: o gate de cada serviço apenas consulta o estado que já foi calculado. Ele não verifica os outros mil serviços por conta própria. Com milhares de serviços fazendo a mesma pergunta ao mesmo tempo, essa verificação individual não escalaria.

Para a Gerência de Configuração, três pontos se destacam:

- O tier é um atributo do item de configuração, e passa a definir a ordem em que cada serviço recebe uma mudança.
- O commit passa a ter um estado rastreado: em qualquer momento dá para saber até onde ele chegou.
- A promoção deixa de acontecer só entre ambientes, como teste e produção. Dentro da própria produção, a mudança também é promovida de um grupo de serviços para o próximo.

Em resumo: a mudança chega primeiro onde falhar custa pouco, e só avança quando esses serviços mostram que ela é segura.

2. Simulações, critérios e segurança

Para avançar de um tier para o outro, é preciso que um número suficiente de serviços do tier atual tenha feito o deploy com sucesso.

E o que conta como falha? Para o artigo, falha é qualquer serviço que não conseguiu concluir o deploy. Por exemplo:

- Um alerta automático, como o consumo de recursos do contêiner acima do normal, sinal de que o desempenho piorou.
- Uma violação do nível de serviço combinado (o SLA) daquele serviço.
- Um contêiner que nem consegue subir.

Nesses casos, o serviço volta sozinho para a versão anterior, com o rollback que vimos antes. E essa falha entra na conta do tier que está sendo liberado.

Se as falhas passam do limite, o commit é marcado com um problema, o que a Uber chama de "commit issue". A partir daí, ele fica bloqueado: não segue para os tiers seguintes enquanto a correção não for incluída. O autor do commit é avisado na hora, para investigar e decidir o que fazer, por exemplo reverter a mudança.

O resultado é que os serviços mais críticos nunca chegaram a receber o código com problema.

Vale uma observação: o artigo não divulga os números exatos, como a porcentagem de serviços que precisa passar em cada tier ou quantas falhas bloqueiam o commit.

Mas como escolher esses números? Aqui aparece o principal dilema do artigo: velocidade contra confiança.

Esperar mais em cada tier dá mais segurança. Só que, enquanto o commit está sendo validado, os serviços críticos ficam bloqueados e atrasados em relação à branch principal. E isso inclui correções de bugs e de segurança, que também demoram mais para chegar.

Para não testar isso direto em produção, a Uber construiu um simulador. Ele usa o histórico real de deploys, a configuração de CD de cada serviço e os parâmetros do rollout para prever quando cada tier seria liberado.

A simulação mostrou que o que mais pesa no tempo total são as janelas de deploy de cada time. Se os serviços de um tier só fazem deploy em horário comercial, o commit fica parado esperando a janela abrir. Por isso, o dia e a hora em que o commit entra fazem muita diferença.

Com os parâmetros iniciais, o resultado foi ruim: os serviços do tier 1 ficariam bloqueados por pelo menos 40 horas, e a meta era liberar tudo em no máximo 24 horas. Os detalhes desses resultados vêm na próxima parte.

A Uber então ajustou três coisas: o limite de progresso exigido em cada tier, o tamanho dos grupos, que ficaram maiores, e regras para a configuração de CD dos serviços. Com isso, a simulação passou a liberar todos os tiers em até 24 horas. E quando commits críticos reais passaram pelo mecanismo, o comportamento ficou próximo do previsto.

Como próximo passo, a Uber pretende dar pesos diferentes aos serviços: um serviço que costuma detectar bem os problemas no deploy deve contar mais do que um que costuma deixá-los passar.

Somando tudo, a segurança vem em camadas:

- Testes automáticos antes do commit entrar na branch principal.
- Gates em cada serviço: tempo de maturação, janela de deploy e alertas.
- Rollback automático no serviço que falhou.
- Liberação por tier, que limita até onde o erro chega.
- Bloqueio do commit com problema até a correção, com aviso ao autor.

Nenhuma camada sozinha é suficiente, mas juntas reduzem bastante o raio de impacto de uma falha.

Para a Gerência de Configuração, três pontos se destacam:

- O "commit issue" é controle de mudança: uma mudança com defeito conhecido não segue adiante sem a correção, e fica registrado qual commit causou o problema e qual o corrigiu.
- Enquanto a mudança não se prova segura, os serviços críticos continuam em uma versão conhecida e estável, como uma baseline.
- Os parâmetros do rollout também são configuração. A Uber mudou esses parâmetros com base em dados e testou o efeito no simulador antes de levar para produção, a mesma disciplina que aplica ao código.

Em resumo: a Uber não escolheu entre velocidade e segurança. Ela mediu o custo de cada lado e ajustou o equilíbrio com dados. Se isso funcionou, é o que vamos ver a seguir.

Referências

Controlling the Rollout of Large-Scale Monorepo Changes, Uber Engineering, ago. 2025 - https://www.uber.com/us/en/blog/controlling-the-rollout-of-large-scale-monorepo-changes/
Continuous deployment for large monorepos, Uber Engineering, ago. 2024 - https://www.uber.com/dk/en/blog/continuous-deployment/
