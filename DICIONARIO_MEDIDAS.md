# Dicionário de Medidas — TORQ Industrial

204 medidas na tabela `_Medidas`, organizadas por pasta. A coluna Página indica onde a medida aparece diretamente em um visual; medidas sem página são componentes de outras medidas ou ficam disponíveis para exploração.


## 00 | Metas e Limites (parâmetros gerenciais)

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Meta OEE | Meta gerencial de OEE (85%). Parâmetro de gestão, não é dado observado na base. | `0.85` | 0% | Produção & Eficiência |
| Meta Aderência | Meta gerencial de aderência ao plano (98%). Parâmetro de gestão. | `0.98` | 0% | — |
| Meta OTIF | Meta gerencial de OTIF ao cliente (90%). Parâmetro de gestão. | `0.90` | 0% | — |
| Meta Fornecedor no Prazo | Meta gerencial de pontualidade de fornecedores (80%). Parâmetro de gestão. | `0.80` | 0% | — |
| Limite Scrap | Limite gerencial de scrap (0,8% da produção). Parâmetro de gestão. | `0.008` | 0.0% | Qualidade & Custos |
| Limite Downtime | Limite gerencial de downtime (15% das horas planejadas). Parâmetro de gestão. | `0.15` | 0% | — |
| Limite CNQ | Limite gerencial do custo da não qualidade (0,70% do custo total). Parâmetro de gestão. | `0.007` | 0.00% | — |
| Limite Reprovação MP | Limite gerencial de reprovação de matéria-prima no recebimento (2%). | `0.02` | 0.0% | — |
| Limite OPs Abaixo Segurança | Limite gerencial: % de OPs com estoque final de MP abaixo do estoque de segurança (10%). | `0.10` | 0% | — |

## 01 | Produção

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Produção Real | Quantidade total produzida (boa + refugo) nas ordens de produção. | `SUM(fProducao[Qtd Produzida])` | #,0 | Control Tower, Produção & Eficiência |
| Produção Boa | Quantidade produzida aprovada (sem refugo). | `SUM(fProducao[Qtd Boa])` | #,0 | — |
| Plano | Quantidade planejada/demandada nas ordens de produção (demanda_qtd). | `SUM(fProducao[Demanda Qtd])` | #,0 | Control Tower |
| Gap Produção | Produção Real menos Plano. Negativo = produção abaixo do plano. | `[Produção Real] - [Plano]` | +#,0;-#,0;0 | Control Tower, Tooltip Linha |
| Aderência ao Plano % | Produção Real dividida pelo Plano. | `DIVIDE([Produção Real], [Plano])` | 0.0%;-0.0%;0.0% | Control Tower, Produção & Eficiência |
| Atendimento Produto Bom % | Produção Boa dividida pelo Plano: quanto da demanda foi atendido com produto aprovado. | `DIVIDE([Produção Boa], [Plano])` | 0.0%;-0.0%;0.0% | — |
| Nº OPs | Número de ordens de produção. | `COUNTROWS(fProducao)` | #,0 | — |
| OPs Parciais % | Percentual de ordens com status Parcial. | `DIVIDE(CALCULATE(COUNTROWS(fProducao), fProducao[Status OP] = "Parcial"), [Nº OPs])` | 0.0%;-0.0%;0.0% | — |
| Produção por Hora | Unidades produzidas por hora operando. | `DIVIDE([Produção Real], [Horas Operando])` | #,0.0 | — |
| Produção Média Móvel 3M | Média móvel de 3 meses da produção real (linha de tendência; não é previsão). | `ver TMDL (medida com variáveis)` | #,0 | — |

## 02 | Eficiência

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Horas Planejadas | Horas planejadas de operação. | `SUM(fProducao[Horas Planejadas])` | #,0.0 | — |
| Horas Operando | Horas efetivamente operando. | `SUM(fProducao[Horas Operando])` | #,0.0 | — |
| Horas Ritmo Padrão (oculta) | Horas operando ponderadas pela performance da OP (horas equivalentes no ritmo padrão). Base aditiva para agregar a Performance. | `SUM(fProducao[Horas Ritmo Padrão])` | #,0.0 | — |
| Disponibilidade % | Horas Operando / Horas Planejadas. Equivale à utilização do tempo planejado. | `DIVIDE([Horas Operando], [Horas Planejadas])` | 0.0%;-0.0%;0.0% | Control Tower, Produção & Eficiência, Tooltip Linha |
| Performance % | Performance agregada: média da performance das OPs ponderada pelas horas operando. | `DIVIDE([Horas Ritmo Padrão], [Horas Operando])` | 0.0%;-0.0%;0.0% | Control Tower, Produção & Eficiência, Tooltip Linha |
| Qualidade % | Produção Boa / Produção Real (Yield). | `DIVIDE([Produção Boa], [Produção Real])` | 0.0%;-0.0%;0.0% | Control Tower, Produção & Eficiência, Tooltip Linha |
| OEE % | OEE = Disponibilidade × Performance × Qualidade, recalculado no nível de agregação do contexto. | `[Disponibilidade %] * [Performance %] * [Qualidade %]` | 0.0%;-0.0%;0.0% | Control Tower, Produção & Eficiência, Tooltip Linha |
| OEE Gap Meta p.p. | Diferença em pontos percentuais entre o OEE e a meta. | `([OEE %] - [Meta OEE]) * 100` | +0.0;-0.0;0.0 | — |
| OEE Classificação | Classificação do OEE: Na meta (≥ meta), Atenção (até 10 p.p. abaixo) ou Crítico. | `VAR _v = [OEE %] RETURN SWITCH(TRUE(), ISBLANK(_v), BLANK(), _v >= [Meta OEE], "● Na meta", _v >= [Meta OEE] - 0.10, "● Atenção", "● Crítico")` | texto | — |
| OEE Maior Perda | Componente do OEE com o menor índice (onde está a maior perda). | `ver TMDL (medida com variáveis)` | texto | — |
| Taxa Padrão un/h (oculta) | Unidades por hora em ritmo padrão (Produção Real / Horas Ritmo Padrão). Usada para converter horas perdidas em unidades estimadas. | `DIVIDE([Produção Real], [Horas Ritmo Padrão])` | #,0.0 | — |

## 03 | Qualidade

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Refugo | Quantidade refugada (scrap). | `SUM(fProducao[Qtd Refugo])` | #,0 | — |
| Scrap % | Refugo / Produção Real. | `DIVIDE([Refugo], [Produção Real])` | 0.00% | Control Tower, Qualidade & Custos, Tooltip Linha |
| Retrabalho | Quantidade retrabalhada. | `SUM(fProducao[Qtd Retrabalho])` | #,0 | — |
| Retrabalho % | Retrabalho / Produção Real. | `DIVIDE([Retrabalho], [Produção Real])` | 0.00% | Qualidade & Custos |
| Yield % | Rendimento: Produção Boa / Produção Real. | `[Qualidade %]` | 0.0%;-0.0%;0.0% | Qualidade & Custos |
| FPY % (aprox.) | First Pass Yield aproximado: (Produção Boa − Retrabalho) / Produção Real. Assume que as unidades retrabalhadas estão contidas na produção boa. | `DIVIDE([Produção Boa] - [Retrabalho], [Produção Real])` | 0.0%;-0.0%;0.0% | — |
| Taxa de Defeitos % | (Refugo + Retrabalho) / Produção Real. | `DIVIDE([Refugo] + [Retrabalho], [Produção Real])` | 0.00% | — |
| Custo Refugo | Custo das unidades refugadas. | `SUM(fProducao[Custo Refugo])` | \R$ #,0 | — |
| Custo Retrabalho | Custo do retrabalho. | `SUM(fProducao[Custo Retrabalho])` | \R$ #,0 | — |
| Custo Não Qualidade | CNQ = Custo de Refugo + Custo de Retrabalho. | `[Custo Refugo] + [Custo Retrabalho]` | \R$ #,0 | Qualidade & Custos, Tooltip Linha |
| CNQ % do Custo | Custo da Não Qualidade / Custo Total. | `DIVIDE([Custo Não Qualidade], [Custo Total])` | 0.00% | — |
| CNQ Defeitos | CNQ apenas das OPs com causa de defeito relevante (exclui 'Sem defeito relevante'). Base do Pareto e do heatmap. | `CALCULATE([Custo Não Qualidade], KEEPFILTERS(dCausaDefeito[Relevante] = 1))` | \R$ #,0 | Qualidade & Custos |
| Pareto Defeitos % Acum. | Percentual acumulado do CNQ por causa de defeito (ordem decrescente). | `ver TMDL (medida com variáveis)` | 0% | Qualidade & Custos |
| Principal Causa Defeito | Causa de defeito relevante com maior custo da não qualidade no contexto. | `ver TMDL (medida com variáveis)` | texto | — |

## 04 | Downtime

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Horas Parada | Horas de parada (Horas Planejadas − Horas Operando). | `SUM(fProducao[Horas Parada])` | #,0.0 | Produção & Eficiência, Tooltip Linha |
| Downtime % | Horas Parada / Horas Planejadas. | `DIVIDE([Horas Parada], [Horas Planejadas])` | 0.0%;-0.0%;0.0% | Control Tower |
| Horas Setup | Horas de parada por setup/troca. | `SUM(fProducao[Horas Setup])` | #,0.0 | — |
| Horas Manutenção | Horas de parada por manutenção. | `SUM(fProducao[Horas Manutenção])` | #,0.0 | — |
| Horas Falta Material | Horas de parada por falta de material. | `SUM(fProducao[Horas Falta Material])` | #,0.0 | — |
| Paradas Pareto h | Horas de parada das OPs com causa principal relevante (exclui 'Sem parada relevante'). Base do Pareto. | `CALCULATE([Horas Parada], KEEPFILTERS(dCausaParada[Relevante] = 1))` | #,0.0 | Control Tower |
| Pareto Paradas % Acum. | Percentual acumulado das horas de parada por causa principal (ordem decrescente). | `ver TMDL (medida com variáveis)` | 0% | Control Tower |
| Referência 80% | Linha de referência de 80% para os gráficos de Pareto. | `0.8` | 0% | Control Tower, Qualidade & Custos |
| Principal Causa Parada | Causa principal de parada com mais horas no contexto. | `ver TMDL (medida com variáveis)` | texto | — |
| Impacto Downtime un. (est.) | ESTIMATIVA: unidades não produzidas devido às paradas = Horas Parada × Taxa Padrão (un/h). | `[Horas Parada] * [Taxa Padrão un/h]` | #,0 | — |
| Capacidade Teórica un. (est.) | ESTIMATIVA: produção possível se todas as horas planejadas fossem operadas no ritmo padrão = Horas Planejadas × Taxa Padrão. | `[Horas Planejadas] * [Taxa Padrão un/h]` | #,0 | — |
| Perda Ritmo un. (est.) | ESTIMATIVA: unidades perdidas por operar abaixo do ritmo padrão = (Horas Operando − Horas Ritmo Padrão) × Taxa Padrão. | `([Horas Operando] - [Horas Ritmo Padrão]) * [Taxa Padrão un/h]` | #,0 | — |
| Ponte de Perdas un. | Valor de cada etapa da ponte Capacidade Teórica → Produção Boa (unidades estimadas pelas horas perdidas × taxa padrão). | `ver TMDL (medida com variáveis)` | #,0 | Produção & Eficiência |

## 05 | Custos

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Custo Total | Custo total das ordens de produção (matéria-prima + mão de obra + energia + refugo + retrabalho). | `SUM(fProducao[Custo Total OP])` | \R$ #,0 | Qualidade & Custos |
| Custo Matéria-Prima | Custo de matéria-prima (custo unitário × quantidade produzida). | `SUM(fProducao[Custo MP])` | \R$ #,0 | — |
| Custo Mão de Obra | Custo de mão de obra (custo unitário × quantidade produzida). | `SUM(fProducao[Custo Mão de Obra])` | \R$ #,0 | — |
| Custo Energia | Custo de energia (custo unitário × quantidade produzida). | `SUM(fProducao[Custo Energia])` | \R$ #,0 | — |
| Custo Unitário | Custo Total / Produção Real. | `DIVIDE([Custo Total], [Produção Real])` | \R$ #,0.00 | — |
| Custo por Unidade Boa | Custo Total / Produção Boa. Revela deterioração de custo quando há perda de qualidade. | `DIVIDE([Custo Total], [Produção Boa])` | \R$ #,0.00 | Qualidade & Custos |

## 06 | Supply Chain

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Lead Time Previsto (dias) | Lead time médio previsto do fornecedor, em dias. | `AVERAGE(fProducao[Lead Time Previsto])` | #,0.0 | Supply Chain |
| Lead Time Real (dias) | Lead time médio real do fornecedor, em dias. | `AVERAGE(fProducao[Lead Time Real])` | #,0.0 | Supply Chain |
| Desvio Lead Time (dias) | Lead time real − previsto (média). Positivo = atraso. | `AVERAGE(fProducao[Atraso Fornecedor Dias])` | +0.0;-0.0;0.0 | — |
| Fornecedor no Prazo % | Percentual de OPs abastecidas com entrega do fornecedor no prazo (flag da base; tolera até 1 dia de atraso). | `AVERAGE(fProducao[Fornecedor No Prazo])` | 0.0%;-0.0%;0.0% | Supply Chain |
| Atraso Médio Fornecedor (dias) | Atraso médio, em dias, considerando apenas entregas fora do prazo. | `CALCULATE(AVERAGE(fProducao[Atraso Fornecedor Dias]), fProducao[Fornecedor No Prazo] = 0)` | #,0.0 | Supply Chain |
| Lote MP Recebido | Quantidade de matéria-prima recebida nos lotes associados às OPs. | `SUM(fProducao[Lote MP Recebido])` | #,0 | — |
| Lote MP Reprovado | Quantidade de matéria-prima reprovada no recebimento. | `SUM(fProducao[Lote MP Reprovado])` | #,0 | — |
| Reprovação MP % | Lote MP Reprovado / Lote MP Recebido. | `DIVIDE([Lote MP Reprovado], [Lote MP Recebido])` | 0.00% | Supply Chain |

## 07 | Estoques

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Estoque Final Médio MP | Média do estoque final de MP registrado nas OPs. A base traz uma posição por OP (não é razão contínua de estoque), por isso usa-se média. | `AVERAGE(fProducao[Estoque Final MP])` | #,0 | Supply Chain |
| Estoque Segurança Médio MP | Média do estoque de segurança de MP registrado nas OPs. | `AVERAGE(fProducao[Estoque Segurança MP])` | #,0 | Supply Chain |
| Índice Estoque / Segurança | Estoque final médio dividido pelo estoque de segurança médio. | `DIVIDE(SUM(fProducao[Estoque Final MP]), SUM(fProducao[Estoque Segurança MP]))` | 0.00x | — |
| Consumo MP | Consumo de matéria-prima nas OPs. | `SUM(fProducao[Consumo MP])` | #,0 | Supply Chain |
| Recebimento MP | Recebimento de matéria-prima registrado nas OPs. | `SUM(fProducao[Recebimento MP])` | #,0 | — |
| Cobertura (nº de OPs) | Estoque final médio / consumo médio por OP: quantas OPs equivalentes o estoque cobre. | `DIVIDE(AVERAGE(fProducao[Estoque Final MP]), AVERAGE(fProducao[Consumo MP]))` | #,0.0 | Supply Chain |
| Rupturas | Número de OPs com ruptura de estoque de MP. | `SUM(fProducao[Ruptura])` | #,0 | Supply Chain |
| Taxa de Ruptura % | Rupturas / Nº de OPs. | `DIVIDE([Rupturas], [Nº OPs])` | 0.00% | — |
| OPs Abaixo da Segurança | Número de OPs cujo estoque final de MP ficou abaixo do estoque de segurança. | `SUM(fProducao[Abaixo Segurança])` | #,0 | — |
| OPs Abaixo da Segurança % | Percentual de OPs com estoque final de MP abaixo do estoque de segurança. | `DIVIDE([OPs Abaixo da Segurança], [Nº OPs])` | 0.0%;-0.0%;0.0% | Control Tower, Supply Chain |
| Status Estoque | Classificação de risco de estoque no contexto (ex.: por matéria-prima): CRÍTICO se % de OPs abaixo da segurança ≥ 12% ou taxa de ruptura ≥ 3%; ATENÇÃO se ≥ limite gerencial (10%); EXCESSO se índice estoque/segurança > 4; senão SAUDÁVEL. | `ver TMDL (medida com variáveis)` | texto | Supply Chain |
| Materiais em Risco | Número de matérias-primas classificadas como CRÍTICO ou ATENÇÃO. | `COUNTROWS(FILTER(VALUES(dMateriaPrima[Matéria-Prima]), [Status Estoque] IN {"CRÍTICO", "ATENÇÃO"})) + 0` | #,0 | — |

## 08 | Fornecedores

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Supplier Score | Score 0–100 do fornecedor = 40% Pontualidade (% no prazo) + 30% Qualidade do lote (1 − reprovação/3%, limitado a 0–1) + 30% Aderência de lead time (1 − desvio médio/5 dias, limitado a 0–1). Pesos são parâmetros gerenciais. | `ver TMDL (medida com variáveis)` | 0 | Supply Chain |
| Supplier Score Classe | Classe do Supplier Score: A (≥ 75), B (≥ 60), C (< 60). | `VAR _s = [Supplier Score] RETURN SWITCH(TRUE(), ISBLANK(_s), BLANK(), _s >= 75, "A", _s >= 60, "B", "C")` | texto | Supply Chain |
| Fornecedores Fora da Meta | Número de fornecedores com pontualidade abaixo da meta. | `COUNTROWS(FILTER(VALUES(dFornecedor[Fornecedor]), [Fornecedor no Prazo %] < [Meta Fornecedor no Prazo])) + 0` | #,0 | — |

## 09 | Logística

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| OTIF % | Percentual de OPs entregues ao cliente no prazo e na quantidade (flag OTIF da base). | `AVERAGE(fProducao[OTIF])` | 0.0%;-0.0%;0.0% | Control Tower, Supply Chain |
| Entregas Atrasadas | Número de OPs com atraso de entrega ao cliente (> 0 dias). | `CALCULATE(COUNTROWS(fProducao), fProducao[Atraso Entrega Dias] > 0) + 0` | #,0 | — |
| Entregas Atrasadas % | Entregas atrasadas / Nº de OPs. | `DIVIDE([Entregas Atrasadas], [Nº OPs])` | 0.0%;-0.0%;0.0% | Supply Chain |
| Falha In-Full % (inferida) | INFERÊNCIA: OPs sem OTIF e sem atraso de entrega (falha atribuída à quantidade incompleta) / Nº de OPs. | `DIVIDE(CALCULATE(COUNTROWS(fProducao), fProducao[OTIF] = 0, fProducao[Atraso Entrega Dias] <= 0), [Nº OPs])` | 0.0%;-0.0%;0.0% | Supply Chain |
| Atraso Médio Entrega (dias) | Atraso médio, em dias, das entregas atrasadas. | `CALCULATE(AVERAGE(fProducao[Atraso Entrega Dias]), fProducao[Atraso Entrega Dias] > 0)` | #,0.0 | — |
| Prazo Cliente Médio (dias) | Prazo médio acordado com o cliente, em dias. | `AVERAGE(fProducao[Prazo Cliente Dias])` | #,0.0 | — |
| Custo Frete | Custo total de frete. | `SUM(fProducao[Custo Frete])` | \R$ #,0 | — |
| Frete por Unidade | Custo de frete / Produção Boa. | `DIVIDE([Custo Frete], [Produção Boa])` | \R$ #,0.00 | Supply Chain |

## 10 | Comparativos

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Produção Real PA | Produção real no período imediatamente anterior, de mesma duração. Em branco quando não há histórico completo para comparar. | `ver TMDL (medida com variáveis)` | #,0 | — |
| OEE % PA | OEE no período imediatamente anterior, de mesma duração. Em branco quando não há histórico completo para comparar. | `ver TMDL (medida com variáveis)` | 0.0%;-0.0%;0.0% | — |
| Produção Real Δ PA % | Variação da produção real versus o período anterior de mesma duração. | `VAR _pa = [Produção Real PA] RETURN IF(NOT ISBLANK(_pa), DIVIDE([Produção Real] - _pa, _pa))` | +0.0%;-0.0%;0.0% | — |
| OEE Δ PA p.p. | Variação do OEE em pontos percentuais versus o período anterior de mesma duração. | `VAR _pa = [OEE % PA] RETURN IF(NOT ISBLANK(_pa), ([OEE %] - _pa) * 100)` | +0.0;-0.0;0.0 | — |
| Produção Real Δ Mês | Produção Real: último mês do contexto versus o mês anterior (variação relativa). | `ver TMDL (medida com variáveis)` | +0.0%;-0.0%;0.0% | — |
| Aderência ao Plano % Δ Mês | Aderência ao Plano %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| OEE % Δ Mês | OEE %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Disponibilidade % Δ Mês | Disponibilidade %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Performance % Δ Mês | Performance %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Qualidade % Δ Mês | Qualidade %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Scrap % Δ Mês | Scrap %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Retrabalho % Δ Mês | Retrabalho %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Downtime % Δ Mês | Downtime %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Horas Parada Δ Mês | Horas Parada: último mês do contexto versus o mês anterior (variação relativa). | `ver TMDL (medida com variáveis)` | +0.0%;-0.0%;0.0% | — |
| OTIF % Δ Mês | OTIF %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Custo Não Qualidade Δ Mês | Custo Não Qualidade: último mês do contexto versus o mês anterior (variação relativa). | `ver TMDL (medida com variáveis)` | +0.0%;-0.0%;0.0% | — |
| Custo por Unidade Boa Δ Mês | Custo por Unidade Boa: último mês do contexto versus o mês anterior (variação relativa). | `ver TMDL (medida com variáveis)` | +0.0%;-0.0%;0.0% | — |
| Custo Total Δ Mês | Custo Total: último mês do contexto versus o mês anterior (variação relativa). | `ver TMDL (medida com variáveis)` | +0.0%;-0.0%;0.0% | — |
| Fornecedor no Prazo % Δ Mês | Fornecedor no Prazo %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Lead Time Real (dias) Δ Mês | Lead Time Real (dias): último mês do contexto versus o mês anterior (diferença absoluta). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Reprovação MP % Δ Mês | Reprovação MP %: último mês do contexto versus o mês anterior (em pontos percentuais). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Rupturas Δ Mês | Rupturas: último mês do contexto versus o mês anterior (diferença absoluta). | `ver TMDL (medida com variáveis)` | +0.0;-0.0;0.0 | — |
| Frete por Unidade Δ Mês | Frete por Unidade: último mês do contexto versus o mês anterior (variação relativa). | `ver TMDL (medida com variáveis)` | +0.0%;-0.0%;0.0% | — |

## 11 | Simulação (What-if)

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Sim Redução Downtime | Percentual de redução de downtime escolhido no simulador (padrão 10%). | `SELECTEDVALUE('Simulação Downtime'[Redução Downtime], 0.10)` | 0% | — |
| Sim Horas Recuperadas | SIMULAÇÃO: horas de parada recuperadas = Horas Parada × redução escolhida. | `[Horas Parada] * [Sim Redução Downtime]` | #,0.0 | Produção & Eficiência |
| Sim Produção Adicional | SIMULAÇÃO: produção adicional estimada = horas recuperadas × produção por hora operando atual (mantém a performance observada). | `[Sim Horas Recuperadas] * [Produção por Hora]` | #,0 | Produção & Eficiência |
| Sim OEE Projetado | SIMULAÇÃO: OEE com a disponibilidade recalculada após a redução de downtime (performance e qualidade mantidas). | `DIVIDE([Horas Operando] + [Sim Horas Recuperadas], [Horas Planejadas]) * [Performance %] * [Qualidade %]` | 0.0%;-0.0%;0.0% | Produção & Eficiência |
| Sim Ganho OEE p.p. | SIMULAÇÃO: ganho de OEE em pontos percentuais. | `([Sim OEE Projetado] - [OEE %]) * 100` | +0.0;-0.0;0.0 | — |
| Sim Aderência Projetada | SIMULAÇÃO: aderência ao plano somando a produção adicional estimada. | `DIVIDE([Produção Real] + [Sim Produção Adicional], [Plano])` | 0.0%;-0.0%;0.0% | Produção & Eficiência |
| Sim Redução Scrap | Percentual de redução de scrap escolhido no simulador (padrão 20%). | `SELECTEDVALUE('Simulação Scrap'[Redução Scrap], 0.20)` | 0% | — |
| Sim Unidades Recuperadas | SIMULAÇÃO: unidades que deixariam de ser refugadas = Refugo × redução escolhida. | `[Refugo] * [Sim Redução Scrap]` | #,0 | Qualidade & Custos |
| Sim Economia CNQ | SIMULAÇÃO: redução do custo de refugo = Custo Refugo × redução escolhida. | `[Custo Refugo] * [Sim Redução Scrap]` | \R$ #,0 | Qualidade & Custos |
| Sim Custo por Unidade Boa | SIMULAÇÃO: custo por unidade boa após a redução de scrap = (Custo Total − economia) / (Produção Boa + unidades recuperadas). | `DIVIDE([Custo Total] - [Sim Economia CNQ], [Produção Boa] + [Sim Unidades Recuperadas])` | \R$ #,0.00 | Qualidade & Custos |
| Sim Título Downtime | Título dinâmico do simulador de downtime. | `ver TMDL (medida com variáveis)` | texto | Produção & Eficiência |
| Sim Título Scrap | Título dinâmico do simulador de scrap. | `ver TMDL (medida com variáveis)` | texto | Qualidade & Custos |

## 12 | Alertas\Componentes

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| A1 Desvio (oculta) | Alerta 1 (OEE abaixo da meta): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `VAR _gap = DIVIDE([Meta OEE] - [OEE %], [Meta OEE]) RETURN IF(NOT ISBLANK([OEE %]) && _gap > 0, MIN(1, DIVIDE(_gap, 0.20)))` | 0.00 | — |
| A1 Texto (oculta) | Alerta 1 (OEE abaixo da meta): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A1 Ação (oculta) | Alerta 1 (OEE abaixo da meta): ação sugerida. | `ver TMDL (medida com variáveis)` | texto | — |
| A2 Desvio (oculta) | Alerta 2 (Downtime elevado): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `ver TMDL (medida com variáveis)` | 0.00 | — |
| A2 Texto (oculta) | Alerta 2 (Downtime elevado): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A2 Ação (oculta) | Alerta 2 (Downtime elevado): ação sugerida. | `ver TMDL (medida com variáveis)` | texto | — |
| A3 Desvio (oculta) | Alerta 3 (Scrap acima do limite): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `ver TMDL (medida com variáveis)` | 0.00 | — |
| A3 Texto (oculta) | Alerta 3 (Scrap acima do limite): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A3 Ação (oculta) | Alerta 3 (Scrap acima do limite): ação sugerida. | `ver TMDL (medida com variáveis)` | texto | — |
| A4 Desvio (oculta) | Alerta 4 (MP abaixo do estoque de segurança): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `ver TMDL (medida com variáveis)` | 0.00 | — |
| A4 Texto (oculta) | Alerta 4 (MP abaixo do estoque de segurança): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A4 Ação (oculta) | Alerta 4 (MP abaixo do estoque de segurança): ação sugerida. | `"Rever ponto de pedido e estoque de segurança do material; antecipar reposição."` | texto | — |
| A5 Desvio (oculta) | Alerta 5 (Fornecedor com atraso relevante): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `ver TMDL (medida com variáveis)` | 0.00 | — |
| A5 Texto (oculta) | Alerta 5 (Fornecedor com atraso relevante): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A5 Ação (oculta) | Alerta 5 (Fornecedor com atraso relevante): ação sugerida. | `"Follow-up do fornecedor, revisar lead time contratado e avaliar fonte alternativa."` | texto | — |
| A6 Desvio (oculta) | Alerta 6 (Ruptura de estoque): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `VAR _t = [Taxa de Ruptura %] RETURN IF([Rupturas] > 0, MIN(1, DIVIDE(_t, 0.03)))` | 0.00 | — |
| A6 Texto (oculta) | Alerta 6 (Ruptura de estoque): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A6 Ação (oculta) | Alerta 6 (Ruptura de estoque): ação sugerida. | `"Tratar rupturas como prioridade de PCP: reposição emergencial e revisão de parâmetros."` | texto | — |
| A7 Desvio (oculta) | Alerta 7 (OTIF abaixo da meta): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `VAR _meta = [Meta OTIF] RETURN IF(NOT ISBLANK([OTIF %]) && [OTIF %] < _meta, MIN(1, DIVIDE(DIVIDE(_meta - [OTIF %], _meta), 0.5)))` | 0.00 | — |
| A7 Texto (oculta) | Alerta 7 (OTIF abaixo da meta): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A7 Ação (oculta) | Alerta 7 (OTIF abaixo da meta): ação sugerida. | `ver TMDL (medida com variáveis)` | texto | — |
| A8 Desvio (oculta) | Alerta 8 (Custo da não qualidade elevado): desvio normalizado 0–1 em relação ao limite; em branco quando o alerta está inativo. | `VAR _lim = [Limite CNQ] VAR _v = [CNQ % do Custo] RETURN IF(_v > _lim, MIN(1, DIVIDE(DIVIDE(_v - _lim, _lim), 0.5)))` | 0.00 | — |
| A8 Texto (oculta) | Alerta 8 (Custo da não qualidade elevado): descrição dinâmica da ocorrência. | `ver TMDL (medida com variáveis)` | texto | — |
| A8 Ação (oculta) | Alerta 8 (Custo da não qualidade elevado): ação sugerida. | `"Priorizar o produto de maior CNQ e a causa: " & [Principal Causa Defeito] & "."` | texto | — |

## 12 | Alertas

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Alerta Índice | Índice de prioridade 0–100 do alerta = 100 × Peso de criticidade × (0,4 + 0,6 × Desvio normalizado). Em branco quando o alerta está inativo no contexto filtrado. | `ver TMDL (medida com variáveis)` | 0 | Control Tower |
| Alerta Prioridade | Classe de prioridade do alerta: CRÍTICA (≥ 75), ALTA (≥ 55), MÉDIA (≥ 35) ou BAIXA. | `VAR _s = [Alerta Índice] RETURN SWITCH(TRUE(), ISBLANK(_s), BLANK(), _s >= 75, "CRÍTICA", _s >= 55, "ALTA", _s >= 35, "MÉDIA", "BAIXA")` | texto | Control Tower |
| Alerta Ocorrência | Descrição dinâmica da ocorrência do alerta, sensível aos filtros. | `ver TMDL (medida com variáveis)` | texto | Control Tower |
| Alerta Ação | Ação sugerida para o alerta. | `ver TMDL (medida com variáveis)` | texto | Control Tower |
| Alertas Ativos | Quantidade de alertas ativos no contexto filtrado. | `COUNTROWS(FILTER(Alertas, NOT ISBLANK([Alerta Índice]))) + 0` | #,0 | — |
| Alertas Título | Título dinâmico do painel ATENÇÃO AGORA. | `ver TMDL (medida com variáveis)` | texto | Control Tower |

## 13 | Narrativa e Títulos

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Insight Texto | Narrativa executiva dinâmica em quatro blocos (Observação, Diagnóstico, Impacto, Ação). Descreve associações observadas nos dados, sem afirmar causalidade. | `ver TMDL (medida com variáveis)` | texto | Control Tower |
| Capa Resumo | Resumo da base exibido na capa. | `ver TMDL (medida com variáveis)` | texto | Início |
| Título OEE | Subtítulo dinâmico do bloco de OEE. | `"Maior perda: " & [OEE Maior Perda] & " · Meta " & FORMAT([Meta OEE], "0%", "pt-BR") & " · " & [OEE Classificação]` | texto | Control Tower |
| Título Pareto Paradas | Título narrativo do Pareto de paradas. | `ver TMDL (medida com variáveis)` | texto | Control Tower |
| Título Pareto Defeitos | Título narrativo do Pareto de defeitos. | `ver TMDL (medida com variáveis)` | texto | Qualidade & Custos |
| Título Produção x Plano | Título narrativo do gráfico Produção × Plano. | `"Produção atinge " & FORMAT([Aderência ao Plano %], "0.0%", "pt-BR") & " do plano (gap de " & FORMAT([Gap Produção], "#,0", "pt-BR") & " un.)"` | texto | Control Tower |
| Título Fornecedores | Título narrativo do scorecard de fornecedores. | `ver TMDL (medida com variáveis)` | texto | Supply Chain |
| Título Tooltip Linha | Título do tooltip de linha. | `SELECTEDVALUE(dLinha[Linha], "Seleção atual") & " · principal causa de parada: " & [Principal Causa Parada]` | texto | Tooltip Linha |

## 14 | Cards (contexto)

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| KPI Produção Sub | Linha de contexto do card Produção: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Control Tower, Produção & Eficiência |
| KPI Aderência Sub | Linha de contexto do card Aderência: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Control Tower, Produção & Eficiência |
| KPI OEE Sub | Linha de contexto do card OEE: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Control Tower, Produção & Eficiência, Tooltip Linha |
| KPI Disponibilidade Sub | Linha de contexto do card Disponibilidade: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Produção & Eficiência |
| KPI Performance Sub | Linha de contexto do card Performance: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Produção & Eficiência |
| KPI Qualidade Sub | Linha de contexto do card Qualidade: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Qualidade & Custos |
| KPI Scrap Sub | Linha de contexto do card Scrap: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Control Tower, Qualidade & Custos |
| KPI Retrabalho Sub | Linha de contexto do card Retrabalho: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Qualidade & Custos |
| KPI Downtime Sub | Linha de contexto do card Downtime: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Control Tower |
| KPI Horas Parada Sub | Linha de contexto do card Horas Parada: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Produção & Eficiência |
| KPI OTIF Sub | Linha de contexto do card OTIF: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Control Tower, Supply Chain |
| KPI CNQ Sub | Linha de contexto do card CNQ: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Qualidade & Custos |
| KPI Custo Unidade Boa Sub | Linha de contexto do card Custo Unidade Boa: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Qualidade & Custos |
| KPI Custo Total Sub | Linha de contexto do card Custo Total: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Qualidade & Custos |
| KPI Lead Time Sub | Linha de contexto do card Lead Time: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Supply Chain |
| KPI Fornecedor no Prazo Sub | Linha de contexto do card Fornecedor no Prazo: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Supply Chain |
| KPI Reprovação MP Sub | Linha de contexto do card Reprovação MP: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Supply Chain |
| KPI Rupturas Sub | Linha de contexto do card Rupturas: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Supply Chain |
| KPI Frete Sub | Linha de contexto do card Frete: variação do último mês vs mês anterior e referência. | `ver TMDL (medida com variáveis)` | texto | Supply Chain |

## 15 | Cores (formatação)

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Cor Aderência (oculta) | Cor semântica de Aderência: verde na meta, amarelo em atenção, vermelho crítico. | `ver TMDL (medida com variáveis)` | texto | — |
| Cor OEE (oculta) | Cor semântica de OEE: verde na meta, amarelo em atenção, vermelho crítico. | `VAR _v = [OEE %] VAR _m = [Meta OEE] RETURN SWITCH(TRUE(), ISBLANK(_v), "#A7B1BD", _v >= _m, "#1E9E6A", _v >= _m - 0.1, "#E0A100", "#D64545")` | texto | — |
| Cor OTIF (oculta) | Cor semântica de OTIF: verde na meta, amarelo em atenção, vermelho crítico. | `VAR _v = [OTIF %] VAR _m = [Meta OTIF] RETURN SWITCH(TRUE(), ISBLANK(_v), "#A7B1BD", _v >= _m, "#1E9E6A", _v >= _m - 0.1, "#E0A100", "#D64545")` | texto | — |
| Cor Fornecedor no Prazo (oculta) | Cor semântica de Fornecedor no Prazo: verde na meta, amarelo em atenção, vermelho crítico. | `ver TMDL (medida com variáveis)` | texto | — |
| Cor Supplier Score (oculta) | Cor semântica de Supplier Score: verde na meta, amarelo em atenção, vermelho crítico. | `VAR _v = [Supplier Score] VAR _m = 75 RETURN SWITCH(TRUE(), ISBLANK(_v), "#A7B1BD", _v >= _m, "#1E9E6A", _v >= _m - 15, "#E0A100", "#D64545")` | texto | Supply Chain |
| Cor Scrap (oculta) | Cor semântica de Scrap: verde dentro do limite, amarelo próximo, vermelho acima. | `VAR _v = [Scrap %] VAR _l = [Limite Scrap] RETURN SWITCH(TRUE(), ISBLANK(_v), "#A7B1BD", _v <= _l, "#1E9E6A", _v <= _l * 1.15, "#E0A100", "#D64545")` | texto | — |
| Cor Downtime (oculta) | Cor semântica de Downtime: verde dentro do limite, amarelo próximo, vermelho acima. | `VAR _v = [Downtime %] VAR _l = [Limite Downtime] RETURN SWITCH(TRUE(), ISBLANK(_v), "#A7B1BD", _v <= _l, "#1E9E6A", _v <= _l * 1.15, "#E0A100", "#D64545")` | texto | — |
| Cor CNQ (oculta) | Cor semântica de CNQ: verde dentro do limite, amarelo próximo, vermelho acima. | `VAR _v = [CNQ % do Custo] VAR _l = [Limite CNQ] RETURN SWITCH(TRUE(), ISBLANK(_v), "#A7B1BD", _v <= _l, "#1E9E6A", _v <= _l * 1.15, "#E0A100", "#D64545")` | texto | — |
| Cor Reprovação MP (oculta) | Cor semântica de Reprovação MP: verde dentro do limite, amarelo próximo, vermelho acima. | `ver TMDL (medida com variáveis)` | texto | — |
| Cor OPs Abaixo Segurança (oculta) | Cor semântica de OPs Abaixo Segurança: verde dentro do limite, amarelo próximo, vermelho acima. | `ver TMDL (medida com variáveis)` | texto | — |
| Cor Neutra (oculta) | Cor neutra (azul industrial) para textos de contexto. | `"#1E2D3D"` | texto | — |
| Cor Status Estoque (oculta) | Cor do status de estoque. | `SWITCH([Status Estoque], "CRÍTICO", "#D64545", "ATENÇÃO", "#E0A100", "SAUDÁVEL", "#1E9E6A", "EXCESSO", "#2F6FB5", "#A7B1BD")` | texto | Control Tower |
| Cor Prioridade (oculta) | Cor da classe de prioridade do alerta. | `SWITCH([Alerta Prioridade], "CRÍTICA", "#D64545", "ALTA", "#FF6A00", "MÉDIA", "#E0A100", "BAIXA", "#A7B1BD")` | texto | — |
| Cor Gap (oculta) | Cor do gap de produção: vermelho quando negativo. | `IF([Gap Produção] < 0, "#D64545", "#1E9E6A")` | texto | — |
| Cor Heat OEE (oculta) | Fundo do heatmap Linha × Turno: desvio do OEE da célula em relação ao OEE da seleção (verde ≥ +1 p.p.; neutro ±1; amarelo até −2,5; vermelho abaixo). | `ver TMDL (medida com variáveis)` | texto | — |
| Cor Heat Defeito (oculta) | Fundo do heatmap Linha × Causa de defeito: intensidade do CNQ da célula relativa à maior célula da seleção (5 faixas de laranja). | `ver TMDL (medida com variáveis)` | texto | — |

## 16 | SVG

| Medida | Descrição | Fórmula conceitual (DAX) | Formato | Página |
|---|---|---|---|---|
| Sparkline OEE | Sparkline SVG do OEE mensal no contexto (linha + último ponto destacado). Gerado dinamicamente a partir das medidas. | `ver TMDL (medida com variáveis)` | texto | Control Tower |
| Barra OEE vs Meta | Bullet SVG: barra do OEE contra a meta (traço vertical) em escala de 50% a 100%. | `ver TMDL (medida com variáveis)` | texto | — |
