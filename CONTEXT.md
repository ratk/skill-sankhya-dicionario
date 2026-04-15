# CONTEXT — Próximas Evoluções da Skill sankhya-dicionario

## Estado Atual (2026-04-14)

**Cobertura atual:**
- 199 tabelas: 14 TDD + 50 TSI + 135 TGF
- 5 arquivos de referência (~388KB total)
- Fontes usadas: TDDTAB, TDDCAM, TDDOPC, ALL_CONSTRAINTS, TDDINS, TDDLIG, TDDLGC

**Prefixos já cobertos:** TGF (parcial), TSI, TDD

---

## Próximas Adições Planejadas

### 1. Prefixos com mais de 50 tabelas (ainda não cobertos)

Extrair via TDDTAB + TDDCAM + TDDLIG, seguindo o mesmo processo dos TGF/TSI.

| Prefixo | Qtd | Domínio |
|---|---|---|
| `TCS*` | 133 | CRM / Contratos / Serviços / Projetos / OS |
| `TRD*` | 184 | Relatórios e BI |
| `TIM*` | 147 | Módulo específico (contratos, imóveis) |
| `TGW*` | 135 | WMS — Gestão de Armazém |
| `TPR*` | 119 | (verificar domínio) |
| `TCB*` | 112 | Contábil / Planos de Conta |
| `TRI*` | 95  | (verificar domínio) |
| `TGA*` | 71  | (verificar domínio) |
| `TSE*` | 64  | (verificar domínio) |
| `CMD*` | 46  | (verificar domínio) |

**Como gerar:** Adicionar os prefixos nas queries do `scripts/extract_and_generate.sh`
e incluir novos blocos em `tabelas-tgf-outros.md` ou criar arquivos específicos por prefixo.

---

### 2. Tabelas Relacionadas a TGFCAB (ainda não mapeadas)

Identificadas via TDDLIG — tabelas filhas/lookup de CabecalhoNota:

| Tabela | Instância | Relação |
|---|---|---|
| `TGFACT` | AcompanhamentoNota | filha de TGFCAB |
| `TGFCOM` | ValorComissaoVendedor | filha de TGFCAB |
| `TGFDFN` | DescontoFinanceiro | filha de TGFCAB |
| `TGFFOP` | FinalidadeOperacao | lookup de TGFCAB |
| `TGFOBS` | ObservacaoNotasFiscais | filha de TGFCAB |
| `TGFTPV` | TipoNegociacao | lookup de TGFCAB |
| `TGFTPD` | TipoPedido | lookup de TGFCAB |
| `TGFVTP` | ViaTransporte | lookup de TGFCAB |
| `TGFSFP` | SimulacaoFormaPagamento | filha de TGFCAB |
| `TGFCCM` | ComissaoMultipla | filha de TGFCAB |
| `TGFCFR` | TabelaCalculoFrete | lookup de TGFCAB |
| `TGFNCE` | ColetaEntregaPorNota | filha de TGFCAB |
| `TGFSEG` | SeguroTransporte | filha de TGFCAB |
| `TGFODP` | OrdemDespacho | filha de TGFCAB |
| `TGFDANT` | DocumentoAnterior | filha de TGFCAB |
| `TSIMOE` | Moeda | lookup de TGFCAB |
| `TCSCON` | Contrato | lookup de TGFCAB |
| `TCSOSE` | OrdemServico | lookup de TGFCAB |

---

### 3. Tabelas Relacionadas a TGFITE (ainda não mapeadas)

| Tabela | Instância | Relação |
|---|---|---|
| `TGFICM` | AliquotaICMS | filha de TGFITE |
| `TGFIII` | ImpostosImportacao | filha de TGFITE |
| `TGFCTD` | CustoDevolucao | filha de TGFITE |
| `TGFCUSITE` | CustoItem | filha de TGFITE |
| `TGFDTP` | PrevisaoEntrega | filha de TGFITE |
| `TGFHIP` | HistoricoItemPendente | filha de TGFITE |
| `TGFNRR` | CadastroNaturezaRendimentos | lookup de TGFITE |
| `TGWEND` | EnderecoArmazenagem | lookup de TGFITE |

---

### 4. Tabelas Relacionadas a TGFPAR (ainda não mapeadas)

| Tabela | Instância | Relação |
|---|---|---|
| `TGFTPP` | Perfil | lookup de TGFPAR |
| `TGFCPL` | ComplementoParc | filha de TGFPAR |
| `TGFPAP` | RelacionamentoParceiroProduto | filha de TGFPAR |
| `TGFPPZ` | PrazoPorParceiro | filha de TGFPAR |
| `TGFPPA` | PerfilParceiro | lookup de TGFPAR |
| `TGFIMA` | AliquotaImposto | filha de TGFPAR |
| `TGFLCP` | CreditoPorParceiro | filha de TGFPAR |
| `TGFDFP` | DescontoFinanceiroParcPeriodo | filha de TGFPAR |
| `TGFDPP` | DependenteIR | filha de TGFPAR |
| `TGFPAEM` | ParceiroEmpresGrupoIcms | filha de TGFPAR |
| `TGFPAS` | ParceiroServico | filha de TGFPAR |
| `TCBPLA` | PlanoConta | lookup de TGFPAR |
| `TGCCRED` | GrupoAnaliseCredito | lookup de TGFPAR |
| `TGCPAR` | LimiteCredito | filha de TGFPAR |
| `TSIPAI` | Pais | lookup de TGFPAR |
| `TSIREG` | Regiao | lookup de TGFPAR |
| `TSIRTEF` | RedesTEF | lookup de TGFPAR |

---

## Processo de Extração (Reproduzível)

```bash
# Pré-requisito: container Docker rodando
docker ps | grep wildfly-docker-sankhya-skdev-oracle-addon-1

# Extração completa + geração
bash ~/.claude/skills/sankhya-dicionario/scripts/extract_and_generate.sh
```

Para adicionar novos prefixos, editar as queries em `scripts/extract_and_generate.sh`
e `scripts/gen_skill.py` — adicionar os novos prefixos nas cláusulas `WHERE NOMETAB LIKE`.

---

## Notas Técnicas

- **Encoding Oracle:** sqlplus retorna `?` no lugar de caracteres acentuados. A função `fix()` em `gen_skill.py` faz substituição parcial. Para melhor resultado, usar NLS_CHARACTERSET UTF8 ou ajustar o script.
- **Tabelas _RP_:** são backups de migration (`TDDCAM_RP_4_17B52` etc.) — excluir sempre.
- **Python oracledb:** não funciona em thin mode com esta versão Oracle. Usar sqlplus via `docker exec`.
- **TDDCAM.SISTEMA:** valor `'S'` não é um filtro útil neste banco (retorna 0 registros). Não usar.
