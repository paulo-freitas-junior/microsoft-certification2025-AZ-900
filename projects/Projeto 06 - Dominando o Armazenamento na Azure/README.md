# **Dominando o armazenamento no Azure**

O Azure Storage é a fundação de armazenamento escalável e durável da Microsoft, projetado para atender às exigências de aplicações modernas e cargas de trabalho de dados. Ele oferece uma solução de armazenamento como serviço (SaaS) altamente disponível, com foco em objetos (Blobs), arquivos, filas e tabelas, fornecendo um namespace único para todos esses serviços. A conta de armazenamento provisionada, como a observada em stoaz900dio2025, serve como o ponto de partida fundamental, definindo as propriedades essenciais de desempenho, localização geográfica e redundância.

O planejamento cuidadoso da conta de armazenamento é crucial, pois suas configurações iniciais ditam o perfil de custo e resiliência dos dados. Por exemplo, a escolha entre desempenho Padrão (HDD) e Premium (SSD) define a latência de acesso, enquanto a seleção de redundância, como o Armazenamento com Redundância Local (LRS), determina como os dados são replicados — neste caso, três vezes dentro de um único datacenter para otimização de custo, mas sem proteção contra falhas em nível de região. O Azure também oferece camadas de acesso (Quente, Fria e Arquivos) que ajustam a relação entre o custo de armazenamento da capacidade e o custo das transações, permitindo que os clientes otimizem o custo-benefício de acordo com a frequência de acesso aos dados.

Além da infraestrutura física, as configurações de segurança, como a exigência de HTTPS e a gestão do acesso aos contêineres e blobs, são essenciais. Para garantir a segurança do acesso delegado, a Microsoft recomenda o uso de Shared Access Signatures (SAS) protegidas por credenciais do Microsoft Entra (User Delegation SAS) em vez de chaves de conta (Account Key SAS), uma prática que limita o risco de segurança e se alinha às melhores práticas de autorização em nuvem.O conjunto de decisões tomadas durante a criação de uma conta de armazenamento define o framework operacional e de segurança para todos os dados que ela irá hospedar.

## **I. Visão Geral e Iniciação do Provisionamento da Conta de Armazenamento**

<p align="center">
  <img src="/images/project06/storage01.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 1 - Configuração dos Centro de Armazenamento</em>
</p>

O processo de provisionamento se inicia no portal do Azure, acessando o "Centro de armazenamento", especificamente a seção dedicada a "Contas de armazenamento (Blobs)". A Figura 1 ilustra a tela inicial deste serviço, que confirma a ausência de recursos provisionados, declarando: "Não há contas de armazenamento para exibir."

A conta de armazenamento serve como o recurso fundamental para hospedar todos os objetos de dados escaláveis e duráveis no Azure, provendo um namespace único para Blobs, Files, Queues e Tables. A necessidade de provisionamento é, portanto, o ponto de partida para qualquer aplicação que exija armazenamento escalável. A ação de iniciar a criação da conta de armazenamento é o passo seguinte, disparado pelo botão "Criar conta de armazenamento."

## **II. Detalhamento da Configuração Básica e Implicações de Redundância**

<p align="center">
  <img src="/images/project06/storage02.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 2 - Criação de uma conta de armazenamento</em>
</p>

A configuração básica é essencial, pois define os parâmetros de faturamento, localização e durabilidade que formarão a base da conta de armazenamento.

**2.1 Identificação e Nomenclatura do Recurso**

Os dados de identidade do recurso foram definidos na Figura 2. A conta está sendo criada na Assinatura Azure subscription 1 e associada ao Grupo de Recursos existente AZ900-DIO. O uso de um grupo de recursos é uma prática recomendada para gerenciar o ciclo de vida, faturamento e políticas de controle de acesso (RBAC) dos recursos relacionados.

O Nome da conta de armazenamento escolhido é stoaz900dio2025. Este nome deve ser globalmente único dentro do Azure e é composto apenas por letras minúsculas e números. A conta será implantada na Região (US) East US. A seleção regional é crucial, influenciando a latência de acesso ao dado e as opções de redundância geográfica disponíveis.

**2.2 Análise da Configuração de Tipo e Desempenho**

O **Tipo de conta preferencial** selecionado é Armazenamento de Blobs ou do Azure Data Lake Storage Gen 2. Isso implica que a conta é uma conta de uso geral v2 (GPv2), que suporta a maioria dos recursos modernos do Azure Storage, incluindo Blobs de Bloco, Files, Queues e Tables.

Em relação ao **Desempenho**, a opção Padrão foi escolhida. O desempenho Padrão tipicamente utiliza Hard Disk Drives (HDDs) como meio de armazenamento subjacente. Essa escolha é recomendada para a maioria dos cenários de uso geral (GPv2) onde o custo é uma consideração primordial. Embora a latência seja maior em comparação com a opção 

Premium (que utiliza SSDs para desempenho e baixa latência consistentes), o desempenho Padrão oferece um excelente equilíbrio de custo-benefício para cargas de trabalho que não exigem latência ultrabaixa.

**2.3 Análise Crítica da Redundância: LRS (Locally Redundant Storage)**

A configuração de **Redundância** escolhida é LRS (armazenamento com redundância local).

A LRS é a opção de redundância de menor custo oferecida pelo Azure Storage. Sob o LRS, os dados são replicados três vezes dentro de uma única localização física – especificamente, dentro de um único datacenter na região primária.

A implicação arquitetônica dessa escolha é significativa. Embora a LRS proteja os dados contra falhas transitórias de hardware, como falhas de disco ou rack, ela não oferece proteção contra falhas em nível de datacenter, como incêndios ou inundações.<sup>2</sup> A escolha da LRS, especialmente em conjunto com o desempenho Padrão, sugere um cenário onde o custo é o fator determinante e a durabilidade é considerada aceitável, possivelmente porque os dados armazenados são facilmente reconstruíveis, ou porque rigorosos requisitos de governança de dados impedem a replicação inter-regional.

Para aplicações que exigem alta disponibilidade ou proteção contra desastres regionais, a Microsoft recomenda o uso de Zone-Redundant Storage (ZRS) ou Geo-Redundant Storage (GRS).

O contraste entre as opções de redundância ilustra o trade-off entre custo e resiliência:

**Tabela 1: Comparativo de Opções de Redundância de Armazenamento no Azure**

|**Opção de Redundância**|**Custo Relativo**|**Replicação na Região Primária**|**Proteção contra Desastres Regionais**|**Durabilidade (SLA)**|
| :- | :- | :- | :- | :- |
|LRS (Locally Redundant Storage)|Mais Baixo|3x no mesmo Datacenter|Não|11 Noves|
|ZRS (Zone-Redundant Storage)|Intermediário|3x em Zonas de Disponibilidade (AZs)|Sim (contra falha de 1 DC)|12 Noves|
|GRS (Geo-Redundant Storage)|Mais Alto|3x LRS + 1x Assíncrona em Região Secundária|Sim|16 Noves|

## **III. Definições de Segurança Avançada e Camadas de Acesso**

As configurações na aba 'Avançado' complementam a definição da conta, abordando a segurança de comunicação e a otimização de custo por meio das camadas de acesso.

**3.1 Configurações de Segurança do Endpoint**

<p align="center">
  <img src="/images/project06/storage03.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 3 - Configuração da criação da conta de armazenamento</em>
</p>

Na Figura 3, a opção **Exigir transferência segura (HTTPS)** está habilitada. Esta é uma prática essencial de segurança, garantindo que todas as comunicações com a conta de armazenamento sejam realizadas através de HTTPS, utilizando criptografia TLS. A 

**Versão mínima do TLS** está definida como Versão 1.2, que é o padrão de segurança atual, crucial para mitigar vulnerabilidades associadas a protocolos TLS/SSL mais antigos.

A opção **Habilitar o acesso à chave de conta** também está marcada. Essa permissão é fundamental, pois permite que a conta suporte a geração de Tokens de Acesso Compartilhado (SAS) baseados na Chave de Conta (Account Key SAS). Embora esta opção seja frequentemente necessária para compatibilidade com sistemas legados, ela é frequentemente desabilitada em ambientes de segurança mais rigorosa, que dependem exclusivamente do Microsoft Entra ID (RBAC) para controle de acesso.

**3.2 Protocolos e Namespace Hierárquico**

O **Namespace Hierárquico** está desabilitado, indicando que a conta não está sendo configurada primariamente para uso como Azure Data Lake Storage Gen2, que requer essa funcionalidade para suportar semântica de arquivo e ACLs nativas. Consequentemente, os protocolos **SFTP** e **NFS v3**, que dependem do Namespace Hierárquico para sua operação, também estão desabilitados.

**3.3 Configuração da Camada de Acesso (Imagem 4)**

<p align="center">
  <img src="/images/project06/storage04.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 4 - Configuração da criação da conta de armazenamento</em>
</p>

A configuração de custo e acesso da conta é finalizada com a seleção da camada de acesso padrão para blobs.

A **Camada de acesso** padrão selecionada é Quente (Hot Tier). Esta camada é otimizada para armazenar dados que são **acessados ou modificados frequentemente**.<sup>4</sup> Do ponto de vista de custo, a camada Quente tem o custo de armazenamento de capacidade mais alto, mas em compensação, apresenta os custos de acesso e transação mais baixos.

A combinação do Desempenho Padrão (HDD) e a Camada de Acesso Quente estabelece um perfil de arquitetura otimizado para o consumo ativo de dados, onde a frequência de leitura e escrita é alta, mas a exigência de latência não justifica o custo adicional do armazenamento Premium (SSD). Se o objetivo fosse otimizar o custo de retenção a longo prazo de dados acessados esporadicamente (como backups), a camada Fria (Cool Tier) teria sido a escolha mais apropriada, apesar de impor uma retenção mínima de 30 dias e custos de acesso mais altos.

**Tabela 2: Matriz de Análise de Camadas de Acesso do Azure Blob Storage**

|**Camada de Acesso**|**Custo de Armazenamento**|**Custo de Acesso/Transação**|**Latência**|**Caso de Uso Principal**|
| :- | :- | :- | :- | :- |
|Quente (Hot)|Mais Alto|Mais Baixo|Baixa (Milissegundos)|Dados acessados **frequentemente**.|
|Fria (Cool)|Mais Baixo|Mais Alto|Baixa (Milissegundos)|Dados acessados **infrequentemente** (Mínimo de 30 dias).|
|Arquivos (Archive)|Mais Baixo|Mais Alto|Alta (Horas)|Dados raramente acessados, com latência flexível.|

## **IV. Configuração de Acesso à Rede e Roteamento**

A aba 'Rede' define as políticas de segurança de perímetro da conta de armazenamento e como o tráfego de dados é direcionado.

**4.1 Configuração de Acesso Público**

<p align="center">
  <img src="/images/project06/storage05.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 5 - Configuração dos serviços de rede da conta de armazenamento</em>
</p>

Em **Acesso à rede pública**, a conta está configurada como Habilitar, e o **Escopo de acesso à rede pública** está definido como Habilitar de todas as redes. Esta é a configuração mais permissiva em termos de firewall, permitindo que a conta de armazenamento aceite solicitações de qualquer rede pública.

O portal do Azure sinaliza que permitir o acesso público de todas as redes aumenta o risco de segurança, e sugere que o uso de firewalls de rede ou a restrição a redes virtuais (VNets) específicas forneceria uma proteção mais robusta. Não foram adicionados Pontos de Extremidade Privados (*Private Endpoints*), confirmando que a exposição do acesso é prioritariamente via internet pública.

**4.2 Análise da Preferência de Roteamento (Imagem 6)**

<p align="center">
  <img src="/images/project06/storage06.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 6 - Configuração dos serviços de rede da conta de armazenamento</em>
</p>

A **Preferência de roteamento** selecionada é Roteamento da Microsoft.

A decisão sobre a preferência de roteamento determina como o tráfego de dados é encaminhado entre o cliente e a conta de armazenamento. O Roteamento da Microsoft utiliza a rede global da Azure, otimizada para alto desempenho, baixa latência e maior segurança, pois o tráfego permanece majoritariamente dentro da infraestrutura dedicada da Microsoft.

A alternativa, Roteamento da Internet, utiliza o roteamento padrão da internet para enviar dados ao ponto de extremidade. Para uma conta configurada para uso frequente (Camada Quente) na região *US East US*, o Roteamento da Microsoft é uma escolha lógica para garantir a latência mínima e o desempenho consistente do acesso, aproveitando a otimização da rede de operadora de nível 1 da Microsoft.

## **V. Estratégias de Proteção de Dados e Revisão da Conta**

A aba 'Proteção de dados' permite configurar recursos de resiliência contra erros humanos ou modificações acidentais, elementos cruciais para a durabilidade dos dados.

**5.1 Análise das Configurações de Recuperação**

<p align="center">
  <img src="/images/project06/storage07.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 7 - Configuração da proteção de dados do armazenamento</em>
</p>

A Figura 7 demonstra que todas as opções de recuperação críticas estão **desabilitadas**. Isso inclui:

- Restauração pontual para contêineres.
- Habilitar exclusão temporária para blobs.
- Habilitar exclusão temporária para contêineres.
- Habilitar o controle de versão para blobs.
- Habilitar feed de alterações de blob.

A desativação da **Exclusão Temporária de Blobs** (*Soft Delete*) representa um risco operacional significativo. A exclusão temporária é a primeira linha de defesa contra exclusões acidentais ou maliciosas, permitindo a recuperação de blobs excluídos dentro de um período de retenção definido. Sem a exclusão temporária, a exclusão de um blob é permanente e irrecuperável (*Hard Delete*).

Dado que a conta está configurada com LRS (que já não protege contra desastres regionais ou de datacenter), a ausência de mecanismos de recuperação como a exclusão temporária aumenta drasticamente a vulnerabilidade dos dados a erros operacionais. Para dados valiosos, a habilitação da exclusão temporária é considerada uma exigência de conformidade e resiliência.

**5.2 Revisão Final e Propriedades da Conta**

<p align="center">
  <img src="/images/project06/storage08.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 8 - Overview da conta de armazenamento criada</em>
</p>

A Figura 8 apresenta o painel "Visão geral" da conta de armazenamento stoaz900dio2025 após seu provisionamento bem-sucedido (criada em 27/09/2025). As propriedades confirmam as escolhas feitas:

- **Tipo de Conta:** StorageV2 (uso geral v2).
- **Desempenho:** Standard.
- **Replicação:** LRS (armazenamento com redundância local).
- **Nível de Acesso (Padrão):** Hot.
- **Acesso à Rede Pública:** Habilitado (Habilitar de todas as redes).
- **Exclusão Temporária:** Desabilitado (confirmando o risco de perda permanente de dados).

O recurso está operacional e pronto para a criação de contêineres e o consumo de serviços de armazenamento.

## **VI. Criação de Contêiner e Configuração de Acesso Delegado**

As etapas finais envolvem a criação do contêiner lógico para os blobs e a geração de um token de acesso seguro para delegação de permissões.

**6.1 Criação do Contêiner**

<p align="center">
  <img src="/images/project06/storage09.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 9 - Configuração dos contêiners lógicos de armazenamento</em>
</p>

O contêiner de Blob, o objeto lógico que armazena os dados, foi criado com o nome container-az900.

O **Nível de acesso anônimo** foi definido como Privado (sem acesso anônimo). Embora a conta de armazenamento tenha uma regra de firewall aberta (permitindo acesso de todas as redes), esta configuração de contêiner garante que o acesso aos dados reais dentro do contêiner exija estritamente autorização, seja via Microsoft Entra ID (RBAC), Chaves de Conta, ou Tokens SAS.

**6.2 Geração da Shared Access Signature (SAS)**

<p align="center">
  <img src="/images/project06/storage10.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 10 - Configuração dos Tokens de acesso compartilhados</em>
</p>

A Shared Access Signature (SAS) é um mecanismo de segurança projetado para fornecer acesso delegado limitado aos recursos do Azure Storage via URL assinado.

A Figura 10 detalha o processo de geração de uma SAS para o contêiner container-az900.

O **Método de assinatura** selecionado é Chave de assinatura (Chave 1). Esta escolha implica que o token SAS é assinado usando a chave mestra da conta de armazenamento (Account Key).</sup> Esta é uma consideração crítica de segurança.

A SAS baseada na Chave de Conta é problemática porque, se o token for comprometido, ele concede acesso irrestrito dentro do escopo e das permissões especificadas, independentemente das permissões RBAC do usuário que o criou. Além disso, a chave da conta de armazenamento (Chave 1) possui poderes administrativos totais sobre o recurso.

A **melhor prática de segurança** recomendada pela Microsoft para Blobs é o uso da **Chave de Delegação de Usuário** (*User Delegation SAS*). Uma SAS de Delegação de Usuário é protegida por credenciais do Microsoft Entra ID (RBAC). Isso garante que, mesmo que o token SAS seja vazado, o acesso concedido é limitado não apenas pelas permissões do token, mas também pelas permissões inerentes do usuário que o gerou.

O token SAS gerado na Imagem 10 tem um período de validade curto (expiração em 28/09/2025, aproximadamente um dia após a criação). Este prazo limitado serve como um importante controle de mitigação de risco, limitando o tempo de exposição caso o token seja comprometido. As **Permissões** selecionadas são sete, indicando um amplo escopo de operações (leitura, escrita, exclusão, etc.).

O resultado final é o Token SAS do blob e a Url de SAS do blob (URL assinado), que podem ser distribuídos para conceder acesso temporário e delegado ao contêiner container-az900.

**Tabela 3: Comparativo de Métodos de Assinatura para Shared Access Signatures (SAS)**

|**Método de Assinatura**|**Credenciais de Segurança**|**Segurança**|**Prazo Máximo (Blobs)**|**Recomendação Microsoft**|
| :- | :- | :- | :- | :- |
|Chave de Assinatura (Account Key SAS)|Chave da Conta de Armazenamento|Baixa (Risco de comprometimento da chave mestra)|Sem limite imposto (recomenda-se política).|Não recomendado para acesso limitado.|
|Chave de Delegação de Usuário (User Delegation SAS)|Credenciais Microsoft Entra (RBAC)|Alta (Escopo limitado pelo RBAC).|Máximo de 7 dias.|**Recomendado** para blobs.|

## **VII. Provisionamento do Projeto Azure Migrate e Ferramentas de Migração de Dados**

<p align="center">
  <img src="/images/project06/storage11.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 11 - Migrações para o Azure</em>
</p>

Após o provisionamento da conta de armazenamento e a preparação do acesso, a próxima etapa lógica para uma estratégia de nuvem é o planejamento da migração de ativos de datacenter locais para o Azure, processo facilitado pelo **Azure Migrate**.

**7.1 Criação do Projeto Azure Migrate**

O fluxo de trabalho de migração começa no serviço **Migrações para Azure**, conforme a Figura 11, que descreve as três fases principais do processo de modernização:

1. **Explorar:** Conectar e descobrir todos os servidores e aplicativos no datacenter local.
1. **Decidir e Planejar:** Avaliar o inventário descoberto e criar planos de migração.
1. **Executar:** Migrar efetivamente os aplicativos e servidores.

<p align="center">
  <img src="/images/project06/storage12.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 12 - Configuração projeto de armazenamento</em>
</p>

A Figura 12 detalha a criação do projeto de migração. O projeto migrate-az900-DIO foi provisionado na mesma Assinatura (Azure subscription 1) e Grupo de Recursos (AZ900-DIO) da conta de armazenamento, estabelecendo uma central de gerenciamento para todos os esforços de modernização e migração. A **Geografia** selecionada para o projeto é Brasil, e o **Método de conectividade** escolhido é Ponto de extremidade público.

<p align="center">
  <img src="/images/project06/storage13.jpg" alt="Serviços de Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 13 - Cenários de migração para o Azure</em>
</p>

A Figura 13, que mostra o painel do projeto recém-criado, ilustra a ampla gama de cenários de migração que o Azure Migrate suporta, incluindo Web Apps, Infraestrutura Virtual, Arquivos do Azure e, criticamente, ferramentas específicas de transferência de dados como **Data Box** e o **Migrador de Armazenamento do Azure** (*Azure Storage Mover*).

**7.2 Ferramentas de Migração de Dados para o Azure Storage**

O Azure oferece diversas ferramentas de software e hardware, adequadas para diferentes escalas e velocidades de migração, dependendo da largura de banda de rede disponível e do volume de dados. O método de migração pode ser **online** (usando a rede) ou **offline** (usando dispositivos físicos).

**AzCopy**

O AzCopy é uma ferramenta de linha de comando (CLI) otimizada e de código aberto, ideal para migrações de dados não estruturados de pequena escala e transferências incrementais (online), utilizando a rede. É amplamente utilizado para copiar dados de e para o Azure Storage (Blobs, Arquivos e Azure Data Lake Storage) e suporta a migração SMB para Azure Files e NFS para Blob.

**Azure Data Box**

O Azure Data Box é a solução de transferência de dados em larga escala (terabytes) no método **offline**. O serviço envolve o envio de um dispositivo de armazenamento físico robusto (com capacidade utilizável de 80 TB ou mais) para o datacenter local do cliente. O cliente copia os dados para o dispositivo, que é então enviado de volta ao Azure para upload direto na conta de armazenamento. O Data Box é a opção preferida quando a largura de banda de rede disponível não é suficiente para completar a migração online dentro do cronograma desejado. Ele suporta a transferência de dados para Azure Blobs e Azure Files.

**Azure Storage Explorer e Migrador de Armazenamento do Azure**

- **Azure Storage Explorer:** Esta é uma ferramenta de Interface Gráfica do Usuário (GUI) para gerenciar o conteúdo do Azure Storage. Embora não seja projetada para migrações de dados em massa ou automação, ela é útil para transferências manuais e ocasionais de um número limitado de arquivos.
- **Migrador de Armazenamento do Azure (Azure Storage Mover):** Este é um serviço de migração totalmente gerenciado (mostrado na Imagem 13) que permite migrar arquivos e pastas de origens locais ou AWS S3 para o Armazenamento do Azure (Blobs e Arquivos). Ele é projetado para migrações de fidelidade total, minimizando o tempo de inatividade da carga de trabalho e permitindo o gerenciamento centralizado de migrações distribuídas globalmente.

## **VIII. Conclusões e Recomendações de Conformidade e Otimização**

A análise do processo de provisionamento da conta stoaz900dio2025 e a subsequente criação do projeto Azure Migrate revelam uma arquitetura otimizada para custo e desempenho de acesso frequente dentro de uma única região, em preparação para a consolidação de ativos na nuvem.

**8.1 Síntese Arquitetônica e Trade-offs de Design**

O design da conta priorizou claramente o custo-benefício em detrimento da resiliência máxima e das melhores práticas de segurança de acesso delegado:

1. **Otimização de Custo/Desempenho:** A combinação de desempenho Padrão (HDD), redundância LRS, e Camada de Acesso Quente indica um objetivo de baixo custo operacional para dados ativamente utilizados.
1. **Preparação para Migração:** A criação imediata do projeto Azure Migrate na geografia Brasil confirma a intenção de usar a conta de armazenamento como destino de migração para cargas de trabalho locais. O projeto está preparado para utilizar soluções de transferência de dados escaláveis como Data Box e Azure Storage Mover, indicando um esforço de modernização abrangente.
1. **Segurança de Comunicação Eficaz:** A adesão à exigência de HTTPS e TLS 1.2, juntamente com o Roteamento da Microsoft, garante um transporte de dados seguro e otimizado.
1. **Vulnerabilidade de Resiliência:** A escolha de LRS (vulnerável a desastres de datacenter) combinada com a desativação da Exclusão Temporária e do Versionamento (vulnerável a erros humanos) cria uma postura de baixa resiliência contra perdas de dados, um risco amplificado no contexto de uma migração de dados em massa.
1. **Risco de Segurança Delegada:** A utilização do método Account Key SAS expõe a chave mestra da conta, introduzindo um risco de segurança que poderia ter sido mitigado pela adoção da SAS de Delegação de Usuário.

A arquitetura demonstra uma proteção robusta no Nível de Transporte (camada 4/7), mas exibe falhas significativas nos Níveis de Resiliência de Dados e Segurança Delegada de Identidade.

**8.2 Recomendações Críticas**

Para elevar a conta de armazenamento stoaz900dio2025 aos padrões de produção e resiliência de dados exigidos pela maioria dos ambientes corporativos, especialmente considerando seu papel como destino de migração, as seguintes ações são recomendadas:

**A. Aprimoramento da Proteção de Dados (Mitigação Imediata do Risco Operacional)**

A configuração LRS torna a conta vulnerável a desastres físicos. A principal defesa contra erros operacionais dentro dessa restrição de redundância é a habilitação de recursos de recuperação:

1. **Habilitar Exclusão Temporária (*Soft Delete*):** Ativar a exclusão temporária para Blobs e Contêineres com um período de retenção adequado. Esta é uma etapa obrigatória para fornecer uma rede de segurança contra exclusão acidental e garantir que a perda de dados acidentais não seja permanente.
1. **Habilitar Versionamento de Blobs:** O versionamento permite rastrear e reverter modificações em blobs, fornecendo um histórico de alterações e protegendo contra sobrescrita de dados.

**B. Migração do Método de Autorização SAS**

A prática de usar Account Key SAS deve ser revista e substituída:

1. **Adotar User Delegation SAS:** Para todas as novas gerações de Shared Access Signatures destinadas a Blobs, a organização deve migrar para o método **Chave de Delegação de Usuário**. Isso garante que o acesso delegado seja limitado pelas permissões RBAC do usuário, aderindo à recomendação de segurança da Microsoft.

**C. Revisão do Acesso à Rede**

1. **Restringir o Acesso Público:** Revisar a configuração de firewall de Habilitar de todas as redes. Para ambientes de produção com dados sensíveis, a configuração ideal é restringir o acesso apenas a redes virtuais específicas ou implementar **Pontos de Extremidade Privados (*Private Endpoints*)**. Os Private Endpoints garantem que o tráfego para a conta de armazenamento passe pela espinha dorsal privada da Azure, eliminando a exposição à internet pública.

