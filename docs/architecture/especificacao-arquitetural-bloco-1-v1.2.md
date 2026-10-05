# LanceBem

## Especificação Arquitetural Consolidada --- Bloco 1

**Produto:** LanceBem

**Descrição:** Plataforma de Leilões Comunitários

**Primeiro caso de uso:** Leilão de São Francisco

**Versão:** 1.2

**Ano:** 2026

**Status:** Candidata à validação final

**Escopo:** Blocos 1.1 a 1.9

**Implementação:** Não iniciada

## Identidade do produto

**LanceBem** é uma plataforma de leilões comunitários concebida para
atender organizações que realizam leilões com finalidade comunitária,
beneficente ou institucional.

A plataforma tem como primeiro caso real de utilização o **Leilão de São
Francisco**, no contexto de uma comunidade católica, mas sua arquitetura
não é específica de uma paróquia, igreja ou organização religiosa.

Por essa razão, o domínio técnico e a arquitetura permanecem genéricos e
multi-organização, permitindo que diferentes organizações realizem e
administrem seus próprios leilões de forma isolada.

**---**

## Autoridade do documento

Este documento consolida as decisões arquiteturais validadas nos Blocos
1.1 a 1.9 da **LanceBem --- Plataforma de Leilões Comunitários**.

Após validação humana explícita, esta especificação constituirá a **baseline
arquitetural vigente da versão 1.2** e deverá orientar as etapas
posteriores de modelagem, especificação técnica e implementação. Até essa
validação, a versão 1.1 permanece como baseline arquitetural formal vigente.

Em caso de divergência entre este documento e uma proposta técnica
posterior, a divergência deverá ser explicitamente identificada e
analisada antes de qualquer alteração da arquitetura.

Limitações de framework, biblioteca, Firebase, Google Cloud ou outra
tecnologia não autorizam a alteração silenciosa de uma regra de produto
ou decisão arquitetural validada.

Alterações futuras que modifiquem decisões deste documento deverão
resultar em revisão explicitamente validada e em nova versão da
especificação.

Esta versão foi reconciliada com a baseline conceitual validada dos Blocos 2.1 a 2.9 e 2.D. Quando uma formulação histórica da v1.1 tiver sido expressamente substituída nesta v1.2, prevalece a formulação reconciliada.

**---**

# 1. Finalidade

Este documento consolida exclusivamente as decisões arquiteturais
validadas nos Blocos **1.1 a 1.9** da Plataforma de Leilões
Comunitários.

Sua finalidade é estabelecer a arquitetura técnica de referência antes
da definição física do modelo de dados e antes do início da
implementação.

A plataforma é concebida como um sistema **multi-organização
especializado em leilões comunitários**, tendo o Leilão de São Francisco
como primeiro caso real de utilização, sem vincular tecnicamente a
arquitetura a uma única paróquia, comunidade ou edição de leilão.

Este documento:

-   consolida as decisões arquiteturais vigentes;

-   substitui formulações intermediárias superadas durante a
    especificação;

-   estabelece responsabilidades entre cliente, backend e
    infraestrutura;

-   registra invariantes que a implementação deverá preservar;

-   registra separadamente decisões ainda pendentes;

-   não define ainda a estrutura física definitiva do Firestore;

-   não constitui implementação;

-   não autoriza alteração das regras de produto já validadas.

**---**

# 2. Princípios arquiteturais

## 2.1 Mobile-first

A aplicação será desenvolvida prioritariamente para utilização em
dispositivos móveis.

A experiência deverá considerar especialmente participantes acessando o
leilão por celular, inclusive por links compartilhados por WhatsApp, QR
Code e outros meios.

O suporte a telas maiores permanece necessário, mas não determinará a
arquitetura principal da experiência pública.

## 2.2 Backend autoritativo

Operações críticas de negócio serão decididas pelo backend.

O cliente poderá:

-   apresentar informações;

-   coletar dados;

-   realizar validações preliminares de UX;

-   apresentar cálculos auxiliares;

-   enviar intenções do usuário.

O cliente não poderá determinar definitivamente:

-   validade de lance;

-   lance mínimo oficial;

-   vencedor;

-   liderança;

-   consumo de créditos;

-   saldo oficial;

-   mudança de fase do Gritador Digital;

-   encerramento de Jóia;

-   arremate;

-   autorização administrativa.

Princípio consolidado:

\> **O cliente expressa intenção; o backend decide; o Firestore
registra; o tempo real distribui; o cliente representa.**

**---**

# 3. Stack tecnológica

A arquitetura utilizará:

| Camada | Tecnologia |
|---|---|
| Front-end | Vue 3 |
| Build/dev server | Vite |
| Linguagem | JavaScript |
| Roteamento | Vue Router |
| Estado cliente | Pinia |
| Autenticação | Firebase Authentication |
| Banco operacional | Cloud Firestore |
| Backend | Cloud Functions for Firebase, 2ª geração |
| Arquivos/imagens | Cloud Storage |
| Agendamento individual | Google Cloud Tasks |
| Agendamentos globais, se necessários | Cloud Scheduler |
| Proteção de aplicação | Firebase App Check |
| Autorização de acesso direto | Firebase Security Rules |
| Desenvolvimento local | Firebase Emulator Suite |
| Hospedagem inicial | Firebase Hosting |

Cloud Scheduler não substituirá Cloud Tasks nas transições individuais
do Gritador Digital.

**---**

# 4. Arquitetura geral

A estrutura lógica será:

``` text

                       USUÁRIOS

                           │

                           ▼

                ┌────────────────────┐

                │       Vue 3        │

                │ Vite + JavaScript  │

                │ Router + Pinia     │

                └─────────┬──────────┘

                          │

                   Services / API

                 ┌────────┴─────────┐

                 │                  │

         leituras autorizadas   comandos críticos

                 │                  │

                 ▼                  ▼

            FIRESTORE        CLOUD FUNCTIONS

                 ▲                  │

                 │            autenticação

                 │            autorização

                 │            validação

                 │            transações

                 │            idempotência

                 │            auditoria

                 │                  │

                 └────────┬─────────┘

                          ▼

                     FIRESTORE

                    estado oficial

                      │       │

                      │       ▼

                      │  CLOUD TASKS

                      │       │

                      │       ▼

                      │  CLOUD FUNCTIONS

                      │

                      ▼

                   STORAGE
```

**---**

# 5. Responsabilidade das camadas

## 5.1 Views e Components

Responsáveis por:

-   apresentação;

-   interação;

-   formulários;

-   feedback visual;

-   acessibilidade;

-   captura da intenção do usuário.

Não conterão autoridade de negócio.

## 5.2 Stores e Composables

Responsáveis por:

-   coordenação do estado do cliente;

-   sessão;

-   estados de interface;

-   integração entre telas;

-   acompanhamento de estado em tempo real;

-   contagens regressivas exclusivamente visuais.

Pinia não será utilizado como substituto do estado persistente oficial.

## 5.3 Services

A comunicação com Firebase e backend deverá ser encapsulada.

Fluxo preferencial:

``` text

View / Component

      ↓

Store / Composable

      ↓

Service

      ↓

Firebase / Backend
```

Componentes não deverão espalhar acesso direto à infraestrutura.

## 5.4 Cloud Functions

Cloud Functions constituirão a autoridade para comandos críticos.

O fluxo conceitual de uma operação será:

``` text
autenticar identidade
    ↓
verificar autorização contextual
    ↓
validar entrada e realizar validações preliminares
    ↓
iniciar unidade transacional autoritativa
    ↓
reler e validar o estado mutável do qual a decisão depende
    ↓
aplicar atomicamente os efeitos essenciais de domínio
    ↓
persistir eventual intenção durável para efeito externo recuperável
    ↓
registrar auditoria essencial de forma consistente
    ↓
executar efeitos externos recuperáveis fora da transação
    ↓
retornar resultado estruturado
```

Validações preliminares podem ocorrer antes da transação para rejeitar
entradas evidentemente inválidas, mas não substituem a revalidação
transacional do estado mutável.

Logs técnicos e outros efeitos não essenciais podem ocorrer
posteriormente. Efeitos essenciais à integridade do domínio não poderão
depender de uma etapa posterior irrecuperável.

## 5.5 Firestore

Firestore será a fonte persistente oficial do estado compartilhado.

As Functions decidem alterações críticas, o Firestore registra o
resultado e listeners em tempo real distribuem o novo estado aos
clientes autorizados.

## 5.6 Cloud Tasks

Cloud Tasks será utilizado para despertar transições temporais
individuais, principalmente do Gritador Digital.

Uma Task:

-   não é autoridade;

-   não presume que a transição continua válida;

-   não encerra uma Jóia apenas porque foi executada;

-   deverá consultar o estado atual;

-   deverá validar fase, deadline e versão esperados.

**---**

# 6. Separação entre comandos e consultas

A arquitetura distinguirá conceitualmente consultas e comandos.

## 6.1 Consultas

Obtêm estado e informações que o usuário está autorizado a visualizar.

Leituras autorizadas poderão ocorrer do cliente para Firestore através
da camada de serviços, protegidas pelas regras apropriadas.

## 6.2 Comandos

Expressam intenção de alterar estado relevante.

Exemplos conceituais:

``` text

darLance

iniciarArremate

publicarJoia

publicarLeilao

provisionarCreditos
```

Comandos críticos serão processados pelo backend.

**---**

# 7. Multi-tenancy

## 7.1 Organização como fronteira lógica

A Organização será a principal fronteira lógica de autorização
operacional.

Hierarquia conceitual:

``` text

PLATAFORMA

   │

   ├── ORGANIZAÇÃO A

   │      ├── administradores

   │      ├── créditos

   │      └── leilões

   │             └── Jóias

   │

   └── ORGANIZAÇÃO B

          ├── administradores

          ├── créditos

          └── leilões

                 └── Jóias
```

Essa representação não obriga que a estrutura física do Firestore
utilize exatamente essa hierarquia.

**---**

# 8. Identidade global e autorização contextual

A identidade autenticada do usuário será global.

Entretanto:

``` text

identidade global

≠

acesso global
```

A administração será definida pela relação:

``` text

usuário ↔ organização
```

e não simplesmente por:

``` text

role = admin
```

A mesma pessoa poderá administrar mais de uma organização.

Administrar uma organização não concede acesso a outra.

O administrador da plataforma será conceitualmente distinto do
administrador de organização.

Custom Claims não serão utilizadas como única fonte de associação
dinâmica entre usuários e organizações.

**---**

# 9. Participação contextual

Uma conta global não significa participação automática em todos os
leilões.

Abrir um link público:

``` text

NÃO cria participação
```

A participação surgirá a partir de ação consciente relacionada ao
leilão.

O identificador público pertence à Participação no Leilão. Deve ser único no contexto daquele Leilão e poderá ser diferente para a mesma Pessoa em outro Leilão. A Organização permanece a fronteira de isolamento e autorização, mas não redefine o escopo conceitual do identificador público.

A atividade administrativa e operacional permanecerá isolada entre
organizações.

**---**

# 10. Isolamento de recursos

O backend não confiará em um \`organizationId\` fornecido pelo cliente
como prova de autorização.

Quando necessário, verificará a cadeia de pertencimento:

``` text

Jóia

 ↓

Leilão

 ↓

Organização
```

A mesma fronteira deverá ser respeitada por:

-   backend;

-   Security Rules;

-   Storage Rules;

-   tarefas agendadas;

-   auditoria;

-   consultas administrativas.

**---**

# 11. Superfícies de informação

A arquitetura distinguirá:

``` text

DADOS PÚBLICOS

DADOS PRIVADOS DO PARTICIPANTE

DADOS PRIVADOS DA ORGANIZAÇÃO

DADOS DA ADMINISTRAÇÃO DA PLATAFORMA
```

A privacidade não dependerá apenas de ocultar campos na interface.

Informações públicas poderão incluir, conforme as regras de produto:

-   nome da Jóia;

-   fotografia;

-   informação "Oferecida por";

-   valor/lance atual;

-   nome público de exibição do líder;

-   nome público do participante anterior;

-   valor anterior;

-   estado público do Gritador Digital.

Não serão expostos publicamente:

-   telefone;

-   nome completo privado;

-   Firebase UID;

-   códigos administrativos;

-   dados internos.

**---**

# 12. Operações críticas

Serão consideradas críticas, entre outras:

-   criação de lance;

-   alteração econômica causada por lance;

-   provisionamento de créditos;

-   estorno;

-   publicação;

-   início do Gritador Digital;

-   transições temporais;

-   arremate;

-   encerramento;

-   operações administrativas sensíveis.

Essas operações serão executadas de maneira autoritativa pelo backend.

**---**

# 13. Idempotência

Operações críticas deverão suportar repetição técnica sem repetição do
efeito.

Será utilizado conceito equivalente a:

``` text

operationId
```

Especialmente em:

-   lances;

-   consumo inicial de créditos;

-   reserva complementar de créditos;

-   acerto econômico definitivo no Arremate;

-   liberação de reserva excedente;

-   provisionamento;

-   estorno;

-   início do arremate;

-   transições temporais;

-   arremate.

Se a resposta de uma operação for perdida depois de o backend já ter
realizado a alteração, uma nova tentativa deverá permitir reconciliar o
resultado sem duplicá-lo.

**---**

# 14. Concorrência

Operações concorrentes que possam modificar o mesmo estado crítico
utilizarão mecanismos atômicos apropriados, incluindo transações quando
necessário.

Exemplo:

Dois participantes tentam simultaneamente o mesmo lance mínimo.

Não poderão existir duas confirmações autoritativas incompatíveis para a mesma versão do estado.

Uma operação poderá ser confirmada e a outra deverá receber o novo estado
aplicável. O termo vencedor fica reservado ao participante associado ao Lance vencedor após o Resultado terminal ARREMATADA.

O sistema não aumentará automaticamente o lance do segundo participante.

**---**

# 15. Regras técnicas do lance

O backend recalculará o estado autoritativo aplicável no instante da operação.

São válidas as seguintes regras:

- valores monetários da disputa são expressos sem centavos na experiência do protótipo;
- o valor inicial da Jóia não constitui Lance;
- a interface oferece incrementos predefinidos conforme a faixa vigente da disputa;
- a seleção de um incremento constitui apenas intenção de Lance;
- antes da confirmação, a interface deverá apresentar o valor absoluto que será oferecido;
- o Lance somente existe após confirmação e aceitação autoritativa do backend;
- o Lance histórico registra o valor absoluto confirmado, não apenas o incremento escolhido;
- Jóia encerrada não aceita Lance;
- estado temporal vencido deverá ser reconciliado antes da decisão.

Cada Lance confirmado pertence conceitualmente a exatamente uma Participação e a exatamente uma Jóia. A Participação e a Jóia relacionadas ao Lance deverão pertencer ao mesmo Leilão.

O Lance confirmado constitui fato histórico imutável. Mudanças posteriores de liderança, estado corrente ou Resultado não sobrescrevem nem apagam o Lance já confirmado. Eventual correção administrativa futura deverá preservar o fato original e sua rastreabilidade.

Os incrementos validados para o protótipo são organizados por faixa de valor e devem permanecer centralizados como regra de produto. Os incrementos definidos pelo LanceBem para a faixa econômica vigente da Jóia constituem o conjunto exclusivo de incrementos admissíveis para um novo Lance naquele estado.

A interface deverá apresentar somente as opções válidas para o estado vigente. No momento da confirmação, o backend deverá revalidar autoritativamente se o incremento selecionado continua pertencendo ao conjunto permitido para a faixa econômica aplicável. Qualquer tentativa de utilizar incremento diferente das opções válidas deverá ser rejeitada pelo backend, ainda que resulte em valor superior ao Lance atual ou ao incremento mínimo.

Exemplo conceitual: para Lance atual de R$ 550, estando vigentes os incrementos +R$ 10, +R$ 30 e +R$ 50, o incremento +R$ 30 é admissível e o incremento +R$ 20 é inadmissível.

O Lance histórico continuará registrando o valor absoluto autorizado e confirmado. A especificação física da tabela de faixas e incrementos permanece para a etapa técnica correspondente.

Se o estado da Jóia mudar entre a seleção e a confirmação, o sistema não aumentará, substituirá nem transformará automaticamente a intenção do participante. O backend deverá reavaliar a tentativa segundo o estado autoritativo vigente. Quando o incremento selecionado não for mais admissível ou a intenção original não puder ser confirmada contra esse estado, será necessária nova apresentação das opções e do valor aplicável, seguida de nova autorização consciente do participante.

**---**

# 16. Confirmação do backend

Uma ação no navegador não constitui lance confirmado.

O lance somente passa a existir como lance válido quando for:

``` text

recebido

   ↓

validado

   ↓

autorizado

   ↓

processado

   ↓

confirmado pelo backend
```

O cliente não poderá apresentar uma operação crítica como concluída
antes dessa confirmação.

**---**

# 17. Operações críticas offline

Não haverá fila offline para comandos críticos.

Sem conectividade, ações como:

``` text

dar lance

iniciar arremate

provisionar créditos
```

não poderão ser apresentadas como concluídas.

Se o backend tiver concluído uma operação e somente a resposta tiver
sido perdida, a idempotência permitirá recuperar seu resultado.

**---**

# 18. Tempo real

Firestore será utilizado para distribuir alterações relevantes aos
clientes conectados.

Exemplos:

``` text

novo lance

nova liderança

participante superado

mudança do Gritador

arremate
```

O cliente não precisará atualizar manualmente a página para receber o
estado atualizado.

Tempo real e notificações são conceitos distintos.

Uma notificação futura poderá alertar um participante fora da tela, mas
não constituirá fonte de verdade.

**---**

# 19. Autoridade temporal

O relógio do cliente não determinará nenhuma decisão de negócio.

O navegador poderá apresentar contagens regressivas, mas o backend
determinará:

-   início;

-   deadline;

-   expiração;

-   fase válida;

-   possibilidade de lance;

-   encerramento.

Princípio:

\> **O relógio exibido pelo navegador informa; o relógio e o estado
autoritativos do backend decidem.**

**---**

# 20. Intenção anterior ao prazo não reserva lance

Se um participante tocar em **Dar um lance** antes do deadline, mas a
solicitação chegar ao backend depois da janela válida aplicável, o
instante registrado pelo dispositivo não será utilizado como prova de
precedência.

O horário do clique e a simples chegada da requisição ao endpoint não
reservam precedência temporal.

A validade temporal do lance será determinada pelo tempo autoritativo do
backend durante a tentativa transacional que efetivamente decidir a
operação contra o estado vigente.

Em retries transacionais, estado e tempo serão novamente avaliados. Uma
repetição idempotente de uma operação já decidida recuperará seu
resultado original e não constituirá novo lance.

**---**

# 21. Estado temporal persistente

O Gritador Digital manterá informação persistente suficiente para
reconstruir sua situação.

Conceitualmente:

``` text

estadoArremate

fase

cycleVersion

faseIniciadaEm

deadline
```

Os nomes físicos poderão mudar no modelo de dados.

O requisito é preservar a semântica.

**---**

# 22. Máquina de estados do Gritador Digital

O Gritador Digital é aplicável à Jóia cuja disputa possua ao menos um Lance válido confirmado.

A máquina vigente é:

``` text
ABERTA COM LANCE VÁLIDO
  │
  │ administrador/gritador
  │ INICIAR ARREMATE
  ▼
AVISO INICIAL
1 hora
  │
  ▼
DOU-LHE UMA
10 minutos
  │
  ▼
DOU-LHE DUAS
10 minutos
  │
  ▼
DOU-LHE TRÊS
10 minutos
  │
  ▼
ARREMATADA
```

Jóia sem qualquer Lance válido confirmado não precisa iniciar o Gritador Digital. Por ação autorizada da Organização, poderá passar diretamente para **ENCERRADA SEM LANCES**.

ARREMATADA e ENCERRADA SEM LANCES são resultados terminais distintos.

# 23. Lances durante o aviso inicial

Um lance válido durante:

``` text

AVISO INICIAL
```

atualiza normalmente a disputa.

Entretanto:

``` text

NÃO reinicia

NÃO estende

NÃO substitui
```

o prazo original de uma hora.

**---**

# 24. Lances durante as chamadas finais

Qualquer lance válido durante:

``` text

DOU-LHE UMA

DOU-LHE DUAS

DOU-LHE TRÊS
```

produzirá:

``` text

novo lance

   ↓

nova versão temporal da contagem

   ↓

DOU-LHE UMA

   ↓

nova janela de 10 minutos
```

O processo não retorna ao aviso inicial de uma hora.

Não haverá limite máximo de reinícios enquanto continuarem ocorrendo
lances válidos.

**---**

# 25. Condição de arremate

Depois de iniciada a chamada final, o encerramento regular somente
ocorrerá após:

\> **30 minutos consecutivos sem novo lance válido.**

Como o Ciclo de Arremate oficial pressupõe ao menos um Lance válido confirmado, sua conclusão regular produz **ARREMATADA**. **ENCERRADA SEM LANCES** pertence ao fluxo de encerramento autorizado da Jóia sem disputa e não depende da execução das três chamadas.

Os 30 minutos são consequência de três fases consecutivas:

``` text

Dou-lhe uma   → 10 min

Dou-lhe duas  → 10 min

Dou-lhe três  → 10 min
```

Não constituem um único cronômetro de 30 minutos.

Cada chamada é um evento funcional próprio.

**---**

# 26. Dou-lhe três

A regra vigente determina explicitamente:

\> **"Dou-lhe três" não encerra imediatamente a Jóia.**

Ela possui sua própria janela de 10 minutos.

Durante essa janela ainda poderão ocorrer novos lances.

Um lance válido reinicia a contagem em **Dou-lhe uma**, dentro do mesmo Ciclo de Arremate, com nova janela de
10 minutos.

**---**

# 27. Deadline

Cada fase temporizada terá deadline oficial persistido.

A convenção será:

``` text

agora \< deadline

→ fase ainda vigente

agora \>= deadline

→ fase expirada
```

Essa convenção deverá ser utilizada também nos testes automatizados para
eliminar ambiguidades de fronteira.

**---**

# 28. Materialização atrasada

O nome da fase persistida isoladamente não determinará a validade
temporal.

Exemplo:

``` text

fase persistida:

DOU-LHE UMA

deadline:

11:10:00

tempo oficial:

11:10:03
```

A fase Dou-lhe uma já expirou, ainda que uma Task atrasada não tenha
materializado a próxima fase.

Portanto, atrasos de infraestrutura não ampliarão artificialmente as
janelas.

Em transições automáticas, o deadline da fase seguinte será derivado do
deadline lógico da fase anterior, e não do instante tardio de
materialização da transição.

Tempo lógico do evento e tempo de materialização são conceitos
distintos.

**---**

# 29. Reconciliação temporal

Antes de aceitar ou rejeitar uma operação sensível ao tempo, o backend
deverá reconciliar logicamente o estado persistido com o estado
correspondente ao instante autoritativo atual.

Essa reconciliação lógica é obrigatória para a decisão temporal. A
materialização física das transições correspondentes poderá ocorrer na
mesma operação ou por mecanismo recuperável apropriado, sem alterar a
linha temporal lógica.

Exemplo:

``` text
persistido:
DOU-LHE UMA
deadline 11:10

agora:
11:24
```

Sem novos lances, o estado lógico já corresponde à janela de **Dou-lhe
três**.

A operação deverá ser analisada a partir desse estado lógico, não
simplesmente do nome da fase ainda persistida.

A reconciliação deverá atravessar quantas fronteiras temporais já
estiverem vencidas, preservando os deadlines derivados da linha temporal
original. Somente um novo Lance válido durante uma chamada final reinicia deliberadamente a contagem em DOU-LHE UMA, com nova versão temporal e nova janela de 10 minutos, sem criar outro Ciclo de Arremate conceitual.

# 30. Expiração de Dou-lhe três

Se:

``` text
DOU-LHE TRÊS
deadline = 11:30
```

e a tentativa transacional decisória ocorrer em `now >= 11:30`, Dou-lhe três já terá expirado logicamente.

Como o Ciclo de Arremate pressupõe ao menos um Lance válido confirmado, o estado terminal regular será **ARREMATADA**, com vencedor e Lance vencedor derivados do processo autoritativo.

A simples chegada da requisição ao backend não determina sua validade temporal. O horário do clique e o instante de chegada HTTP não reservam precedência.

Não existe janela adicional após Dou-lhe três, mesmo que a Task responsável pela materialização ainda não tenha atualizado o estado persistido.

**ENCERRADA SEM LANCES** é produzida pelo encerramento autorizado de Jóia sem Lance válido, fora da necessidade de percorrer o Ciclo de Arremate.

# 31. Cloud Tasks

Toda mudança de estado do Gritador que exija processamento futuro deverá
persistir, de forma durável e atomicamente associada à própria mudança
de estado, informação suficiente para que o agendamento necessário possa
ser criado, recriado ou recuperado posteriormente.

A criação da Cloud Task ocorrerá como efeito externo idempotente
posterior. A correção do processo não dependerá do sucesso imediato
dessa chamada externa.

Cloud Tasks continuará sendo mecanismo de despertar. A Task não será a
fonte de verdade do estado nem o único registro de que existe trabalho
temporal futuro.

Ao materializar o agendamento, o backend poderá criar uma Cloud Task
para aproximadamente o deadline correspondente.

Quando executada, a Task deverá verificar, no mínimo:

-   recurso correto;

-   estado atual;

-   fase esperada;

-   versão temporal esperada;

-   deadline esperado;

-   expiração efetiva;

-   ausência de condição que tenha tornado a Task obsoleta.

Somente então poderá produzir a transição correspondente.

**---**

# 32. Tasks obsoletas

Um novo Lance durante as chamadas finais cria nova versão temporal da contagem, sem criar outro Ciclo de Arremate conceitual.

Exemplo:

``` text

versão temporal 27

DOU-LHE DUAS
```

Novo lance:

``` text

versão temporal 28

DOU-LHE UMA
```

Se uma Task referente ao versão temporal 27 executar depois, deverá reconhecer:

``` text

expectedVersion = 27

currentVersion = 28
```

e terminar sem efeito.

A correção do sistema não dependerá da capacidade de cancelar toda Task
antiga.

**---**

# 33. Idempotência das Tasks

Tasks repetidas ou reexecutadas não poderão avançar duas vezes a mesma
transição.

Uma Task que já materializou:

``` text

DOU-LHE UMA

        ↓

DOU-LHE DUAS
```

não poderá transformar uma nova tentativa automaticamente em:

``` text

DOU-LHE TRÊS
```

A execução repetida deverá ser reconhecida como já realizada ou
obsoleta.

**---**

# 34. Independência do administrador

Depois que o administrador/gritador selecionar:

``` text

INICIAR ARREMATE
```

o processo não dependerá de seu navegador permanecer aberto.

O administrador poderá:

-   fechar a página;

-   desligar o aparelho;

-   perder conexão.

O estado persistente, Cloud Functions e Cloud Tasks manterão a
continuidade.

Falha, timeout, resposta perdida, duplicação ou indisponibilidade na
criação de uma Task não poderá tornar o processo irrecuperável. O
sistema deverá conseguir detectar trabalho temporal que deixou de
progredir e recuperá-lo sem depender de participante ou administrador
conectado.

**---**

# 35. Contagem regressiva no cliente

Não serão realizadas gravações por segundo no Firestore.

O cliente receberá informações equivalentes a:

``` text

fase

deadline
```

e reconstruirá visualmente a contagem.

Não haverá:

``` text

10:00

09:59

09:58

...
```

sendo continuamente gravado no banco.

**---**

# 36. Sincronização visual

A aplicação poderá estimar a diferença entre o relógio local e uma
referência temporal do backend para melhorar a precisão visual da
contagem regressiva.

Essa estimativa terá finalidade exclusivamente de UX.

Nunca será utilizada para decidir a validade oficial de um lance.

**---**

# 37. Perda de conexão

Se o participante perder conectividade durante uma chamada, o cliente
não poderá declarar localmente:

``` text

ARREMATADA
```

apenas porque seu contador chegou a zero.

A interface deverá indicar adequadamente que o estado não está
confirmado.

Quando a conexão for restabelecida, o cliente recuperará o estado
oficial e se reconciliará com ele.

**---**

# 38. Histórico temporal e auditoria

Eventos relevantes do Gritador deverão ser rastreáveis.

Exemplos:

``` text

arremate iniciado

Dou-lhe uma

Dou-lhe duas

Dou-lhe três

reinício provocado por lance

arrematada
```

O histórico permitirá suporte e análise de eventual contestação sem
obrigar que toda informação técnica seja pública.

**---**

# 39. Características da Jóia durante o Gritador

As características utilizadas pelo Gritador Digital serão preparadas
previamente.

Uma vez iniciado o arremate, o conteúdo utilizado deverá permanecer
historicamente estável.

A direção arquitetural validada é utilizar conceitualmente um **snapshot
das características no início do arremate**.

A modelagem física será definida posteriormente.

Não haverá chat ou mensagem improvisada do gritador durante o processo
no Beta.

**---**

# 39.1 Fronteira entre Resultado, pagamento da Jóia e créditos

O Resultado terminal da Jóia dentro do LanceBem encerra-se em **ARREMATADA** ou **ENCERRADA SEM LANCES**. Pagamento da Jóia e retirada são tratados diretamente entre o vencedor e a Organização e não prolongam o ciclo de vida da Jóia dentro da plataforma.

Não fazem parte da máquina de estados da Jóia no LanceBem estados como **AGUARDANDO PAGAMENTO**, **PAGAMENTO** ou **CONCLUÍDA**.

O pagamento da Jóia pelo vencedor à Organização é conceitualmente distinto da aquisição e do uso de créditos pela Organização para utilizar o LanceBem. A arquitetura não deverá misturar esses dois fluxos econômicos nem introduzir checkout, PIX da Jóia ou liquidação da Jóia pela plataforma neste corte.

**---**

# 40. Créditos da organização

Créditos pertencem à Organização e são mantidos em sua Carteira.

Não pertencem ao administrador, ao participante nem individualmente ao Leilão. Organizações distintas não compartilham saldo, garantias ou movimentações econômicas.

**---**

# 41. Política econômica vigente do protótipo

A quantidade de créditos aplicável a uma Jóia é determinada pela faixa econômica correspondente ao valor considerado pela regra vigente:

| Valor | Créditos |
|---|---:|
| menor que R$ 200 | 1 |
| R$ 200 a R$ 499 | 2 |
| R$ 500 a R$ 999 | 3 |
| R$ 1.000 ou mais | 4 |

O teto econômico do protótipo é de 4 créditos por Jóia.

O valor monetário de aquisição de cada crédito não é congelado por esta especificação arquitetural e permanece decisão comercial separada.

Jóia sem Lance válido não produz consumo de créditos.

**---**

# 42. Primeiro Lance e consumo inicial

O primeiro Lance válido de uma Jóia produz consumo imediato dos créditos correspondentes à faixa econômica desse Lance.

Cadastro, abertura do link e criação de Participação não consomem créditos.

O Lance e sua consequência econômica essencial deverão ser tratados de forma atomicamente consistente.

**---**

# 43. Provisionamento e garantia

Provisionamento mínimo é uma projeção de capacidade econômica necessária. Não constitui, por si só, aquisição, consumo ou movimentação de saldo.

Antes do início do Ciclo de Arremate de uma Jóia com disputa, a Organização deverá possuir cobertura econômica total de 4 créditos. Essa cobertura é composta pelos créditos já consumidos no primeiro Lance válido mais a reserva complementar necessária para atingir o total de 4 créditos.

Conceitualmente:

``` text

cobertura total = créditos já consumidos + créditos reservados

cobertura exigida para iniciar o Arremate = 4 créditos
```

Assim, se 1 crédito já tiver sido consumido, reservam-se 3; se 2 tiverem sido consumidos, reservam-se 2; se 3 tiverem sido consumidos, reserva-se 1; se 4 já tiverem sido consumidos, nenhuma reserva adicional será necessária.

A garantia compromete apenas a capacidade complementar da Carteira e não equivale, por si só, a consumo definitivo. Seu objetivo é impedir que uma disputa já iniciada seja interrompida por insuficiência posterior de créditos.

Se a Carteira não possuir capacidade suficiente para completar a cobertura total de 4 créditos, o Ciclo de Arremate não será iniciado. Os Lances válidos já existentes permanecem preservados e a Jóia continua fora do Ciclo até que a cobertura possa ser constituída.

**---**

# 44. Continuidade econômica da disputa

Depois de regularmente iniciado o Ciclo de Arremate, um Lance válido não será recusado por insuficiência econômica posterior da Organização quando a cobertura exigida tiver sido previamente garantida.

A continuidade não será implementada por geração normal de dívida ou pendência automática de créditos.

**---**

# 45. Apuração econômica no resultado

Quando a Jóia terminar ARREMATADA, o custo definitivo será recalculado pela faixa correspondente ao valor final do Lance vencedor.

O consumo definitivo deverá refletir esse custo. Qualquer capacidade garantida além do necessário será liberada para a Carteira da Organização.

Quando a Jóia terminar ENCERRADA SEM LANCES, não haverá consumo de créditos associado à Jóia.

**---**

# 46. Política econômica e rastreabilidade

A política econômica deverá permanecer centralizada e versionável para permitir rastreabilidade histórica. A versão aplicável não poderá ser alterada retroativamente durante uma disputa em andamento.

Versionamento técnico da política não autoriza modificar silenciosamente as regras econômicas validadas nesta baseline.

**---**

# 47. Movimentação de créditos

Saldo atual não substituirá o histórico das operações que produziram a posição econômica da Carteira.

As movimentações deverão permitir rastrear, conforme o fato econômico aplicável, aquisição, comprometimento/garantia, liberação, consumo e eventual ajuste autorizado futuramente.

Provisionamento, por ser projeção, não deverá ser confundido com uma movimentação positiva de créditos.

Compensações ou ajustes futuros não apagarão o fato histórico original.

**---**

# 48. Publicação

Mudanças relevantes de estado, como publicação de Leilão ou Jóia, serão
tratadas como transições explícitas.

O cliente não alterará diretamente estados críticos simplesmente
gravando documentos no Firestore.

**---**

# 49. Erros de domínio

O backend retornará erros estruturados que permitam ao front-end
produzir mensagens contextuais.

Exemplos conceituais:

``` text

LANCE_ABAIXO_DO_MINIMO

JOIA_ENCERRADA

ESTADO_DESATUALIZADO

NAO_AUTORIZADO

COBERTURA_INSUFICIENTE_PARA_INICIAR_ARREMATE
```

Os nomes definitivos serão estabelecidos na implementação.

A camada visual será responsável por traduzir esses erros para linguagem
compreensível.

**---**

# 50. Auditoria

A profundidade da auditoria será proporcional ao risco da operação.

Operações especialmente relevantes incluem:

-   administração de organização;

-   provisionamento;

-   estorno;

-   consumo de créditos;

-   lance;

-   início do Gritador;

-   transições;

-   arremate;

-   encerramentos;

-   correções administrativas futuras.

Logs técnicos e histórico de domínio não deverão ser confundidos como
uma única estrutura.

**---**

# 51. Ambientes

A arquitetura terá:

``` text

LOCAL

   ↓

Firebase Emulator Suite

HOMOLOGAÇÃO

   ↓

projeto Firebase próprio

PRODUÇÃO

   ↓

projeto Firebase próprio
```

Produção não será utilizada como ambiente de desenvolvimento.

**---**

# 52. Separação de homologação e produção

Homologação e produção terão infraestrutura separada para evitar mistura
de:

-   Firestore;

-   Authentication;

-   Storage;

-   Functions;

-   configurações;

-   dados operacionais.

Não serão utilizadas coleções de "teste" dentro da produção como
substituto dessa separação.

**---**

# 53. Configuração explícita de ambiente

O ambiente será definido por configuração.

Não dependerá exclusivamente de heurísticas como:

``` text

localhost = desenvolvimento

outro domínio = produção
```

Branches Git e ambientes de infraestrutura continuarão sendo conceitos
distintos.

**---**

# 54. Variáveis e segredos

Configurações do SDK Firebase utilizadas pelo navegador poderão existir
na configuração do front-end conforme o modelo de segurança do Firebase.

Credenciais realmente sensíveis não poderão ser incluídas:

-   no bundle do navegador;

-   no código versionado;

-   em arquivos \`.env\` commitados.

Quando necessário, serão utilizados mecanismos apropriados de gestão de
segredos.

**---**

# 55. Desenvolvimento local

O Firebase Emulator Suite será utilizado prioritariamente no
desenvolvimento.

O objetivo é permitir testes de:

-   autenticação;

-   Firestore;

-   Functions;

-   Storage;

-   permissões;

-   concorrência;

-   créditos;

-   Gritador Digital;

sem interferir em produção.

**---**

# 56. Autenticação em testes

Desenvolvimento e homologação deverão privilegiar mecanismos de teste do
Firebase Authentication quando disponíveis, evitando disparos
desnecessários de SMS reais.

Produção utilizará o fluxo real correspondente.

**---**

# 57. Tempos reduzidos em testes

Testes do Gritador Digital não precisarão esperar:

``` text

1 hora + 30 minutos
```

A arquitetura permitirá utilizar tempos reduzidos exclusivamente em
ambientes/testes controlados.

Isso não constitui personalização da regra do produto.

Produção manterá:

``` text

1 hora

10 minutos

10 minutos

10 minutos
```

**---**

# 58. Proibição de bypass de produção

Não serão utilizados mecanismos ocultos como:

``` text

?skipTimer=true
```

ou usuários especiais capazes de ignorar regras de negócio em produção.

A testabilidade deverá resultar da arquitetura e da separação dos
ambientes.

**---**

# 59. Repositório

A implementação inicial utilizará um único repositório.

A estrutura conceitual separará:

``` text

front-end

backend

regras Firebase

testes

documentação

configuração
```

Não há decisão de separar o produto em múltiplos repositórios nesta
etapa.

**---**

# 60. Organização conceitual do front-end

A direção arquitetural inclui responsabilidades equivalentes a:

``` text

src/

├── assets/

├── components/

├── views/

├── router/

├── stores/

├── composables/

├── services/

├── domain/

└── utils/
```

Os nomes físicos não estão congelados.

A responsabilidade das camadas está.

**---**

# 61. Organização conceitual do backend

A direção inclui módulos de domínio equivalentes a:

``` text

functions/src/

├── bids/

├── auctions/

├── joias/

├── organizations/

├── participants/

├── credits/

├── auctionClosing/

└── shared/
```

A área compartilhada poderá concentrar:

-   autenticação;

-   autorização;

-   validação;

-   idempotência;

-   erros;

-   auditoria;

-   tempo autoritativo;

-   ferramentas transversais.

**---**

# 62. Configurações centralizadas

Não serão espalhados pelo código números mágicos relativos a:

-   faixas econômicas de créditos;

-   quantidades de créditos;

-   opções de incremento de Lance;

-   duração do aviso inicial;

-   duração das chamadas.

A política comercial será centralizada e versionada.

Os parâmetros do Gritador serão centralizados.

No Beta, organizações não poderão personalizar livremente os tempos do
Gritador.

**---**

# 63. Timestamps e fuso horário

Instantes críticos serão armazenados como timestamps absolutos.

Não serão persistidos como autoridade textos equivalentes a:

``` text

27/09/2026 15:30
```

A apresentação fará a conversão apropriada para o fuso aplicável.

**---**

# 64. Região da infraestrutura

Serviços fortemente relacionados deverão ser implantados em regiões
compatíveis e, quando possível, próximas entre si e adequadas ao público
brasileiro.

A região exata será definida antes do provisionamento definitivo da
infraestrutura.

**---**

# 65. Dados de homologação

Dados pessoais reais não serão copiados indiscriminadamente de produção
para homologação.

Serão utilizados:

-   dados fictícios;

-   ou dados adequadamente anonimizados quando houver necessidade
    justificada.

**---**

# 66. Evolução para CI/CD

A arquitetura deverá permitir evolução futura para um fluxo semelhante
a:

``` text

Git

 ↓

testes

 ↓

build

 ↓

deploy de homologação

 ↓

validação

 ↓

deploy de produção
```

Não é requisito implantar uma infraestrutura sofisticada de CI/CD na
primeira versão.

**---**

# 67. Nomenclatura de domínio

A implementação utilizará nomenclatura técnica suficientemente genérica
para o modelo multi-organização.

Conceitos incluem:

``` text

organization

auction

joia

participant

bid

credit

creditMovement

auctionClosing
```

O código não será estruturado em torno de uma única organização, cidade
ou leilão específico.

Isso não elimina a linguagem especializada do produto, como:

-   Jóia;

-   Dar um lance;

-   Gritador Digital;

-   Dou-lhe uma;

-   Dou-lhe duas;

-   Dou-lhe três.

**---**

# 68. UX/UI temporal --- requisito arquitetural

A representação do Gritador Digital não poderá limitar-se à exibição da
frase:

``` text

DOU-LHE UMA
```

ou:

``` text

DOU-LHE TRÊS
```

O participante precisa compreender intuitivamente:

1\. em qual chamada se encontra;

2\. quanto tempo ainda resta;

3\. se ainda é possível dar um lance;

4\. qual ação deve executar;

5\. o que acontecerá quando o prazo terminar.

**---**

# 69. Representação gráfica do tempo

Cada uma das fases:

``` text

DOU-LHE UMA

DOU-LHE DUAS

DOU-LHE TRÊS
```

deverá possuir representação visual do progresso de sua janela de 10
minutos.

A forma definitiva não está estabelecida.

Poderão ser pesquisadas e prototipadas soluções como:

-   barra de progresso;

-   indicador circular;

-   etapas conectadas;

-   combinação de etapas e contador;

-   outra representação demonstrada como mais compreensível.

A arquitetura **não determina antecipadamente qual componente gráfico
será utilizado**.

**---**

# 70. Informação textual do tempo

O gráfico não será a única forma de comunicação.

A interface deverá apresentar também informação textual equivalente a:

``` text

Dou-lhe três

Última chamada

03:42 restantes

Ainda dá tempo de dar seu lance
```

enquanto a janela estiver efetivamente vigente.

Cor, animação ou posição gráfica não serão utilizadas isoladamente para
transmitir informação essencial.

**---**

# 71. Percepção do ciclo completo

A experiência deverá permitir que o participante compreenda não apenas o
progresso dentro dos dez minutos atuais, mas também sua posição na sequência de chamadas da Jóia que possui disputa:

``` text
JÓIA COM LANCE VÁLIDO
        ↓
CICLO DE ARREMATE
        ↓
UMA → DUAS → TRÊS
        ↓
ARREMATADA
```

A Jóia sem qualquer Lance válido segue fluxo distinto e não precisa percorrer o Ciclo de Arremate:

``` text
JÓIA SEM LANCE VÁLIDO
        ↓
ação autorizada da Organização
        ↓
ENCERRADA SEM LANCES
```

Particular atenção será dada à terceira chamada. Enquanto Dou-lhe três
estiver vigente, ainda será possível dar um Lance válido.

# 72. Reinício visual após novo lance

Quando um lance válido ocorrer durante qualquer chamada final, a
interface deverá refletir claramente:

``` text

novo lance

   ↓

retorno para DOU-LHE UMA

   ↓

reinício da contagem dentro do mesmo Ciclo de Arremate

   ↓

nova janela de 10 minutos
```

A mudança não deverá parecer erro, retrocesso acidental ou falha do
contador.

**---**

# 73. Indicador visual não é autoridade

A barra, círculo, contador ou qualquer outro componente temporal
continuará sendo representação.

Se houver divergência entre:

``` text

animação local
```

e:

``` text

estado confirmado pelo backend
```

prevalecerá o backend.

O componente visual jamais encerrará uma Jóia.

**---**

# 74. UX informativa, não persuasiva

A apresentação temporal terá finalidade de orientação.

Não utilizará mecanismos de pressão artificial ou gamificação como
fundamento da interação.

A experiência deverá priorizar:

-   clareza;

-   previsibilidade;

-   confiança;

-   compreensão;

-   baixa carga cognitiva;

-   prevenção de erros.

**---**

# 75. Pesquisa de UX/UI obrigatória antes do congelamento da interface

A solução visual definitiva do participante não será escolhida apenas
por preferência estética.

Antes de congelar a interface, haverá etapa específica de pesquisa e
validação de UX/UI considerando:

-   experiências digitais temporizadas;

-   interfaces de leilão pertinentes;

-   padrões de countdown e progress indicators;

-   acessibilidade;

-   mobile-first;

-   diferentes níveis de familiaridade digital;

-   legibilidade;

-   tamanho e posicionamento das ações;

-   comunicação de mudanças em tempo real;

-   liderança e superação de lance;

-   compreensão das três chamadas;

-   reinício da contagem em UMA;

-   perda e recuperação de conexão;

-   prevenção de erro;

-   redução de carga cognitiva.

**---**

# 76. Validação por protótipo e compreensão

A pesquisa deverá ser complementada por prototipação e testes de
compreensão.

Questões fundamentais incluem:

``` text

Você ainda pode dar um lance?

Quanto tempo falta?

Em qual chamada estamos?

O que acontece quando esse tempo terminar?

Onde você apertaria para dar um lance?
```

A interface deverá buscar permitir que essas respostas sejam obtidas
pela própria experiência, sem necessidade de explicação externa.

**---**

# 77. Invariantes arquiteturais

A implementação deverá preservar, no mínimo, os seguintes invariantes:

1\. O backend é autoridade das operações críticas.

2\. O cliente envia intenção, não resultado.

3\. Firestore mantém o estado persistente oficial.

4\. O relógio do cliente nunca determina validade temporal.

5\. Operações críticas concorrentes utilizam mecanismos atômicos
apropriados.

6\. Operações críticas repetíveis são idempotentes.

7\. Administrar uma organização não concede autoridade sobre outra.

8\. Informação privada não é protegida apenas pela interface.

9\. Todo Lance confirmado deverá resultar de um dos incrementos oficialmente admissíveis para o estado vigente da Jóia no momento da confirmação, além de satisfazer as demais regras autoritativas de validade.

10\. Lances utilizam reais inteiros.

11\. O sistema nunca aumenta automaticamente o valor autorizado pelo
participante.

12\. Lance durante o aviso inicial não reinicia a hora.

13\. Lance durante qualquer chamada final reinicia a contagem em Dou-lhe uma, dentro do mesmo Ciclo de Arremate.

14\. O arremate requer três janelas consecutivas de 10 minutos sem novo
lance.

15\. Dou-lhe três possui sua própria janela de 10 minutos.

16\. Task obsoleta não altera estado.

17\. Task repetida não repete transição.

18\. Atraso da Task não amplia prazo.

19\. Cliente offline não determina resultado.

20\. Administrador desconectado não interrompe o Gritador.

21\. Crédito pertence à Organização e à sua Carteira.

22\. O primeiro Lance válido consome imediatamente os créditos correspondentes à sua faixa econômica.

23\. Antes do Ciclo de Arremate, a cobertura necessária até o teto de 4 créditos deverá estar garantida.

24\. Garantia compromete capacidade econômica, mas não equivale a consumo definitivo.

25\. Disputa com cobertura regularmente garantida não é interrompida por insuficiência posterior de créditos.

26\. O custo definitivo de Jóia ARREMATADA decorre da faixa do valor final, com liberação do excedente garantido.

27\. Jóia ENCERRADA SEM LANCES não produz consumo de créditos.

28\. Movimentações econômicas são rastreáveis e não apagam os fatos que as originaram.

29\. Homologação e produção permanecem isoladas.

30\. Indicadores temporais do cliente são informativos, não
autoritativos.

31\. A fase e o tempo restante devem ser compreensíveis também sem
depender exclusivamente de cor ou gráfico.

32\. Enquanto Dou-lhe três estiver vigente, a interface deverá comunicar
claramente que ainda é possível dar lance.

33. A necessidade de processamento temporal futuro será persistida de
    forma durável e atomicamente associada à mudança de estado que a
    originou.

34. Falha na criação imediata de Cloud Task não poderá tornar o Gritador
    irrecuperável.

35. A validade temporal de um lance será decidida pelo tempo
    autoritativo da tentativa transacional que efetivamente decidir a
    operação.

36. Horário do clique e simples chegada HTTP não reservam precedência.

37. Retry transacional reavalia estado e tempo; retry idempotente de
    operação já decidida recupera o resultado original.

38. Deadlines automáticos derivam do deadline lógico anterior, não do
    instante de materialização.

39. Reconciliação atrasada atravessa todas as fronteiras temporais já
    vencidas sem ampliar prazos.

40. Task atrasada e Task obsoleta são conceitos distintos.

41. O Ciclo de Arremate pressupõe ao menos um Lance válido confirmado; Jóia sem Lance válido poderá ser encerrada diretamente por ação autorizada como ENCERRADA SEM LANCES.

42. ENCERRADA SEM LANCES decorre do encerramento autorizado de Jóia sem Lance válido e não exige expiração de Dou-lhe três.

43. ENCERRADA SEM LANCES não possui vencedor, lance vencedor nem valor
    de arremate.

44. O valor inicial não constitui lance nem se converte automaticamente
    em valor de arremate.

45. Jóia sem lance válido não reconhece créditos por faixa.

46. Confirmação histórica de lance, liderança atual e vitória são
    conceitos distintos.

47. Estado operacional, histórico de domínio, auditoria e logs técnicos
    possuem responsabilidades distintas.

48. Estado corrente não deverá acumular estruturas históricas
    ilimitadas.

49. Autorização será contextual e não decorrerá apenas de autenticação
    ou identificadores fornecidos pelo cliente.

50. Otimizações de escala não serão introduzidas sem necessidade
    demonstrada.

**---**

# 78. Decisões anteriores expressamente superadas

Para evitar reintrodução acidental de regras antigas, ficam registradas
duas substituições.

## 78.1 Regra superada --- encerramento em Dou-lhe três

Regra anterior:

``` text
Dou-lhe três encerra imediatamente a Jóia.
```

**Não vigente.**

Regra vigente para Jóia com disputa:

``` text
DOU-LHE TRÊS
     ↓
10 minutos
     ↓
ARREMATADA
```

Dou-lhe três possui sua própria janela temporal. Como o Ciclo de Arremate pressupõe ao menos um Lance válido confirmado, sua conclusão regular produz ARREMATADA. Jóia sem Lance válido utiliza o fluxo distinto de encerramento autorizado como ENCERRADA SEM LANCES e não precisa percorrer o Ciclo.

## 78.2 Regras econômicas superadas

Permanecem expressamente não vigentes:

``` text
crédito único fixo por Jóia

política progressiva 1 / 2 / 3 / 5 / 7 / 10

cobrança incremental automática a cada mudança de faixa durante a disputa

pendência automática de créditos como mecanismo normal de continuidade

provisionamento tratado como entrada positiva de saldo
```

A regra vigente utiliza faixas **1 / 2 / 3 / 4**, consumo inicial no primeiro Lance válido, garantia de cobertura antes do Ciclo de Arremate e apuração definitiva pela faixa do valor final, com liberação do excedente garantido.

Essas regras superadas não deverão reaparecer na implementação, documentação técnica ou instruções ao Codex.

**---**

# 79. Pendências abertas

As questões abaixo permanecem deliberadamente fora da decisão
arquitetural consolidada e deverão ser resolvidas nas etapas adequadas.

## 79.1 Identidade e segurança

-   método definitivo de autenticação dos administradores;

-   política de recuperação de conta;

-   política de retenção de dados pessoais.

## 79.2 Organização

-   fluxo definitivo de criação/onboarding da organização;

-   eventual aprovação da organização pela plataforma;

-   estrutura definitiva de slug/URLs;

-   limites finais da personalização visual.

## 79.3 Participantes

-   quantidade definitiva de dígitos do sufixo do código amigável;

-   tratamento definitivo de colisões de nomes públicos de exibição.

## 79.4 Lances

-   comportamento quando o líder atual quiser aumentar o próprio lance;

-   eventual teto de valor ou proteção contra erros graves de digitação;

-   representação monetária interna definitiva: reais inteiros ou
    centavos inteiros.

## 79.5 Administração

-   política de correção e cancelamento administrativo;

-   possibilidade e regras para cancelar um arremate já iniciado;

-   poderes completos do administrador da plataforma.

## 79.6 Créditos e comercialização

-   valor monetário definitivo do crédito;

-   pacotes comerciais de créditos;

-   mecanismo de compra/pagamento dos créditos;

-   bônus promocionais futuros;

-   regras comerciais de suspensão;

-   procedimentos administrativos futuros de aquisição, ajuste e compensação, se necessários.

## 79.7 Notificações

-   mecanismo técnico definitivo;

-   canais utilizados;

-   preferências do participante;

-   tratamento de permissões e opt-in.

## 79.8 Dados e infraestrutura

-   estrutura física definitiva das coleções Firestore;

-   índices;

-   estratégia física de documentos públicos e privados;

-   região definitiva de Firestore, Functions, Tasks e demais serviços;

-   política de backup;

-   retenção de logs.

## 79.9 Transição operacional

-   relação entre WhatsApp e plataforma durante eventual período de
    migração;

-   fonte oficial durante transição;

-   eventual tratamento de lances existentes no WhatsApp.

## 79.10 Jóia e Gritador

-   modelagem física do snapshot das características;

-   representação técnica definitiva dos dois propósitos conceituais já validados: característica curta e descrição do Gritador, sem fundi-los nem reabrir sua distinção funcional;

-   detalhes concretos da implementação de Cloud Tasks;

-   estrutura física do mecanismo durável de recuperação do agendamento,
    preservando os requisitos do Bloco 1.9.1.

## 79.11 UX/UI

-   componente gráfico definitivo das janelas de 10 minutos;

-   representação definitiva da sequência Uma → Duas → Três;

-   comportamento visual preciso de reconexão;

-   protótipos;

-   pesquisa comparativa;

-   testes de compreensão e usabilidade;

-   sistema visual definitivo.

Essas pendências não invalidam a arquitetura consolidada.

**---**

# 80. Limites desta especificação

Esta versão **não autoriza ainda a implementação**.

Ela também não define:

-   esquema físico definitivo do Firestore;

-   componentes Vue definitivos;

-   endpoints/Functions definitivos;

-   nomes finais dos arquivos;

-   Security Rules completas;

-   Storage Rules completas;

-   índices;

-   contratos de dados definitivos;

-   implementação das transações;

-   código das Tasks;

-   layout definitivo;

-   identidade visual definitiva;

-   CI/CD completo.

Essas decisões serão tomadas nas etapas correspondentes.

**---**

# 81. Critério para propostas técnicas futuras

Qualquer proposta técnica produzida posteriormente deverá ser avaliada
em três dimensões distintas:

``` text

REGRAS DE PRODUTO VALIDADAS

          │

          │ não podem ser alteradas

          │ silenciosamente

          ▼

ARQUITETURA VALIDADA

          │

          │ deve ser preservada,

          │ salvo revisão explícita

          ▼

IMPLEMENTAÇÃO
```

Uma limitação de Firebase, Firestore, Cloud Functions, Cloud Tasks ou
outra tecnologia não deverá ser utilizada para modificar silenciosamente
uma regra funcional validada.

Se surgir incompatibilidade técnica real, ela deverá ser apresentada
explicitamente para nova decisão.

**---**

# 82. Governança desta especificação

Esta especificação deverá permanecer versionada no repositório.

A versão 1.0 permanece preservada como baseline arquitetural anterior à
auditoria técnica.

A versão 1.2 é candidata a constituir a nova baseline arquitetural após sua validação humana explícita. Até essa validação, a versão 1.1 permanece como baseline arquitetural formal vigente.

Correções meramente editoriais que não alterem significado poderão ser
tratadas como manutenção documental.

Qualquer alteração que modifique:

-   regra arquitetural;

-   responsabilidade entre camadas;

-   autoridade de operação;

-   isolamento multi-tenant;

-   comportamento temporal;

-   política econômica;

-   requisito estrutural de segurança;

-   invariante;

deverá ser explicitamente analisada e validada antes de ser incorporada
a uma nova versão.

A evolução deverá preservar rastreabilidade, por exemplo:

``` text

v1.0

  ↓

mudança proposta

  ↓

análise de impacto

  ↓

validação

  ↓

v1.1 ou versão superior
```

**---**

# 83. Status da versão

**Documento:** Especificação Arquitetural Consolidada --- Bloco 1

**Versão:** 1.2

**Ano:** 2026

**Abrangência:** Blocos 1.1 a 1.9

**Situação:** Candidata à validação final

**Implementação:** Não iniciada

A versão 1.2 preserva as decisões arquiteturais compatíveis da versão 1.1 e incorpora a reconciliação com a baseline conceitual vigente, incluindo:

-   backend autoritativo;

-   arquitetura Vue 3 + Firebase/Google Cloud;

-   multi-tenancy;

-   isolamento organizacional;

-   concorrência e idempotência;

-   tempo real;

-   autoridade temporal;

-   Gritador Digital com **1 hora + 10 + 10 + 10 minutos**;

-   reinício em Dou-lhe uma diante de qualquer lance nas três chamadas
    finais;

-   Cloud Tasks com validação de versão, fase e deadline;

-   política econômica de créditos **1 / 2 / 3 / 4**, com teto de 4 créditos por Jóia;

-   consumo inicial no primeiro Lance válido, garantia antes do Ciclo e apuração definitiva no resultado;

-   política econômica versionada;

-   ambientes separados;

-   requisito formal de pesquisa, prototipação e validação UX/UI;

-   representação gráfica e textual do tempo restante do Gritador.

Entre as decisões preservadas da versão 1.1, permanecem vigentes:

-   recuperação durável da necessidade de processamento temporal;
-   autoridade temporal da tentativa transacional decisória;
-   distinção entre instante decisório e timestamp persistido;
-   deadlines automáticos derivados da linha temporal lógica;
-   reconciliação lógica obrigatória antes de decisões temporais;
-   encerramento distinguindo **ARREMATADA** de **ENCERRADA SEM
    LANCES**;
-   contrato de idempotência por identidade de intenção;
-   autorização contextual;
-   testabilidade determinística das invariantes temporais;
-   distinção entre confirmação histórica, liderança atual e vitória;
-   separação conceitual entre estado operacional, histórico de domínio,
    auditoria e logs técnicos;
-   análise explícita de contenção na Jóia e na carteira de créditos da
    organização.

**---**

# 84. Próxima etapa

A auditoria técnica pré-implementação da versão 1.0 foi concluída e
avaliada humanamente.

Os achados A01 e A02, a lacuna de encerramento sem lances e os
requisitos B01 a B07 foram tratados e validados nos Blocos 1.9.1 a
1.9.5.

O **Bloco 2 --- Domínio e Modelo de Dados** já produziu baseline conceitual validada nos Blocos 2.1 a 2.9 e 2.D. A próxima etapa deverá partir conjuntamente desta arquitetura reconciliada e dessa baseline conceitual, sem reabrir decisões já validadas.

Esta reconciliação não autoriza implementação. As etapas seguintes deverão transformar as duas baselines compatíveis em especificação técnica e modelo físico de dados, preservando as pendências que ainda exigem decisão explícita.

------------------------------------------------------------------------

# 85. Revisão pós-auditoria --- Etapa 1.9

A Etapa 1.9 incorpora exclusivamente decisões validadas após a auditoria
técnica da versão 1.0.

Ela preserva a stack tecnológica, o modelo multi-organização, as durações do Gritador Digital e os contratos pós-auditoria que permanecem compatíveis. Regras de produto posteriormente superadas pela baseline conceitual são reconciliadas pela versão 1.2.

## 85.1 Bloco 1.9.1 --- Recuperação durável do agendamento

Toda transição que exija processamento futuro deverá deixar persistida
informação durável suficiente para permitir criação, recriação ou
recuperação do agendamento necessário.

A mudança de estado e o registro da necessidade futura deverão ser
atomicamente consistentes quando forem consequência da mesma decisão.

Cloud Tasks será efeito externo idempotente e mecanismo de despertar,
não autoridade do estado.

Falha antes ou depois da criação da Task, resposta perdida, duplicação,
substituição de versão temporal ou indisponibilidade não poderão interromper
definitivamente o processo.

Tasks repetidas ou obsoletas deverão ser inofensivas. Recuperação tardia
nunca ampliará nem reiniciará prazo lógico já transcorrido.

A forma física desse mecanismo, incluindo eventual padrão equivalente a
outbox, permanece decisão do Bloco 2 e da especificação técnica
posterior.

## 85.2 Bloco 1.9.2 --- Contrato temporal autoritativo dos lances

A validade temporal de um lance será determinada pelo tempo autoritativo
do backend durante a tentativa transacional que efetivamente decidir a
operação contra o estado vigente.

O horário do dispositivo, o instante do clique e a simples chegada HTTP
não reservam posição ou precedência.

Em retry transacional, estado e tempo deverão ser novamente avaliados.
Uma tentativa abortada não concede direito temporal à tentativa
posterior.

O instante autoritativo utilizado para decidir temporalmente a operação
e o timestamp posteriormente persistido para registrar o evento são
conceitos distintos. Timestamp de gravação ou materialização não deverá
ser interpretado automaticamente como o instante que decidiu a validade
temporal do lance.

Antes da decisão, o backend deverá reconciliar o estado lógico
correspondente ao instante autoritativo.

A convenção permanece:

``` text
now < deadline
→ fase vigente

now >= deadline
→ fase vencida
```

A expiração de UMA ou DUAS poderá levar a outra fase que ainda aceita
lance. A expiração de TRÊS leva ao estado terminal aplicável e não
permite reabertura por novo lance.

Retry com o mesmo `operationId` de operação já decidida recuperará o
resultado original. Novo `operationId` representa nova intenção.

## 85.3 Bloco 1.9.3 --- Deadlines lógicos e reconciliação atrasada

Transições automáticas preservarão a linha temporal lógica original.

O deadline de uma fase automática será derivado do deadline lógico
anterior, nunca do instante atrasado em que a infraestrutura
materializou a transição.

A reconciliação atravessará todas as fronteiras temporais vencidas até
alcançar o estado correspondente ao tempo autoritativo atual.

Uma única execução técnica poderá materializar múltiplas transições
lógicas. As transições intermediárias continuarão conceitualmente
existentes e rastreáveis.

Somente um Lance válido durante UMA, DUAS ou TRÊS reinicia a contagem em UMA, cria nova versão temporal e nova janela de dez minutos baseada no instante autoritativo da decisão desse Lance, sem criar outro Ciclo de Arremate conceitual.

Lance durante o aviso inicial não altera seu deadline.

Task antecipada não poderá transicionar antes do prazo nem eliminar a
necessidade futura de processamento.

Task atrasada poderá servir como gatilho de reconciliação. Task
pertencente a versão temporal substituída será obsoleta e terminará sem efeito.

Indisponibilidade técnica não congela nem reinicia o relógio lógico do
leilão.

## 85.4 Bloco 1.9.4 --- Jóia encerrada sem lances, interpretação reconciliada

A distinção entre **ARREMATADA** e **ENCERRADA SEM LANCES** permanece vigente.

A baseline conceitual posterior refinou o caminho aplicável: Jóia sem qualquer Lance válido confirmado poderá ser encerrada diretamente por ação autorizada da Organização, sem necessidade de iniciar ou percorrer o Ciclo de Arremate.

Nesse resultado não existirão vencedor, Lance vencedor ou valor de arremate. O valor inicial não será convertido em valor de arremate e não haverá consumo de créditos associado à Jóia.

Tentativas rejeitadas não contam como Lance válido.

Existindo ao menos um Lance válido confirmado, o processo de conclusão oficial ocorre pelo Ciclo de Arremate e sua conclusão regular produz **ARREMATADA**.

ENCERRADA SEM LANCES será terminal no fluxo normal. Eventual reabertura futura exigirá operação administrativa explicitamente definida e auditável.

O resultado deverá permanecer explicitamente distinguível no histórico do Leilão e na superfície pública. Essa exigência não define layout, componente visual ou estrutura física de persistência.

## 85.5 Bloco 1.9.5 --- Requisitos B01 a B07

Os achados B01 a B07 passam a orientar obrigatoriamente a modelagem e as
etapas posteriores.

### B01 --- Unidade transacional

Dados mutáveis necessários à validade de uma operação crítica deverão
ser avaliados na unidade transacional autoritativa. Efeitos essenciais
de domínio deverão ser atomicamente consistentes ou possuir recuperação
durável quando atravessarem sistemas externos.

### B02 --- Idempotência

A identidade de uma operação crítica representará uma única intenção de
negócio, vinculada ao autor, tipo de comando, recurso e parâmetros
relevantes. Reutilização incompatível será rejeitada. Retry legítimo
recuperará o resultado original sem repetir efeitos.

### B03 --- Autorização

Autorização será contextual e resultará da identidade, relação ou papel
autorizado, organização, recurso e operação. Login, URL, slug ou
identificador enviado pelo cliente não concedem autoridade isoladamente.
O modelo deverá permitir matriz verificável de permissões e testes de
isolamento.

### B04 --- Testabilidade temporal

As invariantes temporais do Gritador deverão ser testáveis
deterministicamente sem esperas reais. Tempos reduzidos serão exclusivos
de testes controlados e não constituirão bypass de produção.

### B05 --- Confirmação e atualidade

Confirmação histórica de uma operação e estado corrente do recurso serão
conceitos distintos.

O cliente deverá ser capaz de representar conceitualmente, sem congelar
componentes visuais, pelo menos os estados:

-   enviando;
-   confirmado;
-   rejeitado;
-   resultado desconhecido;
-   sincronizando.

Lance confirmado não implica liderança atual. Liderança atual não
implica vitória. Vitória somente poderá decorrer do resultado oficial de
encerramento persistido pelo backend.

Quando a resposta de uma operação crítica não puder ser determinada
imediatamente pelo cliente, a interface deverá representar
explicitamente a incerteza e reconciliar o resultado com o backend, sem
assumir confirmação ou rejeição.

### B06 --- Observabilidade

Estado operacional, histórico de domínio, trilha de auditoria e logs
técnicos terão responsabilidades conceitualmente distintas. Evidência
necessária à integridade e rastreabilidade do negócio não dependerá
exclusivamente de logs técnicos.

### B07 --- Contenção e consultas

O modelo deverá evitar estruturas históricas ilimitadas no estado
operacional corrente, identificar padrões principais de consulta e
concorrência e permitir paginação e listeners limitados.

A análise de concorrência deverá considerar explicitamente, no mínimo:

-   a Jóia, como ponto de contenção de lances concorrentes e atualização
    de seu estado corrente;
-   a carteira de créditos da organização, que poderá ser compartilhada
    por operações originadas em diferentes Jóias da mesma organização.

Índices serão derivados das consultas definidas.

Particionamento, sharding, cache distribuído, múltiplos bancos,
múltiplas filas ou outras otimizações de escala dependerão de
necessidade demonstrada e não serão antecipados por esta especificação.

------------------------------------------------------------------------

# 86. Efeito normativo da revisão 1.2

A versão 1.2 é candidata a substituir a versão 1.1 como especificação arquitetural vigente. Essa substituição somente produzirá efeito após validação humana explícita desta revisão.

Até essa validação, a versão 1.1 permanece como baseline arquitetural formal vigente. Após a validação da v1.2, as versões 1.0 e 1.1 permanecerão preservadas no histórico para rastreabilidade.

A versão 1.2 não reabre decisões compatíveis já validadas. Ela reconcilia explicitamente as formulações da v1.1 que foram superadas ou refinadas pela baseline conceitual validada dos Blocos 2.1 a 2.9 e 2.D.

Em caso de conflito entre formulação histórica e esta versão, prevalece a formulação reconciliada da v1.2.

As decisões pós-auditoria da Etapa 1.9 continuam vigentes naquilo que não foi expressamente refinado nesta reconciliação.

------------------------------------------------------------------------

# 87. Pendências após a revisão 1.2

Permanecem abertas, entre outras já registradas na Seção 79:

-   autenticação administrativa, recuperação de conta e retenção de
    dados pessoais;
-   onboarding, aprovação, slug e personalização da organização;
-   sufixo do código amigável, colisões de nome público e ação exata que
    cria participação;
-   aumento do próprio lance pelo líder, proteção contra erro grave de
    valor e representação monetária interna;
-   correção, cancelamento, eventual cancelamento após início do
    arremate e poderes do administrador da plataforma;
-   valor monetário definitivo do crédito, pacotes, aquisição e regras comerciais de suspensão;
-   canais e preferências de notificações;
-   estrutura física Firestore, separação público/privado, índices,
    região, backup e retenção;
-   estrutura física da recuperação durável de agendamento;
-   relação operacional entre WhatsApp e plataforma em eventual
    migração;
-   modelagem física do snapshot da Jóia;
-   componentes, pesquisa, protótipos e testes definitivos de UX/UI.

A definição de **ENCERRADA SEM LANCES** deixa de ser pendência de
produto.

------------------------------------------------------------------------

# 88. Critério de entrada no Bloco 2

Após a validação humana explícita desta revisão, as etapas técnicas posteriores deverão partir da versão 1.2 como fonte arquitetural vigente, em conjunto com a baseline conceitual validada dos Blocos 2.1 a 2.9 e 2.D.

A modelagem deverá preservar explicitamente, quando aplicável:

-   Organization como fronteira lógica de autorização e titular da
    carteira de créditos;
-   Auction como recurso pertencente à organização e contexto de
    participação;
-   Jóia como unidade da disputa, com estado atual separado do histórico de Lances, liderança contextual, política econômica aplicável e características historicamente estáveis;
-   identidade autenticada global separada da participação contextual: o
    Bloco 2 deverá modelar a relação de participação sem equiparar
    automaticamente conta global e `Participant`;
-   intenção de dar lance separada do lance válido confirmado: o Bloco 2
    deverá modelar `Bid` sem equiparar automaticamente uma intenção
    ainda não decidida a um lance existente no domínio;
-   Ciclo de Arremate/Gritador com identidade conceitual única por ocorrência, fase, deadlines, versões temporais de reinício, eventos lógicos, materialização e recuperação de agendamento;
-   Carteira distinguindo saldo disponível, capacidade garantida/comprometida e histórico econômico;
-   Movimentação de créditos rastreável, idempotente e sem apagamento da origem;
-   Provisionamento como projeção derivada, distinto de movimentação;
-   Garantia como comprometimento de capacidade econômica antes do Ciclo de Arremate;
-   relações administrativas contextualizadas por organização;
-   superfícies públicas e privadas compatíveis com autorização.

O Bloco 2 não deverá congelar prematuramente nomes de campos, coleções
ou endpoints antes da análise correspondente.

------------------------------------------------------------------------

# 89. Encerramento do Bloco 1

Com a incorporação dos Blocos 1.9.1 a 1.9.5, o Bloco 1 passa a possuir
uma especificação arquitetural pós-auditoria.

A arquitetura permanece tecnicamente orientada pelos princípios de
backend autoritativo, multi-tenancy, consistência transacional,
idempotência, autoridade temporal, recuperação durável, isolamento
organizacional, rastreabilidade e UX informativa.

A implementação permanece não iniciada.

------------------------------------------------------------------------

# Histórico de versões


| Versão | Ano | Status | Descrição |
| --- | --- | --- | --- |
| 1.0 | 2026 | Validada | Consolidação inicial das decisões arquiteturais dos Blocos 1.1 a 1.8, incluindo o adendo de UX/UI temporal do Gritador Digital. |
| 1.1 | 2026 | Validada | Revisão pós-auditoria. Incorpora exclusivamente as decisões validadas nos Blocos 1.9.1 a 1.9.5, incluindo recuperação durável do agendamento, contrato temporal autoritativo, deadlines lógicos, encerramento sem lances e requisitos B01 a B07. |
| 1.2 | 2026 | Candidata à validação final | Reconciliação da baseline arquitetural v1.1 com a baseline conceitual validada dos Blocos 2.1 a 2.9 e 2.D. Atualiza economia de créditos, garantia e provisionamento, encerramento sem lances, identidade do Ciclo, escopo do identificador público e regras de Lance, preservando as decisões arquiteturais compatíveis. |