#  Bot de WhatsApp - Gestão de Aluguel de Roupas

Este projeto contém a configuração de um fluxo automatizado (workflow) criado no **n8n** para gerenciar o atendimento via WhatsApp de uma loja de aluguel de vestidos e trajes juninos. O bot integra o WhatsApp com o Google Sheets para facilitar reservas, envio de contratos e lembretes de devolução.

##  Funcionalidades Principais

* ** Autoatendimento e Boas-vindas:** * Escuta mensagens recebidas (via Webhook) e responde automaticamente com o link do catálogo, horário de funcionamento e endereço da loja.
* Adiciona o contato a uma planilha de controle e pausa o bot (`Status_bot = suspenso`) para que um atendente humano assuma a conversa.


* **✅ Confirmação de Aluguel Automatizada:**
* Monitora novas linhas adicionadas à planilha de aluguéis (Google Sheets Trigger).
* Dispara uma mensagem de confirmação contendo: número do vestido, data da festa, data de devolução e CPF do cliente.
* Envia automaticamente o **contrato de locação em PDF** com os termos e regras.


* **⏰ Lembretes de Devolução (Cronograma Diário):**
* Executa todos os dias às 06h00 da manhã.
* Verifica na base de dados quais clientes possuem a data de devolução marcada para o dia atual ("hoje").
* Envia uma mensagem amigável lembrando sobre a devolução, informando os horários da loja e alertando sobre taxas de atraso.


* **⚙️ Disparo Manual em Massa (Contingência):**
* Fluxo manual para cadastrar e disparar confirmações e contratos para uma lista específica de clientes via código JavaScript.



## 🛠️ Tecnologias e Ferramentas Utilizadas

* **[n8n](https://n8n.io/):** Plataforma principal de automação de fluxo de trabalho.
* **[Z-API](https://z-api.io/):** API utilizada para envio e recebimento de mensagens, links e arquivos no WhatsApp.
* **Google Sheets API:** Banco de dados do projeto, utilizado para ler cadastros e atualizar status de clientes.
* **JavaScript:** Utilizado no nó *Code* para tratamento avançado de datas e manipulação de arrays.

## 📦 Como Instalar e Configurar

### 1. Requisitos Prévios

* Uma instância do **n8n** rodando (versão Cloud, Desktop ou Self-hosted).
* Uma conta ativa em uma API de WhatsApp (como a **Z-API**) com sua instância conectada.
* Uma **Conta de Serviço do Google (Service Account)** com credenciais geradas para acesso ao Google Sheets.

### 2. Importando o Workflow

1. Abra o seu n8n.
2. Na aba lateral, clique em **Workflows** e depois em **Add Workflow**.
3. No canto superior direito, clique na engrenagem de opções (ou nos três pontos) e selecione **Import from File**.
4. Selecione o arquivo `.json` do projeto.
5. Salve o fluxo.

### ⚠️ 3. Substituição de Variáveis Obrigatórias (Atenção aos campos `..`)

Ao importar o arquivo JSON, você notará que as configurações dos nós possuem valores entre pontos, como `..Seu Client Token..` ou `..Sua Pasta do Google Sheets..`. Estes são marcadores de espaço (placeholders).

Para que o bot funcione no seu ambiente, você **deve acessar as configurações dos nós correspondentes e substituir essas marcações pelos seus próprios links e credenciais**:

* **`..Sua Pasta do Google Sheets..` / `..Link da pasta do Google Sheets..**`:
* **Onde encontrar:** Nos nós do tipo *Google Sheets* e *Google Sheets Trigger*.
* **O que fazer:** Você precisa selecionar a sua própria credencial do Google, procurar a planilha correta no campo `Document` e escolher a aba correspondente no campo `Sheet`. Esses links falsos indicam apenas onde a sua planilha deve ser conectada.


* **`..Seu Link da API do Whatsapp..` / `..Seu link de API..**`:
* **Onde encontrar:** Nos nós do tipo *HTTP Request* que realizam o envio de mensagens.
* **O que fazer:** Substitua pela URL de disparo da sua API. Se estiver usando a Z-API, será algo parecido com `https://api.z-api.io/instances/SUA_INSTANCIA/token/SEU_TOKEN/send-text`.


* **`..Seu Client Token..`**:
* **Onde encontrar:** Na seção *Headers* dentro dos nós *HTTP Request*.
* **O que fazer:** O *Client Token* (ou Token de Autenticação/Bearer) é a sua chave de segurança da API do WhatsApp. Substitua o texto entre `..` pelo token real gerado no painel da sua API.



### 4. Configurando as Credenciais do n8n

* **Nós do Google Sheets:**
* Crie e selecione a credencial `Google Service Account account` ou `Google Sheets Trigger OAuth2 API` dependendo do nó.


* **Nós de Webhook:**
* O nó "Webhook1" possui uma URL de Teste e uma URL de Produção. Copie a URL gerada pelo seu n8n e cole no painel da sua API de WhatsApp (ex: Z-API) para que ela saiba para onde enviar as mensagens recebidas.



## 🗂️ Estrutura da Planilha (Google Sheets)

Para que o bot funcione corretamente, certifique-se de que sua planilha tenha (no mínimo) as seguintes colunas exatas referenciadas no fluxo:

* `Nome Completo`
* `Telefone Whatsapp` (Formato sugerido: DDI + DDD + Número)
* `CPF`
* `Número do vestido`
* `Data da festa` (Formato: DD/MM/AAAA)
* `Data de devolução` (Formato: DD/MM/AAAA)
* `Status_bot`

---

*Nota: Este workflow foi construído prevendo regras específicas (horários, multas de 20%, e prazos contratuais). Revise os textos dos nós `HTTP Request` e `Append or update row in sheet` caso precise adaptar para a realidade de outro negócio.*****
