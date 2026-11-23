1. O que é CI/CD e apresentação do Up CD

Vimos o problema: muita escala e um processo manual. Antes da solução, vale alinhar três termos que costumam ser confundidos.

Integração contínua, o CI: cada commit entra na branch principal e dispara build e testes automáticos. O objetivo é descobrir o erro cedo.

Entrega contínua, ou Continuous Delivery: o software fica sempre pronto para produção, mas alguém ainda aperta o botão para publicar.

Deploy contínuo, ou Continuous Deployment: todo build aprovado vai para produção sozinho.

A sigla CD serve para os dois, mas no artigo da Uber ela significa sempre deploy contínuo.

Em 2022, a Uber decidiu aposentar o sistema antigo e construir o Up CD, dentro da própria plataforma Up. Ele tem quatro características:

- É padronizado: um fluxo único no lugar de mais de 100 modelos de pipeline.
- É seguro: é ligado às ferramentas de teste e de monitoramento da Uber.
- Vem ativado por padrão: deixou de ser uma ferramenta separada e opcional.
- Foi feito para monorepo: mostra a cada time apenas os commits que afetam o seu serviço.

A meta era aumentar a automação sem aumentar o número de incidentes.

2. Fluxo de funcionamento do Up CD

Agora, o caminho de um commit até a produção, em cinco passos.

Passo 1, o commit: um desenvolvedor faz o push para a branch principal do monorepo.

Passo 2, o evento: o push gera um evento no Kafka, que avisa o Up CD da mudança.

Passo 3, a análise: o sistema usa o grafo de dependências do Bazel, a ferramenta de build, para descobrir quais serviços o commit afeta. Assim, cada serviço tem o seu próprio histórico, só com os commits que importam para ele.

Passo 4, o build: para cada serviço afetado, é gerada uma imagem de contêiner.

Passo 5, o deploy: a imagem passa por uma sequência de etapas até a produção. Entre uma etapa e outra ficam os gates, que são as condições para avançar.

Três detalhes merecem atenção.

O fluxo é o mesmo para todos os serviços. Cada time configura apenas as etapas e os gates.

As etapas são independentes. Cada uma leva o commit mais recente que passou pela anterior, então nem todo commit é publicado em todas as etapas.

E quem coordena tudo é o Cadence, o motor de workflows de código aberto criado pela própria Uber.

Para a Gerência de Configuração, três pontos se destacam:

- A análise com o Bazel identifica os itens de configuração afetados por uma mudança.
- A imagem gerada no build é o artefato versionado que percorre as etapas.
- O histórico por serviço responde à pergunta que o processo antigo não respondia: o que está em produção agora?

Em resumo, é um único fluxo, do commit à produção, sem ninguém apertar o botão. Mas automatizar não basta: o deploy precisa ser seguro. É esse o papel dos gates, do monitoramento e do rollback, que vêm a seguir.

Referências

Continuous deployment for large monorepos, Uber Engineering, ago. 2024 - https://www.uber.com/dk/en/blog/continuous-deployment/
Controlling the Rollout of Large-Scale Monorepo Changes, Uber Engineering, ago. 2025 - https://www.uber.com/us/en/blog/controlling-the-rollout-of-large-scale-monorepo-changes/
Continuous Delivery, Martin Fowler, mai. 2013 - https://martinfowler.com/bliki/ContinuousDelivery.html
Continuous Delivery vs Continuous Deployment, Jez Humble, ago. 2010 - https://continuousdelivery.com/2010/08/continuous-delivery-vs-continuous-deployment
