# Wireframes das Telas Principais

## 1. Objetivo

Este documento define os wireframes iniciais do aplicativo de biblioteca de jogos para Windows. Os wireframes descrevem estrutura, hierarquia de informacao e principais acoes de cada tela, sem definir ainda o design visual final.

A interface deve seguir a direcao visual do produto: tema escuro, azul como cor de destaque, navegacao simples e foco em biblioteca pessoal.

## 2. Estrutura Geral do App

A maioria das telas principais deve seguir a mesma base:

```text
+--------------------------------------------------------------------------------+
| Topbar: busca, acoes rapidas, status online/offline, controles de janela         |
+----------------------+|--------------------------------------------------------+
| Sidebar              || Conteudo principal                                     |
|                      ||                                                        |
| - Biblioteca         || Titulo da tela                                         |
| - Favoritos          || Filtros / acoes                                        |
| - Recentes           || Area principal                                         |
| - Ocultos            ||                                                        |
| - Varredura          ||                                                        |
| - Configuracoes      ||                                                        |
|                      ||                                                        |
+----------------------+|--------------------------------------------------------+
```

O divisor `|` entre sidebar e conteudo deve ser arrastavel. O usuario pode clicar, segurar e mover para a esquerda ou direita para deixar a sidebar mais estreita ou mais larga.

### Sidebar

Itens recomendados:

- Biblioteca.
- Favoritos.
- Recentes.
- Ocultos.
- Revisao de achados, quando houver candidatos pendentes.
- Nova varredura.
- Configuracoes.

Comportamento de largura:

- Largura padrao confortavel para textos completos.
- Largura minima compacta para quem quer mais espaco na biblioteca.
- Largura maxima limitada para nao prejudicar o conteudo principal.
- O ajuste deve ser feito arrastando o divisor lateral.
- A ultima largura escolhida deve ser lembrada ao reabrir o app.
- Em modo estreito, a sidebar pode mostrar icones com textos reduzidos ou ocultos.

### Topbar

Elementos recomendados:

- Campo de busca.
- Botao para adicionar jogo manualmente por pasta.
- Botao para iniciar nova varredura.
- Opcao para adicionar diretorio de biblioteca.
- Controle de tamanho dos icones: pequeno, medio e grande.
- Indicador discreto de online/offline.

## 3. Tela de Primeiro Uso

Objetivo: apresentar o app e iniciar a busca sem exigir configuracao.

```text
+--------------------------------------------------------------------------------+
|                                                                                |
|                         Biblioteca de Jogos                                    |
|               Organize e abra seus jogos em um so lugar.                       |
|                                                                                |
|        +--------------------------------------------------------------+        |
|        | A busca inicial vai procurar jogos instalados no seu PC.     |        |
|        | Nada sera adicionado sem sua revisao.                        |        |
|        +--------------------------------------------------------------+        |
|                                                                                |
|                      [ Iniciar busca agora ]                                    |
|                      [ Adicionar por pasta ]                                    |
|                                                                                |
|        Texto discreto: Biblioteca local, sem conta e sem sincronizacao.         |
|                                                                                |
+--------------------------------------------------------------------------------+
```

Comportamento:

- Na primeira execucao, a busca pode iniciar automaticamente depois de uma breve introducao.
- O usuario pode cancelar ou adicionar manualmente escolhendo uma pasta.
- O texto deve reforcar que nada sera adicionado sem revisao.

## 4. Tela de Varredura em Andamento

Objetivo: mostrar progresso e manter o usuario informado.

```text
+----------------------+---------------------------------------------------------+
| Biblioteca           | Procurando jogos instalados                             |
| Favoritos            |                                                         |
| Recentes             | [ barra de progresso aproximado                    ]    |
| Ocultos              |                                                         |
| Varredura            | Fonte atual: Atalhos do Menu Iniciar                    |
| Configuracoes        | Caminho atual: C:\Program Files\...                     |
|                      |                                                         |
|                      | Candidatos encontrados: 12                              |
|                      | Arquivos analisados: 4.381                              |
|                      |                                                         |
|                      | +---------------------------------------------------+   |
|                      | | Achados recentes                                  |   |
|                      | | - Hollow Knight                                   |   |
|                      | | - Stardew Valley                                  |   |
|                      | | - Game.exe em D:\Games\...                        |   |
|                      | +---------------------------------------------------+   |
|                      |                                                         |
|                      | [ Cancelar busca ]                                      |
+----------------------+---------------------------------------------------------+
```

Comportamento:

- A lista de achados recentes deve atualizar progressivamente.
- Cancelar deve preservar candidatos ja encontrados.
- Erros de permissao devem ser discretos e nao interromper a busca.

## 5. Tela de Revisao de Achados

Objetivo: permitir que o usuario aceite, ignore ou edite candidatos antes de entrarem na biblioteca.

```text
+----------------------+---------------------------------------------------------+
| Biblioteca           | Revisao de jogos encontrados                            |
| Favoritos            |                                                         |
| Recentes             | [ Buscar candidato... ] [ Fonte v ] [ Confianca v ]     |
| Ocultos              |                                                         |
| Revisao (18)         | [ Aceitar selecionados ] [ Ignorar selecionados ]        |
| Nova varredura       |                                                         |
| Configuracoes        | +---+------------------+------------+----------+------+ |
|                      | |   | Nome sugerido    | Fonte      | Conf.    | Acoes| |
|                      | +---+------------------+------------+----------+------+ |
|                      | | x | Hades            | Steam      | Alta     | Edit | |
|                      | | x | Celeste          | Atalho     | Alta     | Edit | |
|                      | |   | launcher.exe      | D:\Games   | Media    | Edit | |
|                      | |   | setup.exe         | Programa   | Baixa    | Ign. | |
|                      | +---+------------------+------------+----------+------+ |
|                      |                                                         |
|                      | Painel lateral: motivo, caminho, icone/capa, detalhes.  |
+----------------------+---------------------------------------------------------+
```

Informacoes por candidato:

- Nome sugerido.
- Caminho do executavel.
- Fonte detectada.
- Confianca.
- Motivo da deteccao.
- Icone ou capa.
- Acoes: aceitar, ignorar, editar.

## 6. Biblioteca Principal

Objetivo: ser a tela central do app, com acesso rapido aos jogos.

```text
+----------------------+---------------------------------------------------------+
| Biblioteca           | Biblioteca                                               |
| Favoritos            |                                                         |
| Recentes             | [ Buscar jogo...             ] [ Todos v ] [ Genero v ] |
| Ocultos              |                                      [ + ] [ Scan ] [S M G]|
| Nova varredura       |                                                         |
| Configuracoes        | Continuar jogando                                       |
|                      | +-------------+ +-------------+ +-------------+         |
|                      | |    CAPA     | |    CAPA     | |    CAPA     |         |
|                      | | Jogo A      | | Jogo B      | | Jogo C      |         |
|                      | +-------------+ +-------------+ +-------------+         |
|                      |                                                         |
|                      | Todos os jogos                                           |
|                      | +-------------+ +-------------+ +-------------+         |
|                      | |    CAPA     | |    CAPA     | |    CAPA     |         |
|                      | | Jogo D      | | Jogo E      | | Jogo F      |         |
|                      | +-------------+ +-------------+ +-------------+         |
+----------------------+---------------------------------------------------------+
```

Acoes principais:

- Buscar.
- Filtrar.
- Alterar tamanho dos icones.
- Adicionar jogo manualmente por pasta.
- Nova varredura.
- Adicionar diretorio de biblioteca opcional.
- Abrir detalhes do jogo.
- Iniciar jogo por acao rapida, quando aplicavel.

## 7. Tamanhos de Icones da Biblioteca

O controle de visualizacao deve ter tres estados: pequeno, medio e grande.

### Icones Pequenos

Uso: bibliotecas grandes, usuario quer ver muitos jogos por tela.

```text
+---------------------------------------------------------+
| [capa] Nome curto       [capa] Nome curto               |
| [capa] Nome curto       [capa] Nome curto               |
| [capa] Nome curto       [capa] Nome curto               |
| [capa] Nome curto       [capa] Nome curto               |
+---------------------------------------------------------+
```

Caracteristicas:

- Mais colunas.
- Capas menores.
- Nome em uma ou duas linhas.
- Menos metadados visiveis.

### Icones Medios

Uso: modo padrao recomendado.

```text
+---------------------------------------------------------+
| +----------+  +----------+  +----------+  +----------+  |
| |  CAPA    |  |  CAPA    |  |  CAPA    |  |  CAPA    |  |
| | Jogo A   |  | Jogo B   |  | Jogo C   |  | Jogo D   |  |
| +----------+  +----------+  +----------+  +----------+  |
+---------------------------------------------------------+
```

Caracteristicas:

- Equilibrio entre quantidade e leitura.
- Capa clara.
- Nome legivel.
- Favorito e origem podem aparecer como pequenos indicadores.

### Icones Grandes

Uso: experiencia mais visual e confortavel.

```text
+---------------------------------------------------------+
| +------------------+  +------------------+              |
| |                  |  |                  |              |
| |       CAPA       |  |       CAPA       |              |
| |                  |  |                  |              |
| | Nome do Jogo     |  | Nome do Jogo     |              |
| | Genero / Origem  |  | Genero / Origem  |              |
| +------------------+  +------------------+              |
+---------------------------------------------------------+
```

Caracteristicas:

- Menos jogos por tela.
- Capas maiores.
- Pode mostrar genero, origem ou ultimo acesso.
- Melhor para navegacao casual.

## 8. Painel de Detalhes do Jogo

Objetivo: concentrar informacoes e acoes de um jogo selecionado.

```text
+----------------------+---------------------------------------------------------+
| Biblioteca           | +-----------------------------------------------------+ |
| Favoritos            | | Banner / imagem de fundo                            | |
| Recentes             | +-----------------------------------------------------+ |
| Ocultos              |                                                         |
| Nova varredura       | Nome do Jogo                              [ Jogar ]     |
| Configuracoes        | Genero: Acao, Aventura                                 |
|                      | Desenvolvedora: Nome                                  |
|                      | Publicadora: Nome                                     |
|                      | Lancamento: 2020                                      |
|                      |                                                         |
|                      | Sinopse                                               |
|                      | Texto curto descrevendo o jogo em poucas linhas.       |
|                      |                                                         |
|                      | Caminho                                               |
|                      | C:\Games\NomeDoJogo\game.exe                          |
|                      |                                                         |
|                      | [ Editar ] [ Favoritar ] [ Ocultar ] [ Remover ]       |
+----------------------+---------------------------------------------------------+
```

Estados:

- Com metadados completos.
- Sem capa/banner.
- Sem sinopse.
- Executavel nao encontrado.
- Jogo oculto.

## 9. Modal de Adicionar Jogo Manualmente

Objetivo: permitir cadastro manual simples quando a varredura nao encontrar algo. O usuario deve escolher apenas a pasta do jogo; o app tenta encontrar o executavel principal sozinho.

```text
+------------------------------------------------------------+
| Adicionar jogo                                             |
|                                                            |
| Escolha a pasta onde o jogo esta instalado                 |
| [ C:\Games\NomeDoJogo                    ] [ Procurar ]   |
|                                                            |
| [ ] Buscar capa e sinopse online                           |
|                                                            |
| Texto discreto: O app vai procurar o executavel principal. |
|                                                            |
|                         [ Cancelar ] [ Analisar pasta ]    |
+------------------------------------------------------------+
```

Comportamento:

- A pasta do jogo e obrigatoria.
- Nome, executavel e diretorio de trabalho devem ser sugeridos automaticamente.
- Se houver apenas um executavel provavel, o app pode adicionar o jogo direto ou mostrar confirmacao simples.
- Se houver varios executaveis provaveis, o app mostra uma etapa curta para escolher.
- Busca online e opcional.
- Depois de adicionar, abrir detalhes do jogo.
- Campos tecnicos, como argumentos e diretorio de trabalho, ficam na edicao avancada.

### Escolha de Executavel Quando Necessario

Essa etapa aparece somente se a pasta tiver mais de um executavel provavel.

```text
+------------------------------------------------------------+
| Escolha como iniciar o jogo                                |
|                                                            |
| Encontramos mais de uma opcao nesta pasta.                 |
|                                                            |
| ( ) NomeDoJogo.exe       Alta confianca                    |
| ( ) Launcher.exe         Media confianca                   |
| ( ) ConfigTool.exe       Baixa confianca                   |
|                                                            |
|                         [ Voltar ] [ Adicionar jogo ]      |
+------------------------------------------------------------+
```

## 10. Modal de Adicionar Diretorio de Biblioteca

Objetivo: permitir que o usuario cadastre opcionalmente uma pasta onde jogos estao ou serao instalados.

```text
+------------------------------------------------------------+
| Adicionar diretorio de biblioteca                          |
|                                                            |
| Escolha uma pasta onde voce costuma instalar jogos.        |
|                                                            |
| Diretorio                                                  |
| [ D:\Games                               ] [ Procurar ]    |
|                                                            |
| Nome opcional                                              |
| [ Jogos do disco D                       ]                 |
|                                                            |
| [ ] Varrer essa pasta agora                                |
|                                                            |
| Texto discreto: Isso e opcional e pode ser removido depois.|
|                                                            |
|                         [ Cancelar ] [ Salvar diretorio ]  |
+------------------------------------------------------------+
```

Comportamento:

- O diretorio cadastrado entra em varreduras futuras.
- Se "Varrer essa pasta agora" estiver marcado, o app inicia uma varredura limitada ao diretorio.
- Remover um diretorio nao remove jogos ja cadastrados.
- O app deve permitir desativar um diretorio sem apagar o cadastro.

## 11. Modal de Editar Jogo

Objetivo: corrigir dados locais e metadados.

```text
+------------------------------------------------------------+
| Editar jogo                                                |
|                                                            |
| Nome                                                       |
| [________________________________________]                 |
|                                                            |
| Genero                                                     |
| [________________________________________]                 |
|                                                            |
| Sinopse                                                    |
| [____________________________________________________]     |
| [____________________________________________________]     |
|                                                            |
| Capa                                                       |
| [ caminho/preview                         ] [ Trocar ]     |
|                                                            |
| Executavel                                                 |
| [ C:\...\game.exe                         ] [ Procurar ]   |
|                                                            |
| [ Buscar metadados novamente ]                             |
|                                                            |
|                         [ Cancelar ] [ Salvar ]            |
+------------------------------------------------------------+
```

Campos editaveis:

- Nome.
- Genero.
- Sinopse.
- Desenvolvedora.
- Publicadora.
- Data de lancamento.
- Capa.
- Banner.
- Executavel.
- Argumentos.
- Diretorio de trabalho.

## 12. Tela de Biblioteca Vazia

Objetivo: orientar o usuario sem parecer erro.

```text
+----------------------+---------------------------------------------------------+
| Biblioteca           |                                                         |
| Favoritos            |                Sua biblioteca esta vazia                |
| Recentes             |                                                         |
| Ocultos              |        Procure jogos instalados ou adicione manualmente. |
| Nova varredura       |                                                         |
| Configuracoes        |        [ Procurar jogos ] [ Adicionar por pasta ]         |
|                      |        [ Adicionar diretorio de biblioteca ]              |
|                      |                                                         |
+----------------------+---------------------------------------------------------+
```

## 13. Tela de Nenhum Resultado na Busca

Objetivo: informar que filtros ou busca nao retornaram jogos.

```text
+----------------------+---------------------------------------------------------+
| Biblioteca           | Biblioteca                                               |
| Favoritos            |                                                         |
| Recentes             | [ termo pesquisado...                            ]       |
| Ocultos              |                                                         |
| Nova varredura       |              Nenhum jogo encontrado                     |
| Configuracoes        |       Tente outro termo ou limpe os filtros.             |
|                      |                                                         |
|                      |              [ Limpar filtros ]                          |
+----------------------+---------------------------------------------------------+
```

## 14. Tela de Configuracoes

Objetivo: concentrar preferencias locais.

```text
+----------------------+---------------------------------------------------------+
| Biblioteca           | Configuracoes                                           |
| Favoritos            |                                                         |
| Recentes             | Aparencia                                               |
| Ocultos              | Tema: Escuro azul                                       |
| Nova varredura       | Tamanho dos icones: [ Pequeno ] [ Medio ] [ Grande ]    |
| Configuracoes        | Largura da sidebar: Ajustada arrastando a divisoria     |
|                      | Biblioteca                                              |
|                      | [ ] Buscar metadados online automaticamente             |
|                      | Diretorios de biblioteca                                |
|                      | - D:\Games                         [ Desativar ] [ X ]  |
|                      | [ Adicionar diretorio ]                                 |
|                      | [ Limpar cache de imagens ]                             |
|                      |                                                         |
|                      | Varredura                                               |
|                      | [ Nova varredura completa ]                             |
|                      | Ultima varredura: 28/05/2026                            |
+----------------------+---------------------------------------------------------+
```

Preferencias iniciais:

- Tamanho dos icones.
- Largura da sidebar por arrastar.
- Diretorios de biblioteca opcionais.
- Busca de metadados online.
- Limpar cache.
- Nova varredura.

## 15. Tela de Executavel Nao Encontrado

Objetivo: lidar com jogos movidos ou removidos.

```text
+------------------------------------------------------------+
| Nao foi possivel abrir o jogo                              |
|                                                            |
| O executavel salvo nao foi encontrado:                     |
| C:\Games\NomeDoJogo\game.exe                              |
|                                                            |
| [ Localizar novo executavel ] [ Manter cadastro ] [ Remover ]|
+------------------------------------------------------------+
```

Comportamento:

- Localizar novo executavel atualiza o cadastro.
- Manter cadastro fecha o aviso.
- Remover pede confirmacao antes de apagar da biblioteca.

## 16. Prioridade de Implementacao das Telas

### MVP Essencial

- Biblioteca vazia.
- Varredura em andamento.
- Revisao de achados.
- Biblioteca principal.
- Detalhes do jogo.
- Adicionar jogo manualmente por pasta.
- Editar jogo.

### MVP Polido

- Tamanhos de icones.
- Sidebar redimensionavel.
- Adicionar diretorio de biblioteca.
- Nenhum resultado.
- Executavel nao encontrado.
- Configuracoes basicas.

### Futuro

- Tela de colecoes inteligentes.
- Tela de estatisticas.
- Tela de backup/exportacao.
- Modo visual de destaque ou tela cheia.
