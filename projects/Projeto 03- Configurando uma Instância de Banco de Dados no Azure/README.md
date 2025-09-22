# Configurando uma Instância Gerenciada de SQL no Microsoft Azure

O objetivo desde Projeto é de orientar um passo a passo na criação de uma Instância Gerenciada de SQL do Azure usando o Portal do Azure. As configurações utilizadas neste tutorial são em seu padrão básicas do próprio Azure, afim de se criar um entendimento de como é o procedimento de instanciar recursos necessários bem como demais configurações envolvidas para a criação de um banco de dados.

Instância Gerenciada SQL no Azure pode ser compreendido como uma série de decisões arquitetônicas, cada uma com implicações diretas em **segurança**, **desempenho** e **custos.**

---


## 1. Configurar a Instância (Guia "Básico")

São definidos os dados essenciais da instância: nome, região, grupo de recursos, método de autenticação e credenciais de administrador. Todas essas informações são obrigatórias para provisionamento da instância.

<p align="center">
  <img src="/images/project03/Azure01.jpg" alt="Tela principal de criação da instância" style="max-width: 100%;">
  <br>
  <em>Figura 1 - Tela principal de criação da instância</em>
</p>

Os campos a serem preenchidos são:

| Configuração | Descrição |
|--------------|-----------|
| Assinatura | Selecione sua assinatura do Azure. |
| Grupo de recursos | Crie ou selecione um grupo existente. |
| Nome da instância | Escolha um nome válido. |
| Região | Escolha a região onde a instância será criada. |
| Pertence a um pool de instâncias? | Se necessário, selecione “Sim”. |
| Método de autenticação | Use “Autenticação SQL” para este tutorial. |
| Logon de administrador | Escolha um nome de usuário válido (evite `serveradmin`). |
| Senha | Crie uma senha forte (mínimo de 16 caracteres). |

A primeira etapa é configurar os detalhes básicos do projeto e da instância.

- **Assinatura:** Selecione a assinatura do Azure que será usada para os custos e recursos. Na imagem, a assinatura é "Azure subscription 1".
- **Grupo de recursos:** Escolha um grupo de recursos existente ou crie um novo para organizar todos os recursos da sua instância. A imagem mostra um novo grupo chamado "db-SQL-Azure".
- **Nome da Instância Gerenciada:** Defina um nome para a sua instância. A imagem usa "db-sqlserver-azure".
- **Região:** Selecione a região geográfica onde a instância será hospedada. A imagem mostra "West US 2".
- **Pertence a um pool de instâncias?:** Essa opção permite que você adicione a nova instância a um pool existente, o que pode otimizar custos e recursos. Na imagem, a opção "Não" está selecionada.
- **Computação + Armazenamento:** Configure a série, vCores e tamanho do armazenamento. A imagem mostra o uso de "Geração 5, 8 vCores, 256 GB storage".

---

## 2. Computação + Armazenamento

Esta é a sua decisão de dimensionamento. O modelo de vCore permite que se escolha a quantidade de recursos de computação (vCores e memória) e armazenamento de forma independente, oferecendo flexibilidade para adaptar a instância à carga de trabalho.

É selecionada a camada de serviço (como “Uso Geral”), define o número de vCores, o tamanho do armazenamento e o tipo de redundância de backup. Essas configurações determinam o desempenho e a resiliência da instância.

<p align="center">
  <img src="/images/project03/Azure02.jpg" alt="Tela Computação e Armazenamento" style="max-width: 100%;">
  <br>
  <em>Figura 2 - Computação e Armazenamento</em>
</p>

Clique em **Configurar instância gerenciada** e defina:

| Configuração | Valor sugerido |
|--------------|----------------|
| Camada de Serviço | Uso Geral |
| Geração do hardware | Standard (Gen 5) |
| vCores | 8 (ou conforme necessidade) |
| Armazenamento | Defina em GB conforme o tamanho esperado |
| Licença do SQL Server | Escolha entre pagamento por uso ou Benefício Híbrido |
| Redundância do armazenamento de backup | Geográfica (recomendado) |

Clique em **Aplicar** para salvar.

---

## 3. Configurações de Rede

A conectividade da Instância Gerenciada SQL é um ponto de segurança crítico. Ela é implantada dentro de uma **rede virtual (VNet)** do Azure, garantindo isolamento e segurança de nível corporativo.

Seleciona-se a rede virtual e sub-rede onde a instância será implantada. Também define se haverá ponto de extremidade público e quais acessos serão permitidos, garantindo conectividade segura.

<p align="center">
  <img src="/images/project03/Azure02.jpg" alt="Configuração de Rede" style="max-width: 100%;">
  <br>
  <em>Figura 3 - Configuração de Rede</em>
</p>

Na guia **Rede**, configure:

| Configuração | Valor sugerido |
|--------------|----------------|
| Rede virtual / Sub-rede | Crie ou selecione uma existente |
| Tipo de conexão | Escolha conforme necessidade |
| Ponto de extremidade público | Desabilitar |
| Permitir acesso de | Sem acesso (para segurança) |

- **Rede Virtual:** Uma VNet é uma rede privada na nuvem. Ela isola sua instância de outros serviços, criando uma camada de segurança robusta. A Instância Gerenciada SQL exige uma sub-rede dedicada para garantir que os recursos de gerenciamento internos funcionem corretamente.

- **Tipo de Conexão:** Esta configuração afeta diretamente o desempenho.

  - **Proxy:** Todas as conexões passam por um gateway do Azure. É o método padrão e garante a segurança, mas pode adicionar uma pequena latência.

  - **Redirecionamento:** O cliente se conecta diretamente ao nó de computação do banco de dados após a autenticação inicial. Este modelo é altamente recomendado pela documentação da Microsoft para a maioria dos cenários, pois melhora a latência e o throughput (vazão).

- **Ponto de Extremidade Público:** Por padrão, o acesso à instância é restrito à VNet. Habilitar o Ponto de Extremidade Público permite que clientes externos (como de uma rede corporativa ou de outro serviço Azure) se conectem. É essencial usar este recurso com cautela, configurando grupos de segurança de rede (NSGs) para restringir o acesso apenas a endereços IP confiáveis.

---

## 4. Segurança

A Microsoft oferece uma série de recursos de segurança integrados para proteger instâncias e os dados.

- **Microsoft Defender para SQL:** Este serviço atua como uma linha de defesa proativa. Ele escaneia vulnerabilidades, detecta ameaças de injeção de SQL e comportamentos anômalos. Habilitá-lo é uma prática recomendada para qualquer ambiente de produção.

- **Identidade:** As Identidades Gerenciadas (Managed Identities) permitem que a instância se autentique em outros serviços do Azure (como o Azure Key Vault) sem a necessidade de gerenciar credenciais. Isso reduz a exposição de segredos e simplifica a automação.

- **Transparent Data Encryption (TDE):** TDE criptografa os dados em repouso (armazenados em disco). A opção *"Chave gerenciada por serviço"* usa chaves de criptografia protegidas e gerenciadas pelo Azure, proporcionando um alto nível de segurança sem a complexidade de gerenciar chaves próprias.

As configurações de segurança foram mantidas como padrão neste exemplo de Projeto. Elas envolvem políticas de acesso e integração com serviços como o Microsoft Defender, caso desejado.

<p align="center">
  <img src="/images/project03/Azure03.jpg" alt="Configurações sobre Segurança" style="max-width: 100%;">
  <br>
  <em>Figura 4 - Configurações sobre Segurança</em>
</p>

---

## 5. Configurações Adicionais

Essas configurações permitem personalizar a instância para atender aos requisitos específicos das aplicações.

Nessa sessão é possível ajustar ordenação, fuso horário, replicação geográfica e janela de manutenção. Essas opções personalizam o comportamento da instância conforme requisitos técnicos ou operacionais.

<p align="center">
  <img src="/images/project03/Azure04.jpg" alt="Configurações Adicionais" style="max-width: 100%;">
  <br>
  <em>Figura 1 - Configurações adicionais</em>
</p>

Configure conforme necessário:

| Configuração | Valor sugerido |
|--------------|----------------|
| Ordenação | Escolha conforme necessidade |
| Fuso horário | Selecione o fuso horário da instância |
| Replicação geográfica | Não |
| Janela de manutenção | Escolha um horário conveniente |

- **Ordenação e Fuso Horário:** A ordenação define como os dados são classificados e comparados. É crucial escolher a ordenação correta para garantir que as operações de banco de dados, como ORDER BY e comparações de strings, funcionem conforme o esperado conforme o idioma. O fuso horário garante que a data e a hora da instância estejam sincronizadas com a região definida.

- **Replicação Geográfica:** Para cenários de alta disponibilidade e recuperação de desastres, a replicação geográfica cria uma réplica da instância em uma região diferente. Isso protege a carga de trabalho contra falhas regionais, permitindo um failover rápido.

- **Janela de Manutenção:** Esta configuração permite agendar as atualizações do Azure para a instância dentro de uma janela de tempo específica, minimizando o impacto nas operações de negócios.

---

## 6. Marcas

Tags são a melhor forma de organizar or recursos e associá-los a projetos, departamentos ou centros de custo. Eles são fundamentais para uma governança eficaz e para a análise de custos na fatura do Azure.

Adiconar tags (marcas) servem para facilitar a organização e o gerenciamento dos recursos. Exemplos incluem identificar o proprietário ou o ambiente (produção, teste, etc.), úteis para controle de custos e governança.

<p align="center">
  <img src="/images/project03/Azure05.jpg" alt="Tela principal de criação da instância" style="max-width: 100%;">
  <br>
  <em>Figura 6 - Tela principal de criação da instância</em>
</p>

Adicione marcas para organização:

- `Proprietário`: Identifica quem criou.
- `Ambiente`: Produção, Desenvolvimento etc.

---

## 7. Revisar e Criar

Antes de finalizar é necessário revisar todas as configurações feitas, em seguida basta clicar em “Criar” para iniciar a implantação da instância gerenciada.

<p align="center">
  <img src="/images/project03/Azure06.jpg" alt="Revisar e Criar" style="max-width: 100%;">
  <br>
  <em>Figura 7 - Revisão e Criação 1</em>
</p>

2º Parte da Tela de Revisão:

<p align="center">
  <img src="/images/project03/Azure06_1.jpg" alt="Revisar e Criar" style="max-width: 100%;">
  <br>
  <em>Figura 7 - Revisão e Criação 2</em>
</p>

1. Clique em **Examinar + criar**.
2. Verifique todas as configurações.
3. Clique em **Criar** para iniciar a implantação.

---

## 8. Monitorar a Implantação

Acompanhamento do progresso da criação pelo painel de notificações do portal. Após a conclusão, é possível acessar a instância diretamente pelo grupo de recursos.

<p align="center">
  <img src="/images/project03/Azure07.jpg" alt="Grupo de Recursos" style="max-width: 100%;">
  <br>
  <em>Figura 7 - Grupo de Recursos do Azure</em>
</p>

- Acompanhe o progresso pelo ícone de **Notificações**.
- Após a conclusão, acesse o grupo de recursos para visualizar a instância.

---

## 9. Criar um Banco de Dados

Com a instância pronta, é possível adicionar um banco de dados clicando em “+ Novo banco de dados”, definir o nome e escolher se será vazio ou restaurado de backup.

<p align="center">
  <img src="/images/project03/Azure08.jpg" alt="Criação do Banco de Dados" style="max-width: 100%;">
  <br>
  <em>Figura 8 - Azure SQL - SQL Databases</em>
</p>

### Procedimentos para Criação do Banco de Dados

<p align="center">
  <img src="/images/project03/Azure09.jpg" alt="Criação do Banco de Dados SQL" style="max-width: 100%;">
  <br>
  <em>Figura 9 - Criação Do Banco de Dados</em>
</p>

Esta é a etapa inicial onde são definas as configurações fundamentais do banco de dados.

- **Assinatura e Grupo de Recursos:** Assim como na Instância Gerenciada, a Assinatura define o contrato de faturamento, e o Grupo de Recursos serve como um contêiner para organizar todos os recursos do projeto. É uma boa prática agrupar recursos relacionados para facilitar o gerenciamento e o controle de custos.

- **Nome do Banco de Dados:** Escolha um nome que seja único dentro do servidor. O nome deve ser descritivo para que seja possível identificá-lo facilmente entre outros bancos de dados.

- **Servidor:** Um Banco de Dados SQL sempre reside em um servidor lógico. Pode-se selecionar um servidor existente ou criar um novo. Na criação de um novo servidor, deve-se definir seu Nome e Localização (região).

- **Método de Autenticação:** A autenticação é a maneira como os usuários e serviços se conectam ao banco de dados. Pode-se escolher:

  - **Autenticação somente do Microsoft Entra:** Altamente recomendada por questões de segurança, permite a autenticação usando identidades do Microsoft Entra ID (antigo Azure AD).

  - **Usar autenticação SQL e Microsoft Entra:** Permite criar um login de administrador SQL tradicional, além de usar identidades do Microsoft Entra.

  - **Usar autenticação SQL:** Utiliza apenas a autenticação SQL.

<p align="center">
  <img src="/images/project03/Azure09_2.jpg" alt="Criação do Servidor de Banco de Dados SQL" style="max-width: 100%;">
  <br>
  <em>Figura 9 - Criação do Servidor de Banco de Dados SQL</em>
</p>

Somente após a criação do **Servidor de Banco de Dados** que será possível concluír as configurações de criação do banco de dados.

- **Pool elástico SQL:** Um pool elástico é um conjunto de recursos compartilhados para vários bancos de dados. Ele é ideal para cargas de trabalho imprevisíveis, pois os recursos são distribuídos dinamicamente entre os bancos de dados do pool, otimizando o custo e o desempenho.

- **Ambiente de Carga de Trabalho:** É possível escolher entre Desenvolvimento e Produção. Esta escolha define configurações padrão de desempenho, como o número de vCores e a redundância de armazenamento.

- **Computação + Armazenamento:** Opção que define decisão de dimensionamento. O modelo Sem Servidor (Serverless) é uma ótima opção para cargas de trabalho intermitentes, pois ele pausa automaticamente o banco de dados quando inativo e retoma quando uma conexão é solicitada. Os custos são baseados no uso de computação.

- **Redundância do Armazenamento de Backup:** Define como os backups são replicados. A Redundância de zona protege os dados contra falhas em um datacenter, enquanto a Redundância geográfica protege contra falhas em uma região inteira.

<p align="center">
  <img src="/images/project03/Azure09_1.jpg" alt="Criação do Banco de Dados SQL" style="max-width: 100%;">
  <br>
  <em>Figura 9 - Continuação da Conf. Criação do Banco de Dados</em>
</p>


**Configurações de Rede**

Esta seção controla como o banco de dados se conecta e se comunica com outros serviços e aplicações.

- **Conectividade de Rede:** Define como os clientes se conectam.

  - **Sem acesso:** O banco de dados só pode ser acessado de dentro da rede do Azure.

  - **Ponto de extremidade público:** Permite a conexão a partir da internet pública. Requer a configuração de regras de firewall para restringir o acesso apenas a endereços IP confiáveis.

  - **Ponto de extremidade privado:** Uma conexão segura e privada a partir de uma rede virtual do Azure. Esta é a opção mais segura para a maioria dos cenários.

- **Política de Conexão:** Afeta o desempenho.

  - **Padrão:** Usa o redirecionamento para conexões de dentro do Azure e proxy para conexões de fora.

  - **Redirecionamento:** Conecta o cliente diretamente ao nó de computação do banco de dados, reduzindo a latência.

  - **Proxy:** Todas as conexões passam por um gateway do Azure.

- **Versão mínima do TLS:** Defina a versão mínima do Transport Layer Security (TLS) para criptografia da comunicação. A versão 1.2 é a recomendada para garantir a segurança dos dados em trânsito.

<p align="center">
  <img src="/images/project03/Azure09_3.jpg" alt="Configuração de Rede" style="max-width: 100%;">
  <br>
  <em>Figura 10 - Configuração de Rede do Banco de Dados</em>
</p>


**Segurança**

O Azure oferece recursos de segurança robustos para proteger o banco de dados.

- **Microsoft Defender para SQL:** Este serviço é uma linha de defesa contra ameaças e vulnerabilidades. Ele oferece avaliação de vulnerabilidade e detecção de ameaças para proteger os dados.

- **Identidade do Servidor:** Permite que o servidor use uma identidade gerenciada para acessar outros serviços do Azure, como o Azure Key Vault, sem a necessidade de armazenar credenciais.

- **Gerenciamento Transparente de Chaves de Criptografia de Dados:** Ativa o Transparent Data Encryption (TDE), que criptografa os dados, backups e logs em repouso. A opção "Chave gerenciada por serviço" utiliza chaves geradas e gerenciadas pelo Azure, simplificando o processo de criptografia.

- **Always Encrypted:** Esta tecnologia permite que os dados sensíveis sejam criptografados na aplicação cliente e nunca sejam expostos como texto simples no banco de dados. Ela cria uma separação entre os dados e os administradores do banco de dados, que podem gerenciar a instância sem acessar os dados confidenciais.

<p align="center">
  <img src="/images/project03/Azure10.jpg" alt="Configuração de Segurança" style="max-width: 100%;">
  <br>
  <em>Figura 11 - Configuração de Segurança do Banco de Dados</em>
</p>

**Configurações Adicionais e Rótulos**

- **Fonte de Dados:** Possibilita criar um banco de dados vazio, restaurar a partir de um backup existente ou usar um banco de dados de exemplo.

- **Ordenação:** Define as regras para classificação e comparação de dados textuais.

- **Janela de Manutenção:** Permite agendar um período preferencial para que as atualizações do Azure ocorram, minimizando o impacto em sua aplicação.

<p align="center">
  <img src="/images/project03/Azure11.jpg" alt="Configuração de Segurança" style="max-width: 100%;">
  <br>
  <em>Figura 12 - Configurações adcionais do Banco de Dados</em>
</p>

- **Rótulos (Tags):** Tags (marcas) são pares de chave-valor que ajudam a organizar, gerenciar e monitorar seus recursos de forma eficaz, especialmente para o controle de custos.

<p align="center">
  <img src="/images/project03/Azure12.jpg" alt="Configuração de Segurança" style="max-width: 100%;">
  <br>
  <em>Figura 13 - Configuração de Marcas do Banco de Dados</em>
</p>


**Revisar e Criar**
Momento final e crucial antes da implantação do recurso.

*Essa etapa tem duas funções principais:*

1. **Validação Final:** O Azure executa uma verificação automatizada para garantir que todas as configurações definidas sejam válidas e compatíveis entre si. Ele verifica se o nome do banco de dados é único, se o servidor existe e se as configurações de rede e segurança estão corretas. Se houver algum erro, o sistema irá apontá-lo para que possa ser corrigido.

2. **Resumo Detalhado e Transparência:** É aqui que o Azure apresenta um resumo completo de todas as escolhas feitas, organizadas de forma clara para uma análise.

*Tela de "Revisar e criar":*

- **Detalhes do Produto:** Uma visão geral do que está sendo criado, como "Banco de Dados SQL", e uma estimativa de custo baseada nas configurações que você selecionou. Isso garante o entendimento do impacto financeiro baseado nas deciões escolhidas.

- **Termos de Uso:** É necessário aceitar os termos e condições do serviço. Esta é uma formalidade importante que garante a conformidade com as políticas da Microsoft.

- **Visão Geral das Configurações:** Esta seção é a mais importante. Ela lista todas as escolhas, como:

  - **Básico:** Assinatura, grupo de recursos, nome do banco de dados e servidor.

  - **Computação e Armazenamento:** O modelo de serviço (como Sem Servidor), a quantidade de vCores, o tamanho do armazenamento e a redundância de backup.

  - **Segurança:** Configurações como o status do Microsoft Defender, a forma de autenticação e a ativação da criptografia.

  - **Rede:** A política de conexão e a versão do TLS.

  - **Rótulos:** As tags para organizar o recurso.

  <p align="center">
  <img src="/images/project03/Azure13.jpg" alt="Configuração de Segurança" style="max-width: 100%;">
  <br>
  <em>Figura 14 - Revisão e Criação do Banco de Dados</em>
</p>

Depois de revisar e confirmar tudo, o botão "Criar" se tornará o passo final para iniciar o processo de implantação do Banco de Dados SQL no Azure.

<p align="center">
  <img src="/images/project03/Azure13_1.jpg" alt="Configuração de Segurança" style="max-width: 100%;">
  <br>
  <em>Figura 15 - Revisão e Criação do Banco de Dados</em>
</p>

---

## Material de pesquisa

- [Documentação oficial da Instância Gerenciada de SQL do Azure](https://learn.microsoft.com/pt-br/azure/azure-sql/managed-instance/instance-create-quickstart?view=azuresql&tabs=azure-portal)

---
