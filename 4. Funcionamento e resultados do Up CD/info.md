1. Gates, monitoramento e rollback

Vimos que o commit passa por uma sequência de etapas até chegar à produção. Agora a pergunta é: o que impede um commit ruim de simplesmente seguir em frente?

A resposta são os gates. Pense num portão entre uma etapa e outra: o commit só passa se todas as condições forem atendidas. O artigo cita quatro condições que os times podem combinar:

- Tempo de maturação: o commit já ficou tempo suficiente na etapa anterior? É o chamado "soak time". A ideia é deixar a versão rodando um pouco antes de avançar, para dar tempo de um problema aparecer.
- Janela de deploy: estamos dentro do horário definido pelo time, por exemplo o horário comercial? Assim, se algo der errado, tem gente acordada para resolver.
- Outras operações: já existe alguma coisa acontecendo com o serviço, como um deploy manual ou um escalonamento automático? Se sim, o CD espera.
- Alertas: existe algum alerta disparado no serviço que faria o deploy ser revertido? Se sim, o commit não avança.

Um detalhe importante: os times não montam o pipeline do zero, como faziam antes. Eles só escolhem as etapas e combinam os gates a partir de opções prontas. Foi isso que acabou com os mais de 100 modelos de pipeline.

E por trás desses gates está o monitoramento. O Up CD é integrado às ferramentas de observabilidade e de teste da Uber. Ou seja, o deploy não é "publicar e torcer": o sistema olha os alertas do serviço para decidir se pode continuar.

Quando algo dá errado, entra o rollback, que é voltar para a versão anterior, a última que estava funcionando. Muitos serviços têm mecanismos que detectam o problema e revertem o deploy automaticamente.

E se alguém intervém manualmente, por exemplo fazendo um deploy na mão, o CD não briga com a pessoa: ele registra isso no histórico e, na maioria das vezes, simplesmente pausa ou não faz nada.

Tem ainda um ganho que parece pequeno, mas é enorme: como o deploy é contínuo, cada deploy leva poucos commits. Se der problema, fica muito mais fácil descobrir qual commit causou. No processo antigo, um deploy podia juntar dezenas de mudanças, e achar o culpado era uma investigação.

Para a Gerência de Configuração, isso significa:

- Os gates são critérios formais de promoção: um item de configuração só sobe de ambiente quando cumpre as regras definidas.
- O rollback só é possível porque cada versão é um artefato versionado, a imagem gerada no build. Dá para voltar exatamente para a anterior.
- O sistema mostra, para cada serviço, qual commit está em cada ambiente, inclusive os deploys manuais. A rastreabilidade não se perde.

2. Resultados de 2022 a 2024

E funcionou? O artigo compara o início de 2022, antes do Up CD, com março de 2024:

- Serviços: de 4.500 para 5.000
- Commits por semana no monorepo: de 5.600 para 11.000
- Deploys em produção por semana: de 7.000 para 50.000
- Deploys orquestrados pelo CD, parcial ou totalmente: de 66% para 95%
- Serviços com deploy totalmente automatizado até produção: de 7% para 65%

Ou seja: o número de commits praticamente dobrou, e o número de deploys cresceu cerca de sete vezes. E o deploy automático até produção, que era exceção, virou a maioria.

Agora, a pergunta que importa: mais deploys significou mais problemas? Não. No mesmo período de 12 meses, os incidentes reportados a cada 1.000 mudanças de código caíram mais de 50%.

Mas é preciso ser honesto com o dado, como os próprios autores foram: outras iniciativas de segurança aconteciam ao mesmo tempo, então não dá para dizer que a queda veio só do CD.

Ainda assim, a meta do projeto foi cumprida: mais automação, sem aumento de incidentes.

Só que, com tudo isso funcionando, apareceu um problema novo. Se o deploy é automático e um commit afeta mil serviços ao mesmo tempo, um erro também se espalha automaticamente para mil serviços. É esse o problema das grandes alterações, que vem a seguir.

Referências

Continuous deployment for large monorepos, Uber Engineering, ago. 2024 - https://www.uber.com/dk/en/blog/continuous-deployment/
