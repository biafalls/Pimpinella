# 🍦 StockEasy

**Qual sorvete vender primeiro?** O StockEasy ajuda a equipe da **Sorveteria Pinpinela** a responder essa pergunta antes de abrir o freezer.

O sistema foi desenvolvido como projeto acadêmico de **Haskell** para organizar o estoque de picolés e potes de sorvete. Cada lote tem sua própria quantidade e data de validade, então dá para acompanhar entregas diferentes do mesmo sabor.

> 🚧 **Status do projeto:** Em desenvolvimento


<img align="left" width="230" alt="Ice Cream Cat GIF by UGCPazar" src="https://github.com/user-attachments/assets/ca6000b0-1af4-49a1-bf3b-b80d7ffce483" />


### &emsp;Gerenciamento Inteligente de Estoque

&emsp;Chega de dúvidas na hora de abrir o freezer! O StockEasy automatiza o controle<br>
&emsp;de lotes, datas de validade e prioridades de saída para a Sorveteria Pinpinela,<br>
&emsp;ajudando a organizar o estoque com precisão e eficiência.

<br clear="all">


## Objetivo

Ajudar a sorveteria a acompanhar o estoque e reduzir desperdícios. Se dois lotes de picolé de morango têm validades diferentes, a **Vitrine do Dia** destaca o que vence primeiro. Se um lote já passou da validade, ele aparece no filtro de **Vencidos**.

## O que tem no StockEasy?

- **Vitrine do Dia** - mostra o lote que deve sair primeiro e os próximos vencimentos.
- **Estoque** - reúne os lotes com suas quantidades e validades. Na demonstração, é possível registrar entradas e saídas, filtrar os vencidos e consultar produtos com saldo baixo.
- **Cardápio** - organiza os sabores e as linhas, como Fruta e Leite. No cadastro de cada lote, a equipe combina essas opções com a categoria Picolé ou Pote.

## 🛠️ Tecnologias

| Camada | Tecnologias |
| --- | --- |
| Interface | React, Vite e CSS |
| API | Haskell e Servant |
| Banco de dados | MySQL |

## 📁 Estrutura

```
├── backend/                 API em Haskell (Servant + Warp)
│   ├── app/
│   │   ├── Main.hs          inicia o servidor na porta 8080
│   │   ├── Models.hs        tipos (Cadastro, Lote, Saida, respostas)
│   │   ├── Queries.hs       consultas SQL (mysql-simple)
│   │   └── Handler.hs       rotas (type API) e handlers
│   ├── stockeasy.cabal
│   └── Dockerfile
├── database/
│   ├── stockeasy.sql        criação das tabelas
│   |__ massa_teste.sql      dados de teste
├── frontend/                React + Vite
│   └── src/
│       ├── main.jsx         aplicativo: estado, dados e navegação
│       ├── paginas/         Vitrine.jsx, Estoque.jsx, Cardapio.jsx
│       ├── Editor.jsx       janela de edição (formulários)
│       ├── componentes.jsx  menu, rodapé, títulos e etiquetas
│       ├── AbreVitrine.jsx  abertura da Vitrine (prato girando)
│       ├── organico.jsx     manchas, ondas e ilustrações (paradas)
│       ├── estoque.js       regras de data, situação e prioridade
│       └── api.js           chamadas à API Haskell
│       └── style.css
└── compose.yaml             sobe MySQL + API + front
```

## 🔌 Rotas

| Método | Rota | Corpo enviado | Resposta |
| --- | --- | --- | --- |
| GET | `/api/categorias` | – | `{ "cadastros": [ { "id": 1, "nome": "Picolé" } ] }` |
| GET | `/api/sabores` | – | `{ "cadastros": [...] }` |
| POST | `/api/sabores` | `{ "id": 0, "nome": "Pistache" }` | `{ "resultado": 8 }` (id criado) |
| PUT | `/api/sabores/:id` | `{ "id": 1, "nome": "Pistache" }` | vazio (200) |
| DELETE | `/api/sabores/:id` | – | vazio (200) |
| GET / POST | `/api/linhas` | igual a sabores | igual a sabores |
| PUT / DELETE | `/api/linhas/:id` | igual a sabores | vazio (200) |
| GET | `/api/lotes` | – | `{ "lotes": [...] }` |
| GET | `/api/lotes/:id` | – | o lote, ou 404 |
| POST | `/api/lotes` | `{ "id": 0, "sabor_id": 1, "categoria_id": 1, "linha_id": 1, "quantidade": 10, "validade": "2026-12-01" }` | `{ "resultado": 13 }` |
| PUT | `/api/lotes/:id` | mesmo corpo do POST | vazio (200) |
| DELETE | `/api/lotes/:id` | – | vazio (200) |
| PATCH | `/api/lotes/:id/saida` | `{ "saida": 3 }` | vazio (200) |

Erros voltam como `{ "erro": "mensagem" }` com 400, 404 ou 409.

## ▶️ Como rodar

Precisa só do Docker (o Codespace já vem com ele; no PC, instale o Docker Desktop).
Na raiz do projeto:

```bash
docker compose up --build
```

Isso sobe o MySQL (com a massa de teste), a API Haskell e o front, todos no horário de Brasília (UTC-3).
A primeira vez demora (10–20 min), porque compila as bibliotecas do Haskell; depois fica em cache.

- Front: porta **5173** (no Codespace, aba **PORTS**)
- API: `http://localhost:8080/api/lotes`

| Para... | Comando |
| --- | --- |
| Parar | `Ctrl+C` |
| Recompilar depois de mexer no Haskell | `docker compose up --build` |
| Apagar o banco e recarregar a massa de teste | `docker compose down` e `rm -rf .mysql-data` |
| Entrar no MySQL | `docker compose exec mysql mysql -ustockeasy -pstockeasy pimpinella` |
