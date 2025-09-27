# CONFIGURANDO RECURSOS E DIMENSIONAMENTOS EM MÁQUINAS VIRTUAIS NO AZURE

## **I. Introdução Contextualizada do Cenário de Implantação**

Este relatório técnico oferece uma análise detalhada do processo de provisionamento de uma Máquina Virtual (VM) Linux no portal Azure. A implantação é marcada pela estratégia dupla de otimizar custos operacionais por meio de Instâncias Spot, enquanto se mantém a alta resiliência regional pela seleção de Zonas de Disponibilidade.

A análise se concentra em examinar as implicações arquitetônicas, de custo e, crucialmente, de segurança de cada seleção. O objetivo principal combinar esses elementos, parece ser hospedar uma carga de trabalho que seja inerentemente *tolerante à interrupção* (adequada para Spot) — como processamento em lote ou desenvolvimento/teste— mas que exija proteção contra a falha total de um datacenter, garantindo a continuidade do serviço em nível regional.

**II. Passo 1: Configuração Fundamental do Projeto e Resiliência Regional**

<p align="center">
  <img src="/images/project05/computacao01.jpg" alt="Serviços de Computação" style="max-width: 100%;">
  <br>
  <em>Figura 1 - Configuração dos serviços de computação</em>
</p>

A primeira fase da criação da VM estabelece o escopo administrativo e a fundação geográfica e resiliente da instância.

**2.1. Detalhes do Projeto e Gerenciamento de Recursos**

A máquina virtual está sendo provisionada sob a Azure subscription 1, que atua como o principal limite de faturamento e governança para a alocação de recursos.

Para o gerenciamento, foi criado um novo **Grupo de Recursos** (RG) denominado rg-DIO\_AZ900. Um Grupo de Recursos funciona como um **contêiner lógico crucial para gerenciar recursos** que compartilham o mesmo ciclo de vida. A adoção da nomenclatura padronizada, como sugerido pela estrutura rg-DIO\_AZ900, demonstra uma aderência a boas práticas de governança. O valor dessa prática reside na coordenação do gerenciamento: quando a solução associada ao projeto (AZ-900) for concluída, a exclusão do Resource Group garantirá que todos os componentes relacionados — incluindo a VM, discos, interfaces de rede e IPs— sejam removidos simultaneamente, prevenindo a persistência de recursos órfãos e os consequentes custos indesejados.

**2.2. Detalhes da Instância e Configuração de Resiliência**

A VM foi nomeada vm-ubuntu-az900 e implantada na região **(US) West US 2**. A escolha crítica de infraestrutura é a configuração das **Opções de Disponibilidade**, onde foi selecionada a "Zona selecionada pelo Azure (versão prévia)".

**As Zonas de Disponibilidade (AZs)** representam datacenters física e logicamente separados dentro de uma região, cada um com energia, resfriamento e rede independentes, projetados para proteger aplicações e dados contra falhas de datacenter. Ao escolher uma Zona (ou permitir que o Azure selecione uma), a VM é implantada como um ***serviço zonal***, significando que o recurso está "fixado" a uma zona específica. A resiliência total da aplicação, neste modelo, requer que o **cliente** replique ativamente o aplicativo e os dados entre uma ou mais zonas.

A combinação de uma **VM Spot** (baixo custo, alta volatilidade) e uma **Zona de Disponibilidade** (alta resiliência regional) levanta uma consideração arquitetônica importante. O uso de Spot indica uma tolerância à falha em nível de instância, mas a seleção de uma Zona de Disponibilidade (AZ) demonstra uma preocupação com a resiliência contra falhas regionais de grande escala. Essa arquitetura sugere uma estratégia de risco mitigado: buscar máxima economia de custo por meio de VMs descartáveis, mas garantir que a infraestrutura fundacional seja robusta o suficiente para resistir a desastres geográficos maiores.

## **III. Passo 2: Imagem, Segurança e Otimização de Custos Spot**

<p align="center">
  <img src="/images/project05/computacao02.jpg" alt="Serviços de Computação" style="max-width: 100%;">
  <br>
  <em>Figura 2 - Configuração dos serviços de computação</em>
</p>

Objetivo é detalhar as escolhas do sistema operacional e a ativação da estratégia de otimização de custos através da Instância Spot.

**3.1. Configurações de Sistema Operacional e Geração**

A seleção do **Tipo de Segurança** Padrão e a **Imagem** Ubuntu Server 24.04 LTS - x64 Gen2, que é uma imagem Linux robusta, utilizando a arquitetura de 64 bits (x64) com Geração 2 (baseada em UEFI). A escolha da imagem é compatível com recursos de segurança adicionais, embora o tipo de segurança inicial tenha sido definido como Padrão.

**3.2. Implementação e Análise Estratégica da Instância Spot**

Foi selecionada a execução com **Desconto de Spot do Azure**, confirmando a intenção de utilizar a capacidade de computação não utilizada do Azure a um custo significativamente reduzido.

A configuração da política de remoção é crucial para o gerenciamento de risco e custo:

- **Tipo de Remoção (Eviction Type):** Selecionado Preço ou capacidade. Esta opção é mais rigorosa no controle de custos do que a opção "Somente capacidade". Ela permite que a VM seja removida se o preço Spot exceder o limite máximo definido pelo usuário *ou* se o Azure precisar da capacidade para instâncias Pay-as-you-go.
- **Política de Remoção (Eviction Policy):** Selecionado Excluir. Esta política determina que, no caso de remoção (expulsão), a instância da VM e seus discos efêmeros (se existissem) serão **excluídos permanentemente**. Embora os Discos Gerenciados persistentes sejam retidos, a instância VM deve ser recriada a partir do zero quando a capacidade estiver disponível novamente.

A seleção da política Excluir implica diretamente que a carga de trabalho sendo executada é considerada ***stateless*** ou depende inteiramente de mecanismos de persistência de dados externos. O modelo de aplicação deve ser capaz de lidar com a interrupção abrupta e com a necessidade de re-provisionamento completo da instância, o que é um fator de design essencial para arquiteturas baseadas em VMs Spot.

## **IV. Passo 3: Dimensionamento, Preços e Credenciais de Administrador**

<p align="center">
  <img src="/images/project05/computacao03.jpg" alt="Serviços de Computação" style="max-width: 100%;">
  <br>
  <em>Figura 3 - Configuração dos serviços de computação</em>
</p>

Objetivo é fornecer detalhes essenciais sobre o perfil de desempenho da VM, a estratégia de controle de custos e o acesso inicial.

**4.1. Dimensionamento da Máquina Virtual (Standard\_D2ls\_v3)**

O tamanho da VM selecionado é Standard\_D2ls\_v3, com 2 vCPUs e 8 GiB de memória, ao preço Spot inicial de US$ 0,01774 por hora.

Essa VM pertence à família D de Uso Geral, que oferece um equilíbrio entre CPU e memória. O sufixo 'ls' no nome (Standard\_D2ls\_v3) é indicativo da série Low Cost Storage. Uma característica chave dessa série é a **ausência de armazenamento temporário local** (disco efêmero), o que contribui para um preço de entrada mais baixo da instância. Consequentemente, o sistema operacional e quaisquer operações de cache ou swap dependerão inteiramente do desempenho e da latência dos Discos Gerenciados persistentes anexados, que são cobrados separadamente.

**4.2. Detalhes de Custo Spot e Estratégia de Preço**

O preço máximo que o usuário está disposto a pagar por hora (Preço máximo que você deseja pagar por hora) foi definido em 0,096 USD. 

Esta definição do preço máximo é uma mitigação de risco deliberada. O preço Spot atual (US$ 0,01774) é significativamente mais baixo do que o teto de gastos definido (US$ 0,096). Ao estabelecer um teto elevado, foi sinalizado que ***a prioridade é a estabilidade do tempo de atividade***, minimizando a probabilidade de expulsão da VM baseada puramente na flutuação do preço Spot. A instância só será expulsa por preço se o custo subir dramaticamente (mais de 5 vezes o preço atual) ou se a capacidade for requerida pelo Azure. (*Obs: Valores relacionados ao período de Setembro/2025)*

A hibernação da VM está desativada.

**4.3. Configuração da Conta de Administrador**

Para a configuração da conta de administrador Linux, foi definido o **Tipo de Autenticação** como Senha e o **Nome de usuário** como ***dioaz900***.

Embora a autenticação por senha seja funcional, a recomendação de segurança padrão da Microsoft para VMs Linux é sempre priorizar o uso de **Chaves Públicas SSH**. Chaves SSH oferecem um mecanismo de autenticação criptográfica superior, essencial para mitigar o risco de ataques de força bruta, especialmente quando as portas administrativas são expostas publicamente.

<p align="center">
  <img src="/images/project05/computacao04.jpg" alt="Serviços de Computação" style="max-width: 100%;">
  <br>
  <em>Figura 4 - Configuração dos serviços de computação</em>
</p>

**Tabela 1: Detalhamento do Tamanho e Perfil de Desempenho da VM Standard\_D2ls\_v3**

|**Métrica**|**Valor**|**Série VM**|**Análise de Custo/Desempenho**|
| :- | :- | :- | :- |
|Tamanho|Standard\_D2ls\_v3|Uso Geral (D-family)|Otimizada para custo (low cost storage).|
|vCPUs|2|Balanceado.|Performance base suficiente para o SO e aplicações leves.|
|RAM (GiB)|8|Proporção 1:4 (CPU:Memória).|Adequado para aplicações que demandam mais memória por núcleo.|
|Armazenamento Temporário|Ausente (Implícito pela série 'ls')|Reduz o custo de entrada, mas aumenta a dependência do IOPS dos discos persistentes.||
|Preço Spot (Atual 09/2025)|US$ 0,01774/hora|Valor de desconto significativo.||
|Preço Máximo (Cap) (09/2025)|US$ 0,096/hora|Estratégia de estabilidade: alto teto para evitar expulsão por preço.||

## **V. Passo 4: Definições de Ingressos de Rede e Implicações de Segurança**

<p align="center">
  <img src="/images/project05/computacao05.jpg" alt="Serviços de Computação" style="max-width: 100%;">
  <br>
  <em>Figura 5 - Configuração dos serviços de computação</em>
</p>

Objetivo é abordar as regras de portas de entrada, o ponto mais crítico da implantação em termos de segurança de rede.

**5.1. Configuração de Regras de Portas de Entrada**

Na seção Regras de portas de entrada, foi selecionada opção: ***Permitir portas selecionadas*** e especificou duas portas públicas: HTTP (80) e SSH (22).

**5.2. Análise Crítica do Aviso de Segurança do Azure**

A interface do portal exibe um aviso claro: "Isso permitirá que todos os endereços IP acessem sua máquina virtual. É recomendado somente para testes."

Esta configuração implementa imediatamente uma vulnerabilidade de segurança significativa. Ao abrir a **Porta 22 (SSH)** para o mundo (implícita pela configuração de portas selecionadas sem restrições avançadas), a VM fica exposta a varreduras contínuas e ataques automatizados de força bruta, que são comuns em ambientes de nuvem.<sup>6</sup> Dado que o usuário selecionou a autenticação por senha (em vez de chave SSH), o risco é maximizado. A filosofia de segurança de rede do Azure (Network Access Control) exige a limitação da conectividade a dispositivos ou sub-redes específicas, um princípio que foi violado pela exposição da porta 22 ao tráfego de entrada da internet pública.<sup>7</sup>

A abertura da **Porta 80 (HTTP)**, embora necessária para servir tráfego web, não garante a comunicação criptografada, o que é uma deficiência de segurança moderna. A melhor prática é utilizar sempre HTTPS (Porta 443).

**5.3. Recomendações de Mitigação de Risco e Melhores Práticas de Segurança**

A configuração atual da rede deve ser corrigida imediatamente após o provisionamento, pois a exposição administrativa compromete o ambiente. A Microsoft oferece soluções robustas para evitar a abertura de portas administrativas:

1. **Azure Bastion:** Esta é a solução PaaS recomendada. Ela permite que os usuários se conectem à VM via SSH/RDP através do portal Azure usando SSL/TLS na porta 443, eliminando a necessidade de expor as portas 22 ou 3389 ao endereço IP público da VM.
1. **Acesso Just-in-Time (JIT):** Integrado ao Microsoft Defender for Cloud, o JIT é uma ferramenta de segurança que mantém as portas administrativas fechadas por padrão. Ele abre as portas ***somente mediante solicitação aprovada***, por um período limitado (tipicamente até três horas) e apenas para endereços IP de origem especificados. O JIT reduz drasticamente a superfície de ataque ao garantir que o acesso administrativo só seja permitido quando estritamente necessário.

**Tabela 2: Análise de Risco das Portas de Entrada Públicas Selecionadas**

|**Porta (Protocolo)**|**Serviço**|**Risco Associado à Abertura Pública (0.0.0.0/0)**|**Melhor Prática Recomendada**|
| :- | :- | :- | :- |
|22 (TCP)|SSH|Acesso administrativo direto. Exposição a ataques de força bruta, conforme alertado.|Restringir o IP de origem no NSG ou utilizar **Azure Bastion/Acesso JIT**.|
|80 (TCP)|HTTP|Servidor web não criptografado. Transmissão de dados sensíveis em texto simples.|Mover para HTTPS (443). Usar Application Gateway ou Front Door para WAF e segurança perimetral.|
|Fonte de Entrada|Qualquer IP (0.0.0.0/0)|Superfície de ataque máxima. Viola o princípio de segurança de menor privilégio.|Definir sub-redes de gerenciamento ou Application Security Groups (ASGs) para limitar o acesso.|

## **VI. Conclusões e Checklist de Implementação Otimizada**

**6.1. Síntese Arquitetônica**

O provisionamento da Máquina Virtual reflete uma implementação estratégica de Infraestrutura como Serviço (IaaS) projetada para alcançar eficiência de custos sem sacrificar a resiliência regional contra desastres de larga escala.

1. **Otimização de Custo:** O uso de uma Instância Spot (Standard\_D2ls\_v3) e o tamanho de custo otimizado (sem armazenamento temporário) demonstram um foco em minimizar despesas operacionais.
1. **Gerenciamento de Risco de Custo:** A definição de um preço máximo alto (US$ 0,096)  (dados de Setembro/2025) para a expulsão Spot é uma tática de gerenciamento de risco que prioriza a disponibilidade da VM em relação à obtenção do custo mais baixo absoluto, protegendo o ambiente contra interrupções frequentes devido a picos de preço de capacidade.
1. **Resiliência Regional:** A implantação em uma Zona de Disponibilidade garante que a instância esteja protegida contra a falha de um datacenter inteiro na região, um diferencial crítico para cargas de trabalho que, embora possam ser interrompidas momentaneamente, não podem tolerar desastres geográficos.<sup>.</sup>

**6.2. Checklist Crítico de Segurança Pós-Provisionamento**

A configuração atual, embora arquitetonicamente sólida em termos de custo e resiliência, apresenta uma falha de segurança de rede de alto risco. As seguintes ações corretivas são obrigatórias imediatamente após o provisionamento da VM:

- **Bloqueio da Porta 22 (SSH):** O Network Security Group (NSG) associado à VM deve ser modificado para restringir o acesso à Porta 22. A origem (Source) da regra de entrada (Ingress) deve ser alterada de Any para um endereço IP de gerenciamento conhecido ou para o Service Tag AzureBastion.
- **Implementação de Acesso Remoto Seguro:** A melhor prática é provisionar o **Azure Bastion** na VNet associada para permitir conexões SSH seguras e privadas através do portal, eliminando a exposição pública da porta 22.
- **Adoção de JIT:** Se o Azure Bastion não for utilizado, a implementação do **Acesso Just-in-Time (JIT)** via Microsoft Defender for Cloud deve ser configurada para garantir que as portas administrativas permaneçam fechadas por padrão.
- **Segurança de Serviço Web:** Se a VM se destina a hospedar um serviço web, a Porta 80 deve ser desativada, e o tráfego deve ser migrado para o protocolo criptografado HTTPS (Porta 443).

---
# **ANÁLISE DE CONFIGURAÇÃO DE DISCOS E CRIPTOGRAFIA**

Análise de provisionamento da Máquina Virtual Spot, focando-se exclusivamente nas escolhas de **Armazenamento (Discos)** e nas implicações de segurança e desempenho resultantes das configurações apresentadas.

## **I. Configuração de Armazenamento (Discos)**

<p align="center">
  <img src="/images/project05/discos01.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 6 - Configuração dos serviços de Armazenamento em Discos</em>
</p>

A Imagem 1 detalha as escolhas de disco gerenciado (Managed Disk) para o Sistema Operacional, a redundância e a abordagem de criptografia de dados em repouso.

**1.1. Configuração e Desempenho do Disco de SO**

O disco do Sistema Operacional (SO) foi configurado com os seguintes atributos:

- **Tamanho do Disco de SO:** Padrão de imagem (30 GiB).
- **Tipo de Disco de SO:** SSD Premium (armazenamento com redundância local).

**Análise:**

A seleção de **Premium SSD** é uma escolha de alto desempenho, ideal para cargas de trabalho que exigem alta taxa de transferência (throughput) e baixa latência. Embora o uso de VMs Spot sugira uma intenção de baixo custo, o Premium SSD garante o melhor desempenho de I/O para as operações do sistema operacional e do sistema de arquivos, o que é crucial para o tempo de inicialização da VM e a responsividade geral do SO.

O disco utiliza **Armazenamento com Redundância Local (LRS)**. O LRS armazena três cópias dos dados dentro de um único datacenter, protegendo contra falhas de hardware local, mas não contra um desastre em nível de datacenter (que é mitigado pela Zona de Disponibilidade, configurada no passo inicial).

**1.2. Gerenciamento de Criptografia de Dados em Repouso**

A configuração define o nível de proteção para os dados em repouso (dados armazenados no disco).

- **Gerenciamento de Chaves:** Chave de criptografia gerenciada pela plataforma.
- **Criptografia no Host (Encryption at Host):** **Desativada (caixa desmarcada)**.

**Análise de Segurança (Server-Side Encryption vs. Encryption at Host):**

1. **Chaves Gerenciadas pela Plataforma (PMKs):** Esta é a configuração padrão e mais básica de segurança do Azure. Todos os Managed Disks são **automaticamente criptografados em repouso** (Server-Side Encryption – SSE) usando chaves gerenciadas pela Microsoft (PMKs). A SSE protege os dados quando persistem nos clusters de armazenamento do Azure.
1. **Criptografia no Host (Encryption at Host):** Ao deixar esta opção desmarcada, o usuário opta por não habilitar uma camada de segurança aprimorada. A Criptografia no Host garante que os dados sejam criptografados no nível do host da Máquina Virtual *antes* de serem armazenados no Storage Cluster. Embora o disco principal já use SSE, a Criptografia no Host é crucial para:
   1. Criptografar o **cache do disco de SO e de dados**.
   1. Assegurar o fluxo de dados criptografado de ponta a ponta entre a VM e o armazenamento.
   1. Criptografar discos temporários (embora a VM Standard\_D2ls\_v3 seja de "Low Cost Storage" e não tenha disco temporário, a proteção do cache permanece relevante).

A recomendação de segurança para ambientes que manipulam dados sensíveis é habilitar a Criptografia no Host para fornecer o máximo de proteção de ponta a ponta.

**1.3. Política de Ciclo de Vida do Disco**

A opção **Excluir com VM** está **marcada**.

**Análise de Custo e Operação:**

Esta configuração é uma prática recomendada para VMs voláteis, como as Instâncias Spot. Ao marcar esta caixa, o disco de SO será **automaticamente excluído** quando a Máquina Virtual for excluída (por remoção Spot ou exclusão manual). Isso garante que não haja custos persistentes de armazenamento para discos órfãos após a interrupção da VM, alinhando-se com a estratégia de custo otimizado da Instância Spot.

## **II. Gerenciamento Avançado de Discos e Cache**

<p align="center">
  <img src="/images/project05/discos02.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 7 - Configuração dos serviços de Armazenamento em Discos</em>
</p>

A Imagem 2 exibe o gerenciamento de discos de dados e as configurações de cache para o disco de SO.

**2.1. Discos Gerenciados e Zonas de Disponibilidade**

O resumo da seção Avançado confirma que a opção **Usar discos gerenciados** está ativada.

**Análise:**

A opção confirma uma restrição arquitetônica fundamental: **"A zona de disponibilidade exige discos gerenciados"**. Como a VM foi configurada em uma Zona de Disponibilidade, o uso de Discos Gerenciados é obrigatório, pois eles são o pré-requisito para o modelo de alta disponibilidade zonal no Azure.

**2.2. Disco de SO Efêmero**

Foi selecionada a opção **Nenhum** para o Disco de SO efêmero.

**Análise:**

Discos de SO Efêmeros usam o disco temporário local da VM (se houver) para hospedar o SO, o que oferece latência ultrabaixa, mas torna o disco ***não persistente*** em caso de reinicialização ou desalocação. A seleção Nenhum significa que o disco de SO persistente (Premium SSD LRS) será usado, garantindo a **durabilidade** do SO e do sistema de arquivos, o que é preferível para a maioria das cargas de trabalho, incluindo as que utilizam Instâncias Spot, pois o estado do SO será retido após uma expulsão seguida de re-provisionamento.

**2.3. Configuração de Cache do Disco de SO**

O posicionamento do cache do SO (Posicionamento do cache do SO) está selecionado, o que, por padrão para discos de SO, define o **Caching do Host** para **Leitura/Escrita (Read/Write)**.

**Análise de Desempenho:**

O Caching do Host permite que a VM utilize a memória local do host para armazenar dados usados recentemente, o que pode aumentar significativamente o desempenho de leitura.

- **Disco de SO:** O padrão de cache Leitura/Escrita é geralmente recomendado para discos de SO, pois o SO realiza operações balanceadas de leitura e escrita.
- **Limitação:** O Caching do Host é suportado apenas para discos com tamanho inferior a 4 TiB. O disco de 30 GiB se enquadra facilmente neste requisito, garantindo o benefício do cache para o desempenho do SO.

A configuração de cache contribui para um ambiente operacional de alta velocidade, complementando a escolha do Premium SSD para o disco de SO.

---
# **ANÁLISE DETALHADA DE REDE, SEGURANÇA PERIMETRAL E PERFORMANCE**

Análise das configurações de rede da Máquina Virtual. As escolhas feitas nesta etapa definem a conectividade da VM, o perímetro de segurança e a otimização de desempenho.

## **I. Configuração da Interface de Rede (VNet, Subnet e IP Público)**

<p align="center">
  <img src="/images/project05/rede01.jpg" alt="Serviços de Rede" style="max-width: 100%;">
  <br>
  <em>Figura 8 - Configuração dos serviços de Rede</em>
</p>

A primeira seção do guia "Rede" estabelece a infraestrutura de rede virtual (VNet) onde a VM será implantada.

**1.1. Estrutura da Rede Virtual e Subnet**

O usuário está criando uma nova rede virtual ((Novo) vnet-westus2) e uma nova sub-rede (snet-westus2-1).

- **VNet:** vnet-westus2 (rg-DIO\_AZ900). O nome da VNet está alinhado com o Grupo de Recursos e a região (West US 2), seguindo boas práticas de nomeação. Uma VNet é o limite de rede privado e isolado na nuvem Azure, permitindo que os recursos se comuniquem com segurança entre si, com a internet e com redes locais.
- **Sub-rede:** snet-westus2-1, com o bloco de endereço 172.16.0.0/24. As sub-redes são divisões da VNet, permitindo a organização e o controle de acesso de tráfego (via NSGs).

**1.2. Implantação e Redundância do Endereço IP Público**

<p align="center">
  <img src="/images/project05/rede02.jpg" alt="Serviços de Rede" style="max-width: 100%;">
  <br>
  <em>Figura 9 - Configuração dos serviços de Rede</em>
</p>

O usuário opta por criar um novo IP público, vm-ubuntu-az900-ip, com características específicas que se alinham à escolha inicial de Zona de Disponibilidade da VM.

- **SKU:** Standard. O SKU Standard é o padrão moderno do Azure, oferecendo recursos aprimorados em comparação com o SKU Basic (que está em processo de aposentadoria).
- **Resiliência Zonal:** A opção selecionada é **Com redundância de zona** (Zone-redundant). Esta configuração é mandatória ao implantar a VM em uma Zona de Disponibilidade. Um IP público com redundância de zona garante que o endereço IP sobreviva à falha de uma única Zona de Disponibilidade, pois o recurso é replicado em várias zonas dentro da região. Isso garante a alta disponibilidade em nível de rede.

O aviso exibido na Figura 8, "A anexação de um IP público existente está desabilitada quando a zona selecionada pelo Azure é escolhida...", reforça que a arquitetura zonal da VM impõe requisitos específicos para a criação e anexação de recursos de IP público.

## **II. Definições de Segurança de Rede (Network Security Group - NSG)**

A seção de Portas de entrada públicas e Grupo de segurança de rede define o perímetro de segurança para a VM.

**2.1. Configuração do NSG**

O usuário escolhe uma configuração de NSG simplificada (possivelmente Básico ou inline) ao selecionar **Permitir portas selecionadas** sob o Grupo de segurança de rede do adaptador. Um Network Security Group (NSG) atua como um firewall, filtrando o tráfego de rede de entrada (inbound) e saída (outbound) para a VM.

**2.2. O Risco Crítico das Portas de Entrada**

O usuário selecionou explicitamente a abertura das seguintes portas:

- **HTTP (80)**
- **SSH (22)**

O portal Azure emite um aviso de segurança severo: **"Isso permitirá que todos os endereços IP acessem sua máquina virtual. Isso é recomendado somente para testes."**

- **Exposição SSH (Porta 22):** Abrir a porta SSH (22) para a internet pública (implícito pela ausência de restrições de IP de origem) expõe a VM a ataques automatizados de força bruta. Este risco é amplificado pelo fato de o usuário ter optado pela autenticação baseada em **Senha** (conforme a análise inicial) em vez de Chaves SSH. A melhor prática de segurança, que foi comprometida, exige o bloqueio da porta 22 ao tráfego de entrada da Internet.
- **Exposição HTTP (Porta 80):** A abertura da Porta 80 para serviços web permite a comunicação, mas não oferece criptografia em trânsito. A recomendação moderna é migrar para HTTPS (Porta 443) para garantir a segurança da comunicação.

A combinação de um IP público com redundância de zona (alta disponibilidade) com portas de gerenciamento abertas ao mundo representa uma falha de segurança que precisa de correção imediata, preferencialmente por meio de serviços como **Azure Bastion** (que acessa a VM por SSH/443 privado) ou **Acesso Just-in-Time (JIT)** (que fecha a porta 22 por padrão, abrindo-a apenas mediante solicitação).

## **III. Otimização de Performance e Gerenciamento do Ciclo de Vida**

<p align="center">
  <img src="/images/project05/rede03.jpg" alt="Serviços de Rede" style="max-width: 100%;">
  <br>
  <em>Figura 10 - Configuração dos serviços de Rede</em>
</p>

**3.1. Habilitação de Rede Acelerada**

A opção **Habilitar rede acelerada** está **marcada**.

- **Análise de Performance:** A Rede Acelerada (Accelerated Networking) é uma otimização de desempenho de rede que utiliza a virtualização de E/S de raiz única (SR-IOV). Isso permite que o tráfego de rede ignore o host, reduzindo significativamente a latência, o jitter e a utilização da CPU da VM. É uma escolha de performance recomendada para máquinas virtuais de uso geral, como a série D, quando implantadas com 2 ou mais vCPUs, o que se aplica à VM Standard\_D2ls\_v3.

**3.2. Gerenciamento do Ciclo de Vida do IP e NIC**

A opção **Excluir o IP Público e a NIC quando a VM for excluída** está **marcada**.

- **Análise de Custo e Gerenciamento:** Esta é uma excelente prática operacional, alinhada com a natureza descartável de uma Instância Spot. Ao marcar esta caixa, o Azure garante que a Interface de Rede (NIC) e o Endereço IP Público associados à VM serão removidos automaticamente quando a VM for desalocada ou excluída. Isso evita cobranças desnecessárias por recursos órfãos, complementando a política de exclusão do disco de SO (analisada no relatório anterior).

**3.3. Balanceamento de Carga**

O usuário selecionou **Nenhum** nas Opções de balanceamento de carga.

- **Análise de Arquitetura:** Esta configuração indica que a VM operará como uma instância única, sem distribuição de tráfego por um Load Balancer (Camada 4) ou um Application Gateway (Camada 7). Se o propósito do ambiente for testar uma única instância, essa configuração é aceitável; no entanto, para qualquer carga de trabalho de produção de alta disponibilidade, um balanceador de carga seria necessário para distribuir o tráfego entre várias VMs.

