# Especificacao da Varredura de Jogos no Windows

## 1. Objetivo

Definir como o aplicativo deve procurar jogos instalados no Windows, gerar candidatos para revisao do usuario e evitar falsos positivos. A varredura deve ser ampla o suficiente para encontrar jogos de launchers conhecidos, atalhos e instalacoes standalone, mas segura o bastante para nao modificar arquivos nem adicionar itens automaticamente sem confirmacao.

O resultado da varredura nao e um jogo definitivo. O resultado e um `scan_candidate` que precisa ser revisado, aceito, editado ou ignorado pelo usuario.

## 2. Principios

- **Nao invasiva:** a varredura nao deve alterar, apagar ou mover arquivos.
- **Cancelavel:** o usuario deve poder cancelar a busca a qualquer momento.
- **Progressiva:** mostrar resultados conforme forem encontrados.
- **Em camadas:** verificar primeiro fontes mais confiaveis e depois locais mais amplos.
- **Tolerante a erro:** permissoes negadas, arquivos bloqueados e caminhos inacessiveis nao devem interromper a varredura.
- **Revisavel:** todo candidato deve explicar por que foi encontrado.
- **Local-first:** a varredura local nao depende de internet.
- **Sem validacao de licenca:** o app nao tenta determinar se o jogo e original, pirata, gratuito ou comprado.

## 3. Escopo da Varredura

### Incluido

- Atalhos do menu iniciar.
- Atalhos da area de trabalho do usuario.
- Atalhos da area de trabalho publica.
- Pastas comuns de jogos.
- Pastas comuns de programas.
- Diretorios de biblioteca cadastrados opcionalmente pelo usuario.
- Bibliotecas detectaveis de launchers conhecidos.
- Jogos standalone em discos locais.
- Executaveis com estrutura de jogo ao redor.

### Fora do Escopo Inicial

- Downloads automaticos.
- Verificacao de licenca.
- Remocao de DRM.
- Modificacao de arquivos do jogo.
- Varredura profunda em pastas de sistema criticas.
- Varredura em unidades de rede por padrao.
- Execucao de arquivos encontrados para validar se sao jogos.

## 4. Fontes de Busca

### Atalhos do Windows

Locais recomendados:

- `%USERPROFILE%\Desktop`
- `%PUBLIC%\Desktop`
- `%APPDATA%\Microsoft\Windows\Start Menu\Programs`
- `%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs`

O scanner deve resolver atalhos `.lnk` para identificar:

- Caminho do executavel alvo.
- Argumentos.
- Diretorio de trabalho.
- Nome exibido pelo atalho.
- Icone associado, quando disponivel.

Atalhos sao fontes de alta confianca porque normalmente representam aplicativos iniciaveis pelo usuario.

### Pastas Comuns de Jogos

Locais iniciais:

- `C:\Games`
- `C:\Jogos`
- `D:\Games`
- `D:\Jogos`
- `E:\Games`
- `E:\Jogos`
- Pastas equivalentes encontradas em discos locais disponiveis.

Essas pastas devem ter prioridade maior que uma varredura ampla em todo o disco.

### Diretorios de Biblioteca do Usuario

Apos uma varredura completa, o app deve permitir que o usuario cadastre opcionalmente diretorios onde costuma instalar jogos.

Exemplos:

- `D:\Games`
- `E:\Jogos`
- `C:\Users\<usuario>\Games`

Regras:

- O cadastro desses diretorios e opcional.
- O app deve funcionar normalmente sem nenhum diretorio manual.
- Diretorios cadastrados devem ser incluidos nas proximas varreduras.
- O usuario pode remover ou desativar um diretorio cadastrado.
- Ao adicionar um diretorio, o app pode oferecer "varrer agora" ou "usar apenas nas proximas varreduras".
- O app nao deve instalar jogos nesses diretorios; eles sao apenas fontes de busca.

### Pastas de Programas

Locais iniciais:

- `C:\Program Files`
- `C:\Program Files (x86)`
- Pastas equivalentes quando o Windows estiver instalado em outra unidade.

Esses locais podem conter muitos aplicativos que nao sao jogos. A varredura deve usar filtros e heuristicas mais conservadoras nesses diretorios.

### Launchers Conhecidos

O app deve tentar detectar bibliotecas locais de launchers quando possivel, sem exigir login.

Fontes candidatas:

- Steam.
- Epic Games.
- GOG Galaxy.
- Battle.net.
- Ubisoft Connect.
- EA app.
- Xbox app/Microsoft Store, quando tecnicamente viavel.

A deteccao por launcher deve ser tratada como alta confianca quando a fonte local indicar claramente um jogo instalado.

## 5. Ordem da Varredura

A varredura deve seguir uma ordem previsivel:

1. Carregar configuracoes e candidatos existentes.
2. Verificar atalhos do menu iniciar e area de trabalho.
3. Detectar launchers instalados e suas bibliotecas conhecidas.
4. Verificar diretorios de biblioteca cadastrados pelo usuario, se existirem.
5. Verificar pastas comuns de jogos.
6. Verificar pastas comuns de programas com heuristicas conservadoras.
7. Verificar outras unidades locais com limites de profundidade.
8. Consolidar duplicados.
9. Salvar candidatos pendentes.
10. Emitir evento de finalizacao.

Essa ordem permite que a interface mostre resultados confiaveis rapidamente, enquanto a busca mais ampla continua em segundo plano.

## 6. Regras de Profundidade e Desempenho

Para evitar varreduras lentas demais:

- Pastas conhecidas de jogos podem ter profundidade maior.
- Pastas de programas devem ter profundidade limitada.
- Pastas de sistema devem ser ignoradas.
- Pastas temporarias e caches devem ser ignoradas.
- A UI deve receber progresso periodico.
- O backend deve permitir cancelamento cooperativo entre diretorios.
- A varredura deve evitar reler caminhos ja analisados na mesma execucao.

Pastas a ignorar por padrao:

- `C:\Windows`
- `C:\ProgramData\Microsoft`
- `C:\Users\<usuario>\AppData\Local\Temp`
- `C:\Users\<usuario>\AppData\Local\Microsoft`
- `C:\$Recycle.Bin`
- `System Volume Information`
- `node_modules`
- `.git`
- Pastas de cache conhecidas.

## 7. Identificacao de Executaveis Candidatos

Extensoes aceitas inicialmente:

- `.exe`
- `.lnk`, quando resolvido para `.exe`

Executaveis devem ser ignorados quando o nome indicar:

- `uninstall`
- `unins`
- `setup`
- `installer`
- `update`
- `updater`
- `patcher`
- `redist`
- `vcredist`
- `dxsetup`
- `crashreport`
- `crashhandler`
- `helper`
- `service`
- `webview`

Esses termos nao devem ser a unica regra, mas devem reduzir a pontuacao ou excluir candidatos obvios.

## 8. Heuristicas de Confianca

Cada candidato deve receber `confidence_score` entre `0` e `100`.

### Sinais Positivos

- Atalho no menu iniciar ou area de trabalho: `+25`.
- Encontrado em biblioteca de launcher conhecido: `+35`.
- Pasta pai parece nome de jogo: `+15`.
- Presenca de arquivos grandes de dados: `+10`.
- Presenca de subpastas comuns de jogos: `+10`.
- Presenca de arquivos como `steam_appid.txt`, manifestos ou configs de jogo: `+10`.
- Executavel possui icone proprio: `+5`.
- Nome do executavel combina com nome da pasta: `+10`.

Subpastas comuns:

- `Binaries`
- `Content`
- `Engine`
- `Data`
- `Game`
- `Win64`
- `Win32`
- `x64`
- `bin`

### Sinais Negativos

- Nome indica desinstalador, instalador ou atualizador: `-40`.
- Caminho esta em pasta de sistema: `-50`.
- Executavel em pasta temporaria/cache: `-30`.
- Nome generico como `launcher.exe` sem outros sinais: `-15`.
- Arquivo muito pequeno sem contexto de jogo: `-10`.
- Candidato duplicado de outro ja encontrado: marcar como duplicado ou reduzir.

### Faixas de Interpretacao

- `80-100`: alta confianca.
- `50-79`: media confianca.
- `25-49`: baixa confianca.
- `0-24`: nao exibir por padrao, salvo em modo diagnostico futuro.

Mesmo candidatos de alta confianca devem passar pela revisao do usuario no MVP.

## 9. Estrutura do Candidato

Cada item encontrado deve gerar um objeto equivalente a `scan_candidates`.

Campos recomendados:

- `id`
- `suggested_title`
- `executable_path`
- `working_directory`
- `launch_arguments`
- `detected_source`
- `source_kind`
- `confidence_score`
- `confidence_label`
- `reason`
- `evidence`
- `icon_path`
- `cover_image_path`
- `status`
- `created_at`

Exemplo de `source_kind`:

- `shortcut`
- `launcher`
- `common_games_folder`
- `program_files`
- `drive_scan`
- `manual_folder_scan`
- `user_library_directory`

Exemplo de `reason`:

- `Encontrado por atalho no Menu Iniciar.`
- `Detectado em biblioteca da Steam.`
- `Executavel encontrado em pasta comum de jogos com estrutura compativel.`

## 10. Duplicados

O scanner deve evitar mostrar o mesmo jogo varias vezes.

Comparacoes recomendadas:

- Mesmo `executable_path`.
- Mesmo diretorio raiz do jogo.
- Mesmo nome normalizado e caminho parecido.
- Mesmo identificador de launcher, quando existir.

Comportamento:

- Se o jogo ja existir em `games`, nao criar candidato novo, salvo se o caminho mudou.
- Se dois candidatos apontarem para o mesmo executavel, manter o de maior confianca.
- Se dois candidatos parecem o mesmo jogo em origens diferentes, mostrar separadamente apenas se os caminhos forem diferentes e fizer sentido para o usuario.

## 11. Integracao com Launchers

### Steam

Priorizar leitura de arquivos locais de biblioteca e manifestos quando disponiveis. A deteccao deve tentar identificar:

- Pastas de biblioteca.
- Nome do app.
- Caminho de instalacao.
- Executavel provavel.

### Epic Games

Priorizar manifestos locais quando disponiveis. A deteccao deve tentar identificar:

- Nome do jogo.
- Diretorio de instalacao.
- Executavel principal, quando especificado.

### GOG Galaxy

Usar dados locais detectaveis e caminhos de instalacao. Quando a fonte local nao for clara, tratar como heuristica de media confianca.

### Outros Launchers

Battle.net, Ubisoft Connect, EA app e Xbox app podem ter suporte incremental. Caso a deteccao seja incerta, os achados devem cair para revisao como media ou baixa confianca.

## 12. Progresso e Eventos

O backend deve emitir eventos para a interface.

Eventos:

- `scan_started`
- `scan_source_started`
- `scan_progress`
- `scan_candidate_found`
- `scan_candidate_updated`
- `scan_warning`
- `scan_cancelled`
- `scan_finished`

Dados de progresso recomendados:

- Fonte atual.
- Caminho atual, quando seguro exibir.
- Quantidade de arquivos analisados.
- Quantidade de candidatos encontrados.
- Percentual aproximado, quando calculavel.
- Estado: `running`, `cancelling`, `cancelled`, `finished`, `failed`.

O percentual pode ser aproximado, porque algumas pastas nao permitem saber o total de arquivos antes da leitura.

## 13. Cancelamento

O cancelamento deve ser cooperativo.

Ao cancelar:

- Parar de entrar em novos diretorios.
- Concluir operacoes pequenas ja em andamento.
- Salvar candidatos ja encontrados como pendentes.
- Emitir `scan_cancelled`.
- Permitir que o usuario revise o que ja foi encontrado.

Cancelamento nao deve corromper o banco local.

## 14. Adicionar Jogo Manualmente por Pasta

O fluxo manual deve evitar exigir configuracao tecnica do usuario. Em vez de pedir executavel, argumentos e diretorio de trabalho logo de inicio, o app deve pedir apenas a pasta do jogo.

Fluxo:

1. Usuario escolhe a pasta raiz do jogo.
2. Scanner executa uma analise limitada apenas naquela pasta.
3. Scanner procura executaveis candidatos.
4. Scanner aplica as mesmas heuristicas de confianca, mas com contexto mais forte porque a pasta foi escolhida pelo usuario.
5. Se houver um unico executavel de alta confianca, o jogo pode ser sugerido diretamente.
6. Se houver varios executaveis provaveis, o usuario escolhe qual sera usado.
7. Campos avancados ficam disponiveis apenas na edicao do jogo.

Regras:

- A analise deve ser rapida e limitada a pasta selecionada.
- A pasta escolhida nao vira automaticamente um diretorio de biblioteca.
- O app pode perguntar separadamente se o usuario deseja salvar aquela pasta pai como diretorio de biblioteca.
- Nenhum executavel deve ser iniciado durante a analise.

## 15. Adicionar Diretorio de Biblioteca

O usuario deve poder adicionar opcionalmente um diretorio onde jogos estao ou serao instalados no futuro.

Fluxo:

1. Usuario abre configuracoes ou menu de varredura.
2. Usuario clica em "Adicionar diretorio de biblioteca".
3. Usuario escolhe uma pasta.
4. App salva o caminho como fonte de busca opcional.
5. App oferece executar varredura nessa pasta imediatamente ou aguardar a proxima varredura completa.

Regras:

- Diretorios de biblioteca devem ser tratados como fontes de media/alta confianca, dependendo dos achados.
- Diretorios removidos nao devem apagar jogos ja cadastrados.
- Se o diretorio nao existir mais, o app deve marcar como indisponivel e permitir remover.
- O usuario deve conseguir desativar temporariamente um diretorio sem excluir o cadastro.

## 16. Permissoes e Erros

Erros esperados:

- Acesso negado.
- Arquivo em uso.
- Caminho removido durante a varredura.
- Atalho quebrado.
- Disco indisponivel.
- Manifesto malformado.

Comportamento:

- Registrar erro tecnico em log local.
- Exibir apenas mensagens simples quando relevante.
- Continuar a varredura sempre que possivel.
- Nao repetir tentativas infinitamente no mesmo caminho.

## 17. Privacidade e Seguranca

- A varredura deve ler metadados locais, mas nao enviar caminhos completos para servicos externos.
- Caminhos locais devem aparecer apenas na interface do usuario local.
- Nenhum executavel encontrado deve ser executado automaticamente.
- A acao de abrir jogo deve acontecer apenas depois do usuario aceitar o item na biblioteca e clicar em jogar.
- O app nao deve tentar identificar pirataria, remover protecoes ou modificar instalacoes.

## 18. Interface de Revisao

A tela de revisao deve permitir:

- Ver candidatos por confianca.
- Filtrar por fonte.
- Selecionar varios candidatos.
- Aceitar selecionados.
- Ignorar selecionados.
- Editar nome e caminho antes de aceitar.
- Ver motivo da deteccao.
- Ver caminho local.

Cada candidato deve mostrar:

- Nome sugerido.
- Caminho.
- Fonte.
- Confianca.
- Motivo.
- Icone/capa quando disponivel.

## 19. Testes Recomendados

### Testes Unitarios

- Normalizacao de nomes.
- Exclusao de executaveis de uninstall/update/setup.
- Calculo de confianca.
- Deteccao de duplicados.
- Resolucao de caminhos.

### Testes de Integracao

- Varredura em diretorio simulado com jogos falsos.
- Varredura com atalhos validos e quebrados.
- Varredura com permissoes negadas.
- Cancelamento no meio da busca.
- Consolidacao de duplicados.
- Persistencia de candidatos no SQLite.
- Adicao manual por pasta com um executavel claro.
- Adicao manual por pasta com multiplos executaveis.
- Diretorio de biblioteca adicionado, removido e desativado.

### Cenarios Manuais

- PC com Steam e jogos instalados.
- PC com Epic Games e jogos instalados.
- PC com jogos standalone em `D:\Games`.
- PC com muitos programas em `Program Files`.
- PC offline.
- Jogo removido depois de cadastrado.

## 20. Criterios de Aceite

- A varredura encontra candidatos sem exigir configuracao inicial.
- A interface recebe eventos de progresso.
- O usuario consegue cancelar a varredura.
- Candidatos encontrados sao salvos para revisao.
- O usuario consegue adicionar manualmente um jogo escolhendo apenas a pasta.
- O usuario consegue cadastrar opcionalmente diretorios de biblioteca para varreduras futuras.
- Falsos positivos obvios sao reduzidos por heuristicas.
- Duplicados obvios nao aparecem repetidamente.
- Erros de permissao nao interrompem a varredura completa.
- Nenhum jogo e executado automaticamente.
- Nenhum arquivo do usuario e modificado durante a busca.
