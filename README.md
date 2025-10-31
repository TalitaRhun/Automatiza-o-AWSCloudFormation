# Automatiza-o-AWSCloudFormation
AWS CloudFormation, ajuda a automatizar o processo de criação, configuração e gerenciamento  de recursos da AWS.        Este guia ajudará a criar uma infraestrutura básica como uma VPC e uma Instância EC2.

🛠️ Roteiro Passo a Passo para Implementação de Infraestrutura com AWS CloudFormation

* Passo 1: É preparar o ambiente local

- Instalar e Configurar a AWS CLI:
Garanta que a AWS Command Line Interface esteja instalada e configurada com credenciais que tenham permissões para criar recursos (especialmente CloudFormation, EC2 e VPC).

- Anotação: Registre os comandos usados para configurar o seu perfil AWS (aws configure).

* Passo 2: Criando o Template de Rede (VPC)
Vamos começar pela base: a rede virtual (VPC).

1- Crie o arquivo do Template:

Crie um arquivo chamado templates/vpc.yaml.

2- Estrutura Básica:

Adicione as seções AWSTemplateFormatVersion, Description e Resources.

3- Defina os Recursos:

Declare um recurso do tipo AWS::EC2::VPC.

Declare um AWS::EC2::Subnet (pelo menos um).

Declare um AWS::EC2::InternetGateway e anexe-o à VPC (AWS::EC2::VPCGatewayAttachment).

* Anotação: Use o campo Tags em todos os recursos para facilitar a identificação.

4- Adicione Saídas (Outputs):

Exporte o ID da VPC e o ID da Subnet usando a seção Outputs e a função !Ref.

Insight: Anote como a função !Ref é usada para obter o ID do recurso recém-criado.

* Passo 3: Criando o Template de Aplicação (EC2)
  Como provisionar uma instância EC2 dentro da VPC que você acabou de definir.

1- Crie o arquivo do Template:

Crie um arquivo chamado templates/webserver.yaml.  

2- Defina Parâmetros:

-Adicione a seção Parameters.
-Crie um parâmetro chamado VpcId e um chamado SubnetId (com Type: String).
-Crie um parâmetro para o tipo de instância (InstanceType).
Insight: Anote a importância dos Parameters para tornar o seu template reutilizável em diferentes ambientes.

3- Defina os Recursos:
-Declare um AWS::EC2::SecurityGroup para permitir tráfego SSH (porta 22) e HTTP (porta 80).
-Declare um AWS::EC2::Instance.
-Use o Parâmetro InstanceType.
-Use !Ref SubnetId e !Ref SecurityGroup para vinculá-lo à sua rede.
-Use UserData para instalar um servidor web básico (ex: Apache) ao inicializar.
-Anotação: Registre a sintaxe de uso do Fn::Sub para injetar variáveis nos scripts do UserData.

* Passo 4: Implantação da Infraestrutura (Criação da Stack)
  - Com os templates prontos, posso provisionar os recursos usando a AWS CLI ou o Console.

1. Validação:
-Sempre valide o seu template antes de criar a Stack: aws cloudformation validate-template --template-body file://templates/vpc.yaml

Anotação: O que a validação verifica? (Apenas sintaxe, não lógica ou limites de conta).

2. Criação da Stack de Rede:
-Use o comando aws cloudformation create-stack com o arquivo vpc.yaml.

3. Criação da Stack de Aplicação:
-Você precisará fornecer os valores de VpcId e SubnetId gerados pela primeira Stack.

-Use aws cloudformation describe-stacks para buscar os Outputs da Stack VPC.

-Use o comando aws cloudformation create-stack com o arquivo webserver.yaml, passando os parâmetros: --parameters ParameterKey=VpcId,ParameterValue=vpc-xxxxxxxx --------

-ParameterKey=SubnetId,ParameterValue=subnet-xxxxxxxx

*Insight: Como você poderia automatizar a passagem dos Outputs da primeira Stack para a segunda Stack? ( Fn::ImportValue para templates não aninhados, ou Nested Stacks).
