# Documento de Modelo de Dados Local

## 1. Objetivo

Definir o modelo de dados local do aplicativo de biblioteca de jogos. O app deve armazenar biblioteca, candidatos de varredura, diretorios opcionais, metadados, imagens em cache, preferencias e historico basico em SQLite.

O modelo deve sustentar uma experiencia local-first: a biblioteca funciona sem internet, sem conta e sem sincronizacao em nuvem.

## 2. Banco Local

Banco recomendado:

- **SQLite** embarcado.
- Arquivo salvo no diretorio de dados do aplicativo do usuario.
- Migrations versionadas.
- Dados de biblioteca e configuracoes mantidos apos atualizacao do app.

Diretorios recomendados:

- Banco: diretorio de dados do app.
- Capas/banners/cache: subpasta propria, referenciada por caminho relativo sempre que possivel.
- Logs tecnicos: subpasta separada, com limpeza futura.

## 3. Convencoes

- `id` deve ser identificador unico interno, preferencialmente UUID ou inteiro autoincremental.
- Datas devem ser salvas em ISO 8601 UTC.
- Booleanos podem ser representados como `0` e `1` no SQLite.
- Campos de caminho devem armazenar caminhos absolutos locais.
- Campos derivados, como `normalized_title`, devem facilitar busca e deduplicacao.
- Exclusao definitiva deve ser usada com cuidado; quando fizer sentido, preferir flags como `is_hidden`.
- Dados vindos da internet devem ser editaveis pelo usuario e cacheados localmente.

## 4. Entidades Principais

```text
games
  |-- game_genres
  |-- game_tags
  |-- game_assets
  |-- play_sessions
  |-- game_metadata_matches

scan_candidates
  |-- pode virar games

library_directories
  |-- usados em varreduras futuras

metadata_cache
  |-- origem para preencher games

app_settings
  |-- preferencias locais
```

## 5. Tabela `games`

Representa jogos aceitos na biblioteca do usuario.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `title` | text | sim | Nome exibido do jogo. |
| `normalized_title` | text | sim | Nome normalizado para busca e duplicados. |
| `executable_path` | text | sim | Caminho do executavel principal. |
| `launch_arguments` | text | nao | Argumentos opcionais de inicializacao. |
| `working_directory` | text | nao | Diretorio de trabalho sugerido. |
| `install_directory` | text | nao | Pasta raiz do jogo. |
| `source_type` | text | sim | Origem do cadastro. |
| `source_label` | text | nao | Nome amigavel da origem, como Steam ou Pasta manual. |
| `source_id` | text | nao | Identificador externo quando houver, como app id de launcher. |
| `cover_image_path` | text | nao | Caminho local da capa cacheada ou escolhida. |
| `banner_image_path` | text | nao | Caminho local do banner cacheado ou escolhido. |
| `icon_path` | text | nao | Caminho local do icone extraido ou escolhido. |
| `summary` | text | nao | Sinopse curta. |
| `developer` | text | nao | Desenvolvedora. |
| `publisher` | text | nao | Publicadora. |
| `release_date` | text | nao | Data ou ano de lancamento. |
| `franchise` | text | nao | Serie/franquia, quando houver. |
| `is_favorite` | integer | sim | `0` ou `1`. |
| `is_hidden` | integer | sim | `0` ou `1`. |
| `personal_status` | text | nao | Status pessoal do usuario. |
| `last_played_at` | text | nao | Ultima execucao pelo app. |
| `play_time_seconds` | integer | sim | Tempo acumulado estimado. |
| `created_at` | text | sim | Criacao do registro. |
| `updated_at` | text | sim | Ultima atualizacao. |

Valores de `source_type`:

- `scan`
- `manual_folder`
- `launcher`
- `shortcut`
- `imported`

Valores iniciais de `personal_status`:

- `none`
- `playing`
- `finished`
- `paused`
- `abandoned`
- `want_to_play`

## 6. Tabela `scan_candidates`

Representa jogos encontrados pela varredura antes da aprovacao.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `suggested_title` | text | sim | Nome sugerido. |
| `normalized_title` | text | sim | Nome normalizado. |
| `executable_path` | text | sim | Executavel encontrado. |
| `launch_arguments` | text | nao | Argumentos detectados, principalmente de atalhos. |
| `working_directory` | text | nao | Diretorio de trabalho detectado. |
| `install_directory` | text | nao | Pasta raiz provavel do jogo. |
| `detected_source` | text | nao | Descricao da fonte detectada. |
| `source_kind` | text | sim | Tipo da fonte. |
| `confidence_score` | integer | sim | Pontuacao de `0` a `100`. |
| `confidence_label` | text | sim | `high`, `medium`, `low`. |
| `reason` | text | nao | Explicacao curta para o usuario. |
| `evidence_json` | text | nao | Evidencias tecnicas em JSON. |
| `icon_path` | text | nao | Icone extraido, se houver. |
| `cover_image_path` | text | nao | Capa sugerida, se houver. |
| `status` | text | sim | Estado do candidato. |
| `created_at` | text | sim | Criacao do candidato. |
| `updated_at` | text | sim | Ultima atualizacao. |

Valores de `source_kind`:

- `shortcut`
- `launcher`
- `common_games_folder`
- `program_files`
- `drive_scan`
- `manual_folder_scan`
- `user_library_directory`

Valores de `status`:

- `pending`
- `accepted`
- `ignored`
- `duplicate`

## 7. Tabela `library_directories`

Representa diretorios opcionais cadastrados pelo usuario para varreduras futuras.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `path` | text | sim | Caminho da pasta. |
| `label` | text | nao | Nome amigavel escolhido pelo usuario. |
| `is_enabled` | integer | sim | Se o diretorio entra em varreduras. |
| `last_scanned_at` | text | nao | Ultima varredura nesse diretorio. |
| `created_at` | text | sim | Criacao do registro. |
| `updated_at` | text | sim | Ultima atualizacao. |

Regras:

- O cadastro e opcional.
- Remover um diretorio nao remove jogos ja cadastrados.
- Diretorio inexistente deve ser marcado na UI como indisponivel.
- `path` deve ser unico depois de normalizacao.

## 8. Tabela `metadata_cache`

Armazena resultados de buscas online para evitar chamadas repetidas e permitir uso offline dos dados selecionados.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `provider` | text | sim | Fonte dos metadados. |
| `provider_game_id` | text | nao | Identificador externo na fonte. |
| `query` | text | sim | Termo usado na busca. |
| `title` | text | nao | Titulo retornado. |
| `cover_url` | text | nao | URL original da capa. |
| `banner_url` | text | nao | URL original do banner. |
| `cover_image_path` | text | nao | Caminho local da capa cacheada. |
| `banner_image_path` | text | nao | Caminho local do banner cacheado. |
| `genres_json` | text | nao | Generos retornados em JSON. |
| `summary` | text | nao | Sinopse curta. |
| `developer` | text | nao | Desenvolvedora. |
| `publisher` | text | nao | Publicadora. |
| `release_date` | text | nao | Data ou ano de lancamento. |
| `franchise` | text | nao | Serie/franquia. |
| `raw_payload` | text | nao | Payload original em JSON para auditoria tecnica. |
| `created_at` | text | sim | Criacao do cache. |
| `updated_at` | text | sim | Ultima atualizacao. |

Regras:

- Falha de metadados nao deve impedir cadastro local.
- O usuario pode sobrescrever qualquer dado aplicado ao jogo.
- Dados aplicados ao jogo devem ser copiados para `games`, nao dependerem apenas do cache.

## 9. Tabela `game_metadata_matches`

Registra qual resultado de metadados foi aplicado a um jogo.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `game_id` | text/integer | sim | Jogo relacionado. |
| `metadata_cache_id` | text/integer | nao | Resultado aplicado. |
| `provider` | text | nao | Fonte usada. |
| `provider_game_id` | text | nao | ID externo. |
| `applied_at` | text | sim | Momento da aplicacao. |

Relacionamentos:

- `game_id` referencia `games.id`.
- `metadata_cache_id` referencia `metadata_cache.id`, quando existir.

## 10. Tabelas `game_genres` e `game_tags`

Essas tabelas evitam prender o jogo a um unico genero ou tag.

### `game_genres`

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `game_id` | text/integer | sim | Jogo relacionado. |
| `genre` | text | sim | Genero do jogo. |
| `source` | text | sim | `metadata`, `manual` ou `imported`. |

### `game_tags`

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `game_id` | text/integer | sim | Jogo relacionado. |
| `tag` | text | sim | Tag livre. |
| `source` | text | sim | `manual`, `system` ou `metadata`. |

Regras:

- Generos vindos da internet podem ser editados.
- Tags manuais devem ser preservadas mesmo se metadados forem buscados novamente.

## 11. Tabela `game_assets`

Representa imagens e arquivos visuais associados ao jogo.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `game_id` | text/integer | sim | Jogo relacionado. |
| `asset_type` | text | sim | Tipo do asset. |
| `local_path` | text | sim | Caminho local cacheado ou escolhido. |
| `source_url` | text | nao | URL original, se houver. |
| `source` | text | sim | `metadata`, `manual`, `extracted`. |
| `created_at` | text | sim | Criacao do registro. |

Valores de `asset_type`:

- `cover`
- `banner`
- `icon`
- `screenshot`

## 12. Tabela `play_sessions`

Registra sessoes de jogo iniciadas pelo app. No MVP, pode ser simples ou adiada para uma fase posterior.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `id` | text/integer | sim | Identificador interno. |
| `game_id` | text/integer | sim | Jogo relacionado. |
| `started_at` | text | sim | Inicio da sessao. |
| `ended_at` | text | nao | Fim da sessao. |
| `duration_seconds` | integer | nao | Duracao estimada. |
| `exit_status` | text | nao | Resultado conhecido do processo. |

Regras:

- Se o monitoramento de processo nao estiver implementado, salvar apenas `last_played_at` em `games`.
- Quando o tempo de jogo existir, atualizar `games.play_time_seconds`.

## 13. Tabela `app_settings`

Armazena preferencias locais simples em chave/valor.

Campos:

| Campo | Tipo | Obrigatorio | Descricao |
| --- | --- | --- | --- |
| `key` | text | sim | Nome da configuracao. |
| `value` | text | nao | Valor serializado. |
| `updated_at` | text | sim | Ultima atualizacao. |

Chaves iniciais:

| Chave | Valor esperado | Descricao |
| --- | --- | --- |
| `first_run_completed` | `true`/`false` | Controla experiencia inicial. |
| `metadata_online_enabled` | `true`/`false` | Permite busca online. |
| `library_icon_size` | `small`/`medium`/`large` | Tamanho dos cards da biblioteca. |
| `sidebar_width` | numero em pixels | Largura da sidebar redimensionavel. |
| `last_scan_at` | ISO 8601 | Ultima varredura completa. |
| `theme` | `dark_blue` | Tema visual atual. |

## 14. Indices Recomendados

Indices iniciais:

- `games.normalized_title`
- `games.executable_path`
- `games.install_directory`
- `games.is_hidden`
- `games.is_favorite`
- `scan_candidates.status`
- `scan_candidates.executable_path`
- `scan_candidates.normalized_title`
- `library_directories.path`
- `metadata_cache.provider`
- `metadata_cache.provider_game_id`
- `game_genres.game_id`
- `game_tags.game_id`
- `play_sessions.game_id`

Unicidade recomendada:

- `library_directories.path` normalizado deve ser unico.
- `games.executable_path` pode ser unico no MVP, mas deve permitir excecoes futuras se necessario.
- `metadata_cache(provider, provider_game_id)` deve ser unico quando `provider_game_id` existir.

## 15. Regras de Integridade

- Um `game` deve ter `title` e `executable_path`.
- Um `scan_candidate` aceito deve gerar no maximo um `game`.
- Candidatos ignorados devem permanecer registrados para evitar reaparecerem imediatamente.
- Remover diretorio de biblioteca nao remove jogos.
- Remover jogo deve remover associacoes em `game_genres`, `game_tags`, `game_assets`, `game_metadata_matches` e sessoes futuras relacionadas, conforme politica definida.
- Caminhos inexistentes nao devem apagar registros automaticamente; devem gerar estado de erro na UI.

## 16. Normalizacao

Normalizacao de titulo:

- Remover espacos duplicados.
- Converter para minusculas.
- Remover pontuacao irrelevante para comparacao.
- Remover sufixos comuns como launcher, demo ou shortcut apenas para comparacao, sem alterar o titulo exibido.

Normalizacao de caminho:

- Padronizar separadores.
- Resolver caminhos relativos quando possivel.
- Comparar caminhos sem diferenca de caixa em Windows.
- Remover barra final para comparacoes.

## 17. Migracoes

O app deve ter uma tabela de controle de migrations ou usar mecanismo da biblioteca escolhida.

Regras:

- Migrations devem ser incrementais.
- Migrations nao devem apagar dados do usuario sem backup ou confirmacao.
- Novos campos devem ter defaults seguros.
- Alteracoes destrutivas devem ser evitadas no MVP.

## 18. Backup e Exportacao Futura

Mesmo fora do MVP, o modelo deve facilitar exportacao local.

Formato futuro sugerido:

- Exportar banco ou JSON consolidado.
- Incluir metadados e preferencias.
- Opcionalmente incluir capas e banners cacheados.
- Importacao deve lidar com caminhos que mudaram entre maquinas.

## 19. Criterios de Aceite

- O modelo permite cadastrar jogos detectados e jogos adicionados por pasta.
- O modelo permite revisar candidatos antes de aceitar.
- O modelo permite salvar diretorios de biblioteca opcionais.
- O modelo permite persistir genero, sinopse, capa, banner e metadados principais.
- O modelo permite favoritos, ocultos, status pessoal e preferencias de UI.
- O modelo continua util offline depois que dados e imagens forem salvos localmente.
- O modelo evita duplicados obvios sem bloquear ajustes manuais do usuario.
