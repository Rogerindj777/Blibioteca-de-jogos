# Plano de Empacotamento e Instalacao para Windows

## 1. Objetivo

Definir como o aplicativo sera empacotado, instalado, atualizado e removido no Windows. O objetivo e oferecer uma instalacao simples para usuarios casuais, preservando biblioteca, configuracoes e cache local entre versoes.

Este plano assume a stack recomendada no documento tecnico: Tauri, Rust, React, TypeScript e SQLite.

## 2. Principios

- **Instalacao simples:** poucos passos e linguagem clara.
- **Preservar dados:** atualizacoes nao devem apagar biblioteca local.
- **Desinstalacao segura:** dados do usuario nao devem ser removidos sem confirmacao explicita.
- **Sem configuracao inicial obrigatoria:** apos instalar, o app pode iniciar a experiencia de varredura.
- **Windows-first:** foco inicial em Windows desktop.
- **Build reproduzivel:** processo de empacotamento deve ser documentado e automatizavel.

## 3. Formatos de Distribuicao

Formato recomendado para MVP:

- Instalador `.exe` gerado pelo Tauri.
- Build portavel `.zip` pode ser oferecido futuramente para usuarios avancados.

Recomendacao inicial:

- Priorizar instalador `.exe` tradicional para Windows.
- Evitar build portavel no primeiro MVP se isso atrasar assinatura, atualizacao ou suporte.

## 4. Requisitos do Instalador

O instalador deve:

- Instalar o aplicativo em local apropriado.
- Criar atalho no Menu Iniciar.
- Opcionalmente criar atalho na Area de Trabalho.
- Registrar nome, versao e publicador no Windows.
- Permitir desinstalacao pelo painel de aplicativos do Windows.
- Preservar dados do usuario em atualizacoes.

O instalador nao deve:

- Exigir conta.
- Exigir internet para abrir o app.
- Apagar biblioteca local durante atualizacao.
- Rodar varredura antes do usuario abrir o app.

## 5. Diretorios Locais

### Aplicativo

Arquivos instalados do app devem ficar no local definido pelo instalador/Tauri para aplicativos Windows.

### Dados do Usuario

Dados locais devem ficar em diretorio de dados do usuario, como:

- `%APPDATA%\<NomeDoApp>`
- ou local equivalente definido pela API de diretorios do Tauri.

Conteudo esperado:

- Banco SQLite.
- Capas e banners cacheados.
- Icones extraidos.
- Configuracoes locais.
- Logs tecnicos.

### Cache

Cache de imagens e metadados deve ficar separado de dados essenciais.

Regras:

- Limpar cache nao deve apagar a biblioteca.
- Desinstalar o app nao deve apagar cache e banco sem confirmacao explicita.
- Atualizar o app deve preservar cache, salvo migracao necessaria.

## 6. Configuracao de Build

Itens a configurar:

- Nome do aplicativo.
- Identificador unico do app.
- Versao.
- Icone.
- Nome do publicador.
- Janelas e permissoes do Tauri.
- Recursos incluidos no pacote.
- Assinatura de codigo, quando disponivel.

Arquivos esperados no projeto futuro:

- `package.json`
- `src-tauri/tauri.conf.json` ou equivalente.
- `src-tauri/Cargo.toml`
- Icones em formatos exigidos pelo Tauri.
- Scripts de build.

## 7. Versionamento

Usar versionamento semantico:

```text
MAJOR.MINOR.PATCH
```

Exemplos:

- `0.1.0`: primeiro prototipo instalavel.
- `0.2.0`: varredura e revisao completas.
- `1.0.0`: primeiro lancamento estavel.

Regras:

- Incrementar `PATCH` para correcoes.
- Incrementar `MINOR` para recursos novos compativeis.
- Incrementar `MAJOR` para mudancas incompatveis ou reset de formato.

## 8. Assinatura de Codigo

Para distribuicao publica, o ideal e assinar o instalador.

Beneficios:

- Reduz alertas do Windows.
- Aumenta confianca do usuario.
- Facilita distribuicao mais profissional.

Para MVP local:

- Pode-se iniciar sem assinatura, mas o usuario pode ver alertas do Windows SmartScreen.
- Documentar esse risco antes de divulgar para usuarios finais.

## 9. Atualizacoes

Atualizacoes automaticas podem ficar fora do MVP, mas o empacotamento deve permitir evoluir para isso.

MVP:

- Usuario baixa nova versao.
- Instalador atualiza arquivos do app.
- Banco e dados locais permanecem.
- Migrations rodam ao abrir o app atualizado.

Futuro:

- Verificador de atualizacoes.
- Download da nova versao.
- Instalacao assistida.
- Canal estavel e canal beta.

## 10. Migracoes de Banco

Ao atualizar:

1. App abre.
2. Backend verifica versao do banco.
3. Migrations pendentes sao aplicadas.
4. Se falhar, app mostra erro simples e preserva dados.

Regras:

- Fazer backup automatico antes de migration destrutiva.
- Evitar migrations destrutivas no MVP.
- Nunca apagar biblioteca sem confirmacao.

## 11. Primeira Execucao Apos Instalacao

Fluxo esperado:

1. Usuario instala o app.
2. Usuario abre pelo Menu Iniciar ou atalho.
3. App cria estrutura local de dados se nao existir.
4. App mostra tela de primeiro uso.
5. App inicia ou oferece iniciar varredura.
6. Usuario revisa candidatos antes de adicionar.

O instalador nao deve iniciar varredura por conta propria.

## 12. Desinstalacao

Comportamento recomendado:

- Remover arquivos instalados do app.
- Manter dados locais do usuario por padrao.
- Em versao futura, oferecer opcao clara para apagar biblioteca e cache.

Motivo:

- Evita perda acidental da biblioteca.
- Permite reinstalar o app sem reconstruir tudo.

## 13. Permissoes no Windows

O app deve funcionar sem permissao de administrador sempre que possivel.

Pode haver limitacoes:

- Algumas pastas podem negar leitura durante varredura.
- O app deve ignorar essas pastas e continuar.
- O usuario nao deve precisar executar como administrador para uso comum.

O app nao deve:

- Instalar drivers.
- Criar servicos do Windows no MVP.
- Alterar configuracoes globais do sistema.

## 14. Antivírus e SmartScreen

Riscos esperados:

- Apps novos e nao assinados podem gerar alerta.
- Varredura de muitos executaveis pode parecer sensivel para alguns antivirus.

Mitigacoes:

- Assinatura de codigo antes de distribuicao ampla.
- Nome e publicador consistentes.
- Instalador limpo e sem downloads ocultos.
- Logs claros.
- Varredura somente apos abertura do app e com UI visivel.

## 15. CI e Build Automatizado

Pipeline recomendado no futuro:

1. Instalar dependencias.
2. Rodar testes de frontend.
3. Rodar testes de Rust/backend.
4. Rodar build do frontend.
5. Rodar build Tauri para Windows.
6. Gerar instalador.
7. Publicar artefatos de release.

Artefatos:

- Instalador Windows.
- Checksums.
- Release notes.

## 16. Checklist de Release

Antes de publicar uma versao:

- Versao atualizada.
- Icone correto.
- Nome do app correto.
- Instalador gerado.
- App abre apos instalacao.
- Atalho no Menu Iniciar criado.
- Banco SQLite criado no primeiro uso.
- Varredura inicial funciona.
- Atualizacao preserva dados.
- Desinstalacao remove app sem apagar biblioteca.
- Teste em Windows limpo.

## 17. Testes de Instalacao

### Instalacao Nova

- Instalar em maquina sem versoes anteriores.
- Abrir app.
- Confirmar criacao de dados locais.
- Rodar varredura inicial.

### Atualizacao

- Instalar versao antiga.
- Criar biblioteca local.
- Instalar nova versao por cima.
- Confirmar que jogos, capas, configuracoes e diretorios permanecem.

### Desinstalacao e Reinstalacao

- Desinstalar app.
- Reinstalar.
- Confirmar se dados preservados sao reutilizados.

### Windows Sem Internet

- Instalar e abrir sem internet.
- Confirmar biblioteca e varredura local funcionando.
- Confirmar metadados online indisponiveis sem quebrar fluxo.

## 18. Criterios de Aceite

- O app gera instalador Windows.
- O app instala com poucos passos.
- O app cria atalho no Menu Iniciar.
- O app abre sem exigir internet.
- O app preserva dados locais em atualizacoes.
- A desinstalacao nao apaga biblioteca sem confirmacao explicita.
- A primeira execucao prepara banco e diretorios locais.
- O instalador nao executa varredura antes do usuario abrir o app.
