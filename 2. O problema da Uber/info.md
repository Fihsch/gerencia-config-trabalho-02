1. Problemas do processo antigo

Antes do Up CD, levar código até a produção dependia de pessoas, e cada time fazia do seu jeito. A Uber já tinha um sistema de deploy contínuo, mas ele tinha seis problemas.

Deploy manual: muitos deploys eram disparados por alguém, na mão. É um processo lento e sujeito a erro humano.

CD opcional: o deploy contínuo era um sistema à parte. Cada time decidia se ativava e configurava o seu, e muitos não ativavam.

Sem padrão: a pipeline era escrita em YAML, um arquivo de configuração que permitia montar qualquer fluxo. O resultado foi mais de 100 modelos de pipeline diferentes.

Sem segurança: a pipeline só executava uma sequência de passos. Teste e monitoramento não eram etapas obrigatórias, então um código com erro podia chegar à produção sem ninguém perceber.

Dois sistemas: depois que os serviços migraram para o Up, a plataforma de nuvem interna da Uber, o CD continuou separado dela. Eram duas ferramentas para cuidar do mesmo serviço, o que confundia os times.

Difícil evoluir: sem um fluxo comum, uma melhoria feita no processo de um time não chegava aos outros. Não havia como melhorar a segurança da empresa toda de uma vez.

Para a Gerência de Configuração, três pontos se destacam:

- Controle de mudanças: uma mudança podia chegar à produção sem passar por nenhuma verificação obrigatória.
- Gerência de release: mais de 100 pipelines diferentes é o oposto de um processo de liberação definido e repetível.
- A pipeline também é um item de configuração: sem padrão, cada time mantinha a sua versão, e ninguém tinha uma visão do todo.

Em resumo: pouca automação e muito risco em cada deploy.

2. Indicadores iniciais de 2022

Os números do início do projeto, em 2022, mostram o tamanho do problema.

| Indicador | Valor em 2022 |
|-----------|----------------|
| Microsserviços | cerca de 4.500, em 3 monorepos (Go, Java e Web) |
| Commits nos monorepos | cerca de 5.600 por semana, muitos afetando mais de um serviço |
| Deploys em produção | cerca de 7.000 por semana |
| Deploys manuais, sem CD | 34% |
| Serviços com deploy 100% automático | 7% |

Dois desses números parecem se contradizer, mas medem coisas diferentes. Os 34% falam de deploys: um terço era disparado na mão, sem CD. Os outros 66% passavam pelo CD, mas a maioria só em parte do caminho, com uma pessoa ainda fazendo a etapa final. Já os 7% falam de serviços: só 7% dos microsserviços iam do commit à produção sem ninguém intervir.

O dado mais importante: mais de 50% dos incidentes eram causados diretamente por mudanças de código. Ou seja, o maior risco da Uber era o próprio processo de colocar código novo em produção. E o volume de código só crescia.

Para a Gerência de Configuração, esses indicadores mostram a importância de medir o processo de mudança e de release. Sem saber quantos deploys são manuais e de onde vêm os incidentes, não há como saber onde está o risco. São esses mesmos indicadores que a Uber usa depois para medir o resultado do Up CD.

Em resumo: muita escala, processo manual e sem padrão. Foi para resolver isso que a Uber criou o Up CD, apresentado a seguir.

Referências

Continuous deployment for large monorepos, Uber Engineering, ago. 2024 - https://www.uber.com/dk/en/blog/continuous-deployment/
Controlling the Rollout of Large-Scale Monorepo Changes, Uber Engineering, ago. 2025 - https://www.uber.com/us/en/blog/controlling-the-rollout-of-large-scale-monorepo-changes/
Optimizing Continuous Deployment at Uber: Automating Microservices in Large Monorepos, InfoQ, set. 2024 - https://www.infoq.com/news/2024/09/uber-continuous-feployment