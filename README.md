# Azure Cognitive Search: Utilizando AI Search para Indexação e Consulta de Dados

Este projeto demonstra como implementar uma solução de **busca inteligente baseada em IA**, utilizando o **Azure Cognitive Search** para realizar a **indexação, enriquecimento e consulta de dados** provenientes de uma base de documentos.

---

## 🛠️ Tecnologias Utilizadas

- **Azure Cognitive Search** – Serviço de busca escalável com inteligência artificial incorporada
- **Azure Storage Account** – Armazenamento de documentos a serem indexados
- **Azure AI Services (opcional)** – Enriquecimento cognitivo para extração de dados semânticos
- **Python com Flask** – API que consome o serviço de busca e expõe endpoints para consulta
- **Postman** – Teste das requisições HTTP para validar a API

---

## 🎯 Objetivos do Projeto

- Criar e configurar um serviço de **pesquisa inteligente na nuvem com Azure**
- **Indexar automaticamente** documentos armazenados no Azure Storage
- Desenvolver uma **API RESTful** que consome o Azure Cognitive Search e permite consultas customizadas
- Aplicar **filtros de refino**, **ordenação por relevância** e lógica de busca semântica
- Facilitar a integração com interfaces de busca ou outros sistemas

---

## 🏗️ Passo a Passo da Implementação

### 1️⃣ Criando o Serviço no Azure

1. Acesse o [Azure Portal](https://portal.azure.com/)
2. Busque por **Azure Cognitive Search** e clique em **Criar**
3. Preencha os dados do serviço:
   - **Grupo de Recursos**: Escolha um grupo existente ou crie um novo (ex: `meu-grupo-recursos`)
   - **Nome do Serviço**: Escolha um nome único (ex: `meu-search-service`)
   - **Região**: Selecione a mais próxima ou a de sua preferência
   - **Plano de Preço**: Selecione o plano **Free (F0)** — ideal para testes e pequenos projetos
4. Após a criação, acesse o recurso e **copie o `endpoint` e a `admin key`**, pois serão usados na configuração da API

---

### 2️⃣ Criando um Índice no Azure Cognitive Search

Após a criação do serviço, o próximo passo é configurar o **índice de busca**, que define como os dados serão estruturados e consultados.

1. No portal do Azure, acesse o serviço **Azure Cognitive Search**
2. Vá até **Índices > + Adicionar**
3. Configure o índice com os campos necessários:
   - `id`, `titulo`, `conteudo`, `autor`, `data_publicacao`
4. Defina o `id` como **chave primária**
5. Marque quais campos serão:
   - **searchable**: para permitir que os dados sejam pesquisados
   - **filterable**: para aplicar filtros nas buscas
   - **sortable**: para ordenação, como no caso da `data_publicacao`
6. Salve o índice

#### Exemplo de estrutura do índice (JSON):

```json
{
  "name": "artigos-index",
  "fields": [
    { "name": "id", "type": "Edm.String", "key": true },
    { "name": "titulo", "type": "Edm.String", "searchable": true },
    { "name": "conteudo", "type": "Edm.String", "searchable": true },
    { "name": "autor", "type": "Edm.String", "filterable": true },
    { "name": "data_publicacao", "type": "Edm.DateTimeOffset", "sortable": true }
  ]
}
```3️⃣ Criando a API para Consulta com Flask
Agora, vamos criar uma API para consultar o índice configurado no Azure Cognitive Search.

3.1 Instalação das Dependências
Primeiro, instale as dependências no seu ambiente Python:

bash
Copiar
Editar
pip install flask requests
3.2 Criando o arquivo app.py
Crie um arquivo chamado app.py e adicione o seguinte código:

python
Copiar
Editar
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

# Configurações do Azure Search
AZURE_SEARCH_ENDPOINT = "https://meu-search-service.search.windows.net"
AZURE_SEARCH_API_KEY = "SUA_CHAVE_AQUI"
AZURE_SEARCH_INDEX = "artigos-index"

@app.route("/buscar", methods=["GET"])
def buscar():
    termo = request.args.get("q")  # Recebe o termo de busca pela URL
    headers = {
        "Content-Type": "application/json",
        "api-key": AZURE_SEARCH_API_KEY
    }
    query = {
        "search": termo
    }

    response = requests.post(
        f"{AZURE_SEARCH_ENDPOINT}/indexes/{AZURE_SEARCH_INDEX}/docs/search?api-version=2021-04-30-Preview",
        json=query,
        headers=headers
    )

    return jsonify(response.json())

if __name__ == "__main__":
    app.run(debug=True)
🔑 Importante: Substitua "SUA_CHAVE_AQUI" pela chave de administrador do seu serviço Azure.

3.3 Testando a API com Postman
Inicie o servidor localmente com o comando:

bash
Copiar
Editar
python app.py
No Postman, faça uma requisição GET para:

arduino
Copiar
Editar
http://127.0.0.1:5000/buscar?q=pesquisa
Você deverá receber os resultados da busca em formato JSON, como no exemplo abaixo:

json
Copiar
Editar
{
  "value": [
    {
      "@search.score": 1.23,
      "id": "001",
      "titulo": "Artigo sobre Azure",
      "conteudo": "Este é um conteúdo explicando como funciona o Azure Cognitive Search.",
      "autor": "João Silva",
      "data_publicacao": "2023-07-10T00:00:00Z"
    }
  ]
}
📅 Testando e Validando
Para testar a API localmente e validar o comportamento de pesquisa, use o Postman ou outra ferramenta de sua escolha para enviar consultas para o endpoint da API.

🌟 Conclusão
Este projeto implementa um serviço de busca inteligente utilizando Azure Cognitive Search com funcionalidades de enriquecimento de dados e análise semântica. A API criada permite consultas personalizadas com filtros e relevância, tornando a experiência de busca mais eficiente e eficaz.

📝 Referências
Documentação do Azure Cognitive Search

Flask Documentation

markdown
Copiar
Editar

---

### Explicações das separações:

1. **Títulos e Subtítulos**: Você pode usar títulos (`#` para títulos principais e `###` para subtítulos) para separar claramente cada seção do projeto.
2. **Linhas Horizontais**: Use `---` ou `***` para criar uma linha horizontal que ajuda a separar visualmente diferentes seções no seu `README.md`.
3. **Espaços entre os blocos de código e o texto**: Entre um bloco de código e o texto, insira uma linha em branco para garantir que o código e o texto fiquem bem separados.
4. **Blocos de Código**: Ao utilizar os três crases (\`\`\`) antes e depois de um código, você cria um bloco de código visualmente separado. Para cada tipo de código, como JSON, Python ou Bash, você deve abrir e fechar com os três crases, cada um com a linguagem adequada, para destacá-lo corretamente.

Essa estrutura garante que seu `README.md` ficará bem organizado e legível no GitHub ou em qualquer outro repositório de código.

Se precisar de mais alguma coisa, só avisar! 😊
