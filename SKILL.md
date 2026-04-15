---
name: sankhya-dicionario
description: >
  Conhecimento avançado sobre o dicionário de dados do Sankhya OM: tabelas TDD* (metamodelo
  interno), TSI* (sistema/configuração) e TGF* (backoffice comercial/fiscal/financeiro).
  Acionar quando o usuário perguntar sobre estrutura de tabelas Sankhya, campos de TGFCAB,
  TGFPAR, TGFPRO, TGFFIN, TGFITE, TGFEST, TGFTOP, TGFNAT, TGFVEN, TSIUSU, TSIEMP, TSICID,
  TSIBCO, relacionamentos entre tabelas, chaves primárias e estrangeiras, significado de campos,
  opções de campos (enums), metamodelo TDDTAB/TDDCAM, dicionário de dados Sankhya,
  "qual tabela armazena X", "quais campos tem Y", "como relacionar A com B",
  "tabelas mais utilizadas", "estrutura do banco Sankhya", "campos adicionais AD_".
version: 1.0.0
---

# Dicionário de Dados Sankhya OM

Especialidade: estrutura interna do banco de dados Sankhya OM — tabelas, campos,
relacionamentos, PKs, FKs, enums e metamodelo.

**Base de conhecimento:** extraída diretamente do banco Oracle de produção via
`TDDTAB` (descrições de tabelas) e `TDDCAM` (descrições de campos) — 199 tabelas,
~13.000 campos mapeados.

---

## Organização do Banco Sankhya OM

### Prefixos das Tabelas

| Prefixo | Domínio | Qtd |
|---|---|---|
| `TGF*` | Gestão Fiscal / Backoffice (comercial, financeiro, fiscal) | ~1.437 |
| `TFP*` | Folha de Pagamento / Pessoal | ~748 |
| `TSI*` | Sistema / Configuração (usuários, empresas, cidades, bancos) | ~240 |
| `TDD*` | Dicionário de Dados (metamodelo interno Sankhya) | ~197 |
| `TRD*` | Relatórios e BI | ~184 |
| `TIM*` | (módulo específico) | ~147 |
| `TCS*` | CRM / Contratos / Serviços / Projetos | ~133 |
| `TGW*` | WMS — Gestão de Armazém | ~135 |
| `AD_*` | Campos/tabelas adicionais (customizações) | — |

### Camadas de Importância

```
Críticas (núcleo):    TGFPAR, TGFPRO, TGFCAB, TGFITE, TGFFIN, TGFEMP
Operacionais:         TGFTOP, TGFNAT, TGFVEN, TGFEST, TGFLOC, TGFVOL, TGFORD
Configuração:         TSIUSU, TSIEMP, TSICID, TSIUFS, TSIBCO, TSIBAI, TSIEND
Tributário/Fiscal:    TGFCFO, TGFISS, TGFALI, TGFTCFOP, TGFPAEM
Preços:               TGFTAB, TGFNTA, TGFITE (VLRUNIT, VLRTOT)
Metamodelo:           TDDTAB, TDDCAM, TDDINS, TDDOPC, TDDLIG, TDDLGC
```

---

## Relacionamentos Centrais

```
TGFPAR (parceiro) ──────────────────────────────────┐
TSIEMP (empresa) ──────────────────────────────┐    │
TGFTOP (tipo operação) ─────────────────────┐  │    │
                                            │  │    │
TGFCAB (cabeçalho nota)  ←── CODEMP ────────┘  │    │
                          ←── CODPARC ─────────┘────┘
                          ←── CODTIPOPER ─────────────→ TGFTOP
                          ←── CODVEND ────────────────→ TGFVEN
                          │
                          └──→ TGFITE (itens)
                                ←── CODPROD ────────→ TGFPRO
                                ←── CODLOCAL ───────→ TGFLOC
                                ←── CODVOL ─────────→ TGFVOL
                          │
                          └──→ TGFFIN (financeiro/títulos)
                                ←── CODPARC ────────→ TGFPAR
                                ←── CODEMP ─────────→ TSIEMP

TGFEST (estoque):  CODPROD + CODLOCAL + CODEMP → posição de estoque
TSIUSU (usuário):  CODUSU → referenciado em TGFCAB.CODUSU, TGFITE, TGFFIN etc.
```

---

## Convenções do Banco Sankhya

### Tipos de Campos (TDDCAM.TIPCAMPO)

| Código | Tipo Oracle | Uso |
|---|---|---|
| `I` | NUMBER | Inteiro / FK / PK |
| `F` | NUMBER(p,s) | Decimal / monetário |
| `S` | VARCHAR2 | Texto / flag S/N / código |
| `D` | DATE | Data |
| `T` / `H` | DATE | Data+Hora (timestamp) |
| `L` | VARCHAR2(1) | Lógico: `S`=sim, `N`=não |
| `M` | CLOB | Memo / texto longo |
| `B` | BLOB | Binário / arquivo |

### Campos Padrão Presentes na Maioria das Tabelas

| Campo | Tipo | Significado |
|---|---|---|
| `CODEMP` | I | Código da empresa (FK → TSIEMP) |
| `CODUSU` | I | Usuário da última alteração (FK → TSIUSU) |
| `DTALTER` | H | Data/hora da última alteração |
| `DTINCLUSAO` | D | Data de inclusão do registro |
| `ADICIONAL` | S(1) | `S`=campo adicional customizado, `N`=nativo |

### Padrão de Flags S/N

A maioria dos campos booleanos usa `VARCHAR2(1)` com valores `'S'` (sim) e `'N'` (não).
Em `DynamicVO`, esses campos podem ser lidos com `vo.asBoolean("CAMPO")` ou como String.

### Campos Adicionais (AD_)

Campos `AD_*` são customizações sobre tabelas nativas. Criados via:
- **Addon Studio:** `<nativeTable>` no `datadictionary/`
- **Módulo Java:** XML do Construtor de Telas

Prefixo `AD_` é **reservado pelo Sankhya** — addons devem usar prefixo próprio (ex: `SGT_`).

---

## Metamodelo TDD — Como Consultar

O próprio Sankhya armazena seu dicionário nas tabelas TDD. Útil para:
- Listar campos de uma tabela: `SELECT * FROM TDDCAM WHERE NOMETAB = 'TGFCAB' ORDER BY ORDEM`
- Obter descrição de tabela: `SELECT DESCRTAB FROM TDDTAB WHERE NOMETAB = 'TGFCAB'`
- Ver opções de um campo: `SELECT o.VALOR, o.OPCAO FROM TDDOPC o JOIN TDDCAM c ON c.NUCAMPO=o.NUCAMPO WHERE c.NOMETAB='TGFCAB' AND c.NOMECAMPO='TIPMOV'`
- Ver relacionamentos: `SELECT * FROM TDDLIG WHERE NOMETAB = 'TGFCAB'`

---

## Como Usar Esta Skill

Ao responder perguntas sobre estrutura do banco Sankhya:

1. **"Qual tabela armazena X?"** → Consultar `references/tabelas-tgf-core.md` e `references/tabelas-tsi.md`
2. **"Quais campos tem TGFCAB?"** → `references/tabelas-tgf-core.md` seção TGFCAB
3. **"Como relacionar parceiro com nota?"** → TGFCAB.CODPARC → TGFPAR.CODPARC
4. **"O que é TDDCAM?"** → `references/tabelas-tdd.md`
5. **"Quais opções o campo TIPMOV aceita?"** → Opções da seção TGFCAB em tabelas-tgf-core.md
6. **"Quais tabelas TGF existem?"** → `references/tabelas-tgf-outros.md` (catálogo)
7. **"Qual entityName usar no JapeFactory para X?"** → `references/tabelas-ligacoes.md`
8. **"Como TGFCAB se liga a TGFPAR logicamente?"** → `references/tabelas-ligacoes.md` seção TGFCAB

---

## Referências

| Conteúdo | Arquivo |
|---|---|
| Metamodelo interno (TDDTAB, TDDCAM, TDDINS, TDDOPC, TDDLIG...) + mapa de instâncias | `references/tabelas-tdd.md` |
| Sistema/configuração (TSIUSU, TSIEMP, TSICID, TSIBCO, TSIBAI...) | `references/tabelas-tsi.md` |
| Tabelas TGF core (TGFPAR, TGFPRO, TGFCAB, TGFITE, TGFFIN, TGFEMP...) | `references/tabelas-tgf-core.md` |
| Tabelas TGF complementares (fiscal, MDF-e, NF-e, comissões, tarefas...) | `references/tabelas-tgf-outros.md` |
| Tabelas filhas/lookup de TGFCAB, TGFITE e TGFPAR (TGFICM, TGFTPP, TSIMOE...) | `references/tabelas-relacionadas.md` |
| TCS (CRM/OS/Contratos/Projetos) e TCB (Contabilidade/Plano de Contas) | `references/tabelas-tcs-tcb.md` |
| TGW (WMS/Armazém) e TIM (Imobiliário) | `references/tabelas-tgw-tim.md` |
| TPR (Produção/Manufatura), TRI (EFD-REINF) e TGA (Agronegócio) | `references/tabelas-tpr-tri-tga.md` |
| Ligações lógicas TDDLIG + instâncias TDDINS + campos TDDLGC (entityNames para JapeFactory) | `references/tabelas-ligacoes.md` |
