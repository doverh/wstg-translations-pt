<h1>Prefácio, por Eoin Keary</h1>

O problema de softwares inseguros é talvez o desafio técnico mais importante de nosso tempo. O aumento exponencial de aplicativos web, que permitem o surgimento de negócios, redes sociais, entre outros, tornou mais urgente o estabelecimento de uma abordagem robusta para desenvolver e proteger a Internet, Aplicativos Web e Dados.

No Open Web Application Security Project® (OWASP®), estamos tentando fazer do mundo um lugar onde o software inseguro seja uma anomalia, não a norma. O Guia de Testes OWASP  tem um papel importante a desempenhar na solução desse sério problema. É de vital importância que nossa abordagem para testar  a segurança de softwares seja baseada nos princípios da engenharia e da ciência. Precisamos de uma abordagem que seja consistente, que possa ser repetida e que tenha uma abordagem definida para testar aplicativos web. Um mundo sem padrões mínimos em termos de engenharia e tecnologia é um mundo no caos.

Não é preciso dizer que você não pode criar um aplicativo seguro sem realizar testes de segurança nele. O teste é parte de uma abordagem mais ampla para construir sistemas seguros. Muitas organizações de desenvolvimento de software não incluem testes de segurança como parte de seu processo padrão de desenvolvimento de software. O que é ainda pior, muitos fornecedores de segurança fornecem testes com níveis de qualidade e rigor variáveis, isto é, sem obedecer uma norma.

O teste de segurança, por si só, não é uma medida autônoma suficiente para determinar quão seguro um aplicativo é, porque há um número infinito de maneiras pelas quais um aplicativo pode ser invadido. É simplesmente impossivel testar todas as possibilidades. Não podemos hackear a nós mesmos com segurança, enquanto possuírmos um tempo limitado para testar e defender,  o invasor não tem tais restrições.

Em conjunto com outros projetos OWASP, como o Guia de Revisão de Codigo (Code Review Guide), o  Guia de Desenvolvimento(Development Guide) e ferramentas como o <a href="https://www.zaproxy.org">OWASP ZAP</a> , este é um ótimo começo para se construir e manter aplicativos seguros. Este guia de testes mostrará como verificar a segurança do seu aplicativo em execução. Recomendo enfaticamente o uso desses guias como parte de suas iniciativas para segurança de aplicativos.

<h2>Por que OWASP</h2>

Criar um guia como este é uma tarefa gigantesca, exigindo a experiência de centenas de pessoas mundo afora. Existem muitas maneiras diferentes de testar as falhas de segurança e este guia captura o consenso dos principais especialistas sobre como realizar testes de forma rápida, precisa e eficiente. O OWASP oferece a pessoas com ideias semalhantes na área de segurança  a capacidade de trabalhar em conjunto e compor uma abordagem predominante  ao problema da segurança.

A importância de ter este guia disponível de forma totalmente gratuita e aberto é importante para a missão da fundação. Ele dá a qualquer pessoa a capacidade de compreender as técnicas usadas para testar problemas comuns de segurança. A segurança não deve ser uma arte obscura ou um segredo fechado que apenas alguns podem praticar. Deve ser aberto a todos e não exclusivo aos profissionais de segurança, mas também ao controle de qualidade, desenvolvedores e gerentes técnicos. O projeto para construir este guia mantém esse conhecimento nas mãos das pessoas que precisam dele - você, eu e qualquer pessoa que esteja envolvida na construção de software.

Este guia precisa chegar às mãos de desenvolvedores e testadores de software. Não há especialistas em segurança de aplicativos suficientes no mundo para fazer qualquer diferença significativa no problema geral. A responsabilidade inicial pela segurança do aplicativo precisa recair sobre os ombros dos desenvolvedores, pois eles escrevem o código. Não deveria ser uma surpresa que os desenvolvedores não estejam produzindo códigos seguros se não os estiverem testando ou considerando os tipos de bugs que introduzem vulnerabilidade.

Manter essas informações atualizadas é um aspecto crítico deste projeto. Ao adotar a abordagem wiki, a comunidade OWASP pode evoluir e expandir as informações neste guia para acompanhar a rapida evolução no cenário de ameaças à segurança de aplicativos.

Este guia é um grande testemunho da paixão e energia que nossos membros e voluntários desse projeto tem por este assunto. Certamente poderá ajudar a mudar o mundo, uma linha de código por vez.

<h2>Ajustando e priorizando</h2>

Você deveria adotar este guia em sua organização. Você pode ajustar as informações de maneira que respondam às tecnologias, processos e estrutura organizacional de sua organização.

Em geral, existem várias ocupações dentro das organizações que podem usar este guia:
<ul>
  <li>Desenvolvedores devem usar este guia para garantir que estão produzindo um código seguro. Desenvolver testes que façam parte do código normal e       dos procedimentos de testes unitários.</li>
  
  <li>Testadores e o controle de qualidade devem usar este guia para expandir o conjunto de casos de teste craidos para os aplicativos. Identificar essas   vulnerabilidades antecipadamente economiza tempo e esforços consideráveis posteriormente.</li>
  
  <li>Especialistas em segurança devem usar este guia em combinação com outras técnicas como uma forma de verificar se nenhuma falha de segurança foi       desconsiderada em um aplicativo.</li>
  
  <li>Gerentes de projeto devem considerar a razão da existência deste guia e também que os problemas de segurança são manifestados por meio de bugs no     código e no design.</li>
</ul>

A coisa mais importante a ser lembrambrada ao realizar o teste de segurança é a continua priorização. Há um número infinito de maneiras as quais um aplicativo  pode falhar, e as organizações sempre tem tempo e recursos limitados para testar. Tempo e recursos devem ser gastos com sabedoria. Tente se concentrar nas falhas de segurança que são um risco real para o seu negócio. Tente contextualizar o risco em termos do aplicativo e seus casos de uso.

Este guia é melhor visto como um conjunto de técnicas que você usa para encontrar diferentes tipos de falhas de segurança. Mas nem todas as técnicas são igualmente importantes. Tente evitar o uso do guia como uma lista de verificação, pois novas vulnerabilidades estão sempre surgindo e nenhum guia pode ser uma lista determinada de "coisas a serem testadas", todavia um grande começo.

<h2>O papel das ferramentas automatizadas</h2>

Inúmeras empresas vendem ferramentas automatizadas de análise e testes de segurança. Lembre-se das limitações dessas ferramentas e asuse apenas naquilo em que são boas. Como Michael Howard colocou na Conferência OWASP AppSec de 2006 em Seattle, "As ferramentas não tornam o software seguro! Elas ajudam a escalar o processo e a forçam a aplicação de políticas."

Ainda mais importante, essas ferramentas são genéricas - o que significa que não foram projetadas especificamente para seu código, mas para aplicativos em geral. Isso significa que, embora eles possam encontrar alguns problemas genéricos, eles não têm conhecimento suficiente do seu aplicativo,o que permitiria detectar mais falhas. Na minha experiência, os problemas de segurança mais sérios não são aqueles genéricos, mas aqueles profundamente interligados a sua lógica de negócio e ao design customizado do aplicativo.

Essas ferramentas podem ser muito úteis, pois identificam muitos potenciais problemas. Embora a execução das ferramentas não leve muito tempo, por outro lado a investigação e validação de cada um dos problemas leva tempo . Se o objetivo é encontrar e eliminar as falhas mais sérias o mais rápido possível, considere se o seu tempo será melhor gasto com ferramentas automatizadas ou com as técnicas descritas neste guia. Ainda assim, essas ferramentas certamente fazem parte de um programa equilibrado de segurança de aplicativos. Usadas com sabedoria, elas podem oferecer suporte a seus processos para produzir códigos mais seguros.

<h2>Um apelo à ação</h2>

Se você está construindo, projetando ou testando software, encorajo-o fortemente a se familializar com as orientações de teste de segurança deste documento. É um ótimo roteiro para testar os problemas mais comuns que o desenvolvimento de aplicativos enfrenta atualmente, embora não definitivo. Caso encontre erros, adicione uma nota à página de discussão ou faça a alteração você mesmo. Você estará ajudando milhares de outras pessoas que usam este guia.

Por favor <a href="https://owasp.org/membership/">junte-se a nós</a> como um membro individual ou corporativo para que possamos continuar a produzir materiais como este guia de testes e tantos outros grandes projetos da OWASP.

Obrigado aos antigos e futuros colaboradores deste guia, seu trabalho ajudará a tornar os aplicativos em todo o mundo mais seguros.

--Eoin Keary, membro gestor da OWASP,  19 de Abril de 2013.

Projeto Livre de Segurança de Aplicativos Web e OWASP são marcas registradas da OWASP Foundation, Inc.





