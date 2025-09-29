# Ferramentas de Implantação e Gerenciamento de Recursos no Azure

O Azure oferece uma variedade de ferramentas flexíveis para interagir, implantar e gerenciar seus recursos. A escolha da ferramenta ideal geralmente depende do caso de uso (manual versus automatizado) e da preferência de interface (gráfica versus linha de comando).

## 1. Portal do Azure

O **Portal do Azure** é uma interface gráfica de usuário (GUI) baseada na web que permite criar, gerenciar e monitorar recursos do Azure.

* **Finalidade:** É uma ferramenta fácil de usar para prototipagem, gerenciamento diário, visualização e tarefas de configuração.
* **Recursos:**
    * Interface web intuitiva.
    * Criação de painéis configuráveis.
    * Acesso a informações de cobrança e configurações de assinatura.
* **Vantagem:** Ideal para usuários que preferem uma abordagem visual ou para aqueles que estão começando a usar o Azure.

<p align="center">
  <img src="/images/project10/ferramentas.jpg" alt="Ferramentas" style="max-width: 100%;">
  <br>
  <em>Figura 1 - Visão geral ferramentas de Implantação e Gerenciamento</em>
</p>

---
## 2. Azure Cloud Shell

<p align="center">
  <img src="/images/project10/cloudshell.jpg" alt="Ferramentas" style="max-width: 100%;">
  <br>
  <em>Figura 2 - Visão geral Azure Cloud Shell</em>
</p>

O **Azure Cloud Shell** é um ambiente de shell autenticado e acessível via navegador, hospedado e gerenciado pela Microsoft no Azure.

* **Finalidade:** Oferece uma estação de trabalho de administração pré-configurada para o Azure que pode ser acessada de qualquer lugar.
* **Recursos Principais:**
    * **Autenticação Automática:** Autentica automaticamente o acesso à sua conta Azure.
    * **Persistência de Arquivos:** Persiste arquivos através de sessões, montando um compartilhamento de Arquivos do Azure.
    * **Ferramentas Pré-instaladas:** Inclui as ferramentas de linha de comando mais comuns, como **Azure CLI** e **Azure PowerShell**, além de editores de texto, ferramentas de contêiner, ferramentas de banco de dados e suporte a linguagens como Python, .NET e Node.js.
    * **Experiência de Shell Flexível:** Permite escolher entre as experiências **Bash** ou **PowerShell**.

### 2.1. Azure CLI (Interface de Linha de Comando do Azure)

* **O que é:** Uma ferramenta de linha de comando **multiplataforma** que permite criar e gerenciar recursos do Azure usando comandos interativos ou scripts.
* **Sintaxe:** Semelhante à sintaxe de scripts Bash, tornando-a uma opção mais natural para usuários que trabalham com sistemas Linux.
* **Saída Padrão:** Por padrão, a saída é uma *string* **JSON**.

### 2.2. Azure PowerShell

<p align="center">
  <img src="/images/project10/04_powershell.jpg" alt="PowerShell" style="max-width: 100%;">
  <br>
  <em>Figura 3 - Visão geral Azure PowerShell</em>
</p>

* **O que é:** Um módulo PowerShell (Az) com *cmdlets* que permite criar e gerenciar recursos do Azure.
* **Sintaxe:** Segue o esquema de nomenclatura **verbo-substantivo** do PowerShell (ex: `New-AzResourceGroup`), sendo uma opção natural para usuários que trabalham principalmente com sistemas Windows.
* **Saída Padrão:** Por padrão, a saída são **objetos**.

Ambas as ferramentas (Azure CLI e Azure PowerShell) são ideais para **automatizar tarefas comuns** e estão disponíveis para instalação local no Windows, macOS e Linux.

---
## 3. Azure Resource Manager (ARM)

<p align="center">
  <img src="/images/project10/resource_manager.jpg" alt="Azure Resource Manager" style="max-width: 100%;">
  <br>
  <em>Figura 4 - Visão geral Azure Resource Manager</em>
</p>

O **Azure Resource Manager (ARM)** é o serviço de implantação e gerenciamento do Azure. Ele fornece uma **camada de gerenciamento consistente** que permite criar, atualizar e excluir recursos na sua assinatura do Azure, independentemente de você usar o Portal, PowerShell, CLI ou APIs REST.

* **Função:** Recebe todas as solicitações (autentica e autoriza) antes de encaminhá-las ao serviço Azure apropriado.
* **Benefícios:**
    * **Consistência:** Garante que todas as solicitações sejam tratadas pela mesma API, resultando em funcionalidades uniformes.
    * **Implantação em Grupo:** Permite implantar, gerenciar e monitorar todos os recursos para uma solução como um grupo (usando **Grupos de Recursos**), em vez de lidar com recursos individualmente.
    * **Controle de Acesso:** Integra-se ao Azure RBAC (Controle de Acesso Baseado em Função).

### 3.1. Modelos ARM (ARM Templates)

Os modelos ARM são a forma de **Infraestrutura como Código (IaC)** nativa do Azure.

* **O que são:** Arquivos **JSON (JavaScript Object Notation)** usados para criar e implantar a infraestrutura do Azure.
* **Natureza Declarativa:** Você especifica **o que** deseja implantar (o estado final do recurso) em vez de escrever a sequência de comandos para criá-lo. Isso garante **resultados repetíveis** e consistência na implantação, evitando desalinhamentos ambientais.
* **Recursos:** Oferecem orquestração, validação integrada e suporte a arquivos modulares.

---
## 4. Azure Arc

<p align="center">
  <img src="/images/project10/06_arc.jpg" alt="Azure Arc" style="max-width: 100%;">
  <br>
  <em>Figura 5 - Visão geral Azure Arc</em>
</p>

O **Azure Arc** estende o gerenciamento e a governança do Azure para ambientes multinuvem e *on-premises*.

* **Finalidade:** Simplifica o gerenciamento e a governança, oferecendo uma plataforma de gerenciamento consistente.
* **Funcionalidade:** Projeta seus recursos existentes que **não são Azure** (servidores, clusters Kubernetes e bancos de dados) no **Azure Resource Manager**, permitindo gerenciá-los como se estivessem sendo executados no Azure.
* **Benefícios:**
    * Permite o uso de serviços e recursos de gerenciamento familiares do Azure (como Azure Policy, Azure Monitor e Azure RBAC) para seus recursos fora do Azure.
    * Implementa inventário, gerenciamento, governança e segurança consistentes em todo o seu ambiente.

---

**Outras Ferramentas e Conceitos Importantes:**

* **Infraestrutura como Código (IaC):** Prática que permite codificar os recursos de infraestrutura do Azure (e de outros ambientes) em sintaxe declarativa para garantir a **consistência na implantação em todo o ecossistema** e gerenciar a configuração em escala. Além dos Modelos ARM (que usam JSON), outras ferramentas de IaC incluem **Bicep** e **Terraform**.
* **Azure DevOps (Azure Pipelines):** Oferece serviços para equipes compartilharem código, rastrearem trabalho e enviarem software. O **Azure Pipelines** é usado para criar, testar e implantar aplicativos usando CI/CD (Integração Contínua/Entrega Contínua).
* **SDKs e APIs REST:** Desenvolvedores podem interagir com o Azure programaticamente usando os SDKs para várias linguagens (como Python, Java, .NET) ou diretamente através da API REST.
