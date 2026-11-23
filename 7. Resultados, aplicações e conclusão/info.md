1. Resultados do Up CD

Depois de tudo que foi visto, a pergunta é: funcionou?

O primeiro artigo compara o início de 2022 com março de 2024:

- Serviços: de 4.500 para 5.000
- Commits por semana no monorepo: de 5.600 para 11.000
- Deploys em produção por semana: de 7.000 para 50.000
- Serviços com deploy totalmente automatizado até produção: de 7% para 65%

Ou seja, o volume de mudanças dobrou e a quantidade de deploys cresceu cerca de sete vezes.

E a segurança? No mesmo período de 12 meses, os incidentes reportados a cada 1.000 mudanças de código caíram mais de 50%. Mas os próprios autores fazem uma ressalva: outras iniciativas aconteciam ao mesmo tempo, então não dá para atribuir essa queda somente ao CD.

Existe também um ganho indireto: com menos commits em cada deploy, fica mais fácil descobrir qual commit causou um problema.

2. Resultados do rollout por tiers

No segundo artigo, o resultado mais interessante não é a quantidade de incidentes evitados, que o artigo não informa, e sim a forma como o sistema foi calibrado.

Antes de colocar o mecanismo em produção, a Uber usou um simulador.

Com os parâmetros originais, a orquestração só terminava dentro da mesma semana se o commit entrasse antes das 22h UTC de segunda-feira. Mesmo no melhor caso, os serviços do tier 1 ficariam bloqueados por 40 horas, enquanto a meta era de 24 horas.

Com os parâmetros ajustados, a simulação mostrou todos os grupos sendo liberados em até 24 horas. E quando mudanças críticas reais acionaram o mecanismo, as previsões ficaram bem próximas da realidade.

A principal lição é um equilíbrio. A intuição levava a esperar por um sinal mais forte antes de avançar, mas isso deixava os serviços críticos muito atrasados em relação à branch principal. Segurança demais também tem custo: uma correção de bug ou um patch de segurança demora mais para chegar em produção.

3. Conexão com a aula de Escalabilidade e Provisionamento

Na aula, vimos o canary: a versão nova vai para poucos usuários, por exemplo 7%. O sistema compara erro e latência com os outros 93% e, se piorar, o balanceador tira o servidor novo. Na prática, com weight=3, montamos um canary de 25%.

A Uber aplica essa mesma ideia em um nível acima.

Em vez de dividir os usuários de um serviço, ela divide os serviços afetados por um commit:

- O tier 5 faz o papel dos "7%"
- O limite de falhas faz o papel de "comparar erro e latência"
- Marcar o commit como problemático faz o papel de "tirar o servidor novo"  

Também vimos na aula a pergunta "vale a pena eliminar o ponto único de falha?", com três exemplos: o cardápio da cantina, as inscrições da Semana Acadêmica e a matrícula. A resposta dependia do custo de cada serviço ficar fora do ar.

Os tiers da Uber são exatamente essa classificação. O cardápio seria tier 5; a matrícula seria tier 0. O bug aparece primeiro onde ficar fora custa pouco.

Além disso, uma biblioteca compartilhada no monorepo funciona como um ponto único de falha no código: não é um servidor que existe uma vez só, mas um código do qual milhares de serviços dependem.

Por fim, a aula disse que o balanceador "transforma o deploy de evento em rotina". É isso que explica os números do Up CD: só é possível chegar a dezenas de milhares de deploys por semana quando o deploy deixa de ser um evento.

Do ponto de vista de Gerência de Configuração, o commit é a unidade rastreada. A Uber usa o grafo de dependências do Bazel para saber quais serviços cada commit afeta, e um commit marcado como problemático fica bloqueado até que a correção seja incluída.

4. Aplicações

Esse padrão não é exclusivo da Uber:

- O Slack libera primeiro para os próprios workspaces internos e depois por porcentagem.
- O Google usa canary em seus releases.
- A Amazon usa pipelines com rollback automático.
- A Cloudflare sofreu grandes quedas em 2025 por mudanças de configuração propagadas para todo o mundo de uma vez e, depois disso, passou a usar rollout progressivo também para configuração.

E não é preciso ter 5.000 serviços para aplicar a ideia. Basta ter um código compartilhado do qual várias coisas dependem. Ambientes em estágios no GitHub Actions ou GitLab, canary no Kubernetes ou feature flags já permitem fazer isso em escala menor.

5. Conclusão

A Uber automatizou o deploy para entregar mais rápido e em pedaços menores. Depois percebeu que a automação também espalha um erro mais rápido.

A resposta foi aplicar a ideia do canary em um nível acima. Em vez de dividir usuários, ela divide serviços; em vez de usar porcentagem, usa criticidade.

O commit chega primeiro onde uma falha custa pouco e só alcança os serviços críticos depois que os outros provaram que ele é seguro.

A Uber não eliminou o risco. Ela escolheu onde ele aparece primeiro.

Referências

Continuous deployment for large monorepos, Uber Engineering, ago. 2024 - https://www.uber.com/dk/en/blog/continuous-deployment/
Controlling the Rollout of Large-Scale Monorepo Changes, Uber Engineering, ago. 2025 - https://www.uber.com/us/en/blog/controlling-the-rollout-of-large-scale-monorepo-changes/
Deploys at Slack, Slack Engineering, abr. 2020 - https://slack.engineering/deploys-at-slack/
Canarying Releases (The Site Reliability Workbook, cap. 16), Google, jul. 2018 - https://sre.google/workbook/canarying-releases/
Automating safe, hands-off deployments, Amazon Builders' Library, jun. 2020 - https://aws.amazon.com/builders-library/automating-safe-hands-off-deployments
Code Orange: Fail Small, Cloudflare, 2026 - https://blog.cloudflare.com/code-orange-fail-small-complete/