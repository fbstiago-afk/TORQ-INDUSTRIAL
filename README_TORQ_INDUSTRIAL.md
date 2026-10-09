# TORQ INDUSTRIAL — Industrial Performance & Supply Chain Control Tower

*From Factory Data to Operational Decisions.* Case de portfólio de Business Intelligence com empresa e dados fictícios.

## 1. Objetivo

Dar a gestores industriais uma leitura integrada de Produção → Eficiência → Qualidade → Custos → Estoques → Fornecedores → Atendimento ao cliente, respondendo em segundos: a fábrica está bem, onde está o maior problema, quanto custa e o que atacar primeiro.

## 2. Como abrir

1. Abra `TORQ_Industrial.pbip` no Power BI Desktop (formato PBIP/PBIR/TMDL).
2. Clique em **Atualizar**. O modelo lê `base_industria_manufatura_2_anos.csv` da pasta indicada no parâmetro `PastaProjeto` (Transformar dados → Gerenciar parâmetros). Valor atual: `E:\Projetos BI\08 - Industria TORQ INDUSTRIAL\`.
3. A capa abre primeiro; o botão **INICIAR** leva à Control Tower (no Desktop, Ctrl + clique).

## 3. Auditoria da base

| Item | Resultado |
|---|---|
| Registros | 10.318 ordens de produção, uma linha por OP (`ordem_producao` é única) |
| Período | 01/10/2024 a 30/09/2026, 24 meses completos |
| Colunas | 64, sem valores nulos e sem duplicidades |
| Dimensões | 3 plantas, 9 linhas (planta + linha), 3 turnos, 10 produtos em 5 categorias, 8 clientes/segmentos, 8 fornecedores, 8 matérias-primas, 6 causas de parada, 8 causas de defeito |
| Consistência | `qtd_produzida = qtd_boa + qtd_refugo`; `horas_planejadas = horas_operando + horas_parada`; `horas_parada = setup + manutenção + falta de material`; `lead_time_real = previsto + atraso`; `estoque_final = inicial + recebimento − consumo`; `custo_total = produção × custos unitários + refugo + retrabalho` |

Pontos de atenção encontrados:

- O código da linha (`L01-Montagem` etc.) se repete entre plantas. A chave da linha passou a ser planta + linha (9 linhas reais, não 7).
- `performance_pct` não pode ser reconstruída a partir de quantidade × ciclo padrão / horas. Ela foi tratada como dado da OP e agregada por média ponderada pelas horas operando.
- O estoque é uma posição por OP, não uma razão contínua (o estoque inicial de uma OP não é o final da anterior). Por isso o modelo usa médias e frequências (% de OPs abaixo da segurança, rupturas) e não "estoque atual".
- `fornecedor_no_prazo = Sim` tolera até 1 dia de atraso.
- `otif = Não` ocorre com atraso de entrega (38,7% das OPs) e também sem atraso (22,2%), sempre em OPs com produção boa abaixo da demanda. Essa segunda parcela é mostrada como "falha de quantidade (inferida)".
- `taxa_defeito_pct` da base não bate com (refugo + retrabalho) / produção; a medida do modelo usa a fórmula explícita.
- Matéria-prima e fornecedor são atributos da OP (lote consumido), não do produto: um SKU usa várias MPs e uma MP vem de vários fornecedores.

### O que a base não permite

Máquinas, timestamps de eventos, timeline de paradas, MTBF/MTTR, pedidos de compra, Gantt de suprimentos, preço de venda/receita, custo planejado, PPM por lote, estoque diário e dias de cobertura reais. Nada disso foi inventado. Forecast não foi implementado: a série é mensal, estável e termina em mês fechado, sem plano futuro; o modelo traz apenas uma média móvel de 3 meses como medida de tendência, e as páginas trazem simuladores what-if identificados como SIMULAÇÃO.

## 4. Modelo (estrela)

Fato `fProducao` (grão: ordem de produção) e 9 dimensões, todas 1:* com filtro unidirecional:

| Tabela | Chave | Conteúdo |
|---|---|---|
| dCalendario | Data | Ano, trimestre, mês, mês/ano, início do mês, semana, dia da semana (tabela de datas) |
| dProduto | SKU | Produto, categoria, família, ciclo padrão |
| dLinha | Linha Key (planta\|linha) | Linha, planta, código, processo |
| dTurno | Turno | Turno e ordem |
| dFornecedor | ID Fornecedor | Fornecedor, UF |
| dMateriaPrima | ID Matéria-Prima | Matéria-prima, grupo |
| dCliente | ID Cliente | Cliente, segmento |
| dCausaParada | Causa Parada | Causa principal de parada e flag de relevância |
| dCausaDefeito | Causa Defeito | Causa principal de defeito e flag de relevância |

Planta fica dentro de `dLinha` (evita floco de neve). Tabelas de apoio desconectadas: `Alertas`, `Insight`, `Ponte`, `Simulação Downtime`, `Simulação Scrap` e os field parameters `Indicador` e `Analisar por`. A consulta `Base` (staging) lê e tipa o CSV uma única vez; fato e dimensões derivam dela. Colunas numéricas da fato ficam ocultas: todo número exibido é medida explícita. Colunas e medidas têm descrição (preparo para Copilot/Q&A).

Colunas criadas no Power Query (e não em DAX): chave da linha, horas em ritmo padrão (horas operando × performance), custos por componente, flags 0/1 e a classificação de estoque por OP.

## 5. Principais medidas e regras

| Tema | Regra |
|---|---|
| OEE | Disponibilidade (horas operando / planejadas) × Performance (média ponderada por horas operando) × Qualidade (boa / produzida), recalculado em cada nível |
| Ponte de perdas | Capacidade teórica = horas planejadas × taxa padrão (un/h); perdas = horas de setup, manutenção, falta de material e ritmo × taxa padrão; refugo em unidades. A soma fecha exatamente na produção boa. Tudo rotulado como ESTIMATIVA |
| Pareto | Horas de parada (ou CNQ) por causa principal da OP, com % acumulado e referência de 80%. "Sem parada/defeito relevante" fica fora |
| Custo por unidade boa | Custo total / produção boa |
| CNQ | Custo de refugo + custo de retrabalho |
| Status de estoque (contexto) | CRÍTICO: ≥ 12% das OPs abaixo da segurança ou ruptura ≥ 3%; ATENÇÃO: ≥ 10%; EXCESSO: índice estoque/segurança > 4; senão SAUDÁVEL |
| Status de estoque (OP) | CRÍTICO < segurança; ATENÇÃO até 1,5×; SAUDÁVEL 1,5× a 5×; EXCESSO > 5× |
| Supplier Score (0–100) | 40% pontualidade + 30% qualidade do lote (1 − reprovação/3%) + 30% aderência de lead time (1 − desvio médio/5 dias) |
| Índice de prioridade (0–100) | 100 × peso de criticidade do tema × (0,4 + 0,6 × desvio normalizado em relação ao limite). CRÍTICA ≥ 75, ALTA ≥ 55, MÉDIA ≥ 35, BAIXA abaixo |
| Comparativos | Δ Mês = último mês do contexto vs mês anterior; PA = período anterior de mesma duração (em branco se não houver histórico completo) |

Metas e limites são parâmetros gerenciais, não dados observados (pasta `00 | Metas e Limites`): OEE 85%, aderência 98%, OTIF 90%, fornecedor no prazo 80%, scrap 0,8%, downtime 15%, CNQ 0,70% do custo, reprovação de MP 2%, OPs abaixo da segurança 10%. Pesos de criticidade dos alertas: ruptura 1,0; OEE e OTIF 0,9; downtime, scrap e estoque 0,8; fornecedor e não qualidade 0,7.

O dicionário completo está em `DICIONARIO_MEDIDAS.md`.

## 6. Páginas

| Página | Pergunta | Conteúdo |
|---|---|---|
| Início | — | Capa com identidade TORQ e botão INICIAR |
| Control Tower | A fábrica está bem? Onde está o maior problema? | 6 KPIs com contexto, Produção × Plano, OEE decomposto, ATENÇÃO AGORA, gargalos por linha com sparkline SVG, Pareto de paradas, supply risk, insight executivo |
| Produção & Eficiência | Onde perdemos capacidade? | Tendência de OEE, ponte de perdas (waterfall), heatmap Linha × Turno, explorador com field parameters, scatter eficiência × qualidade, simulador de downtime |
| Qualidade & Custos | Onde perdemos dinheiro? | Evolução de scrap/retrabalho, Pareto de defeitos por custo, heatmap Linha × Defeito, produtos críticos, scrap por fornecedor, simulador de scrap |
| Supply Chain | O que pode parar a fábrica ou o cliente? | Matriz de risco de fornecedores, Supplier Scorecard, OTIF por segmento, materiais críticos, lead time real × previsto, decomposição do OTIF |
| Tooltip Linha (oculta) | — | Mini-painel da linha: OEE e componentes, paradas, scrap, gap, CNQ e causa principal |

Navegação por botões no cabeçalho (página atual em azul). Filtros sincronizados entre páginas: Ano, Mês, Planta, Linha, Turno, Categoria. Tabelas e matrizes com valores centralizados.

## 7. Decisões de design

- Identidade do guia de marca: azul industrial `#1E2D3D`, laranja TORQ `#FF6A00`, cinza técnico `#A7B1BD`, fundo `#F3F6F9`, Segoe UI. Verde, amarelo e vermelho só com significado (meta, atenção, crítico), sempre acompanhados de texto ou seta.
- Estilo centralizado no tema `TORQ_Industrial.json` (fundo, borda, raio, títulos, padding); os visuais só sobrescrevem o que é específico.
- Títulos narrativos e dinâmicos; cards com variação do último mês, meta e status.
- Sem gauges, pizzas, 3D ou mapas. Somente visuais nativos: o HTML Content (visual do AppSource) não foi incluído para o projeto abrir em qualquer máquina sem dependência; o papel dele é cumprido por SVG gerado em medida DAX (sparkline) e por tabelas com texto dinâmico.
- Drillthrough não foi criado: a página de tooltip e o cross-filter cobrem a investigação por linha sem navegação extra.

## 8. Validação

- 140 arquivos JSON do relatório validados contra os schemas oficiais do PBIR (Microsoft).
- Totais conferidos contra o CSV em Python: produção 1.535.826 un.; plano 1.558.961; OEE 73,9% (D 81,8% × P 91,0% × Q 99,3%); 13.584 h de parada; CNQ R$ 4,67 Mi; OTIF 39,1%; fornecedor no prazo 51,6%; 228 rupturas.
- Consulte a resposta de entrega para o status da validação no Power BI Desktop (abertura, atualização e Performance Analyzer devem ser conferidos na máquina).

## 9. Principais achados (período completo)

- OEE de 73,9%, 11,1 p.p. abaixo da meta; a perda está na disponibilidade (81,8%), não na qualidade (99,3%).
- Quebra de equipamento é a causa principal em 10,7% das OPs e concentra 28,9% das horas de parada; nessas OPs o OEE cai para 45,8%.
- O 3º turno opera com OEE de 72,3%, cerca de 2 p.p. abaixo dos demais.
- OTIF de 39,1%: 38,7% das OPs atrasam a entrega e 22,2% falham em quantidade.
- Fundição Horizonte e Polímeros Max: 36% e 42% de entregas no prazo, lead time de 31 e 27 dias, e scrap de 1,08% e 1,02% nas OPs que abastecem (0,6% nos demais). Juntas respondem por 35% do CNQ com 25% do volume.
- Painel Controle Pro concentra 25% do custo da não qualidade (R$ 1,19 Mi).
- 11,2% das OPs terminam com estoque de MP abaixo da segurança; 228 rupturas.
