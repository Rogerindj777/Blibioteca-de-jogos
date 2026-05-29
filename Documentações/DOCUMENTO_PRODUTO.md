# Documento de Produto: Biblioteca de Jogos para Windows

## 1. Visao Geral

O aplicativo sera uma biblioteca de jogos para Windows focada em organizar, revisar e iniciar jogos ja instalados no computador do usuario. A experiencia principal deve ser simples: instalar, abrir o app e deixar que ele procure automaticamente jogos locais sem exigir login, conta ou configuracao inicial.

O produto deve funcionar como uma biblioteca pessoal, nao como uma loja. A interface deve ser moderna, escura, com predominancia de preto e azul, inspirada em aplicativos atuais de biblioteca e launcher de jogos, especialmente o padrao visual de bibliotecas como Steam, Playnite e GOG Galaxy.

## 2. Objetivos

- Permitir que o usuario encontre e organize jogos instalados no PC de forma pratica.
- Reduzir configuracao inicial ao minimo, com varredura automatica logo no primeiro uso.
- Oferecer uma biblioteca visual com busca, filtros simples e abertura direta dos jogos.
- Funcionar de forma local e offline para jogos ja cadastrados.
- Permitir correcao manual quando a deteccao automatica falhar ou encontrar falsos positivos.

## 3. Publico-Alvo

O publico inicial e o usuario casual de PC que possui jogos espalhados por diferentes launchers, pastas e discos, mas nao quer configurar uma biblioteca manualmente.

Esse usuario valoriza:

- Instalacao simples.
- Interface bonita e facil de entender.
- Organizacao automatica.
- Poucos passos para abrir um jogo.
- Controle manual apenas quando necessario.

## 4. Principios do Produto

- **Local primeiro:** a biblioteca deve existir e funcionar sem conta, login ou sincronizacao em nuvem.
- **Zero configuracao inicial:** o app deve iniciar a busca automaticamente no primeiro uso.
- **Usuario no controle:** achados da varredura devem passar por revisao antes de entrar definitivamente na biblioteca.
- **Offline por padrao:** jogos cadastrados devem continuar acessiveis sem internet.
- **Metadados opcionais:** capas, nomes e detalhes online devem melhorar a experiencia, mas nunca bloquear o uso basico.
- **Postura neutra sobre origem:** o app cataloga jogos e executaveis existentes na maquina, sem validar licenca e sem facilitar obtencao ilegal de jogos.

## 5. Escopo do MVP

### Incluido

- Aplicativo desktop para Windows.
- Varredura ampla por jogos instalados.
- Tela de revisao dos jogos encontrados.
- Biblioteca em grade com capas ou placeholders.
- Busca textual por nome.
- Filtros simples, como todos, favoritos e ocultos.
- Cadastro manual simplificado escolhendo apenas a pasta do jogo.
- Cadastro opcional de diretorios de biblioteca para futuras instalacoes e varreduras.
- Edicao basica de jogo cadastrado.
- Marcar como favorito.
- Ocultar jogo da biblioteca principal.
- Botao para iniciar jogo.
- Funcionamento offline para biblioteca local.
- Busca online opcional de metadados quando houver internet.
- Detalhes enriquecidos do jogo, incluindo genero, sinopse, desenvolvedora, publicadora, ano de lancamento e imagens.

### Fora do MVP

- Conta de usuario.
- Sincronizacao em nuvem.
- Loja ou compra de jogos.
- Chat, amigos ou recursos sociais.
- Conquistas.
- Overlay dentro dos jogos.
- Atualizacao automatica de jogos.
- Download de jogos.
- Links, cracks, tutoriais ou qualquer mecanismo relacionado a pirataria.
- Suporte completo a controles ou modo TV.

## 6. Experiencia Principal

### Primeiro Uso

Ao abrir o app pela primeira vez, o usuario deve ver uma interface de boas-vindas direta e visualmente integrada ao produto. A busca por jogos deve comecar automaticamente, sem exigir que o usuario escolha pastas ou conecte contas.

Durante a varredura, a interface deve mostrar:

- Progresso geral.
- Locais sendo analisados.
- Quantidade de possiveis jogos encontrados.
- Opcao para cancelar e adicionar jogos manualmente por pasta depois.

### Varredura

A varredura ampla deve procurar indicios de jogos instalados em:

- Atalhos do menu iniciar e area de trabalho.
- Pastas comuns de programas.
- Pastas comuns de jogos.
- Bibliotecas conhecidas de launchers instalados.
- Discos e diretorios com padroes comuns de instalacao de jogos.
- Executaveis com nomes e estruturas compativeis com jogos.

Como a varredura ampla pode gerar falsos positivos, o app nao deve adicionar tudo automaticamente sem revisao.

### Revisao dos Achados

Apos a busca, o usuario deve revisar uma lista de candidatos. Cada item deve exibir:

- Nome sugerido.
- Caminho local.
- Origem detectada, quando houver.
- Nivel de confianca ou indicacao visual semelhante.
- Capa encontrada ou placeholder.
- Acao para adicionar.
- Acao para ignorar.
- Acao para editar antes de adicionar.

O usuario deve poder adicionar todos os itens confiaveis de uma vez, mas tambem remover falsos positivos individualmente.

### Biblioteca

A tela principal deve ser a biblioteca do usuario. Ela deve priorizar uma grade visual de jogos com:

- Capa.
- Nome.
- Estado de favorito.
- Indicacao de origem quando relevante.
- Destaque para jogos recentes ou favoritos.

A navegacao deve incluir:

- Sidebar com secoes principais.
- Busca sempre acessivel.
- Filtros simples.
- Botao para adicionar jogo manualmente por pasta.
- Botao para nova varredura.
- Opcao para cadastrar um diretorio de biblioteca, caso o usuario queira monitorar ou reutilizar uma pasta onde costuma instalar jogos.

### Adicionar Jogo Manualmente

O fluxo manual deve ser simples. O usuario deve escolher a pasta do jogo e o app deve tentar identificar sozinho o executavel principal, nome, icone, capa e metadados.

Fluxo esperado:

- Usuario clica em adicionar jogo.
- Usuario escolhe a pasta onde o jogo esta instalado.
- App analisa a pasta e sugere o executavel principal.
- Se houver mais de um executavel provavel, o app pede confirmacao.
- Jogo e adicionado ou enviado para revisao com os dados encontrados.

O usuario nao deve precisar preencher caminho do executavel, diretorio de trabalho ou argumentos no fluxo basico. Esses campos podem existir em edicao avancada.

### Diretorios de Biblioteca Opcionais

Apos a varredura completa, o app deve permitir que o usuario adicione diretorios de biblioteca. Esses diretorios representam pastas onde os jogos estao ou serao instalados futuramente.

Exemplos:

- `D:\Games`
- `E:\Jogos`
- `C:\Users\<usuario>\Games`

Esse recurso deve ser opcional. O app continua funcionando sem nenhum diretorio configurado manualmente.

Quando um diretorio de biblioteca for cadastrado:

- Ele deve aparecer nas proximas varreduras.
- O usuario pode remover o diretorio depois.
- O app pode priorizar esse diretorio em buscas futuras.
- O app nao deve instalar jogos nessa pasta; apenas usa o caminho como fonte de busca.

### Detalhes do Jogo

Ao selecionar um jogo, o usuario deve ver uma tela ou painel de detalhes com:

- Nome do jogo.
- Capa/banner.
- Genero ou generos do jogo.
- Sinopse curta.
- Desenvolvedora e publicadora, quando encontradas.
- Ano ou data de lancamento, quando encontrada.
- Plataforma/origem local detectada.
- Caminho do executavel.
- Origem detectada.
- Botao principal para jogar.
- Acoes para editar, favoritar, ocultar ou remover da biblioteca.

### Metadados e Sinopse Online

Quando houver internet, o app deve tentar enriquecer os jogos cadastrados com informacoes publicas encontradas online. Esse processo deve ser opcional, discreto e nunca impedir o uso da biblioteca local.

Os metadados desejados sao:

- Nome oficial do jogo.
- Capa principal.
- Banner ou imagem de fundo.
- Genero ou generos.
- Sinopse curta.
- Desenvolvedora.
- Publicadora.
- Ano ou data de lancamento.
- Serie/franquia, quando aplicavel.
- Links de referencia, se forem uteis para auditoria interna do cadastro.

A busca de metadados deve respeitar algumas regras de produto:

- O usuario deve poder editar qualquer informacao preenchida automaticamente.
- Quando houver mais de um resultado possivel, o app deve pedir confirmacao em vez de substituir dados silenciosamente.
- A sinopse deve ser curta, clara e adequada para aparecer na tela de detalhes.
- Dados ja salvos localmente devem continuar disponiveis offline.
- Falhas de internet, APIs ou resultados ausentes devem gerar apenas estados discretos, sem bloquear o usuario.

Para evitar uma experiencia confusa, o app deve diferenciar visualmente:

- Dados detectados localmente.
- Dados preenchidos automaticamente pela internet.
- Dados editados manualmente pelo usuario.

## 7. Direcao Visual

A interface deve usar uma estetica escura, moderna e funcional.

### Paleta

- Preto e tons de cinza muito escuros como base.
- Azul como cor principal de acao e destaque.
- Branco ou cinza claro para texto principal.
- Cinza medio para textos secundarios.
- Cores de alerta apenas quando necessario.

### Layout

- Sidebar fixa ou semi-fixa para navegacao.
- Area central com grade de jogos.
- Cards de jogos bem definidos, sem excesso de bordas decorativas.
- Detalhes do jogo em painel lateral ou pagina dedicada.
- Interface densa o suficiente para biblioteca, mas sem parecer ferramenta tecnica.

### Tom Visual

O app deve parecer uma biblioteca pessoal premium:

- Mais organizacao do que marketing.
- Mais utilidade do que vitrine.
- Visual atual, mas sem poluicao.
- Inspirado em launchers modernos, sem copiar marcas ou identidades especificas.

## 8. Politica Sobre Jogos Locais e Origem

O app deve tratar jogos como itens locais cadastraveis pelo usuario. Ele nao deve verificar se um jogo e original, pirata, comprado, gratuito ou instalado por outro meio.

O produto pode aceitar qualquer executavel ou atalho local como item de biblioteca, desde que ja exista na maquina do usuario.

O app nao deve:

- Ensinar a baixar jogos ilegalmente.
- Fornecer cracks, chaves, ativadores ou links suspeitos.
- Remover DRM.
- Modificar arquivos de jogos para burlar protecoes.
- Prometer compatibilidade com conteudo ilegal.

A comunicacao recomendada e: "O aplicativo organiza jogos e atalhos existentes no seu computador. Ele nao fornece, modifica ou valida licencas de jogos."

## 9. Estados e Fluxos Importantes

### Biblioteca Vazia

Quando nao houver jogos cadastrados, o app deve oferecer:

- Iniciar varredura.
- Adicionar jogo manualmente escolhendo uma pasta.
- Mensagem curta explicando que a biblioteca e local.

### Nenhum Jogo Encontrado

Se a varredura nao encontrar jogos, o app deve:

- Informar que nada foi encontrado.
- Oferecer adicionar manualmente escolhendo a pasta do jogo.
- Oferecer adicionar um diretorio de biblioteca opcional para futuras varreduras.

### Jogo Removido ou Movido

Se o usuario tentar abrir um jogo cujo caminho nao existe mais, o app deve:

- Informar que o arquivo nao foi encontrado.
- Oferecer localizar novo executavel.
- Permitir remover ou manter o item na biblioteca.

### Sem Internet

Se nao houver internet:

- A biblioteca local deve continuar funcionando.
- Capas ja salvas devem continuar aparecendo.
- Busca de metadados deve ser ignorada ou marcada como indisponivel.

## 10. Criterios de Aceite

- Ao abrir pela primeira vez, o app inicia uma busca por jogos sem exigir configuracao.
- O usuario consegue cancelar a varredura inicial.
- O usuario consegue revisar os jogos encontrados antes de adiciona-los.
- O usuario consegue adicionar um jogo manualmente escolhendo apenas a pasta do jogo.
- O usuario consegue cadastrar opcionalmente um diretorio de biblioteca para futuras varreduras.
- O usuario consegue editar nome, caminho e capa de um jogo.
- O usuario consegue ocultar e favoritar jogos.
- O usuario consegue iniciar um jogo cadastrado.
- A biblioteca continua acessivel sem internet.
- A busca online de metadados nao bloqueia a biblioteca local.
- O app deixa claro que apenas organiza jogos existentes e nao facilita obtencao ilegal.

## 11. Cenarios de Teste

### PC com Launchers Instalados

Validar deteccao de jogos em ambientes com Steam, Epic Games, GOG, Battle.net, Xbox App ou similares.

### PC com Jogos Standalone

Validar deteccao de jogos instalados em pastas comuns e personalizadas, como `C:\Games`, `D:\Games` e diretorios criados pelo usuario.

### PC com Muitos Executaveis

Validar se a tela de revisao ajuda o usuario a separar jogos reais de falsos positivos.

### Uso Offline

Validar se a biblioteca abre, exibe jogos ja cadastrados e permite iniciar executaveis locais sem internet.

### Jogo Movido

Validar comportamento quando o caminho salvo nao existe mais.

### Cancelamento de Varredura

Validar se o usuario pode cancelar a busca inicial e continuar usando o app com cadastro manual.

## 12. Metricas de Sucesso

- Usuario consegue chegar a uma biblioteca utilizavel em poucos minutos.
- Baixa quantidade de passos ate iniciar o primeiro jogo.
- Falsos positivos sao faceis de remover na revisao.
- Usuario entende que a biblioteca e local e privada.
- O app continua util mesmo sem metadados online.
- A maioria dos jogos reconhecidos recebe capa, genero e sinopse sem edicao manual.
- O usuario consegue corrigir metadados incorretos rapidamente.

## 13. Ideias de Melhorias Futuras

As melhorias abaixo podem ser discutidas e priorizadas depois do MVP. Elas nao devem bloquear a primeira versao, mas ajudam a dar uma direcao mais ambiciosa ao produto.

- **Colecoes inteligentes:** agrupar automaticamente por genero, launcher, pasta, favoritos, jogos recentes ou jogos sem capa.
- **Status pessoal:** permitir marcar jogos como jogando, zerado, abandonado, quero jogar ou pausado.
- **Tempo de jogo:** registrar tempo aproximado quando o jogo for iniciado pelo app.
- **Notas e avaliacao pessoal:** permitir que o usuario de nota, escreva observacoes e registre progresso.
- **Capas customizadas:** permitir trocar capa, banner e icone por arquivos locais.
- **Perfis de visualizacao:** alternar entre grade, lista compacta e modo destaque.
- **Importacao de launchers:** ler bibliotecas conhecidas com mais precisao quando Steam, Epic, GOG ou similares estiverem instalados.
- **Backup local/exportacao:** exportar a biblioteca para arquivo e importar em outro PC sem depender de nuvem.
- **Deteccao de duplicados:** avisar quando o mesmo jogo parecer existir em mais de uma pasta ou launcher.
- **Modo privacidade:** ocultar caminhos completos, jogos ocultos ou informacoes sensiveis em capturas de tela.
- **Atalhos rapidos:** criar favoritos na pagina inicial para abrir jogos recentes com poucos cliques.
- **Filtros avancados:** filtrar por genero, ano, desenvolvedora, publicadora, origem, favorito e status pessoal.

## 14. Proximos Documentos

Depois deste documento de produto, os proximos documentos recomendados sao:

- Documento tecnico de arquitetura.
- Especificacao da varredura de jogos no Windows.
- Wireframes das telas principais.
- Documento de modelo de dados local.
- Especificacao de metadados online e preenchimento de sinopse.
- Plano de empacotamento e instalacao para Windows.
