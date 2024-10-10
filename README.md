
# Chatbot TTS

Este projeto integra o **Amazon Lex V2** para chatbot com **AWS Polly**, permitindo a conversão de texto em fala. O deploy é realizado utilizando o **Serverless Framework v4**.

---

## Sumário
1. [Como clonar o repositório e configurar o ambiente](#como-clonar-o-repositório-e-configurar-o-ambiente)
2. [Como instalar o Serverless Framework e configurar as credenciais da AWS](#como-instalar-o-serverless-framework-e-configurar-as-credenciais-da-aws)
3. [Documentar as rotas disponíveis, incluindo exemplos de requisições e respostas](#documentar-as-rotas-disponíveis-incluindo-exemplos-de-requisições-e-respostas)
4. [Explicar a lógica por trás da rota /v1/tts, incluindo detalhes sobre o hash e a integração com o DynamoDB](#explicar-a-lógica-por-trás-da-rota-v1tts-incluindo-detalhes-sobre-o-hash-e-a-integração-com-o-dynamodb)
5. [Como a API se integra com o AWS Polly e o S3](#como-a-api-se-integra-com-o-aws-polly-e-o-s3)
6. [Processo de deploy da aplicação na AWS (comandos e resultados esperados)](#processo-de-deploy-da-aplicação-na-aws-comandos-e-resultados-esperados)
7. [Configuração do chatbot no Amazon Lex, incluindo intents e slots](#configuração-do-chatbot-no-amazon-lex-incluindo-intents-e-slots)
8. [Conexão do chatbot a plataformas de mensageria](#conexão-do-chatbot-a-plataformas-de-mensageria)
9. [Dificuldades conhecidas](#dificuldades-conhecidas)
10. [Autores](#autores)

---

## 1. Como clonar o repositório e configurar o ambiente

Siga os passos abaixo para clonar o repositório e configurar o ambiente:

1. **Clone o repositório**:
   ```bash
   git clone <URL_DO_REPOSITÓRIO>
   cd <NOME_DO_REPOSITÓRIO>
   ```

2. **Crie e ative um ambiente virtual** (opcional, mas recomendado):
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/Mac
   .\venv\Scripts\activate  # Windows
   ```

3. **Instale as dependências do projeto**:
   ```bash
   pip install -r requirements.txt
   ```

---

## 2. Como instalar o Serverless Framework e configurar as credenciais da AWS

1. **Instale o Serverless Framework**:
   ```bash
   npm install -g serverless
   ```

2. **Instale o AWS CLI v2**:
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

3. **Configure as credenciais da AWS**:
   ```bash
   aws configure
   ```

   Preencha os campos solicitados, como `AWS Access Key`, `AWS Secret Access Key`, `Default region name` e `Default output format`.

---

## 3. Documentar as rotas disponíveis, incluindo exemplos de requisições e respostas

As rotas disponíveis na API são:

### **GET /v1/tts**

**Descrição**: Converte texto em fala.

- **Exemplo de requisição**:
  ```bash
  curl -X GET https://<API_URL>/v1/tts?text=Olá
  ```

- **Exemplo de resposta**:
  ```json
  {
    "audio_url": "https://s3.amazonaws.com/<bucket>/<audio_file>.mp3"
  }
  ```

---

## 4. Explicar a lógica por trás da rota /v1/tts, incluindo detalhes sobre o hash e a integração com o DynamoDB

- **Lógica**: A rota `/v1/tts` recebe um texto como parâmetro e verifica se já foi convertido anteriormente.
- **Hash**: Um hash é gerado a partir do texto para garantir a unicidade e verificar se o áudio já existe.
- **Integração com o DynamoDB**: Os metadados do áudio gerado são armazenados no DynamoDB para futuras referências.

---

## 5. Como a API se integra com o AWS Polly e o S3

1. **Integração com AWS Polly**: 
   - A API chama o AWS Polly para gerar o áudio a partir do texto fornecido.
   - O formato do áudio, a voz e outros parâmetros são configurados na chamada.

2. **Integração com S3**:
   - O áudio gerado é armazenado no S3 para acesso futuro.
   - A URL do arquivo armazenado é retornada como resposta da API.

---

## 6. Processo de deploy da aplicação na AWS (comandos e resultados esperados)

1. **Realize o deploy usando o Serverless**:
   ```bash
   serverless deploy
   ```

2. **Resultados esperados**:
   - A URL da API será exibida após o deploy, indicando que a aplicação foi implantada com sucesso.

---

## 7. Configuração do chatbot no Amazon Lex, incluindo intents e slots

### Etapa 1: Criar um bot do Amazon Lex

Se você ainda não tem um bot no **Amazon Lex**, siga os passos abaixo para criar e implantar um. Presumimos que você esteja usando o bot criado no **Exercício 1** dos Conceitos Básicos. No entanto, é possível usar qualquer bot de exemplo fornecido. 

1. **Crie um bot** no Amazon Lex:
   - Consulte o guia [Exercício 1: Criar um bot do Amazon Lex usando um esquema (Console)](https://docs.aws.amazon.com/lex/latest/dg/exercise1.html).
   
2. **Implemente o bot** e crie um alias:
   - Consulte [Exercício 3: Publique uma versão e crie um alias](https://docs.aws.amazon.com/lex/latest/dg/exercise3.html).
   
---

### Etapa 2: Cadastrar-se no Slack e criar uma equipe

1. **Cadastre-se no Slack** e crie uma equipe:
   - Acesse o [Slack](https://slack.com/) e siga as instruções para criar uma equipe e registrar-se em uma conta.
   
---

### Etapa 3: Criar uma aplicação do Slack

1. **Crie uma aplicação no Slack**:
   - Faça login no [console da API do Slack](http://api.slack.com).
   - Crie uma nova aplicação e configure-a com **mensagens interativas**. 

2. **Configurações básicas do aplicativo**:
   - No menu à esquerda, escolha **Interatividade e atalhos**.
   - Ative os componentes interativos e insira uma URL válida como `https://slack.com`. Esse valor será atualizado posteriormente.
   - Salve as alterações.

3. **Registre as credenciais do aplicativo**:
   - No menu **Informações básicas**, anote o **ID do cliente**, o **segredo do cliente** e o **token de verificação**. Esses valores serão usados posteriormente na integração com o Amazon Lex.

---

### Etapa 4: Integrar o aplicativo do Slack com o bot do Amazon Lex

1. **Ative a associação de canal no Amazon Lex**:
   - Faça login no [Console de Gerenciamento da AWS](https://console.aws.amazon.com/lex/).
   - Selecione o bot do Amazon Lex que você criou na Etapa 1.
   - Na aba **Canais**, escolha **Slack**.
   - Insira as credenciais do aplicativo (ID do cliente, segredo do cliente e token de verificação) obtidas na Etapa 3.
   
2. **URLs de integração**:
   - O Amazon Lex retornará duas URLs: **Postback URL** e **OAuth URL**. Anote esses valores, pois eles serão utilizados para atualizar a configuração do Slack.

---

### Etapa 5: Completar a integração do Slack

1. **Atualize a configuração no Slack**:
   - No [console da API do Slack](http://api.slack.com), selecione o aplicativo criado.
   - No menu **OAuth e permissões**, adicione a **OAuth URL** fornecida pelo Amazon Lex.
   - Na seção **Escopos do token do bot**, adicione os escopos `chat:write` e `team:read`.
   
2. **Atualize o URL de interatividade**:
   - No menu **Interatividade e atalhos**, insira a **Postback URL** fornecida pelo Amazon Lex e salve as alterações.
   
3. **Assine os eventos necessários**:
   - No menu **Assinaturas de eventos**, inscreva-se no evento `message.im` para habilitar mensagens diretas entre o usuário e o bot.

---

### Etapa 6: Testar a integração

1. **Instalar o aplicativo no Slack**:
   - No console do Slack, vá para **Manage Distribution** e clique em **Add to Slack** para instalar o aplicativo.
   - Autorize o bot a responder a mensagens na equipe do Slack.
   
2. **Iniciar o bate-papo com o bot**:
   - No Slack, encontre o bot na seção **Mensagens diretas** e inicie uma conversa para testar a integração.

---

## 8. Conexão do chatbot a plataformas de mensageria

1. **Conexão com Slack**:
   - Crie um aplicativo no Slack e configure as permissões.
   - Obtenha o token de acesso e integre-o ao bot no Amazon Lex.

2. **Conexão com Web**:
   - Utilize a interface do AWS Lex Web UI para conectar o bot em uma página web.

---

## 9. Dificuldades conhecidas

- Integração da parte 1 do projeto com a parte 2.
- Integração do Bot com o Slack.
- Criação do Bot.

---

## 10. Autores

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/hellenilda">
        <img src="https://avatars.githubusercontent.com/u/109177631?v=4" width="120" alt="Hellen Lima" style="border-radius: 50%;">
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
        <img src="https://avatars.githubusercontent.com/u/130941056?v=4" width="120" alt="Letícia Vale" style="border-radius: 50%;">
      </a>
      <p><strong>Letícia Vale</strong></p>
      <a href="https://github.com/Leititcia">Perfil no GitHub</a>
    </td>
    <td align="center">
      <a href="https://github.com/vambertojunior">
        <img src="https://avatars.githubusercontent.com/u/40028930?v=4" width="120" alt="Vamberto Junior" style="border-radius: 50%;">
      </a>
      <p><strong>Vamberto Junior</strong></p>
      <a href="https://github.com/vambertojunior">Perfil no GitHub</a>
    </td>
  </tr>
</table>


