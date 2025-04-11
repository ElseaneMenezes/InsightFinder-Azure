🔍 Azure Cognitive Search: Utilizando AI Search para Indexação e Consulta de Dados
📌 Descrição
Este projeto demonstra como implementar uma solução de busca inteligente baseada em IA, utilizando o Azure Cognitive Search para realizar a indexação, enriquecimento e consulta de dados provenientes de uma base de documentos.

Com o uso de técnicas de análise semântica, filtros personalizados e estratégias de relevância, a aplicação permite buscas rápidas, contextuais e precisas — ideal para sistemas que lidam com grandes volumes de dados textuais.

🛠️ Tecnologias Utilizadas
Azure Cognitive Search – serviço de busca escalável com inteligência artificial incorporada
Azure Storage Account – utilizado como repositório dos documentos a serem indexados
Azure AI Services (opcional) – para aplicar enriquecimentos cognitivos como OCR, análise de sentimentos, extração de palavras-chave, etc.
Python com Flask – API que consome o serviço de busca e expõe endpoints para consulta
Postman – ferramenta para testar e validar as requisições HTTP

🎯 Objetivos do Projeto
Criar e configurar um serviço de pesquisa inteligente na nuvem com Azure
Indexar automaticamente documentos armazenados no Azure Storage
Desenvolver uma API RESTful que consome o Azure Cognitive Search e permite consultas customizadas
Aplicar filtros de refino, ordenação por relevância e lógica de busca semântica
Facilitar a integração com interfaces de busca ou outros sistemas

🏗️ Passo a Passo da Implementação
1️⃣ Criando o Serviço de Busca no Azure
Acesse o Azure Portal

Pesquise por Azure Cognitive Search na barra de serviços

Clique em Criar

Preencha os dados do serviço:

Grupo de Recursos: escolha um grupo existente ou crie um novo (ex: meu-grupo-recursos)

Nome do Serviço: escolha um nome único (ex: meu-search-service)

Região: selecione a mais próxima ou a de sua preferência

Plano de Preço: selecione o plano Free (F0) — ideal para testes e pequenos projetos

Após a criação, acesse o recurso e copie o endpoint e a admin key, pois serão usados para configurar o indexador e a API

2️⃣ Criando um Índice no Azure Cognitive Search
Após a criação do serviço, o próximo passo é configurar o índice de busca, que define como os dados serão estruturados e consultados.

🔧 Etapas para criar o índice:
Acesse o recurso Azure Cognitive Search pelo portal do Azure

No menu lateral, clique em Índices e depois em + Adicionar

Defina um nome para o seu índice, por exemplo: artigos-index

Em seguida, configure os campos que irão compor a estrutura do índice. Um exemplo de esquema para indexar artigos seria:

json
Copiar
Editar
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
🔹 Explicação dos campos:

id: identificador único do documento (chave primária do índice)

titulo: campo pesquisável contendo o título do artigo

conteudo: texto do artigo que será analisado semanticamente

autor: campo filtrável, para permitir buscas por autor específico

data_publicacao: campo ordenável, útil para buscas por data

Depois de criado o índice, ele estará pronto para receber documentos, via indexador ou API.

🌐 Criando uma API para Consultar o Índice
Agora vamos desenvolver uma API simples com Python e Flask para consultar os dados indexados no Azure Cognitive Search.

💻 Instalando as Dependências
Primeiro, instale as bibliotecas necessárias no seu ambiente Python:

bash
Copiar
Editar
pip install flask requests
📄 Criando o arquivo app.py
Crie um novo arquivo chamado app.py com o seguinte conteúdo:

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
📌 Importante: Substitua "SUA_CHAVE_AQUI" pelo valor real da sua chave de administração do serviço de busca.

🔎 Testando a API com o Postman
Com o servidor local rodando, você pode testar sua API da seguinte forma:

No terminal, inicie a aplicação com:

bash
Copiar
Editar
python app.py
Acesse o Postman e faça uma requisição GET para:

arduino
Copiar
Editar
http://127.0.0.1:5000/buscar?q=pesquisa
O parâmetro q representa o termo que será pesquisado no índice. A resposta será um JSON contendo os documentos que correspondem ao termo de busca.

💡 Exemplo de resultado esperado:

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
