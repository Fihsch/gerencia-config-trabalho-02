1. Introdução e Contexto

Quando pensamos na Uber, normalmente lembramos do aplicativo de pedir uma corrida. Porém, a Uber é hoje uma plataforma de tecnologia que atua em diferentes áreas: transporte de passageiros, com o serviço tradicional; entrega de comida e outros produtos, com o Uber Eats; transporte de cargas, com o Uber Freight; além de soluções voltadas a empresas e outros segmentos.

Apesar de serem operações diferentes, todas dependem de sistemas digitais que precisam se comunicar e funcionar de forma confiável. Por trás de uma corrida, por exemplo, podemos imaginar serviços responsáveis por localizar motorista e passageiro, calcular rotas e estimativas de chegada, processar pagamentos e enviar notificações. Em uma operação de entrega, existem outras necessidades, como receber o pedido, conectar o estabelecimento ao entregador e acompanhar a entrega.

Essas funções podem ser realizadas por microserviços. De forma simples, microserviços são componentes menores e especializados de software, cada um responsável por uma função específica. Eles se comunicam e trabalham em conjunto para que, para o usuário, a experiência seja integrada. Assim, uma ação aparentemente simples — como pedir uma corrida ou receber uma entrega — pode depender de diversos serviços funcionando ao mesmo tempo.

Para organizar o desenvolvimento desses serviços, a Uber utiliza grandes repositórios de código, chamados monorepos. Conforme os artigos, essa organização é feita principalmente por linguagens ou tecnologias principais, como Go, Java e Web. No cenário apresentado para 2022, eram aproximadamente 4.500 microserviços distribuídos em três monorepos principais.

Isso significa que a organização não é simplesmente um repositório para Uber, outro para Uber Eats e outro para Uber Freight. Grandes repositórios podem concentrar código utilizado por muitos microserviços, que atendem diferentes funcionalidades e necessidades do negócio. Essa estrutura permite que equipes compartilhem código, ferramentas e componentes, em vez de manter tudo isoladamente.

Ao mesmo tempo, essa escala e esse compartilhamento tornam as mudanças mais complexas. Uma alteração em um componente ou biblioteca compartilhada pode afetar vários microserviços que dependem dela. Como a Uber precisa desenvolver melhorias, corrigir problemas e atualizar seus sistemas constantemente, torna-se importante conseguir disponibilizar mudanças com rapidez, mas sem comprometer a estabilidade da plataforma.
