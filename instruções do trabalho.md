C - Uber:
Deploy contínuo em monorepo com rollout por tiers de criticidade

A Uber tem cerca de 5.000 microsserviços em 3 monorepos, e um único commit pode afetar milhares deles. A empresa automatizou o deploy em produção e passou a liberar mudanças grandes por grupos de serviços, dos menos críticos para os mais críticos.

Material de partida:

[Continuous deployment for large monorepos](https://www.uber.com/dk/en/blog/continuous-deployment/) (Uber Engineering, ago. 2024)
[Controlling the Rollout of Large-Scale Monorepo Changes](https://www.uber.com/us/en/blog/controlling-the-rollout-of-large-scale-monorepo-changes/) (Uber Engineering, ago. 2025)

Separações do trabalho

1. Introdução e contexto - André
2. O problema da Uber - Guilherme Spido
3. Continuous Deployment (Up CD)- Gabriel Bado
4. Funcionamento e resultados do Up CD - Gianluca
5. O problema das grandes alterações - Filipe
6. Rollout progressivo e segurança
7. Resultados, aplicações e conclusão - Nicolas

link apresentação: https://canva.link/p77cteh0dxfi2e0

ordem dos slides

1. Título e integrantes - Pessoa 1
2. Contexto: Uber e sua infraestrutura - Pessoa 1
3. Problemas do processo antigo - Pessoa 2
4. Indicadores iniciais de 2022 - Pessoa 2
5. O que é CI/CD e apresentação do Up CD - Pessoa 3
6. Fluxo de funcionamento do Up CD - Pessoa 3
7. Gates, monitoramento e rollback - Pessoa 4
8. Resultados de 2022 a 2024 - Pessoa 4
9. O problema das grandes alterações - Pessoa 5
10. Blast radius - Pessoa 5
11. Implantação progressiva por criticidade - Pessoa 6
12. Simulações, critérios e segurança - Pessoa 6
13. Comparação dos dois artigos - Pessoa 7
14. Aplicação prática e conclusão - Pessoa 7
15. Referências bibliográficas - Pessoa 7
