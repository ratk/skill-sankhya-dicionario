# Skill: sankhya-dicionario

Conhecimento avançado sobre o **dicionário de dados do Sankhya OM** extraído diretamente do banco Oracle de produção.

## O que cobre

- **199 tabelas** mapeadas com campos, descrições, PKs, FKs e opções de campos
- **~13.000 campos** com descrições do próprio dicionário Sankhya (`TDDCAM`)
- Tabelas **TDD\*** — metamodelo interno (14 tabelas)
- Tabelas **TSI\*** — sistema/configuração (50 tabelas)
- Tabelas **TGF\*** — backoffice comercial/fiscal/financeiro (135 tabelas)
- Relacionamentos completos entre tabelas (chaves estrangeiras reais do banco)
- Enums/opções de campos (valores válidos para campos como `TIPMOV`, `RECDESP`, `CONFIRMADA`, etc.)

## Como foi gerada

A skill foi criada via extração direta do banco Oracle Sankhya OM (`SKCONTAINER` / `XE`):

| Fonte | Conteúdo |
|---|---|
| `TDDTAB` | Descrições de 3.119 tabelas do sistema |
| `TDDCAM` | Descrições de 42.076 campos (tipo, tamanho, ordem) |
| `TDDOPC` + `TDDCAM` | 9.481 opções de campos (enums do sistema) |
| `ALL_CONS_COLUMNS` | 4.712 colunas de chaves primárias |
| `ALL_CONSTRAINTS` | 2.132 relacionamentos FK entre tabelas |

**Data da extração:** 2026-04-14  
**Banco:** Oracle XE — `localhost:1521` / schema `SKCONTAINER`  
**Container Docker:** `wildfly-docker-sankhya-skdev-oracle-addon-1`

## Estrutura dos Arquivos

```
sankhya-dicionario/
├── SKILL.md                          ← Skill principal (carregada automaticamente)
├── README.md                         ← Esta documentação
└── references/
    ├── tabelas-tdd.md               ← 14 tabelas TDD (metamodelo interno)
    ├── tabelas-tsi.md               ← 50 tabelas TSI (sistema/configuração)
    ├── tabelas-tgf-core.md          ← 22 tabelas TGF core (71KB)
    └── tabelas-tgf-outros.md        ← 113 tabelas TGF complementares (95KB)
```

## Tabelas Mais Importantes por Categoria

### Documentos Comerciais
| Tabela | Descrição | PK |
|---|---|---|
| `TGFCAB` | Cabeçalho de notas e pedidos | `NUNOTA` |
| `TGFITE` | Itens de nota | `NUNOTA`, `SEQUENCIA` |
| `TGFFIN` | Títulos financeiros | `NUFIN` |
| `TGFORD` | Ordens de carga | `ORDEMCARGA` |

### Cadastros Base
| Tabela | Descrição | PK | Refs |
|---|---|---|---|
| `TGFPAR` | Parceiros (clientes/fornecedores/motoristas) | `CODPARC` | 152 |
| `TGFPRO` | Produtos | `CODPROD` | 123 |
| `TGFEMP` | Empresa Financeiro | `CODEMP` | 102 |
| `TGFVEN` | Vendedores Representantes | `CODVEND` | 22 |
| `TGFCTT` | Contatos dos Parceiros | `CODCTTO` | 16 |
| `TGFVEI` | Veículos | `CODVEI` | 16 |

### Configuração Operacional
| Tabela | Descrição | PK | Refs |
|---|---|---|---|
| `TGFTOP` | Tipos de Operação | `CODTIPOPER` | 16 |
| `TGFNAT` | Natureza de Receitas e Despesas | `CODNAT` | 26 |
| `TGFLOC` | Locais de estoque | `CODLOCAL` | 39 |
| `TGFVOL` | Volumes/Unidades de medida | `CODVOL` | 29 |
| `TGFEST` | Estoque (posição) | composta | — |
| `TGFTIT` | Tipos de Título | `CODTIPTIT` | 16 |
| `TGFTAB` | Tabelas de Preço | `CODTAB` | — |
| `TGFNTA` | Nome das Tabelas de Preço | `CODTAB` | 30 |
| `TGFSER` | Série Produto | `CODSER` | — |
| `TGFCFO` | Código Fiscal de Operações (CFOP) | `CODCFO` | 13 |
| `TGFGRU` | Grupos de Produtos | `CODGRUPOPROD` | 12 |
| `TGFFAM` | Família de Produtos | `CODFAM` | — |

### Sistema (TSI)
| Tabela | Descrição | PK | Refs |
|---|---|---|---|
| `TSIUSU` | Usuários do sistema | `CODUSU` | 272 |
| `TSIEMP` | Empresas | `CODEMP` | 38 |
| `TSICTA` | Contas Bancárias | `CODCTABCOINT` | 32 |
| `TSICUS` | Centros de Resultado | `CODCENCUS` | 30 |
| `TSICID` | Cidades | `CODCID` | 29 |
| `TSIUFS` | Unidades Federativas | `UF` | 23 |
| `TSIBCO` | Bancos | `CODBCO` | 12 |
| `TSIBAI` | Bairros | `CODBAI` | — |
| `TSIEND` | Endereços | `CODEND` | — |
| `TSIGRU` | Grupos de usuários | `CODGRUPO` | — |
| `TSIPAR` | Parâmetros do sistema | — | — |

### Metamodelo (TDD)
| Tabela | Descrição |
|---|---|
| `TDDTAB` | Descrições de todas as tabelas Sankhya |
| `TDDCAM` | Descrições de todos os campos |
| `TDDINS` | Instâncias (telas e formulários) |
| `TDDOPC` | Opções de campos (enums) |
| `TDDLIG` | Ligações entre tabelas (relacionamentos lógicos) |
| `TDDLGC` | Campos de ligação |
| `TDDREL` | Relacionamentos |
| `TDDPTH` | Paths de navegação |
| `TDDIAC` | Ações disponíveis |
| `TDDCRE` | Credenciais/permissões |
| `TDDPER` | Perfis de permissão |
| `TDDPCO` | Atributos de campo |
| `TDDPRE` | Preferências |
| `TDDEXP` | Exportações |

## Como Atualizar

Para regenerar a skill com dados mais recentes do banco:

```bash
# 1. Extrair dados
bash /tmp/extract_sankhya.sh  # (ou rodar as queries manualmente)

# 2. Processar e gerar arquivos
python3 /tmp/gen_skill.py

# 3. Recriar SKILL.md e README.md se necessário
```

Os scripts de extração estão documentados no histórico do projeto.
Conexão: `SKCONTAINER/tecsis@localhost:1521/XE` via `docker exec wildfly-docker-sankhya-skdev-oracle-addon-1`
