# LanceBem
## Modelo Conceitual Consolidado do Protótipo Funcional

**Produto:** LanceBem  
**Etapa:** Modelo Conceitual  
**Escopo integrado:** Blocos 2.1 a 2.9 + Bloco 2.D consolidado e auditado  
**Situação:** Integração controlada  
**Finalidade:** estabelecer a baseline conceitual única para construção e validação do primeiro protótipo funcional  
**Implementação:** não definida por este documento  
**Princípio:** implementar o que foi validado, evitando ampliar o domínio antes da validação com usuários reais.

---

# 1. Função desta consolidação

Este documento integra duas camadas de modelagem anteriormente construídas:

```text
MODELO CONCEITUAL FUNCIONAL
Blocos 2.1 a 2.9

            +

MODELO CONCEITUAL DOS DADOS
Bloco 2.D

            ↓

MODELO CONCEITUAL CONSOLIDADO
DO PROTÓTIPO FUNCIONAL
```

A integração não consiste em simplesmente colocar um documento depois do outro.

Foram aplicadas três regras:

1. decisões compatíveis foram preservadas e integradas;
2. conceitos detalhados posteriormente pelo Bloco 2.D foram incorporados à estrutura original;
3. formulações anteriores superadas por decisões explicitamente validadas foram substituídas.

A partir da validação desta integração, este documento deverá funcionar como referência conceitual única para o primeiro protótipo.

---

# 2. Objetivo do protótipo

O LanceBem surgiu da observação de um problema concreto em Leilões comunitários conduzidos principalmente pelo WhatsApp:

- dificuldade de alguns participantes para compreender a dinâmica;
- dificuldade para acompanhar Jóias, Lances e liderança;
- dificuldade dos organizadores para conduzir o Leilão;
- confusão causada pela concentração de mensagens no WhatsApp.

A hipótese principal é:

**O LanceBem consegue tornar um Leilão comunitário mais fácil de compreender, participar e conduzir do que sua organização exclusivamente pelo WhatsApp.**

O primeiro protótipo deverá permitir testar essa hipótese com usuários reais.

Ele não pretende validar antecipadamente todas as necessidades futuras do produto.

---

# 3. Princípio central de experiência

O participante deverá conseguir abrir o LanceBem pelo celular e compreender rapidamente:

- qual Leilão está acessando;
- qual Organização o promove;
- qual Jóia está visualizando;
- quanto ela está valendo;
- quem está liderando;
- quais opções de Lance possui;
- quanto seu Lance ficará;
- o que acontecerá se confirmar;
- em que momento da disputa a Jóia se encontra;
- quando o resultado for definitivo.

A complexidade necessária para garantir:

- identidade;
- concorrência;
- integridade;
- tempo;
- créditos;
- histórico;
- auditoria;

permanece no sistema.

Ela não deve ser transferida desnecessariamente para o participante.

---

# 4. Núcleo conceitual integrado

O domínio pode ser representado por:

```text
PESSOA
   │
   ├── VÍNCULO ORGANIZACIONAL
   │          │
   │          ▼
   │     ORGANIZAÇÃO
   │          │
   │          ├── CARTEIRA
   │          │
   │          └── LEILÃO
   │                 │
   │                 ├── PARTICIPAÇÕES
   │                 └── JÓIAS
   │                        │
   │                        ├── LANCES
   │                        ├── CICLO DE ARREMATE
   │                        └── RESULTADO TERMINAL
   │
   └── PARTICIPAÇÃO
              │
              ├── LEILÃO
              ├── LANCES
              └── NOTIFICAÇÕES
```

A Carteira mantém relações econômicas com:

```text
CARTEIRA
   │
   ├── MOVIMENTAÇÕES
   └── GARANTIAS
           │
           └── JÓIA
```

Auditoria atravessa as operações críticas.

---

# 5. Pessoa

Pessoa representa a identidade individual global reconhecida pelo LanceBem.

Conceitualmente, deve ser possível identificar:

- identidade interna;
- nome;
- telefone;
- validação do telefone;
- dados mínimos necessários à identificação e autenticação.

Pessoa não pertence a um Leilão específico.

Uma mesma Pessoa poderá possuir relações diferentes no sistema.

```text
PESSOA
  │
  ├── representa Organização
  └── participa de Leilão
```

Não deverão ser criadas identidades humanas duplicadas apenas porque a mesma Pessoa exerce papéis diferentes.

---

# 6. Telefone e identificação

O telefone é mecanismo de identificação e validação.

Ele não constitui a identidade conceitual da Pessoa.

No fluxo normal do protótipo, um telefone celular validado não deverá identificar simultaneamente mais de uma Pessoa ativa.

Mudança de telefone não cria automaticamente outra Pessoa.

Não serão exigidos CPF, endereço, data de nascimento ou outros dados pessoais adicionais sem necessidade demonstrada.

---

# 7. Organização

Organização representa a entidade responsável pelo Leilão.

O foco inicial são paróquias e comunidades, sem restringir conceitualmente o produto exclusivamente a elas.

Existe uma distinção fundamental:

```text
PESSOA
Administrador autorizado
        ↓
VÍNCULO ORGANIZACIONAL
        ↓
ORGANIZAÇÃO
        ↓
LEILÃO
```

A Organização não deve ser tratada simplesmente como uma Pessoa cadastrando Jóias.

---

# 8. Habilitação da Organização

A Organização deverá passar por processo de habilitação antes de operar normalmente na plataforma.

O objetivo é estabelecer uma fronteira entre:

```text
CONTA INDIVIDUAL
       ≠
ORGANIZAÇÃO HABILITADA
A PROMOVER LEILÕES
```

O protótipo não precisa criar processo burocrático excessivo de verificação.

Entretanto, a autorização para promover Leilões pertence à Organização, não apenas à existência de uma Pessoa autenticada.

---

# 9. Vínculo Organizacional e papéis

Pessoa e Organização relacionam-se por Vínculo Organizacional.

```text
PESSOA N ───── N ORGANIZAÇÃO
             │
             ▼
     VÍNCULO ORGANIZACIONAL
```

Esse vínculo determina:

- qual Pessoa representa determinada Organização;
- quais autorizações possui;
- se o vínculo está operacionalmente ativo.

Administrador e Gritador são papéis ou autorizações.

Não são entidades humanas independentes.

---

# 10. Responsabilidade da Organização

A Organização é responsável, no mundo real, por:

- veracidade das informações do Leilão;
- cadastro das Jóias;
- condução operacional;
- relacionamento com os participantes;
- recebimento do pagamento da Jóia;
- combinação da retirada.

O LanceBem organiza digitalmente a disputa.

Não assume a operação comercial da Jóia.

---

# 11. Fronteira comercial do LanceBem

O protótipo não transforma o LanceBem em marketplace.

Ficam fora do corte:

- pagamento da Jóia pelo LanceBem;
- PIX da Jóia pelo LanceBem;
- checkout;
- carrinho;
- frete;
- entrega;
- logística;
- ranking de vendedores;
- avaliações;
- marketplace nacional de Jóias.

Pagamento e retirada acontecem diretamente entre vencedor e Organização.

Esses acontecimentos não prolongam o ciclo de vida da Jóia dentro do LanceBem.

---

# 12. Leilão

Todo Leilão pertence a exatamente uma Organização.

```text
ORGANIZAÇÃO
     │
     ├── Leilão A
     │      ├── Jóia 1
     │      ├── Jóia 2
     │      └── Jóia 3
     │
     └── Leilão B
```

Uma Organização poderá realizar vários Leilões ao longo do tempo.

O Leilão possui identidade e ciclo de vida próprios.

Estados conceituais gerais:

```text
PREPARAÇÃO
    ↓
EM ANDAMENTO
    ↓
EM ENCERRAMENTO
    ↓
FINALIZADO
```

Esses nomes não obrigam enums físicos com a mesma nomenclatura.

---

# 13. Encerramento do Leilão

A data e o horário previstos possuem função de planejamento e informação.

Eles não encerram automaticamente todo o Leilão.

```text
LEILÃO EM ANDAMENTO
        ↓
Organização solicita encerramento
        ↓
confirma
        ↓
EM ENCERRAMENTO
        ↓
não se iniciam novas disputas
        ↓
disputas legitimamente iniciadas continuam
        ↓
todas terminam
        ↓
FINALIZADO
```

Uma disputa em andamento não será destruída simplesmente porque a data planejada do Leilão foi atingida.

---

# 14. Jóia

Jóia é a unidade central da disputa.

```text
LEILÃO 1 ───── 0..N JÓIA
```

Cada ocorrência pertence a exatamente um Leilão.

Uma mesma ocorrência não poderá participar simultaneamente de dois Leilões.

Entre as informações conceitualmente necessárias estão:

- identidade;
- nome;
- fotografia;
- valor inicial;
- característica curta;
- descrição do Gritador;
- número administrativo interno;
- doador, quando informado;
- estado operacional;
- informações da disputa;
- informações temporais;
- resultado terminal.

---

# 15. Número administrativo

O número administrativo da Jóia permanece interno.

Ele não deverá ser utilizado como principal mecanismo público de identificação.

A interface deverá priorizar elementos que façam sentido para o participante, especialmente nome, fotografia, valor e situação da disputa.

---

# 16. Característica curta e descrição do Gritador

Existem dois propósitos distintos de texto.

**Característica curta**

Informação objetiva que acompanha a apresentação da Jóia.

Exemplo:

```text
Garrote
Animal jovem, bem cuidado
```

**Descrição do Gritador**

Texto utilizado durante a condução cultural do Arremate, especialmente em UMA, DUAS e TRÊS.

Pode ser mais descritivo e utilizar linguagem compatível com o contexto comunitário.

Os dois campos não devem ser fundidos porque possuem funções diferentes.

---

# 17. Textos livres e prevenção de abuso

Campos livres não deverão se transformar em mecanismo para:

- solicitar pagamentos antecipados;
- inserir contatos indevidos;
- desviar o participante do funcionamento previsto;
- publicar conteúdo incompatível.

IA poderá auxiliar na validação de conteúdo.

Ela não substitui regras determinísticas quando estas forem suficientes.

Também não haverá campo de telefone ou WhatsApp específico em cada Jóia apenas para contato.

---

# 18. Doador

Doador é informação opcional relacionada à Jóia.

Não precisa constituir Pessoa cadastrada no LanceBem.

A ausência de identificação do doador não impede a existência da Jóia.

---

# 19. Cadastro dinâmico de doações

O catálogo não precisa estar fechado quando o Leilão começa.

```text
LEILÃO INICIADO
      ↓
nova doação aparece
      ↓
Organização cadastra nova Jóia
      ↓
LanceBem recalcula
a situação econômica
```

O sistema deverá suportar naturalmente a entrada de novas doações durante o evento.

---

# 20. Cadastro, publicação e disputa

Devem permanecer conceitualmente separados:

```text
CADASTRADA
     ↓
PUBLICADA
     ↓
APTA A RECEBER LANCES
     ↓
EM DISPUTA
     ↓
CICLO DE ARREMATE
     ↓
RESULTADO TERMINAL
```

Nem todas essas distinções precisam aparecer como telas ou botões separados.

A separação existe para manter o domínio coerente.

---

# 21. Valor inicial

Valor inicial pertence à Jóia.

Não constitui Lance.

Antes do primeiro Lance válido:

```text
valor inicial existente
Lances válidos = 0
liderança = inexistente
```

O LanceBem não fabrica Lance artificial correspondente ao valor inicial.

---

# 22. Participação

Pessoa participa de um Leilão por meio de Participação.

```text
PESSOA
   ↓
PARTICIPAÇÃO
   ↓
LEILÃO
```

Cada Participação pertence a exatamente:

- uma Pessoa;
- um Leilão.

Para o mesmo par Pessoa + Leilão, o fluxo normal deverá possuir no máximo uma Participação contextual correspondente.

---

# 23. Entrada do participante

A participação deverá exigir o mínimo necessário de fricção.

Fluxo desejado:

```text
LINK DO WHATSAPP
       ↓
LEILÃO
       ↓
CATÁLOGO
       ↓
JÓIA
       ↓
QUERO DAR LANCE
       ↓
identificação/validação
       ↓
ciência das condições
       ↓
PARTICIPAÇÃO
       ↓
LANCE
```

Não é necessário realizar cadastro complexo apenas para visualizar o catálogo.

---

# 24. Identificação pública

O participante será identificado publicamente por:

```text
primeiro nome + número gerado
```

Exemplo:

```text
José 482
```

Esse identificador:

- pertence à Participação;
- é único dentro do Leilão;
- permanece estável naquela Participação;
- não expõe o telefone;
- não constitui mecanismo de autenticação.

A mesma Pessoa poderá possuir outro identificador em outro Leilão.

---

# 25. Ciência de participação real

Antes do primeiro Lance válido, a Pessoa deverá compreender que:

- está participando de Leilão real;
- o Lance representa manifestação real de participação;
- se vencer, tratará pagamento diretamente com a Organização;
- retirada também será combinada diretamente;
- o LanceBem não recebe o pagamento da Jóia;
- o LanceBem organiza a disputa.

Essa informação deverá ser clara e curta.

Não deverá criar barreira burocrática antes da simples navegação.

---

# 26. Catálogo

O catálogo deverá privilegiar compreensão imediata.

Exemplo com disputa:

```text
FOTO

Garrote

Lance atual
R$ 2.100

José 482 está na frente

[ VER E DAR LANCE ]
```

Sem Lance:

```text
FOTO

Bolo caseiro

Valor inicial
R$ 30

Ainda sem Lances

[ VER JÓIA ]
```

O participante não deve precisar abrir várias telas apenas para descobrir o valor atual.

---

# 27. Lance atual antes da ação

Na tela da Jóia, o valor corrente deverá ter destaque antes da ação de Lance.

```text
GARROTE

LANCE ATUAL
R$ 2.100

José 482 está na frente
```

O participante deve compreender a situação antes de agir.

---

# 28. Incrementos

A Organização não configura incrementos individualmente.

O LanceBem determina as opções apresentadas conforme a faixa econômica corrente.

| Valor corrente | Opções |
|---|---|
| até R$ 99 | +R$ 1, +R$ 2, +R$ 5 |
| R$ 100 a R$ 199 | +R$ 3, +R$ 5, +R$ 10 |
| R$ 200 a R$ 499 | +R$ 5, +R$ 10, +R$ 20 |
| R$ 500 a R$ 999 | +R$ 10, +R$ 30, +R$ 50 |
| R$ 1.000 ou mais | +R$ 30, +R$ 50, +R$ 100 |

Os valores permanecem inteiros, sem centavos.

---

# 29. O participante não calcula o total

Exemplo:

```text
Lance atual
R$ 2.100

[ + R$ 30 ]
[ + R$ 50 ]
[ + R$ 100 ]
```

Ao selecionar:

```text
+ R$ 50
```

o sistema apresenta:

```text
Seu Lance será

R$ 2.150

[ CONFIRMAR R$ 2.150 ]
```

O participante não precisa fazer mentalmente a soma.

---

# 30. Seleção não significa Lance

Selecionar incremento não cria Lance histórico.

```text
seleciona incremento
       ↓
visualiza valor total
       ↓
confirma intenção
       ↓
backend valida
       ↓
operação aceita?
   │
   ├── NÃO → nenhum Lance válido
   │
   └── SIM → LANCE CONFIRMADO
```

Tentativa rejeitada não constitui Lance oficial.

---

# 31. Lance confirmado

Lance confirmado é fato histórico.

Cada Lance pertence a:

- exatamente uma Participação;
- exatamente uma Jóia.

A Participação e a Jóia deverão pertencer ao mesmo Leilão.

O histórico registra o valor absoluto confirmado.

Exemplo:

```text
R$ 2.150
```

e não apenas:

```text
+ R$ 50
```

---

# 32. Histórico e imutabilidade

Lance posterior não sobrescreve Lance anterior.

```text
JÓIA
 │
 ├── Lance 1
 ├── Lance 2
 ├── Lance 3
 └── Lance 4
```

Lance confirmado é imutável como fato histórico.

O participante não poderá cancelar diretamente um Lance confirmado no protótipo.

Correções administrativas excepcionais futuras deverão preservar o fato original e sua rastreabilidade.

---

# 33. Autoridade do backend

A interface nunca determina sozinha se um Lance é válido.

O backend é autoridade para:

- valor;
- estado da Jóia;
- Participação;
- concorrência;
- idempotência;
- liderança;
- janela temporal;
- transições do Gritador;
- Resultado;
- operações econômicas relacionadas.

Confirmação visual no dispositivo não substitui confirmação autoritativa.

---

# 34. Concorrência

Dois participantes podem tentar confirmar Lances praticamente ao mesmo tempo.

A precedência não é determinada simplesmente por quem tocou primeiro na tela.

A operação autoritativa efetivamente aceita pelo backend determina o histórico oficial.

O relógio do dispositivo não concede precedência.

---

# 35. Idempotência

A intenção de confirmação deverá possuir identidade operacional suficiente para reconhecer repetição.

```text
OPERAÇÃO
   ↓
aceita
   ↓
LANCE
```

Repetir a mesma operação não cria outro Lance.

A repetição recupera o resultado da operação original.

---

# 36. Lance atual e liderança

Lance atual e líder atual são projeções do histórico autoritativo.

```text
HISTÓRICO DE LANCES
       │
       ├── determina → LANCE ATUAL
       └── determina → LÍDER ATUAL
```

Liderança pertence à Jóia.

Um participante poderá:

- liderar Jóia A;
- não liderar Jóia B;
- ter vencido Jóia C.

---

# 37. Liderança não é vitória

Permanece a distinção:

```text
LANCE CONFIRMADO
       ≠
LIDERANÇA
       ≠
VITÓRIA
```

Mesmo durante DOU-LHE TRÊS:

```text
José 482 está na frente
```

não significa:

```text
José 482 venceu
```

Vitória somente existe depois do Resultado oficial `ARREMATADA`.

---

# 38. Participação em múltiplas Jóias

Uma mesma Participação poderá realizar Lances em várias Jóias do mesmo Leilão.

Uma Pessoa também poderá participar de outros Leilões sem duplicação artificial de sua identidade global.

---

# 39. Notificação essencial

O participante deverá ser informado quando perder a liderança de uma Jóia.

```text
novo Lance válido
       ↓
nova liderança
       ↓
participante anterior
perde liderança
       ↓
notificação
```

Políticas mais sofisticadas de notificação permanecem fora deste corte.

---

# 40. Ciclo de Arremate

O Ciclo de Arremate representa o processo formal de encerramento da disputa de uma Jóia.

Ele pertence à Jóia, não ao Leilão inteiro.

```text
Leilão
   │
   ├── Jóia A ─ disputa ─ Ciclo ─ Resultado
   ├── Jóia B ─ disputa ───────────── ...
   └── Jóia C ─ disputa ─ Ciclo ─ Resultado
```

Uma Jóia pode permanecer em disputa durante horas ou dias antes de entrar no Ciclo.

---

# 41. Início do Arremate

O Ciclo começa por ação operacional autorizada.

Não começa automaticamente porque determinado horário foi atingido.

Para uma Jóia com disputa devem existir:

- Jóia em condição válida;
- Lance atual válido;
- liderança;
- cobertura econômica exigida;
- registro autoritativo do início.

---

# 42. Pré-aviso

Ao iniciar o Arremate:

```text
INICIAR ARREMATE
        ↓
PRÉ-AVISO
1 hora
```

Durante essa hora:

- a Jóia continua recebendo Lances;
- valor pode aumentar;
- liderança pode mudar;
- o líder não é vencedor.

Um Lance durante o pré-aviso **não reinicia a hora**.

---

# 43. Gritador Digital

Depois do pré-aviso:

```text
DOU-LHE UMA
   10 min
      ↓
DOU-LHE DUAS
   10 min
      ↓
DOU-LHE TRÊS
   10 min
      ↓
RESULTADO
```

Para avançar:

```text
tempo encerrado
       +
nenhum novo Lance válido
       ↓
próxima etapa
```

---

# 44. Reinício da contagem

Novo Lance válido durante UMA, DUAS ou TRÊS retorna para:

```text
DOU-LHE UMA
```

com nova janela completa de 10 minutos.

Exemplo:

```text
DOU-LHE TRÊS
     │
  9min58s
     │
novo Lance
     ↓
DOU-LHE UMA
10 minutos
```

O novo Lance não cria outro Ciclo.

Reinicia a contagem formal dentro do Ciclo existente.

---

# 45. Efeito econômico e temporal do Lance

Durante a contagem formal, um novo Lance possui:

```text
LANCE VÁLIDO
    │
    ├── efeito econômico
    │      valor + liderança
    │
    └── efeito temporal
           retorno para UMA
           + novo prazo
```

Trata-se de um único fato de domínio com efeitos diferentes.

---

# 46. Duração do Ciclo

Sem reinícios:

```text
Pré-aviso       60 min
UMA             10 min
DUAS            10 min
TRÊS            10 min
                ──────
Total           90 min
```

Os 90 minutos não representam duração máxima.

Lances durante UMA, DUAS ou TRÊS podem prolongar o Ciclo.

---

# 47. Tempo autoritativo

O relógio visual apresentado ao participante não determina o estado real da disputa.

O backend mantém a referência temporal autoritativa.

A interface representa esse estado.

Em caso de atraso, reconexão ou atualização tardia, o estado real deverá ser reconciliado antes de decidir se determinado Lance ainda pode ser aceito.

---

# 48. Condição do Arremate

Uma Jóia somente poderá ser considerada `ARREMATADA` quando:

1. estiver formalmente no Ciclo;
2. tiver cumprido o pré-aviso;
3. tiver chegado a DOU-LHE TRÊS;
4. os 10 minutos tiverem terminado;
5. nenhum novo Lance válido tiver ocorrido nesse período;
6. existir Lance líder válido;
7. existir Participação associada.

```text
DOU-LHE TRÊS
      +
10 minutos completos
      +
nenhum novo Lance
      ↓
ARREMATADA
```

---

# 49. Resultados terminais

Existem dois resultados terminais:

```text
RESULTADO TERMINAL
       │
       ├── ARREMATADA
       └── ENCERRADA SEM LANCES
```

Os resultados são mutuamente exclusivos.

Uma Jóia possui no máximo um Resultado terminal oficial.

---

# 50. ARREMATADA

O Resultado `ARREMATADA` identifica conceitualmente:

- Jóia;
- Lance vencedor;
- Participação vencedora;
- Pessoa correspondente;
- valor final;
- momento autoritativo;
- Ciclo que produziu o Resultado.

Não é necessária entidade independente `Vencedor`.

```text
ARREMATADA
     ↓
Lance vencedor
     ↓
Participação
     ↓
Pessoa
```

---

# 51. ENCERRADA SEM LANCES

Se a Jóia não possuir qualquer Lance válido, ela não precisa percorrer o Ciclo de Arremate.

O Organizador poderá encerrá-la:

```text
JÓIA SEM LANCES
       ↓
ação autorizada
       ↓
ENCERRADA SEM LANCES
```

Não são criados:

- Lance artificial;
- liderança;
- vencedor;
- custo de Arremate.

---

# 52. Fronteira final da Jóia no LanceBem

O ciclo funcional termina em:

```text
                ┌── ARREMATADA
JÓIA ───────────┤
                └── ENCERRADA SEM LANCES
```

Não existem como estados posteriores internos da Jóia:

```text
AGUARDANDO PAGAMENTO
PAGAMENTO
CONCLUÍDA
```

Pagamento e retirada são responsabilidade externa da Organização.

---

# 53. Imutabilidade do Resultado

Depois de produzido Resultado válido:

- novos Lances não são aceitos;
- liderança deixa de ser transitória;
- vencedor, quando existente, está determinado;
- valor final está consolidado;
- contagem deixa de existir;
- Ciclo termina.

Correção administrativa futura deverá constituir evento auditável, não reabertura silenciosa da disputa.

---

# 54. Carteira

A Carteira pertence à Organização.

```text
ORGANIZAÇÃO
      ↓
CARTEIRA
```

Não pertence:

- ao Leilão;
- à Jóia;
- ao Administrador;
- ao Participante.

As movimentações relevantes deverão ser rastreáveis.

---

# 55. Créditos

O modelo econômico é pré-pago.

O LanceBem não concede normalmente:

- saldo negativo;
- crédito emprestado;
- dívida automática de créditos.

A Organização utiliza capacidade previamente existente em sua Carteira.

O valor monetário de aquisição de cada crédito permanece fora da definição conceitual definitiva enquanto não for comercialmente congelado.

---

# 56. Faixas econômicas

Permanece a tabela:

| Valor da Jóia | Custo total |
|---|---:|
| abaixo de R$ 200 | 1 crédito |
| R$ 200 até abaixo de R$ 500 | 2 créditos |
| R$ 500 até abaixo de R$ 1.000 | 3 créditos |
| R$ 1.000 ou mais | 4 créditos |

Essa tabela é utilizada em momentos distintos do ciclo econômico.

---

# 57. Cadastro não consome crédito

```text
NOVA DOAÇÃO
     ↓
CADASTRO
     ↓
0 créditos consumidos
```

A Jóia poderá participar do cálculo de provisionamento sem gerar cobrança.

---

# 58. Provisionamento

O LanceBem poderá calcular continuamente o provisionamento mínimo da Organização.

```text
LEILÃO
  +
JÓIAS
  +
situação econômica
      ↓
PROVISIONAMENTO
```

Provisionamento representa capacidade econômica projetada.

Não é cobrança.

```text
PROVISIONAMENTO
       ≠
CONSUMO
```

A Organização poderá manter créditos acima do mínimo calculado.

---

# 59. Insuficiência de provisionamento não impede doação

Se nova doação elevar a necessidade econômica projetada acima da Carteira, a Jóia ainda poderá ser cadastrada.

Exemplo:

```text
Carteira
20

Provisionamento
24

Necessidade adicional
4
```

A insuficiência não transforma uma doação legítima em cadastro proibido.

---

# 60. Primeiro Lance e consumo imediato

O primeiro Lance válido produz o primeiro consumo efetivo de créditos daquela Jóia.

O sistema identifica a faixa econômica do valor desse Lance e consome imediatamente a quantidade correspondente.

Exemplo:

```text
Primeiro Lance
R$ 470
      ↓
faixa R$ 200 a < R$ 500
      ↓
2 créditos consumidos
```

Portanto:

**o primeiro Lance consome créditos. Não apenas os reserva.**

---

# 61. Evolução da disputa

Depois do primeiro Lance, o valor poderá mudar de faixa:

```text
R$ 101
 ↓
R$ 180
 ↓
R$ 250
 ↓
R$ 400
 ↓
R$ 550
```

Essas mudanças durante a disputa normal não produzem automaticamente novos consumos a cada Lance.

O ajuste econômico adicional acontece quando o Arremate é iniciado.

---

# 62. Cobertura econômica ao iniciar o Arremate

Ao clicar em `INICIAR ARREMATE`, o sistema recalcula a situação da Jóia.

Antes de iniciar o Ciclo deverá existir cobertura econômica total de:

**4 créditos.**

Os créditos consumidos no primeiro Lance integram essa cobertura.

O sistema reserva somente o necessário para completar quatro.

Exemplo:

```text
Primeiro Lance
R$ 101
      ↓
1 crédito consumido

INICIAR ARREMATE
      ↓
cobertura exigida
4 créditos
      ↓
já consumido
1
      ↓
reserva adicional
3
```

Resultado:

```text
1 consumido
+
3 reservados
=
4 créditos cobertos
```

---

# 63. Insuficiência de cobertura

Se a Carteira não puder completar a cobertura:

```text
INICIAR ARREMATE
      ↓
cobertura total disponível?
   │
   ├── SIM → reserva adicional → inicia Ciclo
   │
   └── NÃO → Ciclo não inicia
```

Os Lances já existentes continuam válidos.

A Jóia permanece fora do Ciclo até a condição ser satisfeita.

---

# 64. Disputa iniciada não é interrompida

Depois da cobertura total e do início legítimo do Ciclo:

- a disputa não será interrompida por valorização da Jóia;
- novo Lance válido não será rejeitado por mudança de faixa;
- a Organização não dependerá de comprar créditos durante a contagem.

Isso é possível porque o custo máximo da tabela já está coberto.

---

# 65. Consumo definitivo

No Arremate, o sistema recalcula o custo pelo valor final.

Exemplo:

```text
Primeiro Lance
R$ 101
      ↓
1 crédito consumido

Iniciar Arremate
      ↓
3 reservados

Arremate
R$ 700
      ↓
custo definitivo
3 créditos
```

Acerto:

```text
cobertura total     4
custo definitivo    3
                    ─
consumo total       3
liberação           1
```

O crédito excedente retorna à disponibilidade da Carteira.

---

# 66. Faixa máxima

Se o Arremate ocorrer em R$ 1.000 ou mais:

```text
custo definitivo = 4 créditos
```

Toda a cobertura é consumida.

Se o primeiro Lance já estiver nessa faixa, os quatro créditos já terão sido consumidos e nenhuma reserva adicional será necessária para iniciar o Arremate.

---

# 67. Jóia encerrada sem Lance e créditos

```text
JÓIA SEM LANCE
      ↓
ENCERRADA SEM LANCES
      ↓
0 crédito consumido
```

Não existe custo de Arremate porque não houve disputa com Lance válido.

---

# 68. Distinções econômicas

Devem permanecer separados:

```text
PROVISIONAMENTO
      ≠
CONSUMO DO PRIMEIRO LANCE
      ≠
GARANTIA/RESERVA
      ≠
CUSTO DEFINITIVO
```

**Provisionamento** é projeção.

**Consumo do primeiro Lance** é cobrança efetiva inicial.

**Garantia/Reserva** assegura cobertura máxima antes do Ciclo.

**Custo definitivo** decorre do valor final do Arremate.

---

# 69. Histórico econômico

Saldo não substitui histórico.

A Carteira deverá permitir rastrear conceitualmente:

- aquisição;
- consumo;
- reserva;
- liberação;
- demais movimentações autorizadas.

Garantia utiliza capacidade de uma única Carteira e relaciona-se à Jóia correspondente.

Não poderá existir cruzamento econômico entre Organizações.

---

# 70. Auditoria

Auditoria é requisito transversal.

Operações críticas deverão preservar autoria, momento e contexto suficientes para reconstrução posterior.

Entre elas:

- início do Arremate;
- Lances confirmados;
- concorrência relevante;
- encerramento sem Lances;
- Resultado;
- consumo de créditos;
- constituição de reserva;
- liberação de créditos;
- ações administrativas excepcionais.

Isso não obriga a existência de uma única entidade física denominada Auditoria.

---

# 71. Fluxo consolidado do participante

```text
RECEBE LINK NO WHATSAPP
          ↓
ABRE O LANCEBEM
          ↓
IDENTIFICA ORGANIZAÇÃO E LEILÃO
          ↓
VÊ AS JÓIAS
          ↓
ESCOLHE UMA
          ↓
VÊ VALOR INICIAL OU LANCE ATUAL
          ↓
QUER PARTICIPAR
          ↓
IDENTIFICAÇÃO SIMPLES
          ↓
VALIDAÇÃO
          ↓
CIÊNCIA DO LEILÃO REAL
          ↓
ESCOLHE INCREMENTO
          ↓
VÊ VALOR TOTAL
          ↓
CONFIRMA
          ↓
BACKEND VALIDA
          ↓
LANCE CONFIRMADO
          ↓
ACOMPANHA LIDERANÇA
          ↓
GRITADOR
          ↓
RESULTADO TERMINAL
```

---

# 72. Fluxo consolidado da Organização

```text
ORGANIZAÇÃO HABILITADA
          ↓
CARTEIRA DISPONÍVEL
          ↓
CRIA LEILÃO
          ↓
CADASTRA JÓIAS
          ↓
LanceBem calcula provisionamento
          ↓
PUBLICA
          ↓
DIVULGA LINK
          ↓
NOVAS DOAÇÕES PODEM CHEGAR
          ↓
CADASTRA NOVAS JÓIAS
          ↓
PROVISIONAMENTO É RECALCULADO
          ↓
ACOMPANHA DISPUTAS
          ↓
PRIMEIROS LANCES GERAM CONSUMO
          ↓
INICIA ARREMATE QUANDO CABÍVEL
          ↓
LanceBem garante cobertura de 4 créditos
          ↓
CICLOS EM ANDAMENTO TERMINAM
          ↓
JÓIAS PRODUZEM RESULTADO
          ↓
LEILÃO É FINALIZADO
```

---

# 73. Fluxo consolidado da Jóia com Lance

```text
DOAÇÃO
  ↓
CADASTRADA
  ↓
VALIDADA
  ↓
PUBLICADA
  ↓
APTA PARA LANCES
  ↓
PRIMEIRO LANCE
  ↓
CONSUMO DA FAIXA
  ↓
EM DISPUTA
  ↓
INICIAR ARREMATE
  ↓
COBERTURA TOTAL DE 4 CRÉDITOS
  ↓
PRÉ-AVISO
  ↓
UMA
  ↓
DUAS
  ↓
TRÊS
  ↓
ARREMATADA
```

Novo Lance durante UMA, DUAS ou TRÊS retorna para UMA.

---

# 74. Fluxo consolidado da Jóia sem Lance

```text
DOAÇÃO
  ↓
CADASTRADA
  ↓
PUBLICADA
  ↓
APTA PARA LANCES
  ↓
nenhum Lance válido
  ↓
ação do Organizador
  ↓
ENCERRADA SEM LANCES
  ↓
0 crédito consumido
```

---

# 75. Cardinalidades conceituais

| Origem | Relação | Destino | Cardinalidade |
|---|---|---|---|
| Pessoa | possui vínculo | Organização | N:N via Vínculo Organizacional |
| Organização | realiza | Leilão | 1:0..N |
| Organização | possui | Carteira | 1:1 operacional |
| Pessoa | participa | Leilão | N:N via Participação |
| Pessoa | possui | Participação | 1:0..N |
| Leilão | possui | Participação | 1:0..N |
| Leilão | contém | Jóia | 1:0..N |
| Participação | realiza | Lance | 1:0..N |
| Jóia | recebe | Lance | 1:0..N |
| Jóia com disputa | possui | Ciclo de Arremate | 1:0..1 |
| Jóia | alcança | Resultado terminal | 1:0..1 |
| Carteira | possui | Movimentação | 1:0..N |
| Carteira | constitui | Garantia | 1:0..N |
| Jóia em Arremate | possui | Garantia ativa | 1:0..1 |
| Participação | recebe | Notificação | 1:0..N |
| Jóia | possui | Doador informado | 1:0..1 |

---

# 76. O que o protótipo deverá testar

A validação não deverá se limitar ao funcionamento técnico.

Precisamos observar se pessoas reais conseguem utilizar o produto.

As perguntas centrais são:

- A pessoa identifica imediatamente que está em um Leilão?
- Reconhece a Organização promotora?
- Entende qual Jóia está vendo?
- Encontra rapidamente o valor atual?
- Entende quem está liderando?
- Entende os botões de incremento?
- Percebe quanto seu Lance ficará antes de confirmar?
- Consegue confirmar sem orientação externa?
- Entende quando passou a liderar?
- Entende quando perdeu a liderança?
- Compreende UMA, DUAS e TRÊS?
- Entende quando a Jóia foi efetivamente arrematada?
- Entende que pagamento e retirada serão tratados com a Organização?
- O administrador consegue cadastrar nova doação durante o evento?
- Entende a situação econômica da Carteira sem precisar conhecer detalhes técnicos?
- Compreende por que determinada Jóia ainda não pode iniciar o Arremate?
- Consegue conduzir o Gritador com menos confusão que pelo WhatsApp?
- O fluxo reduz a quantidade de explicações necessárias aos participantes?

---

# 77. Critério principal de UX

Durante o desenvolvimento deverá permanecer a pergunta:

**Esta informação ou etapa ajuda a pessoa a entender o que está acontecendo ou a realizar com segurança sua próxima ação?**

Se não ajudar, sua presença na interface deverá ser questionada.

Nem tudo que existe no domínio precisa aparecer na tela.

---

# 78. Pendência deliberadamente fora do corte

Permanece sem definição nesta etapa:

**Participação de representantes da Organização no próprio Leilão.**

Ainda não se estabelece se uma Pessoa que atua como Administrador, Gritador ou outro representante poderá também concorrer como participante no Leilão promovido pela própria Organização.

Essa é uma questão de governança.

Ela não bloqueia a construção do primeiro protótipo e não será resolvida por suposição.

---

# 79. Funcionalidades fora do corte

Não entram automaticamente no primeiro protótipo:

- marketplace público;
- pagamento integrado;
- PIX da Jóia;
- checkout;
- frete;
- entrega;
- avaliações;
- ranking;
- programas de fidelidade;
- modelos avançados de risco;
- score de Organizações;
- modalidades adicionais de Leilão;
- funcionalidades sociais adicionais;
- regras comerciais ainda não validadas.

Questões hipotéticas que não afetem o fluxo principal deverão ser registradas como pendências em vez de ampliar continuamente a modelagem.

---

# 80. Fronteira técnica

Esta consolidação não determina:

- collections;
- documentos;
- subcollections;
- campos físicos;
- índices;
- regras Firestore;
- transactions;
- Cloud Functions;
- jobs;
- endpoints;
- stores;
- componentes Vue;
- estratégia física de relógio;
- mecanismo físico de notificações;
- arquitetura física de auditoria.

Essas decisões pertencem à arquitetura de implementação.

O modelo conceitual deverá restringir a arquitetura técnica, e não ser remodelado arbitrariamente para se adequar à tecnologia escolhida.

---

# 81. Invariantes principais da baseline

A continuidade do projeto deverá preservar, no mínimo:

1. Pessoa e Organização são conceitos diferentes.
2. Administrador e Gritador são papéis contextuais.
3. Todo Leilão pertence a uma Organização.
4. Carteira pertence à Organização.
5. Pessoa participa de Leilão por Participação.
6. Identificador público pertence à Participação.
7. Jóia pertence a um Leilão.
8. Valor inicial não é Lance.
9. Lance somente existe depois da aceitação autoritativa.
10. Lance confirmado é histórico e imutável.
11. Lance atual e liderança são projeções do histórico.
12. Liderança não significa vitória.
13. Backend determina validade, concorrência, tempo e Resultado.
14. Operações de confirmação devem ser idempotentes.
15. Uma Jóia com disputa possui no máximo um Ciclo de Arremate oficial.
16. Lance no pré-aviso não reinicia a hora.
17. Lance em UMA, DUAS ou TRÊS retorna para UMA.
18. Sem reinícios, o Ciclo percorre 90 minutos.
19. Resultado terminal é `ARREMATADA` ou `ENCERRADA SEM LANCES`.
20. Jóia sem Lance não precisa entrar no Ciclo de Arremate.
21. Encerrar sem Lances é ação autorizada do Organizador.
22. `ENCERRADA SEM LANCES` não consome créditos.
23. Pagamento e retirada são externos ao LanceBem.
24. Cadastro de Jóia não consome créditos.
25. Provisionamento não é consumo.
26. Primeiro Lance válido consome os créditos de sua faixa.
27. Mudanças de faixa posteriores não produzem automaticamente novos consumos.
28. `INICIAR ARREMATE` exige cobertura econômica total de 4 créditos.
29. Créditos já consumidos integram essa cobertura.
30. Somente o adicional necessário para completar 4 é reservado.
31. Sem cobertura total, o Ciclo não inicia.
32. Depois de iniciado, o Ciclo não é interrompido por valorização posterior.
33. Valor final do Arremate determina o custo definitivo.
34. Reserva excedente é liberada para a Carteira.
35. Resultado válido não é silenciosamente reaberto ou reescrito.
36. Histórico econômico e histórico de Lances devem permanecer auditáveis.

---

# 82. Regras anteriores expressamente superadas

Para impedir ambiguidade documental, deixam de ser normativas quaisquer formulações anteriores que estabeleçam:

```text
ARREMATADA
   ↓
AGUARDANDO PAGAMENTO
   ↓
PAGAMENTO
   ↓
CONCLUÍDA
```

Também fica superada a formulação:

```text
primeiro Lance
   ↓
apenas reserva créditos
```

A regra vigente é:

```text
primeiro Lance válido
   ↓
CONSUMO dos créditos
correspondentes à faixa
```

Também fica superada qualquer interpretação de que, ao iniciar o Arremate, basta reservar créditos correspondentes somente à faixa corrente.

A regra vigente é:

```text
INICIAR ARREMATE
       ↓
COBERTURA TOTAL
DE 4 CRÉDITOS
```

considerando os créditos já consumidos e reservando apenas o adicional necessário.

Essas substituições são deliberadas e decorrem das decisões finais da auditoria.

---

# 83. Marco conceitual alcançado

Com esta integração:

```text
BLOCOS 2.1–2.9
      +
BLOCO 2.D
CONSOLIDADO E AUDITADO
      ↓
MODELO CONCEITUAL CONSOLIDADO
DO PROTÓTIPO FUNCIONAL
```

O LanceBem possui agora uma visão integrada de:

- produto;
- experiência;
- identidade;
- Organização;
- Leilão;
- Participação;
- Jóia;
- Lance;
- liderança;
- concorrência;
- Gritador Digital;
- tempo;
- Resultado;
- Carteira;
- créditos;
- provisionamento;
- garantia;
- histórico;
- notificações;
- auditoria.

Isso não significa que o produto inteiro esteja especificado.

Significa que existe informação conceitual suficiente para interromper a expansão horizontal do domínio e materializar o primeiro protótipo.

---

# 84. Próxima sequência documental

A partir desta baseline:

```text
ESPECIFICAÇÃO ARQUITETURAL v1.1
             +
MODELO CONCEITUAL CONSOLIDADO
             ↓
MAPA DE FLUXOS
             ↓
MAPA DE TELAS
             ↓
WIREFRAMES
             ↓
VALIDAÇÃO UX
             ↓
ARQUITETURA DE IMPLEMENTAÇÃO
             ↓
IMPLEMENTAÇÃO
             ↓
PILOTO
             ↓
APRENDIZADO REAL
             ↓
REVISÃO DO PRODUTO
```

O objetivo da próxima etapa não será ampliar novamente o domínio.

Será transformar as regras já consolidadas em experiência observável e testável.

---

# 85. Marco de decisão

Este documento estabelece o marco:

**Modelo Conceitual Consolidado do Protótipo Funcional LanceBem**

**Composição:** Blocos 2.1 a 2.9 + Bloco 2.D consolidado e auditado.

A partir de sua validação, as consolidações anteriores permanecem como histórico de construção e rastreabilidade, mas este documento passa a constituir a **baseline conceitual vigente** para o primeiro protótipo funcional do LanceBem.

Novas decisões que alterem essa baseline deverão ser tratadas explicitamente como evolução do modelo, e não introduzidas silenciosamente durante a implementação.
