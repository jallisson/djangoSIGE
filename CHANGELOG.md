# Changelog

Todas as mudanças notáveis deste projeto serão documentadas neste arquivo.

O formato é baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/),
e este projeto adere ao [Versionamento Semântico](https://semver.org/lang/pt-BR/).

## [Unreleased]

### Changed

- Bumps de dependências:
  - `dj-database-url` 0.5.0 → 3.1.2 (cinco anos de bumps acumulados).
  - `python-decouple` 3.1 → 3.8 (API do `Csv()` mantida).
  - `flake8` (dev) 3.6.0 → 7.3.0, com `pyflakes`, `pycodestyle` e
    `mccabe` correspondentes.
  - `asgiref`, `sqlparse`, `pillow` já estavam no patch mais recente
    compatível.
- `requirements.txt` agora é gerado via `uv export` (sincronizado com
  `uv.lock`).
- `pyproject.toml` reorganizado em três grupos comentados: stack de
  NFe (pinada em versões antigas, ver nota abaixo), PDF (geraldo) e
  runtime geral. Pins explícitos de `future`, `six`, `eight` e `pytz`
  removidos — continuam disponíveis como deps transitivas.
- `mock` removido das deps de dev (não havia uso nos testes — Python 3
  traz `unittest.mock` builtin).
- Afrouxa constraints de `lxml` (`>=4.9,<5` — teto necessário porque
  `signxml==2.5.2` exige `lxml<5`) e `reportlab` (`>=4.5`). Sem efeito
  em versões instaladas no momento; permite patches futuros sem
  alteração no `pyproject`.
- `requirements.txt` passa a ser gerado via `uv export`, sincronizado
  com `uv.lock`.
- `djangosige.__init__.__version__` passa a `'2.0'` (estava em `'0.0.1'`,
  desalinhado com a release v2.0.0). Template `base.html` volta a usar
  `{{versao}}` (context processor `sige_version` já existente).

### Fixed

- Login retornava `403 Forbidden (Origin checking failed)` sob proxy
  reverso (`nginx` em `8000:80` → `gunicorn`). Adicionado
  `CSRF_TRUSTED_ORIGINS` no `settings.py` (lido via `decouple`) e a env
  `CSRF_TRUSTED_ORIGINS=http://localhost:8000,http://127.0.0.1:8000` no
  `docker-compose.yml`.
- Rodapé do template `base/base.html`: ano `2017` → `2026` e versão
  (antes era o placeholder `{{versao}}` que renderizava vazio por falta
  de context processor) → `2.0`.

### Changed

- README ganhou seção "Comandos úteis" do Docker com exemplos de
  `docker exec -it`, `docker compose logs -f --tail=N` e atalhos
  (`ps`, `restart`, `down`). Inclui nota explícita sobre a ordem das
  flags `-it` (precisam vir antes do nome do container).

### Added

- `database/` adicionado ao `.gitignore`. O volume do postgres é criado
  como `root` pelo container e fazia `git status` emitir `Permissão
  negada` em todo comando.

## [2.0.0] - 2026-05-23

Modernização da infraestrutura de build e execução. A aplicação em si não
mudou — esta release foca em como instalar, rodar e empacotar o projeto.

### Added

- `uv` como gerenciador de dependências (`pyproject.toml`, `uv.lock`,
  `.python-version`), mantendo `requirements.txt` em paralelo para quem
  preferir `pip`.
- `.dockerignore` excluindo `.git`, `.venv`, `database/` e configs locais
  de ferramentas (`.claude/`, `CLAUDE.md`, `AGENTS.md`, `GEMINI.md`).
- Rede dedicada `sige_net` no `docker-compose.yml`.
- Healthcheck do Postgres com `depends_on: service_healthy` no serviço
  `gunicorn`, garantindo ordem correta de inicialização.

### Changed

- Imagem Docker migrada de `alpine:3.7` para `python:3.10-slim`, com
  dependências instaladas via `uv` em `/opt/venv` (evita ser mascarado
  pelo bind mount do `docker-compose`).
- Postgres atualizado para `postgres:18-alpine`. Ponto de mount ajustado
  para `/var/lib/postgresql` (novo padrão com subdirs versionados).
- Serviço `gunicorn` agora usa `build: .` em vez de imagem pré-existente.
- `ALLOWED_HOSTS` expandido para incluir `nginx`, `localhost` e
  `127.0.0.1`.
- `nginx` exposto em `8000:80` (em vez de `80:80`) para evitar conflito
  com portas privilegiadas.
- `MAINTAINER` (deprecado) substituído por `LABEL maintainer`.
- `.gitignore` passa a ignorar configs locais de assistentes de código
  (`CLAUDE.md`, `.claude/`, `AGENTS.md`, `GEMINI.md`, `.cursor*`,
  `.aider*`, `docs/superpowers/`).

### Removed

- Atributos legados do `docker-compose.yml`: chave `version:` (obsoleta no
  Compose v2) e `links:` (substituídos por rede dedicada).

## [1.1.0] - 2026-05-22

Baseline pré-modernização. Esta entrada consolida o estado do djangoSIGE
acumulado em 164 commits anteriores até o ajuste de compatibilidade com
Django 5.x.

### Funcionalidades principais

- Cadastros: clientes, fornecedores, transportadoras, produtos e
  empresas.
- Autenticação: login/logout, perfil por usuário, controle de
  permissões.
- Orçamentos e pedidos de compra/venda com geração de PDF
  ([geraldo](https://github.com/thiagopena/geraldo)).
- Módulo financeiro: plano de contas, fluxo de caixa e lançamentos.
- Módulo de estoque.
- Módulo fiscal: emissão de NF-e/NFC-e versão 4.0, validação de XML,
  download, consulta, cancelamento e manifestação do destinatário;
  comunicação com a SEFAZ via [PySIGNFe](https://github.com/thiagopena/PySIGNFe).
- Interface em português.

### Stack

- Python 3.10+
- Django 5.2.14
- Suporte a SQLite (dev) e Postgres (produção).

[Unreleased]: https://github.com/thiagopena/djangoSIGE/compare/v2.0.0...HEAD
[2.0.0]: https://github.com/thiagopena/djangoSIGE/compare/v1.1.0...v2.0.0
[1.1.0]: https://github.com/thiagopena/djangoSIGE/releases/tag/v1.1.0
