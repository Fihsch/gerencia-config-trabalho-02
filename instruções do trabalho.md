C - Uber:
Deploy contínuo em monorepo com rollout por tiers de criticidade

A Uber tem cerca de 5.000 microsserviços em 3 monorepos, e um único commit pode afetar milhares deles. A empresa automatizou o deploy em produção e passou a liberar mudanças grandes por grupos de serviços, dos menos críticos para os mais críticos.

Material de partida:

[Continuous deployment for large monorepos](https://www.uber.com/dk/en/blog/continuous-deployment/) (Uber Engineering, ago. 2024)
[Controlling the Rollout of Large-Scale Monorepo Changes](https://www.uber.com/us/en/blog/controlling-the-rollout-of-large-scale-monorepo-changes/) (Uber Engineering, ago. 2025)

Separações do trabalho 
1. Introdução e contexto
2. O problema da Uber
3. Continuous Deployment (Up CD)
4. Funcionamento e resultados do Up CD
5. O problema das grandes alterações - Filipe
6. Rollout progressivo e segurança
7. Resultados, aplicações e conclusão - Nicolas

link apresentação: https://canva.link/p77cteh0dxfi2e0