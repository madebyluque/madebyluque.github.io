+++
title = "Github action para jogos da Unity"
date = "2023-07-16T12:01:55-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["gamedev", "github", "unity"]
keywords = ["", ""]
description = "Realize o build dos seus jogos com GitHub Actions"
showFullContent = false
readingTime = false
hideComments = false
+++

Após brincar com desenvolvimento de jogos por alguns anos, decidi levar a sério a carreira de game dev e abri um estúdio de jogos chamado Blue Flower Game Lab.
Estou desenvolvendo meu primeiro projeto sozinho, então tenho tentado automatizar meus processos ao máximo. Foi assim que descobri a [Game.ci](https://game.ci/), uma maneira de automatizar builds de projetos da Unity através do GitHub Actions.

## Configuração
### Ativação e secrets
Crie um arquivo chamado **.github/workflows/activation.yml** e cole o seguinte conteúdo nele:
```
name: Acquire activation file
on:
  workflow_dispatch: {}
jobs:
  activation:
    name: Request manual activation file 🔑
    runs-on: ubuntu-latest
    steps:
      # Request manual activation file
      - name: Request manual activation file
        id: getManualLicenseFile
        uses: game-ci/unity-request-activation-file@v2
      # Upload artifact (Unity_v20XX.X.XXXX.alf)
      - name: Expose as artifact
        uses: actions/upload-artifact@v2
        with:
          name: ${{ steps.getManualLicenseFile.outputs.filePath }}
          path: ${{ steps.getManualLicenseFile.outputs.filePath }}
```

Execute essa ação. O resultado dela será um arquivo de ativação manual. Depois disso, pegue o arquivo e faça o upload dele em [license.unity3d.com](https://license.unity3d.com/manual) para obter um arquivo de licença.
Com o arquivo de licença em mãos, será necessário criar os seguintes segredos no repositório do seu jogo:
- UNITY_LICENSE: conteúdo obtido no arquivo de licença;
- UNITY_EMAIL: e-mail de login na Unity;
- UNITY_PASSWORD: senha para login na Unity.

**Nota:** a senha e o e-mail não são solicitados nem armazenados pela game.ci. São necessárias apenas para reativação da licença durante o processo de build.

### Build
Após obter a licença e configurar os secrets, crie um novo arquivo de pipeline com o seguinte conteúdo:
```
name: Build

on:
  push:
    branches:
      - main
      
jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        targetPlatform:
          # - StandaloneOSX # Build a macOS standalone (Intel 64-bit).
          # - StandaloneWindows # Build a Windows standalone.
          # - StandaloneWindows64 # Build a Windows 64-bit standalone.
          # - StandaloneLinux64 # Build a Linux 64-bit standalone.
          # - iOS # Build an iOS player.
          # - Android # Build an Android .apk standalone app.
          # - WebGL # WebGL.
    steps:
      # Checkout
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          lfs: true

      # Cache
      - uses: actions/cache@v2
        with:
          path: Library
          key: Library-${{ hashFiles('Assets/**', 'Packages/**', 'ProjectSettings/**') }}
          restore-keys: |
            Library-

      # Build
      - name: Build project
        uses: game-ci/unity-builder@v2
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
          UNITY_EMAIL: ${{ secrets.UNITY_EMAIL }}
          UNITY_PASSWORD: ${{ secrets.UNITY_PASSWORD }}
        with:
          targetPlatform: ${{ matrix.targetPlatform }}

      # Output
      - uses: actions/upload-artifact@v2
        with:
          name: Build
          path: build

      # Discord notification
      - name: Discord Notifier
        uses: madebyluque/action-discord-notifier@v1.1.1
        with:
          webhook: Webhook do canal do discord
          message-title: Título da mensagem do discord
          message-description: Descrição da mensagem
```

Com o arquivo criado, você precisará apenas selecionar as plataformas alvo. Para isso, remova o símbolo "#" das plataformas desejadas na seção **targetPlatform**. 

O último passo da pipeline envia uma notificação a um canal do Discord. Como a execução dela pode passar de 10 minutos, optei por ser avisado da conclusão dela em vez de ficar esperando. Para isso, fiz o fork de uma ação no github e modifiquei algumas informações para poder personalizar o título e a descrição da mensagem. Esse passo é opcional, então fique à vontade para removê-lo. 