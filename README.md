# DjangoSIGE [![Build Status](https://travis-ci.org/thiagopena/djangoSIGE.svg?branch=master)](https://travis-ci.org/thiagopena/djangoSIGE)

Sistema Integrado de Gestão Empresarial baseado em Django

Projeto independente open-source desenvolvido em Python 3 no Windows, testado no GNU/Linux e Windows.

## Dependências

- [Python](https://www.python.org/downloads/) — 3.12 (definido em `.python-version` e `pyproject.toml`)
- [Django](http://www.djangoproject.com) — 5.2.x (linha LTS, `>=5.2,<5.3`)
- [PostgreSQL](https://www.postgresql.org/) — 18 (via Docker) ou compatível
- [uv](https://docs.astral.sh/uv/) (recomendado) — gerencia o ambiente e as dependências a partir de `pyproject.toml` / `uv.lock`
- [geraldo](https://github.com/thiagopena/geraldo) — geração de PDF (biblioteca abandonada, será substituída; ver issue [#142](https://github.com/thiagopena/djangoSIGE/issues/142))
- [PySIGNFe](https://github.com/thiagopena/PySIGNFe) (opcional) — geração de NF-e/NFC-e, comunicação com a SEFAZ, DANFE. Mantém pinadas as versões antigas de `cryptography==2.9.2`, `pyOpenSSL==17.5.0` e `signxml==2.5.2`, sem as quais a emissão quebra.
- [apache2](https://www.apache.org/) + [mod_wsgi](https://modwsgi.readthedocs.io/en/develop/) (opcional, alternativo ao Docker)

## Instalação

0. Instalar as bibliotecas/pacotes do sistema (no Linux):

```bash
sudo apt install -y libxml2 gcc python3-dev libxml2-dev libxslt1-dev zlib1g-dev git
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install -y python3.12 python3.12-venv python3.12-dev
```

1. Clone o repositório:

```bash
git clone https://github.com/thiagopena/djangoSIGE.git
cd djangoSIGE
```

### Opção A — uv (recomendado)

[uv](https://docs.astral.sh/uv/) cria o ambiente virtual e instala as
dependências a partir do `pyproject.toml`/`uv.lock` em um único passo,
fixando a versão do Python definida em `.python-version`.

```bash
# Instale o uv (ver https://docs.astral.sh/uv/getting-started/installation/)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Sincronize as dependencias (cria .venv automaticamente)
uv sync
```

A partir daqui, prefixe os comandos `manage.py` com `uv run`:

```bash
uv run python contrib/env_gen.py
uv run python manage.py migrate
uv run python manage.py createsuperuser
uv run python manage.py runserver
```

### Opção B — pip + venv (alternativa)

```bash
python3.12 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Em seguida, com o `venv` ativado:

```bash
python contrib/env_gen.py
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

### Pós-instalação (ambas as opções)

2. Edite o conteúdo do arquivo **djangosige/configs/configs.py**.

3. Acesse `http://localhost:8000` no navegador.

### Docker (opcional)

Há também um `docker-compose.yml` com Postgres 18, Gunicorn e Nginx:

```bash
docker compose up -d
docker compose exec gunicorn python manage.py migrate
docker compose exec gunicorn python manage.py createsuperuser
```

A aplicação fica disponível em `http://localhost:8000`.

#### Comandos úteis

Abrir um shell interativo dentro do container da aplicação (atenção: as
flags `-it` precisam vir **antes** do nome do container):

```bash
docker exec -it djangosige-gunicorn-1 bash
# equivalente via compose:
docker compose exec gunicorn bash
```

Acompanhar os logs em tempo real (`-f` = follow, `--tail=N` limita o
backlog inicial):

```bash
# todos os servicos
docker compose logs -f

# apenas o gunicorn (com as ultimas 100 linhas)
docker compose logs -f --tail=100 gunicorn

# equivalente sem compose
docker logs -f djangosige-gunicorn-1
```

Outros atalhos úteis:

```bash
docker compose ps              # status dos containers
docker compose restart gunicorn
docker compose down            # derruba o stack (preserva volumes)
```

## Implementações

- Cadastro de produtos, clientes, empresas, fornecedores e transportadoras
- Login/Logout
- Criação de perfil para cada usuário.
- Definição de permissões para usuários.
- Criação e geração de PDF para orçamentos e pedidos de compra/venda
- Módulo financeiro (Plano de Contas, Fluxo de Caixa e Lançamentos)
- Módulo para controle de estoque
- Módulo fiscal:
    - Geração e armazenamento de notas fiscais
    - Validação do XML de NF-e/NFC-es
    - Emissão, download, consulta e cancelamento de NF-e/NFC-es **(Testar em ambiente de homologação)**
    - Comunicação com SEFAZ (Consulta de cadastro, inutilização de notas, manifestação do destinatário)
- Interface simples e em português

## Créditos

- [AdminBSBMaterialDesign](https://github.com/gurayyarar/AdminBSBMaterialDesign)
- [geraldo](https://github.com/marinho/geraldo)
- [jQuery-Mask-Plugin](https://igorescobar.github.io/jQuery-Mask-Plugin/)
- [DataTables](https://datatables.net/)
- [JQuery multiselect](http://loudev.com/)

## Ajuda

Para relatar bugs ou fazer perguntas utilize o [Issues](https://github.com/thiagopena/djangoSIGE/issues) ou via email thiagopena01@gmail.com

Como este é um projeto em desenvolvimento, qualquer feedback será bem-vindo.