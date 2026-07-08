![Logo](logo.png)

# PLANO DE TESTES DE HOMOLOGAÇÃO - FLUXOS DE EXCEÇÃO

## 1. INFORMAÇÕES GERAIS

| Campo | Informação |
| :--- | :--- |
| **Projeto** | HOT x BP WEB x BHub (Fluxos de Exceção) |
| **Ambiente** | Homologação |
| **Responsável QA** | Matheus H Campos |
| **PO do Projeto** | Bruna Miguel |
| **Data Planejada** | 08/07/2026 |
| **Versão do Sistema** | 1.0 (Exceções) |
| **Cliente** | HITSS do Brasil |

## 2. OBJETIVO

Validar junto aos usuários de negócio o correto funcionamento dos **fluxos de exceção, rejeições e regras de alçadas de desconto**, garantindo que o sistema processe adequadamente os desvios do fluxo feliz (retornos para ajustes, reprovações e bloqueios) e que a esteira possa ser concluída com sucesso até a **Solicitação de Alta no B.HUB**.

## 3. ESCOPO

### Dentro do Escopo (Exceções)
* **Rejeitado Gestor**: Solicitação de ajuste no BP, permitindo reedição e reenvio.
* **Rejeitado Cliente**: Cancelamento ou interrupção do fluxo comercial por decisão do cliente.
* **Reprovado SPE**: Encerramento definitivo do fluxo por reprovação técnica/financeira da SPE.
* **Controle de Desconto (LPU Sim)**: Validação do cenário onde LPU é SIM e o Target informado é menor que o valor total/receita calculada do projeto (fluxos de aprovação ou rejeição de desconto).
* **Finalização do fluxo**: Transição e validação até a fase de Solicitação de Alta.

### Fora do Escopo
* Acesso inicial ao sistema e configuração de MFA (cobertos no plano principal).
* Execução do Fluxo Feliz sem desvios (coberto no plano principal).

---

## 4. MATRIZ DE RASTREABILIDADE

| Condição de validação | Descrição | Caso de Teste |
| :--- | :--- | :--- |
| **CV-EX-001** | Validação de ajuste solicitado pelo Gestor (Rejeitado Gestor) | CT-EX-001 |
| **CV-EX-002** | Validação de recusa comercial pelo Cliente (Rejeitado Cliente) | CT-EX-002 |
| **CV-EX-003** | Validação de reprovação técnica/financeira pela SPE (Reprovado SPE) | CT-EX-003 |
| **CV-EX-004** | Validação de desconto aprovado/rejeitado (LPU Sim e Target < Receita) | CT-EX-004 |
| **CV-EX-005** | Validação do encerramento com sucesso na Solicitação de Alta | CT-EX-005 |

---

## 6. CASOS DE TESTE

### CV-EX-001/CT-EX-001 - Rejeitado Gestor (Ajuste no BP)

#### Objetivo
Garantir que, quando o gestor rejeitar o BP de simulação solicitando ajustes, o fluxo retorne ao Elaborador permitindo a edição corretiva e o reenvio para aprovação sem perda de integridade.

#### Pré-condições
* BP de Simulação criado e submetido para aprovação do Gestor.
* Status do BP: `Em Aprovação`.

#### Passos para reproduzir
1. Acessar o sistema (HOT).
2. Localizar o BP enviado para aprovação.
3. Clicar em **Rejeitar / Solicitar Ajuste**.
4. Inserir uma justificativa detalhada para o ajuste (ex: *"Reajustar quantidade de recursos"*).
5. A demanda no HOT tem que estar na fase de Elaborar/Anexar BP.
6. Acessar o sistema com o perfil do **Editor** do BP.
7. Verificar se o status do BP mudou para `Rejeitado`.
8. Clicar em **Editar**.
9. Abrir o novo BP criado e realizar a alteração sugerida no BP e clicar em **Salvar**.
10. Clicar em **Submeter** novamente.

#### Resultado Esperado
* O BP retorna para o status de edição com a justificativa de rejeição visível ao Elaborador.
* Após reenvio pelo Elaborador, o status do BP é atualizado e segue novamente para aprovação.

#### Evidência
*(Insira aqui os prints da tela de rejeição do gestor e do status atualizado do BP para ajuste)*

**Resultado:**
( ) Aprovado
( ) Reprovado

---

### CV-EX-002/CT-EX-002 - Rejeitado pelo Cliente

#### Objetivo
Garantir que a rejeição formal da proposta comercial/BP pelo Cliente seja registrada corretamente e interrompa o avanço comercial da oportunidade.

#### Pré-condições
* BP aprovado internamente pelo Gestor e submetido para a aprovação comercial do Cliente.
* Status da demanda no HOT: `Aguardando Aprovação do Cliente`.

#### Passos para reproduzir
1. Acessar o módulo de gerenciamento de demandas no HOT.
2. Simular a ação do **Cliente** clicando em **Rejeitar Proposta / Cliente Rejeitou**.
3. Preencher o motivo da recusa (ex: *"Preço fora do budget"*).
4. Confirmar a rejeição.
5. Verificar o status da demanda no HOT e no BP Web.

#### Resultado Esperado
* A demanda deve atualizar seu status para `Rejeitado pelo Cliente`.
* O sistema deve bloquear qualquer avanço para as fases de pedido ou faturamento para esta demanda específica.

#### Evidência
*(Insira aqui o print da tela da demanda exibindo o status de rejeição pelo cliente)*

**Resultado:**
( ) Aprovado
( ) Reprovado

---

### CV-EX-003/CT-EX-003 - Reprovado SPE

#### Objetivo
Garantir que, caso a SPE (Sociedade de Propósito Específico / Validação Financeira) reprove a demanda, o fluxo seja encerrado e bloqueado de forma definitiva.

#### Pré-condições
* Demanda com BP anexado e aprovada pelo cliente.
* Status atual: `Aguardando Aprovação SPE`.

#### Passos para reproduzir
1. Acessar o HOT com perfil de aprovador **SPE**.
2. Selecionar a demanda pendente.
3. Clicar em **Reprovar**.
4. Informar a justificativa de reprovação.
5. Verificar o status final da demanda no HOT e no BP Web.

#### Resultado Esperado
* O status da demanda deve atualizar para `Reprovado SPE`.
* O fluxo comercial é redirecionado para elaborar PT/PC e no BP precisa ser possível criar uma versão para ajustar as diferenças.

#### Evidência
*(Insira aqui o print da demanda marcada como Reprovado SPE)*

**Resultado:**
( ) Aprovado
( ) Reprovado

---

### CV-EX-004/CT-EX-004 - Aprovação/Rejeição de Desconto (LPU Sim e Target < Receita Gerada)

> [!NOTE]
> Este caso de teste aplica-se apenas caso a pessoa opte por executar o caminho com **LPU = SIM**.
> Se o usuário optar por prosseguir pelo caminho **Sem LPU**, este cenário será ignorado (bypassed) e não passará por essa validação.

#### Objetivo
Validar o fluxo de aprovação de alçada de desconto quando a LPU está marcada como "Sim" e o valor Target estipulado é menor do que a receita total calculada para o projeto.

#### Pré-condições
* Criação de uma simulação de BP com a opção **LPU = Sim**.
* O valor de receita total do projeto gerado pelos itens calculados deve ser superior ao valor Target definido (ex: Receita total R$ 150.000,00 | Target R$ 130.000,00).

#### Passos para reproduzir
1. Criar um novo BP de simulação.
2. Definir o campo **LPU = Sim**.
3. Informar um valor no campo **Target** que seja **menor** que a receita gerada pelo projeto.
4. Submeter o BP para aprovação.
5. **Cenário A (Aprovação de Desconto):**
   * Acessar com perfil aprovador responsável pela alçada de desconto.
   * Aprovar a solicitação de desconto.
   * Verificar se o BP avança para a aprovação regular.
6. **Cenário B (Rejeição de Desconto):**
   * Em uma nova simulação nas mesmas condições, o aprovador seleciona **Rejeitar Desconto**.
   * Verificar se o BP retorna ao Elaborador solicitando revisão do Target ou dos itens da LPU.

#### Resultado Esperado
* O sistema identifica automaticamente a diferença entre o Target e a Receita e aciona o fluxo de aprovação de desconto por alçada.
* **Se Aprovado**: O fluxo segue adiante normalmente.
* **Se Rejeitado**: O BP é bloqueado e retorna para ajuste do elaborador.

#### Evidência
*(Insira aqui prints demonstrando a tela com Target menor que receita, o alerta/fluxo de desconto gerado e as telas de aprovação/rejeição)*

**Resultado:**
( ) Aprovado
( ) Reprovado

---

### CV-EX-005/CT-EX-005 - Solicitação de Alta com Sucesso (Pós-Correções)

#### Objetivo
Garantir que a demanda, após passar e ser devidamente corrigida nos fluxos de exceção anteriores (como reenvio após ajuste ou aprovação de desconto), possa ser finalizada com sucesso ao atingir a Solicitação de Alta para o B.HUB.

#### Pré-condições
* BP aprovado (com desconto aprovado ou ajustes efetuados).
* Demanda concluída no HOT com pedido/PO vinculados.
* Cobertura financeira em 100%.

#### Passos para reproduzir
1. Acessar o BP Web > Grupo.
2. Localizar o BP aprovado com situação `Pronta para alta`.
3. Selecionar a opção **Fluxo de Alta**.
4. Validar se os dados do projeto, POs cadastradas e cobertura financeira (100%) estão corretos na modal.
5. Clicar em **Solicitar Alta**.
6. Acessar o B.HUB e verificar se a solicitação foi integrada com sucesso.

#### Resultado Esperado
* A solicitação de alta é criada e integrada ao B.HUB.
* O status do BP muda para `Aguardando Alta` e o responsável passa a ser o `PCP` (o responsável só vira Dir. de Operações na hora de solicitar alta se for caso de arranque).
* O teste do fluxo é concluído com sucesso neste ponto.

#### Evidência
*(Insira aqui os prints da modal de solicitação de alta e a integração no B.HUB)*

**Resultado:**
( ) Aprovado
( ) Reprovado

---

## 7. REGISTRO DE MELHORIAS

| ID | Descrição | Impacto | Prioridade | Responsável |
| :--- | :--- | :--- | :--- | :--- |
| **OM-EX-001** | | | | |
| **OM-EX-002** | | | | |

---

## 8. CONTROLE DE EXECUÇÃO

| Caso de Teste | Executor | Data | Resultado | Evidência |
| :--- | :--- | :--- | :--- | :--- |
| **CT-EX-001** | | | ( ) Passou ( ) Falhou | |
| **CT-EX-002** | | | ( ) Passou ( ) Falhou | |
| **CT-EX-003** | | | ( ) Passou ( ) Falhou | |
| **CT-EX-004** | | | ( ) Passou ( ) Falhou | |
| **CT-EX-005** | | | ( ) Passou ( ) Falhou | |

---

## 9. CRITÉRIOS DE ACEITAÇÃO

| Critério | Resultado |
| :--- | :--- |
| Reenvio após Rejeição do Gestor funciona com sucesso | ( ) Sim ( ) Não |
| Rejeição pelo Cliente encerra/bloqueia adequadamente | ( ) Sim ( ) Não |
| Reprovação pela SPE impede faturamento e encerra fluxo | ( ) Sim ( ) Não |
| Desvio de Desconto (Target < Receita) exige alçada correta em LPU Sim | ( ) Sim ( ) Não |
| O fluxo sem LPU não exige validação de Target/Desconto | ( ) Sim ( ) Não |
| Integração da Solicitação de Alta com o B.HUB ocorre com sucesso pós-ajustes | ( ) Sim ( ) Não |

---

## 10. RESUMO EXECUTIVO DA HOMOLOGAÇÃO

**Resultado Geral:**
( ) APROVADO PARA PRODUÇÃO
( ) APROVADO COM RESSALVAS
( ) REPROVADO

**Riscos Identificados:**
* ...

**Pendências:**
* ...

**Observações:**
* ...
