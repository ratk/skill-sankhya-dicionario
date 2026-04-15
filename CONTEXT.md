# CONTEXT — Próximas Evoluções da Skill sankhya-dicionario

## Estado Atual (2026-04-14)

**Cobertura:**
- ~450+ tabelas mapeadas
- 9 arquivos de referência (~633KB total)
- Fontes: TDDTAB, TDDCAM, TDDOPC, ALL_CONSTRAINTS, TDDINS, TDDLIG, TDDLGC

**Arquivos gerados:**

| Arquivo | Conteúdo | Tamanho |
|---|---|---|
| `tabelas-tdd.md` | 14 TDD (metamodelo) + mapa de instâncias | 22KB |
| `tabelas-tsi.md` | 50 TSI (usuários, empresas, cidades...) | 47KB |
| `tabelas-tgf-core.md` | 22 TGF core (CAB, ITE, FIN, PAR, PRO...) | 71KB |
| `tabelas-tgf-outros.md` | 113 TGF complementares | 95KB |
| `tabelas-relacionadas.md` | Filhas/lookups de TGFCAB, TGFITE, TGFPAR | 39KB |
| `tabelas-tcs-tcb.md` | TCS (CRM/OS/Contratos) + TCB (Contabilidade) | 42KB |
| `tabelas-tgw-tim.md` | TGW (WMS) + TIM (Imobiliário) | 59KB |
| `tabelas-tpr-tri-tga.md` | TPR (Produção) + TRI (REINF) + TGA (Agro) | 54KB |
| `tabelas-ligacoes.md` | TDDLIG + TDDINS + TDDLGC (entityNames) | 100KB |

**Prefixos cobertos:** TDD, TSI, TGF, TCS, TCB, TGW, TGC, TIM, TPR, TRI, TGA, TSE

---

## Pendentes / Próximas Adições

### Prefixos ainda sem cobertura

| Prefixo | Qtd | Domínio estimado |
|---|---|---|
| `TFP*` | 748 | Folha de Pagamento / Pessoal (TFPFUN, TFPFOL, TFPCAB...) |
| `TRD*` | 184 | Relatórios e BI — apenas TRDEAC e TRDBLC no dicionário |
| `CMD*` | 46 | Poucos dados no TDDCAM neste banco |

### Tabelas relacionadas ainda não mapeadas

Ainda faltam as ligações de:
- `TGFFIN` (Financeiro) → TSICTA, TGFTIT, TGFNAT como lookups
- `TGFPRO` (Produto) → grupos, famílias, séries, unidades
- `TGFTOP` (Tipo de Operação) → tributação, CFOP, alíquotas

### TFP (Folha de Pagamento) — prioridade se necessário

Tabelas chave do módulo pessoal:
- `TFPFUN` — Funcionários (entidade central)
- `TFPFOL` — Eventos da Folha
- `TFPCAB` — Cabeçalho do eSocial
- `TFPEVE` — Eventos
- `TFPDEP` — Departamentos
- `TFPLOT` — Lotações
- `TFPCAR` — Cargos
- `TFPHOR` — Carga Horária
- `TFPFER` — Férias
- `TFPPON` — Ponto

---

## Processo de Atualização

```bash
# Para adicionar TFP ou outros prefixos:
# 1. Adicionar prefixo nas queries de extract_and_generate.sh
# 2. Adicionar arquivo de saída em gen_skill.py
# 3. Rodar:
bash ~/.claude/skills/sankhya-dicionario/scripts/extract_and_generate.sh

# Para geração incremental (sem re-extrair):
python3 /tmp/gen_novos.py   # script reutilizável
```

---

## Notas Técnicas

- **Encoding Oracle:** sqlplus retorna `?` para acentos. A função `fix()` em `gen_skill.py` faz substituição parcial — não é perfeito mas é legível.
- **Tabelas `_RP_`:** backups de migration — excluir sempre nas queries.
- **Python oracledb:** thin mode não suporta esta versão Oracle. Usar sqlplus via `docker exec`.
- **TDDCAM.SISTEMA:** não usar como filtro — retorna 0 neste banco.
- **TRD*:** apenas 2 tabelas no dicionário (TRDEAC, TRDBLC) — provavelmente geradas dinamicamente.
- **CMD*:** baixa cobertura no TDDCAM — módulo pode não ter metadados registrados.
