# Especificacao de Metadados Online e Preenchimento de Sinopse

## 1. Objetivo

Definir como o aplicativo deve buscar, sugerir, aplicar e armazenar metadados online dos jogos, incluindo capa, banner, genero, desenvolvedora, publicadora, data de lancamento e sinopse curta.

Esse recurso deve melhorar a biblioteca, mas nunca bloquear o uso local do app. O usuario deve conseguir adicionar, organizar e abrir jogos mesmo sem internet ou sem resultados online.

## 2. Principios

- **Opcional:** a busca online deve poder ser desativada.
- **Local-first:** dados aplicados ao jogo devem ser salvos localmente.
- **Editavel:** qualquer metadado preenchido automaticamente pode ser corrigido pelo usuario.
- **Nao bloqueante:** falhas de internet ou de API nao impedem cadastro local.
- **Confirmacao quando incerto:** multiplos resultados devem ser apresentados para escolha.
- **Privacidade:** caminhos locais do usuario nao devem ser enviados para fontes externas.
- **Cache:** respostas e imagens devem ser cacheadas para reduzir chamadas repetidas.
- **Fonte substituivel:** o app deve usar uma interface abstrata de provider para permitir trocar ou combinar fontes no futuro.

## 3. Dados Desejados

Metadados principais:

- Nome oficial.
- Capa.
- Banner ou imagem de fundo.
- Genero ou generos.
- Sinopse curta.
- Desenvolvedora.
- Publicadora.
- Ano ou data de lancamento.
- Serie ou franquia.
- Identificador externo da fonte.

Metadados opcionais futuros:

- Plataformas.
- Modos de jogo.
- Capturas de tela.
- Avaliacoes publicas.
- Tempo medio de jogo.
- Classificacao indicativa.

## 4. Momentos de Busca

### Apos Aceitar Candidato

Quando o usuario aceita um candidato da varredura, o app pode sugerir busca online usando o nome detectado.

### Apos Adicionar por Pasta

Quando o usuario adiciona jogo por pasta, o app deve tentar usar:

- Nome da pasta.
- Nome do executavel principal.
- Nome extraido de atalho ou metadado local, se houver.

### Edicao Manual

Na tela de edicao, o usuario deve ter uma acao "Buscar metadados novamente".

### Biblioteca Existente

Em versao futura, o app pode oferecer preenchimento em lote para jogos sem capa ou sem sinopse.

## 5. Consulta e Privacidade

O app deve enviar para providers externos apenas termos de busca relacionados ao nome provavel do jogo.

Nao enviar:

- Caminho local completo.
- Nome do usuario do Windows.
- Lista completa da biblioteca.
- Origem sensivel do arquivo.
- Informacoes sobre licenca ou procedencia do jogo.

Permitido enviar:

- Nome sugerido do jogo.
- Ano aproximado, se ja estiver disponivel sem expor caminho.
- Provider externo escolhido.

## 6. Provider de Metadados

A implementacao deve usar uma interface abstrata.

Contrato conceitual:

```text
MetadataProvider
  search(query) -> MetadataSearchResult[]
  get_details(provider_game_id) -> MetadataDetails
  download_asset(url, type) -> LocalAsset
```

Cada resultado de busca deve conter:

- `provider`
- `provider_game_id`
- `title`
- `release_date`
- `cover_url`
- `confidence_score`
- `summary_preview`

Detalhes completos devem conter:

- `title`
- `genres`
- `summary`
- `developer`
- `publisher`
- `release_date`
- `franchise`
- `cover_url`
- `banner_url`
- `raw_payload`

## 7. Pontuacao de Confianca

Cada resultado online deve receber pontuacao de confianca de `0` a `100`.

Sinais positivos:

- Titulo muito parecido com o nome detectado.
- Mesmo ano aproximado, quando conhecido.
- Resultado possui capa e sinopse.
- Provider retorna identificador estavel.
- Nome nao parece DLC, demo, soundtrack ou editor.

Sinais negativos:

- Titulo muito diferente.
- Resultado parece DLC ou expansao.
- Resultado sem capa e sem sinopse.
- Resultado duplicado.
- Resultado com plataforma claramente incompatvel, quando essa informacao existir.

Faixas:

- `80-100`: pode sugerir preenchimento automatico com confirmacao discreta.
- `50-79`: mostrar opcoes para o usuario escolher.
- `0-49`: nao aplicar automaticamente; exibir apenas se o usuario abrir resultados avancados.

## 8. Sinopse

A sinopse exibida no app deve ser curta, clara e apropriada para a tela de detalhes.

Regras:

- Preferir sinopse oficial curta quando a fonte permitir uso.
- Evitar textos longos demais.
- Remover HTML, markdown externo e quebras excessivas.
- Limitar a sinopse exibida a poucas linhas na UI.
- Salvar o texto completo permitido pela fonte, se fizer sentido, mas exibir resumo curto.
- Permitir edicao manual.

Quando a fonte retornar descricao longa:

- O app pode truncar visualmente.
- O app nao deve inventar informacoes.
- O app nao deve gerar uma sinopse nova como se fosse oficial sem indicar que foi resumida.

## 9. Fluxo de Aplicacao de Metadados

```text
Jogo detectado/adicionado
  -> gerar query segura
  -> consultar provider
  -> calcular confianca
  -> se resultado unico confiavel: sugerir aplicar
  -> se multiplos resultados: usuario escolhe
  -> baixar/cachear imagens selecionadas
  -> copiar dados principais para games
  -> registrar match em game_metadata_matches
```

O usuario deve poder:

- Aplicar tudo.
- Aplicar apenas imagens.
- Aplicar apenas texto.
- Ignorar sugestao.
- Corrigir manualmente depois.

## 10. Cache Local

O app deve usar `metadata_cache` para respostas online.

Regras:

- Cachear buscas para reduzir chamadas repetidas.
- Cachear detalhes aplicados.
- Cachear imagens localmente.
- Dados aplicados ao jogo devem ser copiados para `games`.
- Remover cache nao deve apagar dados ja aplicados ao jogo, exceto imagens se o usuario confirmar.

Imagens:

- Capas e banners devem ser baixados para pasta local do app.
- Preferir nomes internos estaveis, nao nomes originais da web.
- Registrar origem em `game_assets` quando aplicavel.

## 11. Estados de UI

Estados esperados:

- `Nao buscado`.
- `Buscando metadados`.
- `Resultado encontrado`.
- `Multiplos resultados`.
- `Nenhum resultado`.
- `Sem internet`.
- `Erro temporario`.
- `Metadados aplicados`.
- `Editado manualmente`.

Mensagens devem ser discretas e nao interromper o uso principal.

## 12. Edicao e Origem dos Dados

O app deve diferenciar:

- Dados detectados localmente.
- Dados vindos da internet.
- Dados editados manualmente.

Se o usuario editar um campo manualmente:

- O app nao deve sobrescrever esse campo automaticamente em buscas futuras.
- O app pode perguntar antes de substituir.
- Tags e generos manuais devem ser preservados.

## 13. Tratamento de Erros

### Sem Internet

- Nao bloquear cadastro.
- Mostrar estado discreto.
- Permitir tentar novamente depois.

### Provider Indisponivel

- Usar cache existente, se houver.
- Registrar erro tecnico.
- Nao exibir stack trace para o usuario.

### Resultado Incorreto

- Permitir trocar resultado.
- Permitir limpar metadados aplicados.
- Permitir edicao manual.

### Imagem Falhou ao Baixar

- Aplicar metadados textuais mesmo assim.
- Usar placeholder de capa.
- Permitir tentar baixar novamente.

## 14. Seguranca e Conformidade

- Respeitar termos de uso das fontes escolhidas.
- Evitar scraping de paginas que proíbam automacao.
- Nao armazenar dados alem do necessario.
- Nao enviar caminhos locais.
- Nao baixar conteudo executavel como metadado.
- Validar tipo e tamanho de imagens antes de salvar.

## 15. Testes Recomendados

### Testes Unitarios

- Normalizacao de query.
- Calculo de confianca.
- Limpeza de HTML da sinopse.
- Mapeamento de payload externo para modelo interno.
- Protecao contra sobrescrever campos editados manualmente.

### Testes de Integracao

- Busca com provider simulado.
- Aplicacao de resultado unico.
- Escolha entre multiplos resultados.
- Cache de resposta.
- Cache de imagem.
- Falha de internet.
- Provider retornando payload incompleto.

### Testes de UI

- Detalhes sem metadados.
- Detalhes com capa, genero e sinopse.
- Multiplos resultados para escolha.
- Erro discreto de busca.
- Edicao manual apos preenchimento automatico.

## 16. Criterios de Aceite

- O app busca metadados sem bloquear a biblioteca local.
- O app nao envia caminhos locais para providers externos.
- O usuario pode escolher entre multiplos resultados.
- O usuario pode editar qualquer metadado aplicado.
- Capa, banner e sinopse aplicados ficam disponiveis offline.
- Falhas de rede nao impedem adicionar ou abrir jogos.
- Dados manuais nao sao sobrescritos silenciosamente.
