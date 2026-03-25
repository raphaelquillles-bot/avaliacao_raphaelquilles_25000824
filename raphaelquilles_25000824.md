# Avaliação — Engenharia de Software
## Sistema Integrado de Gestão de Farmácia — MVP

**Aluno:** [Raphael lucio Quilles]  
**RA:** [25000824]  
**Data:** 25/03/2026

---

## 1. Definição do MVP
Meu MVP cobre o **módulo de frente de caixa (PDV)** e o **controle básico de estoque**. O foco é garantir a jornada de compra do cliente, desde a identificação até o pagamento, com tratamento específico para medicamentos controlados.

* **Dentro do MVP:** Venda de produtos, cadastro de clientes, validação de receitas para controlados, baixa automática de estoque e fechamento de caixa.
* **Fora do MVP:** Gestão de compras com fornecedores, módulo de RH e integração avançada com convênios nacionais.
* **Por que essas escolhas:** Foquei na operação crítica da farmácia: vender com segurança jurídica (controle de receitas) e precisão de inventário imediata.

---

## 2. Regras de Negócio (RN)
* **RN01 — Retenção de Receita:** Medicamentos de uso controlado só podem ser vendidos mediante o registro dos dados da receita.
* **RN02 — Desconto Fidelidade:** Clientes cadastrados recebem 10% de desconto em medicamentos genéricos.
* **RN03 — Itens Vencidos:** O sistema deve bloquear automaticamente a venda de produtos cuja data de validade seja menor ou igual à data atual.
* **RN04 — Limite de Cancelamento:** Uma venda finalizada só pode ser cancelada pelo Gerente em até 15 minutos após a emissão do cupom.
* **RN05 — Estoque Crítico:** O sistema deve emitir um alerta visual quando um item atingir o estoque mínimo de 5 unidades.

---

## 3. Requisitos Funcionais (RF)
* **RF01:** O sistema deve permitir o cadastro e edição de medicamentos (Nome, Princípio Ativo, Lote, Validade).
* **RF02:** O sistema deve permitir a busca de produtos por nome ou código de barras.
* **RF03:** O sistema deve permitir o cadastro de clientes com CPF e dados de contato.
* **RF04:** O sistema deve registrar a venda e realizar a baixa automática no estoque.
* **RF05:** O sistema deve processar pagamentos via Dinheiro, Cartão e PIX.
* **RF06:** O sistema deve permitir a abertura e o fechamento de caixa por operador.
* **RF07:** O sistema deve emitir o cupom fiscal simplificado após a confirmação do pagamento.
* **RF08:** O sistema deve gerar relatórios diários de faturamento por período.

---

## 4. Requisitos Não Funcionais (RNF)
* **RNF01:** O sistema deve exigir login e senha para acesso, com perfis distintos (Operador vs Gerente).
* **RNF02:** O tempo de resposta para busca de produtos não deve ultrapassar 1 segundo.
* **RNF03:** O sistema deve realizar backup automático do banco de dados a cada 24 horas.
* **RNF04:** A interface deve ser otimizada para uso via teclado (atalhos de PDV).

---

## 5. Casos de Uso (Diagrama Geral)

graph LR
    OP((Operador))
    GE((Gerente))

    OP --> UC01(UC01: Efetuar Venda)
    UC01 -.->|include| UC02(UC02: Consultar Estoque)
    UC01 -.->|include| UC03(UC03: Autenticar Usuário)
    UC01 -.->|include| UC04(UC04: Registrar Receita)
    UC05(UC05: Aplicar Desconto) -.->|extend| UC01
    UC06(UC06: Segunda Via Cupom) -.->|extend| UC01
    UC07(UC07: Cancelar Venda) -.->|extend| UC01

    OP --> UC08(UC08: Cadastrar Cliente)
    GE --> UC09(UC09: Gerar Relatório)
    UC09 -.->|include| UC03
    GE --> UC10(UC10: Manter Medicamento)
---

## 6. Documentação dos Casos de Uso

### UC01 — Efetuar Venda
* **Ator(es):** Operador de Caixa
* **Descrição:** Registrar produtos e finalizar o pagamento do cliente.
* **Pré-condições:** Caixa aberto e Operador logado.
* **Pós-condições:** Venda concluída e estoque baixado.
* **Fluxo Principal:** 1. Iniciar venda; 2. Escanear produtos; 3. Validar estoque; 4. Selecionar pagamento; 5. Emitir cupom.
* **Fluxo Alternativo:** FA01: Item controlado exige receita médica (UC04).
* **Diagrama de Atividades:**
```mermaid
graph TD
    Start((Início)) --> Scan[Informar Produto]
    Scan --> Stock{Tem estoque?}
    Stock -- Não --> Error[Alerta estoque insuficiente] --> Scan
    Stock -- Sim --> Add[Adicionar ao Carrinho]
    Add --> More{Mais itens?}
    More -- Sim --> Scan
    More -- Não --> Pay[Processar Pagamento]
    Pay --> End((Fim))
