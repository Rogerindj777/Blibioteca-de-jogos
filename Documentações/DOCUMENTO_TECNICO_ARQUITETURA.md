# Documento Tecnico de Arquitetura: Biblioteca de Jogos para Windows

## 1. Visao Geral

O projeto sera um aplicativo desktop para Windows, local-first, criado para detectar, organizar e iniciar jogos instalados na maquina do usuario. A arquitetura deve priorizar privacidade, boa performance em varreduras grandes, interface moderna e facilidade de evolucao.

O aplicativo nao deve depender de conta, login ou nuvem para funcionar. Internet sera usada apenas como melhoria opcional para buscar capas, generos, sinopses e outros metadados publicos.

## 2. Stack Recomendada

### Aplicativo Desktop

- **Tauri** como container desktop.
- **Rust** no backend local para acesso ao sistema operacional, varredura de arquivos, abertura de processos e integracoes nativas.
- **React + TypeScript** no frontend para uma interface moderna, componentizada e facil de manter.
- **SQLite** como banco local embarcado.

Essa stack combina instalador leve, boa integracao com Windows, performance para tarefas locais e uma interface web moderna sem transformar o app em uma aplicacao dependente de navegador externo.

### UI

- React com TypeScript.
- CSS modular, Tailwind CSS ou outra solucao equivalente definida no inicio da implementacao.
- Componentes proprios para biblioteca, cards, filtros, paineis, modais e controle de tamanho dos icones.
- Icones via biblioteca consolidada, como Lucide, se o projeto usar React.

### Banco Local

- SQLite armazenado no diretorio de dados do aplicativo do usuario.
- Migrations versionadas.
- Nenhum dado obrigatorio em servidores externos.

## 3. Principios Tecnicos

- **Local-first:** biblioteca, configuracoes e metadados salvos localmente.
- **Offline funcional:** o app deve abrir e iniciar jogos cadastrados sem internet.
- **Varredura segura:** procurar candidatos sem modificar arquivos do usuario.
- **Revisao antes de adicionar:** candidatos encontrados nao entram automaticamente na biblioteca definitiva.
- **Metadados desacoplados:** busca online deve ser um modulo separado, substituivel e opcional.
- **Interface responsiva:** a varredura nao pode travar a UI.
- **Privacidade por padrao:** caminhos locais e biblioteca do usuario nao devem ser enviados para servidores proprios.

## 4. Modulos Principais

### Frontend

Responsavel por toda a experiencia visual do usuario.

Principais areas:

- Tela de primeira execucao e progresso da varredura.
- Tela de revisao de candidatos.
- Biblioteca em grade/lista.
- Alternancia de visualizacao da biblioteca entre icones pequenos, medios e grandes.
- Sidebar redimensionavel por arrastar, com largura persistida por usuario.
- Painel de detalhes do jogo.
- Modais de cadastro e edicao.
- Fluxo simplificado para adicionar jogo a partir de uma pasta.
- Gerenciamento opcional de diretorios de biblioteca do usuario.
- Configuracoes basicas.
- Estados vazios, erros e offline.

### Visualizacao da Biblioteca

A biblioteca deve suportar tres densidades visuais para os cards de jogos:

- **Icones pequenos:** prioriza quantidade de jogos por tela, com capa reduzida e nome compacto.
- **Icones medios:** modo padrao recomendado, equilibrando capa, nome e leitura rapida.
- **Icones grandes:** prioriza impacto visual, capas maiores e navegacao mais confortavel.

Essa preferencia deve ser aplicada imediatamente na interface e persistida em `app_settings`, permitindo que o app reabra no ultimo modo escolhido pelo usuario.

### Sidebar Redimensionavel

A sidebar deve permitir ajuste simples de largura pelo usuario. O usuario deve clicar e segurar no divisor entre a sidebar e o conteudo principal, arrastando para a esquerda ou direita para deixar a barra mais estreita ou mais larga.

Regras tecnicas:

- A largura deve respeitar limites minimo e maximo para evitar layout quebrado.
- A largura escolhida deve ser persistida em `app_settings`.
- O ajuste deve ser aplicado imediatamente, sem precisar reiniciar o app.
- Em larguras menores, a sidebar pode priorizar icones e reduzir textos quando necessario.
- Um duplo clique no divisor pode restaurar a largura padrao em versao futura.

### Backend Local

Responsavel por operacoes que exigem acesso ao sistema operacional.

Responsabilidades:

- Executar varreduras em background.
- Ler atalhos e diretorios relevantes do Windows.
- Identificar candidatos a jogos.
- Iniciar executaveis cadastrados.
- Validar existencia de arquivos.
- Salvar e consultar dados no SQLite.
- Gerenciar cache local de imagens e metadados.

### Servico de Varredura

Modulo dedicado a encontrar possiveis jogos instalados.

Fontes iniciais:

- Atalhos da area de trabalho.
- Atalhos do menu iniciar.
- Pastas comuns: `C:\Games`, `D:\Games`, `C:\Program Files`, `C:\Program Files (x86)`.
- Diretorios de bibliotecas de launchers conhecidos, quando detectaveis localmente.
- Diretorios de biblioteca cadastrados opcionalmente pelo usuario.
- Executaveis em estruturas de pastas semelhantes a jogos.

O servico deve retornar candidatos com confianca estimada, nao jogos finais.

### Servico de Metadados

Modulo responsavel por buscar informacoes online quando permitido pelo usuario e quando houver internet.

Dados desejados:

- Nome oficial.
- Capa.
- Banner.
- Genero.
- Sinopse curta.
- Desenvolvedora.
- Publicadora.
- Ano ou data de lancamento.
- Serie ou franquia.

O servico deve ser abstrato para permitir trocar ou combinar fontes no futuro, como APIs publicas, bases abertas ou scraping permitido por termos de uso. A implementacao deve respeitar limites de uso e licencas das fontes escolhidas.

### Persistencia

Modulo responsavel por acesso ao SQLite.

Responsabilidades:

- Criar e migrar tabelas.
- Persistir biblioteca, candidatos, configuracoes e cache.
- Garantir integridade basica dos dados.
- Evitar duplicacao obvia de jogos.

## 5. Modelo de Dados Inicial

### games

Representa um jogo aceito na biblioteca.

Campos sugeridos:

- `id`
- `title`
- `normalized_title`
- `executable_path`
- `launch_arguments`
- `working_directory`
- `source_type`
- `source_label`
- `cover_image_path`
- `banner_image_path`
- `genre`
- `summary`
- `developer`
- `publisher`
- `release_date`
- `franchise`
- `is_favorite`
- `is_hidden`
- `personal_status`
- `last_played_at`
- `play_time_seconds`
- `created_at`
- `updated_at`

### scan_candidates

Representa itens encontrados pela varredura antes de aprovacao.

Campos sugeridos:

- `id`
- `suggested_title`
- `executable_path`
- `working_directory`
- `detected_source`
- `confidence_score`
- `reason`
- `cover_image_path`
- `status`
- `created_at`

Status possiveis:

- `pending`
- `accepted`
- `ignored`
- `duplicate`

### library_directories

Representa diretorios opcionais cadastrados pelo usuario para futuras varreduras.

Campos sugeridos:

- `id`
- `path`
- `label`
- `is_enabled`
- `last_scanned_at`
- `created_at`
- `updated_at`

Esses diretorios nao sao obrigatorios para o funcionamento do app. Eles existem para usuarios que querem indicar pastas onde costumam instalar jogos, como `D:\Games` ou `E:\Jogos`.

### metadata_cache

Armazena resultados de metadados online.

Campos sugeridos:

- `id`
- `provider`
- `provider_game_id`
- `query`
- `title`
- `cover_url`
- `banner_url`
- `genre`
- `summary`
- `developer`
- `publisher`
- `release_date`
- `raw_payload`
- `created_at`
- `updated_at`

### app_settings

Configuracoes locais do app.

Campos sugeridos:

- `key`
- `value`
- `updated_at`

Configuracoes iniciais:

- `first_run_completed`
- `metadata_online_enabled`
- `library_icon_size`
- `sidebar_width`
- `last_scan_at`
- `theme`

## 6. Fluxos Tecnicos

### Primeiro Uso

1. Frontend abre tela inicial.
2. Backend verifica se `first_run_completed` esta ativo.
3. Se for primeiro uso, frontend inicia varredura automaticamente.
4. Backend executa varredura em background e emite progresso.
5. Candidatos sao salvos como `scan_candidates`.
6. Frontend exibe tela de revisao.
7. Usuario aceita, ignora ou edita candidatos.
8. Candidatos aceitos viram registros em `games`.
9. App marca `first_run_completed`.

### Nova Varredura

1. Usuario aciona "Nova varredura".
2. Backend executa varredura sem apagar biblioteca existente.
3. Backend inclui diretorios de biblioteca habilitados pelo usuario, se existirem.
4. Candidatos duplicados sao marcados ou omitidos.
5. Novos candidatos entram na revisao.
6. Usuario decide o que adicionar.

### Adicionar Jogo por Pasta

1. Usuario aciona "Adicionar jogo".
2. Frontend abre seletor de pasta, nao de executavel.
3. Usuario escolhe a pasta raiz do jogo.
4. Backend analisa somente essa pasta e tenta encontrar o executavel principal.
5. Se houver um candidato claro, o app cria o jogo com dados sugeridos.
6. Se houver multiplos candidatos, o app mostra uma revisao curta para o usuario escolher.
7. Campos como argumentos, diretorio de trabalho e executavel especifico ficam em edicao avancada.

### Adicionar Diretorio de Biblioteca

1. Usuario aciona uma opcao como "Adicionar diretorio de biblioteca".
2. Frontend abre seletor de pasta.
3. Usuario escolhe uma pasta onde jogos estao ou serao instalados.
4. Backend salva a pasta em `library_directories`.
5. O app oferece executar uma varredura nessa pasta agora ou apenas usa-la em varreduras futuras.
6. O recurso e opcional e pode ser desativado ou removido depois.

### Abrir Jogo

1. Usuario clica em "Jogar".
2. Backend verifica se `executable_path` existe.
3. Se existir, inicia processo com argumentos e diretorio de trabalho.
4. App registra `last_played_at`.
5. Em versao futura, app pode monitorar o processo para calcular tempo de jogo.

Se o executavel nao existir, o frontend deve exibir opcao para localizar novo arquivo, remover o jogo ou manter o cadastro.

### Buscar Metadados

1. Usuario aceita candidato ou edita jogo existente.
2. Se busca online estiver habilitada, backend consulta servico de metadados.
3. Se houver resultado unico de alta confianca, app sugere preenchimento automatico.
4. Se houver multiplos resultados, usuario escolhe.
5. Imagens e dados selecionados sao salvos localmente.
6. Biblioteca continua funcionando mesmo se a busca falhar.

## 7. API Interna Entre Frontend e Backend

A comunicacao entre React e Rust deve expor comandos pequenos e explicitos.

Comandos iniciais:

- `get_app_state()`
- `start_scan()`
- `cancel_scan()`
- `list_scan_candidates()`
- `accept_scan_candidate(candidate_id, edits)`
- `ignore_scan_candidate(candidate_id)`
- `list_games(filters)`
- `set_library_icon_size(size)`
- `set_sidebar_width(width)`
- `add_game_from_folder(folder_path)`
- `analyze_game_folder(folder_path)`
- `list_library_directories()`
- `add_library_directory(path, label)`
- `update_library_directory(directory_id, input)`
- `remove_library_directory(directory_id)`
- `get_game(game_id)`
- `create_game(input)`
- `update_game(game_id, input)`
- `hide_game(game_id)`
- `delete_game(game_id)`
- `launch_game(game_id)`
- `search_metadata(query)`
- `apply_metadata(game_id, metadata_id)`
- `get_settings()`
- `update_settings(input)`

Eventos emitidos pelo backend:

- `scan_started`
- `scan_progress`
- `scan_candidate_found`
- `scan_finished`
- `scan_cancelled`
- `game_folder_analysis_finished`
- `metadata_search_started`
- `metadata_search_finished`
- `game_launch_failed`

## 8. Estrategia de Varredura

A varredura ampla deve ser feita em camadas para equilibrar velocidade e qualidade.

### Camada 1: Fontes de Alta Confianca

- Atalhos do menu iniciar.
- Atalhos da area de trabalho.
- Bibliotecas conhecidas de launchers instalados.

### Camada 2: Pastas Provaveis

- `C:\Games`
- `D:\Games`
- `C:\Program Files`
- `C:\Program Files (x86)`
- Diretorios de biblioteca configurados opcionalmente pelo usuario.

### Camada 3: Heuristicas

Sinais positivos:

- Executavel em pasta com nome parecido com jogo.
- Presenca de arquivos de dados grandes.
- Presenca de subpastas como `Binaries`, `Content`, `Engine`, `Data`, `Game`, `Win64`.
- Executavel com icone proprio.
- Caminho localizado dentro de pasta de launcher ou biblioteca.

Sinais negativos:

- Executaveis de uninstall, updater, launcher generico ou redist.
- Pastas de sistema.
- Ferramentas de desenvolvimento.
- Arquivos em cache ou temporarios.

O resultado deve receber uma pontuacao de confianca e uma justificativa curta para exibicao na tela de revisao.

## 9. Seguranca e Privacidade

- O app nao deve modificar arquivos de jogos durante a varredura.
- O app nao deve enviar caminhos locais para servidores proprios.
- O app deve pedir confirmacao antes de abrir executaveis adicionados manualmente se houver sinais suspeitos.
- O app deve armazenar dados localmente em diretorio apropriado do usuario.
- O app deve permitir limpar cache de imagens e metadados.
- O app nao deve incluir recursos para download ilegal, remocao de DRM, cracks ou ativadores.

## 10. Tratamento de Erros

### Erros de Varredura

- Permissao negada em pasta: registrar e continuar.
- Caminho inacessivel: ignorar e continuar.
- Arquivo bloqueado: ignorar e continuar.
- Varredura cancelada: salvar candidatos ja encontrados e indicar cancelamento.

### Erros de Metadados

- Sem internet: exibir estado discreto e manter dados locais.
- API indisponivel: permitir tentar novamente depois.
- Multiplos resultados: pedir escolha do usuario.
- Resultado incorreto: permitir edicao manual.

### Erros ao Abrir Jogo

- Executavel ausente: pedir novo caminho.
- Permissao negada: informar o usuario.
- Processo falhou ao iniciar: exibir erro tecnico simplificado.

## 11. Testes

### Testes Unitarios

- Normalizacao de nomes.
- Heuristicas de confianca.
- Deteccao de duplicados.
- Validacao de entradas de jogo.
- Mapeamento de metadados.

### Testes de Integracao

- Criacao e migracao do banco SQLite.
- Fluxo de varredura com diretorios simulados.
- Aceitar e ignorar candidatos.
- Cadastro manual de jogo.
- Cadastro manual simplificado por pasta.
- Cadastro, remocao e uso de diretorios de biblioteca opcionais.
- Busca e aplicacao de metadados com provider simulado.
- Abertura de jogo usando executavel de teste controlado.

### Testes de Interface

- Biblioteca vazia.
- Varredura em andamento.
- Revisao de candidatos.
- Biblioteca com muitos jogos.
- Alternancia entre icones pequenos, medios e grandes sem quebrar layout.
- Redimensionamento da sidebar por arrastar, respeitando largura minima e maxima.
- Detalhes com e sem metadados.
- Estados offline.
- Jogo removido ou movido.

## 12. Empacotamento e Distribuicao

O app deve gerar instalador para Windows.

Requisitos:

- Instalacao simples.
- Atalho no menu iniciar.
- Atualizacoes podem ser planejadas depois do MVP.
- Dados do usuario devem permanecer apos atualizacao do app.
- Desinstalacao nao deve apagar biblioteca local sem confirmacao explicita.

## 13. Roadmap Tecnico Sugerido

### Fase 1: Base Local

- Criar app Tauri + React + TypeScript.
- Configurar SQLite e migrations.
- Implementar cadastro manual de jogos.
- Implementar biblioteca e detalhes basicos.
- Implementar abertura de executaveis.

### Fase 2: Varredura

- Implementar varredura em camadas.
- Criar tela de progresso.
- Criar tela de revisao de candidatos.
- Implementar aceitacao, edicao e ignorar candidatos.

### Fase 3: Metadados

- Criar interface abstrata de provider.
- Implementar busca online opcional.
- Baixar e cachear capas/banners.
- Preencher genero, sinopse e informacoes basicas.
- Permitir correcao manual.

### Fase 4: Polimento

- Melhorar filtros.
- Adicionar favoritos e ocultos.
- Refinar visualizacoes da biblioteca em icones pequenos, medios e grandes.
- Refinar sidebar redimensionavel e comportamento compacto.
- Refinar design azul/preto.
- Melhorar performance com bibliotecas grandes.
- Preparar instalador Windows.

## 14. Questoes em Aberto

- Qual fonte online sera usada primeiro para metadados de jogos.
- Se a busca online vira habilitada por padrao ou sera confirmada no primeiro uso.
- Se o app tera apenas modo janela ou tambem um modo tela cheia no futuro.
- Qual nivel de detalhe sera usado para medir tempo de jogo.
- Como o usuario podera fazer backup/exportacao da biblioteca em uma versao posterior.
