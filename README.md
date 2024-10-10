# Chatbot TTS

Este projeto integra o **Amazon Lex V2** para chatbot com **AWS Polly**, permitindo a conversão de texto em fala. O deploy é realizado utilizando o **Serverless Framework v4**.

---

## Sumário
- [Chatbot TTS](#chatbot-tts)
  - [Sumário](#sumário)
  - [Desenvolvimento do projeto](#desenvolvimento-do-projeto)
    - [1. Dependências](#1-dependências)
    - [2. Instalar o AWS CLI v2](#2-instalar-o-aws-cli-v2)
    - [3. Configurar o AWS SSO](#3-configurar-o-aws-sso)
    - [4. Configuração do Serverless](#4-configuração-do-serverless)
  - [Como clonar o repositório e configurar o ambiente.](#como-clonar-o-repositório-e-configurar-o-ambiente)
  - [Documentar as rotas disponíveis, incluindo exemplos de requisições e respostas.](#documentar-as-rotas-disponíveis-incluindo-exemplos-de-requisições-e-respostas)
  - [Explicar a lógica por trás da rota /v1/tts, incluindo detalhes sobre o hash e a integração com o DynamoDB.](#explicar-a-lógica-por-trás-da-rota-v1tts-incluindo-detalhes-sobre-o-hash-e-a-integração-com-o-dynamodb)
  - [Como a API se integra com o AWS Polly e o S3.](#como-a-api-se-integra-com-o-aws-polly-e-o-s3)
  - [Processo de deploy da aplicação na AWS (comandos e resultados esperados).](#processo-de-deploy-da-aplicação-na-aws-comandos-e-resultados-esperados)
  - [Configuração do Chatbot no Amazon Lex](#configuração-do-chatbot-no-amazon-lex)
    - [Criação do Bot](#criação-do-bot)
    - [Configuração de Intents e Slots](#configuração-de-intents-e-slots)
    - [Conexão do Chatbot a Plataformas de Mensageria](#conexão-do-chatbot-a-plataformas-de-mensageria)
      - [Conexão com Slack](#conexão-com-slack)
    - [Conexão com Web](#conexão-com-web)
    - [Observações Finais](#observações-finais)
  - [Utilização do sistema](#utilização-do-sistema)
  - [Resultados](#resultados)
  - [Dificuldades conhecidas](#dificuldades-conhecidas)
  - [Autores](#autores)

---

## Desenvolvimento do projeto

### 1. Dependências
Siga os passos abaixo para instalar todas as dependências necessárias:

- **Instalar dependências do Python**:
    ```bash
    cd api-tts/
    pip install -r requirements.txt
    ```

- **Instalar dependências do Node.js**:
    ```bash
    npm install
    ```

- **Instalar o Serverless Framework**:
    ```bash
    npm install -g serverless
    ```

---

### 2. Instalar o AWS CLI v2
1. **Instale o AWS CLI v2**:
    - **Windows**:
        ```bash
        msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
        ```
    - **Linux**:
        ```bash
        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
        unzip awscliv2.zip
        sudo ./aws/install
        ```

2. **Verifique a versão do AWS CLI**:
    ```bash
    aws --version
    ```
    > Saída esperada: `aws-cli/2.x.x Python/3.x.x Linux/...`

### 3. Configurar o AWS SSO
1. **Configure o SSO** executando o comando:
    ```bash
    aws configure sso
    ```

2. **Preencha os campos solicitados**:
    ```bash
    SSO session name (Recommended): # Nome personalizado para a sessão
    SSO start URL [None]: # URL do SSO (ex: https://instituicao.awsapps.com/start/#)
    SSO region [None]: # Exemplo: us-east-1
    SSO registration scopes [sso:account:access]: # Apenas pressione Enter
    CLI default output format [ENTER]: # json
    CLI profile name [AdministratorAccess-ID_DO_USUARIO]: # Nome personalizado do perfil
    ```

3. **Faça login no SSO**:
    ```bash
    aws sso login --profile nome-do-perfil
    ```

---

### 4. Configuração do Serverless
1. **Inicie o Serverless Framework**:
    ```bash
    serverless
    ```

2. **Login ou Registro**:
    - Selecione a opção **Login/Register** e siga as instruções na página web que será aberta.

3. **Criação da aplicação**:
    - Após o login, escolha a opção **Create application** e forneça um nome para a sua aplicação.

4. **Implante a aplicação e aguarde até que o processo de deploy seja concluído**:
    ```bash
    serverless deploy
    ```

---
## Como clonar o repositório e configurar o ambiente.
## Documentar as rotas disponíveis, incluindo exemplos de requisições e respostas.
## Explicar a lógica por trás da rota /v1/tts, incluindo detalhes sobre o hash e a integração com o DynamoDB.
## Como a API se integra com o AWS Polly e o S3.
## Processo de deploy da aplicação na AWS (comandos e resultados esperados).


## Configuração do Chatbot no Amazon Lex

### Criação do Bot

1. **Acesse a Console do Amazon Lex**:
   - Vá para a console do Amazon Lex.

2. **Crie um Novo Bot**:
   - Clique em "Create bot".
   - Dê um nome ao seu bot e configure o idioma (por exemplo, "Portuguese (Brazil)").

### Configuração de Intents e Slots

1. **Adicionar Intents**:
   - Vá para a seção "Intents" e clique em "Create intent".
   - Dê um nome à intenção (por exemplo, "BookHotel").
   - Adicione exemplos de frases que o usuário pode dizer para acionar essa intenção (por exemplo, "Quero reservar um hotel").

2. **Configurar Slots**:
   - Dentro da intenção, adicione slots para capturar informações específicas do usuário.
   - Por exemplo, para a intenção "BookHotel", você pode adicionar slots como "Location" (localização), "CheckInDate" (data de check-in) e "Nights" (número de noites).
   - Configure prompts para solicitar essas informações ao usuário (por exemplo, "Para qual cidade você gostaria de reservar o hotel?").

3. **Adicionar Respostas**:
   - Configure as respostas que o bot dará ao usuário após capturar as informações necessárias.
   - Por exemplo, "Seu hotel em {Location} foi reservado para {Nights} noites a partir de {CheckInDate}."

### Conexão do Chatbot a Plataformas de Mensageria

#### Conexão com Slack

1. **Criar uma Aplicação no Slack**:
   - Acesse o Painel de Aplicações do Slack e clique em "Create New App".
   - Escolha "From scratch" e dê um nome à sua aplicação. Selecione o time onde a aplicação será usada.

2. **Configurar OAuth & Permissions**:
   - No painel da sua aplicação no Slack, vá para "OAuth & Permissions".
   - Em "Redirect URLs", adicione a URL de redirecionamento do Amazon Lex: `https://us-west-2.console.aws.amazon.com/lexv2/home?region=us-west-2#bots`.
   - Adicione os seguintes escopos de permissões:
     - `app_mentions:read`
     - `chat:write`
     - `im:history`
     - `im:read`
     - `im:write`
     - `users:read`

3. **Obter Tokens de Acesso**:
   - No mesmo painel "OAuth & Permissions", clique em "Install App to Workspace" e autorize a aplicação.
   - Copie o "Bot User OAuth Token" gerado.

4. **Configurar o Bot no Amazon Lex**:
   - Acesse a console do Amazon Lex.
   - Selecione o bot que deseja integrar com o Slack ou crie um novo bot.
   - Vá para a seção "Channels" e clique em "Slack".
   - Insira o "Bot User OAuth Token" que você copiou do Slack.
   - Configure as opções adicionais conforme necessário e clique em "Activate".

5. **Testar a Integração**:
   - No Slack, adicione o bot ao canal desejado.
   - Envie uma mensagem para o bot e verifique se ele responde conforme esperado.

### Conexão com Web

1. **Configurar a Interface Web**:
   - Utilize o AWS Lex Web UI para integrar o bot em uma interface web.
   - Siga as instruções no repositório para configurar e personalizar a interface.

2. **Deploy da Interface Web**:
   - Faça o deploy da interface web em um serviço de hospedagem, como AWS S3 ou AWS Amplify.
   - Teste a interface para garantir que o bot está funcionando corretamente.

### Observações Finais

- Certifique-se de testar todas as funcionalidades do bot e ajustar conforme necessário.
- Documente qualquer configuração adicional ou ajustes feitos durante o desenvolvimento.

---

## Utilização do sistema
## Resultados

## Dificuldades conhecidas

<ul>
  <li>Integrar a parte 1 do projeto com a 2</li>
  <li>Integração do Bot com o Slack</li>
  <li>Crianção do Bot</li>
</ul>

## Autores

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/hellenilda">
        <img src="https://avatars.githubusercontent.com/u/109177631?v=4" width="120" alt="Carlos Henrique Rodrigues" style="border-radius: 50%;">
      </a>
      <p><strong>Hellen Lima</strong></p>
      <a href="https://github.com/hellenilda">Perfil no GitHub</a>
    </td>
    <td align="center">
      <a href="https://github.com/Matheus-Dev-Souza">
        <img src="https://avatars.githubusercontent.com/u/96189442?v=4" width="120" alt="Matheus Souza" style="border-radius: 50%;">
      </a>
      <p><strong>Matheus Souza</strong></p>
      <a href="https://github.com/Matheus-Dev-Souza">Perfil no GitHub</a>
    </td>
    <td align="center">
      <a href="https://github.com/Leititcia">
        <img src="https://avatars.githubusercontent.com/u/130941056?v=4" width="120" alt="Renan Lucas" style="border-radius: 50%;">
      </a>
      <p><strong>Letícia Vale</strong></p>
      <a href="https://github.com/Leititcia">Perfil no GitHub</a>
    </td>
    <td align="center">
      <a href="https://github.com/vambertojunior">
        <img src="https://avatars.githubusercontent.com/u/40028930?v=4"width="120" alt="Francinildo Alves" style="border-radius: 50%;">
      </a>
      <p><strong>Vamberto Junior</strong></p>
      <a href="https://github.com/vambertojunior">Perfil no GitHub</a>
    </td>
  </tr>
</table>
