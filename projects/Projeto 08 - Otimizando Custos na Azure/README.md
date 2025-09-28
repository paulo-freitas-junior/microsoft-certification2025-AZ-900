## **Gerenciamento de Custos no Microsoft Azure**

<p align="center">
  <img src="/images/project08/gerenciamento_custo.jpg" alt="Gerenciamento de custos" style="max-width: 100%;">
  <br>
  <em>Figura 1 - Visão geral de gerenciamento de custos no Azure</em>
</p>

### **Introdução**

O **Gerenciamento de Custos + Cobrança** (Cost Management + Billing) do Azure é a solução integrada da Microsoft para **análise, monitoramento e otimização** dos gastos na nuvem. 

Objetivo é prover visibilidade financeira clara sobre os recursos em uso, permitir previsões de custo, definir orçamentos, emitir alertas e agir proativamente para evitar desperdício.

<p align="center">
  <img src="/images/project08/visao_geral_cobranca.jpg" alt="Gerenciamento de custos" style="max-width: 100%;">
  <br>
  <em>Figura 2 - Visão geral de painel de controle de gerenciamento de custos do Azure</em>
</p>

Alguns fatores que afetam os custos no Azure incluem:

- **Região geográfica** (certos datacenters têm preços diferentes).
- **Modelo de cobrança** (ex: pay-as-you-go, instâncias reservadas, planos de economia).
- **Tipo de recurso** (computação, rede, armazenamento, banco de dados, etc.).
- **Tráfego de dados** (saída de rede geralmente é cobrada).
- **Licenciamento** (uso de softwares, licenças híbridas).
- **Tags e alocação de recursos** (para separar responsabilidades ou projetos).

Há também duas calculadoras que ajudam muito no planejamento:

- A **Calculadora de Preços do Azure** (Pricing Calculator), para estimar custos conforme configurações de serviço.
- O **TCO (Total Cost of Ownership) Calculator**, para comparar cenário on-premises versus Azure. 

<p align="center">
  <img src="/images/project08/calculadora.jpg" alt="Calculadora de custos do Azure" style="max-width: 100%;">
  <br>
  <em>Figura 2 - Calculadora de custos do Azure</em>
</p>

## **1. Planejamento e Estimativa**

### **Calculadora / Previsão**

Antes de criar recursos o ideal é usar a Calculadora de Preços do Azure para gerar cenários com os serviços desejados (VMs, banco de dados, redes, etc), escolhendo região, tamanho, quantidade, tempo etc.. Dessa forme é possível estimar mensalidades e custos/hora para diferentes combinações.

### **Preços e APIs**

Os preços do Azure são públicos e documentados via tabelas, e há APIs REST para buscar os dados de custo e uso (Cost Management REST APIs) para automatizar relatórios ou integração com sistemas externos. 

### **Cenários de comparação**

É possível comparar cenários entre manter infraestrutura local (on-premises) e migrar para Azure, considerando CAPEX, OPEX, custos indiretos, redução de manutenção, energia etc. Esse tipo de análise é fundamental antes de adotar nuvem em larga escala.

## **2. Orçamentos e Alertas**

[imagem: orçamento.jpg]
<p align="center">
  <img src="/images/project08/orcamentos.jpg" alt="Orçamentos" style="max-width: 100%;">
  <br>
  <em>Figura 3 - Visão geral orçamentos</em>
</p>

### **O que são orçamentos**

Um **budget** (orçamento) no Azure é uma meta de gasto definida para um escopo (assinatura, grupo de recursos, ou grupo de gerenciamento). Ele ajuda a impor disciplina orçamentária e accountability.

É possível configurar orçamentos alinhados ao mês, trimestre ou ano, e definir data de expiração.

### **Alertas de custo**

<p align="center">
  <img src="/images/project08/custos04.jpg" alt="Gerenciamento de custos" style="max-width: 100%;">
  <br>
  <em>Figura 4 - Visão geral de alerta de custos</em>
</p>

Os alertas são disparados automaticamente quando o consumo real ou previsto atinge percentuais definidos do orçamento. Os alertas podem ser configurados para:

- **Custo real** (quando os gastos já realizados atingem um limite).
- **Custo previsto (forecast)** — com base em tendências de uso, é possível alertar antes de o orçamento ser ultrapassado.

Tipos de alertas incluídos no Cost Management:

- **Budget alerts** (alertas de orçamento)
- **Credit alerts** (alertas relacionados ao consumo de créditos/prepagamento, em contratos corporativos)
- **Quota / spending threshold alerts** (alertas de limite de gasto por departamento ou quota)


### **Ações automatizadas**

É possível associar um **Action Group** do Azure Monitor a um orçamento. Quando o orçamento atinge um alerta, esse grupo pode disparar notificações por e-mail, SMS, webhook ou até acionar lógicas automatizadas (Logic Apps, Runbooks) para executar ações (por exemplo, desligar VMs).

Um cenário típico:

1. Orçamento mensal de X reais.
1. Alerta em 80%: enviar e-mail e executar script leve.
1. Alerta em 100%: desligar ou suspender workloads não críticos.\
   Esse tipo de orquestração permite “fiscalizar” gastos sem intervenção manual constante.

Importante: alertas não interrompem automaticamente recursos por si só — é necessário criar uma automação configurada para isso.

## **3. Análise de Custos (Cost Analysis)**

### **Visão geral**

<p align="center">
  <img src="/images/project08/analise_custos.jpg" alt="Análise de custos" style="max-width: 100%;">
  <br>
  <em>Figura 5 - Visão geral de análise de custos</em>
</p>

Cost Analysis é a interface dentro do portal Azure que permite explorar, filtrar e visualizar os custos de forma interativa. É o ponto de partida para entender onde seu dinheiro está sendo gasto. 

É possível ver gráficos de tendência, comparações mensais, previsões e decomposições por serviço, recurso e outras dimensões. 

**Visões inteligentes (smart views) e personalizadas**

Cost Analysis oferece *smart views* (visões inteligentes) que já trazem insights (ex: maiores consumidores, tendências inesperadas) por padrão. Também é possível criar visões customizadas usando filtros, agrupamentos e salvar essas visões. 

Alguns usos comuns documentados:

- Ver **custos previstos (forecast)** para o período atual.
- Quebrar os custos por **serviço** (ex: VMs, armazenamento, rede) para identificar quais áreas concentram gastos.
- Quebrar por recurso individual para encontrar “vilões” de custo (ex: uma VM subdimensionada gerando alto tráfego).
- Analisar custos de **instâncias reservadas vs sob demanda** (verificar se você está aproveitando os benefícios das reservas).
- Verização por **tags**: se você tiver aplicado tags nos recursos (centro de custo, projeto, ambiente), pode agrupar os custos por essas tags.
- Ver detalhes da fatura (invoice details) dentro do Cost Analysis para correlação entre valores da fatura e custos internos.

### **Exportação de dados / APIs**

Além da visualização via portal, é possível exportar os detalhes de uso e custo (cost and usage details) para CSV ou configurar exports automáticos para contas de armazenamento ou sistemas analíticos.

As APIs de Cost Management permitem consultas programáticas, integração com Power BI ou outras ferramentas de BI para relatórios mais sofisticados.

Também existe documentação específica sobre os campos de detalhamento de uso (usage details), o que ajuda a decifrar cada linha de custo do CSV exportado. 

### **Previsão (Forecast)**

Cost Analysis incorpora previsões baseadas no histórico de uso para estimar o custo até o fim do período. 

## **4. Gestão de Contas, Faturas e Cobrança**

### **Estrutura de cobrança e escopos**

O Azure organiza cobranças em diferentes **escopos**:

- Conta de cobrança / Billing Account
- Subscrição (Subscription)
- Grupo de recursos (Resource Group)
- Grupos de gerenciamento (Management Groups)\
  O Cost Management suporta esses escopos para aplicar orçamentos, relatórios e políticas de custo.

É possível delegar permissões via **RBAC** para que usuários visualizem ou gerenciem custos sem ter permissão administrativa em todo o ambiente.

### **Faturas e pagamentos**

<p align="center">
  <img src="/images/project08/faturas.jpg" alt="Faturas" style="max-width: 100%;">
  <br>
  <em>Figura 6 - Visão geral das Faturas</em>
</p>

Na seção de **Cobrança (Billing)** do portal, é possível:

- Ver e exportar faturas mensais.
- Configurar métodos de pagamento, perfil de cobrança e dados fiscais.
- Analisar histórico de pagamentos.
- Transferir assinaturas entre contas, se necessário.
- Ver detalhamentos de uso e consumo associados à fatura.

A documentação “Overview of Billing” descreve como gerenciar contas, faturas e cobranças. 

### **Benefícios e acordos**

Existem benefícios e descontos que impactam os custos e como eles aparecem nas faturas, tais como:

- **Azure Reservation (RI)**: compromissos antecipados em troca de desconto.
- **Savings Plans** para recursos de computação.
- **Azure Hybrid Benefit**: permite usar licenças existentes (Windows Server, SQL) para pagar menos na nuvem.\
  Esses benefícios devem ser considerados nas análises financeiras para maximizar economia. 

### **Vantagens Aprofundadas do Gerenciamento de Custos no Azure**

1. **Transparência total**\
   Visionamento de custos por serviço, recurso, ambiente ou centro de custo (via tags).\
   Relatórios detalhados com granularidade diária, mensal ou personalizada.
1. **Controle e governança**\
   Delegação de permissões específicas (somente leitura de custos ou orçamento).\
   Políticas financeiras alinhadas com equipes de TI e finanças, com orçamentos e alertas automáticos.
1. **Eficiência financeira (FinOps)**\
   Aplicação de práticas FinOps para otimização contínua de custos — governança, cultura de responsabilidade e automação de intervenções.\
   Uso de reservas, autoscaling, desligamento de recursos não utilizados.
1. **Previsão e planejamento**\
   Cálculo antecipado de custos por meio de previsões (forecast) e orçamentos.\
   Simulações de custo via calculadora para novos cenários antes da implementação.
1. **Ações automatizadas e remediação**\
   Integração com Action Groups e automações para responder a alertas (ex: desligar VMs, escalar instâncias, pausar serviços).\
   Isso permite que sua infraestrutura reaja automaticamente a desvios financeiros.
1. **Integração com sistemas analíticos**\
   Exportação de dados e APIs permitem integrar com Power BI, dashboards personalizados, alertas corporativos.\
   Relatórios financeiros consolidados entre nuvem e outros sistemas de contabilidade.

### **Boas Práticas Recomendadas (mais aprofundadas)**

- **Planejar e implantar tags padronizadas** (por projeto, cliente, ambiente, centro de custo) desde o início.
- **Atualizar orçamentos periodicamente** conforme evolução dos workloads.
- **Monitorar com frequência** (semanal ou diária) nos primeiros meses após migração.
- **Revisar reservas e savings plans** regularmente para ajustar à demanda real.
- **Aproveitar as recomendações do Azure Advisor**, que sugere otimizações de custo (ex: redimensionar VMs, desligar ociosos).
- **Negociar acordos especiais com a Microsoft**, especialmente em contratos corporativos, para obter níveis de desconto ou compromisso.
- **Criar runbooks ou automações reativas** para desligar recursos não críticos em caso de alertas.
- **Educar times de desenvolvimento**, que devem ser conscientes do impacto financeiro dos recursos que criam e mantêm.
