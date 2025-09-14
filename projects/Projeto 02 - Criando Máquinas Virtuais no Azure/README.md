# Projeto 02 – Criando Máquinas Virtuais no Azure

Uma máquina virtual é um computador emulado que roda dentro de um servidor físico. Dentro do Microsoft Azure é possível criar VMs (Virtual Machines) sob demanda para rodar sistemas operacionais diversos, aplicativos, servidores de banco de dados, servidores web e demais recursos oferecidos pelo Azure no ambiente de Cloud.

<p align="center">
  <img src="/images/project02/Azure01.jpg" alt="Tela principal de criação de Recursos do Azure" style="max-width: 100%;">
  <br>
  <em>Figura 1 - Tela principal de criação de Recursos do Azure</em>
</p>

---

## Sobre SLA (Service Level Agreement) no Azure

SLA (Service Level Agreement) é o compromisso formal da Microsoft sobre a disponibilidade mínima garantida de seus serviços no Azure. Ele define o tempo que um serviço estará disponível e operacional — e, se não cumprir, pode haver créditos financeiros para o cliente.

### 1.1 Modelos de SLA no Azure

| Serviço                   | SLA Típico |
|---------------------------|------------|
| Máquinas Virtuais (com HA)| 99.99%     |
| Azure SQL Database        | 99.99%     |
| Azure Storage             | 99.9%      |
| Azure App Service         | 99.95%     |
| Azure Functions           | 99.95%     |
| Azure DNS                 | 100%       |

### 1.2 Comparativo de SLA e Tempo de Inatividade

| SLA (%)   | Inatividade Semanal | Inatividade Mensal | Inatividade Anual |
|-----------|---------------------|---------------------|-------------------|
| 99.000%   | ~1h 40min           | ~7h 18min           | ~3d 15h 36min     |
| 99.900%   | ~10min              | ~43min              | ~8h 45min         |
| 99.950%   | ~5min               | ~21min              | ~4h 22min         |
| 99.990%   | ~1min               | ~4min 23s           | ~52min 35s        |
| 99.999%   | ~6s                 | ~26s                | ~5min 15s         |

> Base de cálculo: 7 dias/semana, 30 dias/mês, 365 dias/ano – Dados de 09/2025

**Interpretação:**
- 99%: aceitável para testes ou ambientes não críticos
- 99.9%: bom para aplicações internas
- 99.95%: ideal para aplicações comerciais
- 99.99%: recomendado para sistemas críticos
- 99.999%: nível “cinco noves” — altíssima disponibilidade, usado em sistemas bancários, hospitalares, etc.

Os custos relacionados aos SLAs derivam conforme o acordo selecionado. Para SLAs com menor período de inatividade, os custos são maiores pois são necessários mais recursos para manter o compromisso de disponibilidade mínima da Microsoft.

---

### 1.3 Modelos de Disponibilidade no Azure

| Modelo de Disponibilidade | Descrição                                                                 | SLA Típico |
|---------------------------|---------------------------------------------------------------------------|------------|
| Instância única           | VM isolada, sem redundância                                               | Sem SLA    |
| Conjunto de disponibilidade | Agrupamento de VMs em diferentes racks físicos (domínios de falha)      | 99.95%     |
| Zonas de disponibilidade  | VMs distribuídas entre zonas físicas distintas dentro da mesma região     | 99.99%     |
| Regiões emparelhadas      | Replicação entre duas regiões geográficas distintas                       | Alta resiliência, sem SLA direto |

---

### 1.4 Aplicação dos SLAs por Modelo

- **🔸 99% – Instância única (sem redundância)**  
  Uso típico: ambientes de desenvolvimento, testes, ou aplicações não críticas  
  Risco: sem garantia de disponibilidade; qualquer falha pode causar downtime

- **🔸 99.9% – Serviços com replicação interna simples**  
  Exemplo: Azure Storage (LRS), App Services sem redundância  
  Uso típico: aplicações internas, sistemas tolerantes a pequenas interrupções

- **🔸 99.95% – Conjunto de disponibilidade**  
  Como funciona: VMs distribuídas entre múltiplos domínios de falha e atualização  
  Uso típico: aplicações empresariais que exigem alta disponibilidade, mas dentro de uma única zona  
  Limitação: não protege contra falhas de zona inteira

- **🔸 99.99% – Zonas de Disponibilidade**  
  Como funciona: VMs distribuídas entre zonas físicas independentes (energia, rede, refrigeração)  
  Uso típico: sistemas críticos como bancos, e-commerces, ERPs  
  Recomendado: para alta disponibilidade com tolerância a falhas de zona

- **🔸 99.999% – Arquiteturas distribuídas multi-região**  
  Como funciona: replicação ativa entre regiões (ex: Azure Front Door + Azure SQL Geo-Replication)  
  Uso típico: aplicações globais, missão crítica, com tolerância a desastres regionais  
  Importante: exige arquitetura personalizada; o SLA é composto por múltiplos serviços

---

### 1.5 Tabela Comparativa: SLA vs Modelo de Disponibilidade

| SLA (%)   | Modelo de Disponibilidade         | Proteção contra falha de zona | Recomendado para...                  |
|-----------|-----------------------------------|-------------------------------|--------------------------------------|
| 99.0%     | Instância única                   | ❌ Não                        | Testes, desenvolvimento              |
| 99.9%     | Serviços com replicação simples   | ❌ Não                        | Aplicações internas                  |
| 99.95%    | Conjunto de disponibilidade       | ⚠️ Parcial                   | Aplicações empresariais              |
| 99.99%    | Zonas de disponibilidade          | ✅ Sim                        | Sistemas críticos                    |
| 99.999%   | Arquitetura multi-região          | ✅ Sim                        | Missão crítica, continuidade global  |

---

### 1.6 Dica de Arquitetura

Para alcançar 99.99% ou mais, é necessário:
- Usar Zonas de Disponibilidade para VMs, bancos de dados e balanceadores
- Configurar replicação geográfica para dados
- Utilizar serviços gerenciados com SLA elevado (ex: Azure SQL, Cosmos DB)
- Implementar monitoramento e failover automático

---

## Criando a Máquina Virtual no Azure

A proposta desse Projeto 02 será apenas de esboçar a criação de uma máquina virtual dentro do Azure. A princípio dentro do Projeto entender os diferentes acordos de SLAs oferecidos pela Microsoft e em seguida iniciar a criação de uma VM com configurações simplificadas dentro do portal do Azure, afim de se ter um primeiro contato com a criação do Recurso e familiarização com as configurações básicas.

<p align="center">
  <img src="/images/project02/Azure02.jpg" alt="Criando uma máquina virtual no Azure" style="max-width: 100%;">
  <br>
  <em>Figura 2 - Criando uma máquina Virtual no Azure</em>
</p>

Dentro das definições básicas para a criação de uma máquina virtual, dentro dos detalhes do projeto, são necessárias informações de assinatura e grupo de recursos.

Em detalhes de instância são definidos o nome da máquina virtual, a região que será criado o recurso, opções de disponibilidade de replicação da VM em Zonas de Disponibilidades diferentes ou Conjuntos de Disponibilidades.

<p align="center">
  <img src="/images/project02/Azure03.jpg" alt="Criando uma máquina virtual no Azure" style="max-width: 100%;">
  <br>
  <em>Figura 3 - Criando uma máquina Virtual no Azure</em>
</p>

Informações como tipo de segurança, imagem da máquina virtual, arquitetura da VM, tamanho da máquina virtual, dados de acesso de uma conta administradora e qual regra de acesso à VM são pré-estabelecidas nesse momento.

O importante desse projeto é entender que dependendo da máquina virtual que será criada, da Região e da Opção de disponibilidade, os modelos de SLA de disponibilidade mínima garantida são diferentes e dependendo do escopo do Projeto precisam ser avaliados.

As abas: Discos, Rede, Gerenciamento, Monitoramento, Avançado, Marcas e Revisar+Criar serão abordadas em projetos adicionais desse curso.

<p align="center">
  <img src="/images/project02/Azure04.jpg" alt="Criando uma máquina virtual no Azure" style="max-width: 100%;">
  <br>
  <em>Figura 4 - Criando uma máquina Virtual no Azure</em>
</p>

Ao finalizar as configurações solicitadas, ao clicar em Revisar+Criar o Azure irá realizar uma validação das solicitações e uma vez aprovado, irá permitir a criação da VM, listando todas a etapas de configurações existentes em todas as abas.

<p align="center">
  <img src="/images/project02/Azure05.jpg" alt="Finalizando a criação de uma máquina virtual no Azure" style="max-width: 100%;">
  <br>
  <em>Figura 5 - Finalizando a criação de uma máquina virtual no Azure</em>
</p>

---
