+++
title = "Recursos e dicas para o desenvolvimento do seu primeiro game"
date = "2023-02-20T11:51:47-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["gamedev"]
keywords = ["", ""]
description = "Game engines, artes etc"
showFullContent = false
readingTime = false
hideComments = false
+++

Fotos de Foto de [Sigmund](https://unsplash.com/es/@sigmund?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) e [Jacek Dylag](https://unsplash.com/@dylu?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText).

Bem, é muito provável que você, assim como eu, sempre sonhou em desenvolver seus próprios jogos. Assim que mudei de carreira e comecei a programar, decidi que tiraria esse sonho do papel e o tornaria realidade. Ainda não consegui terminar meu game, mas as coisas estão caminhando bem. Trarei aqui algumas dicas para que você possa fazer o mesmo.

## Antes de mais nada: é um processo longo e trabalhoso

![Muito trabalhoso!](https://media.giphy.com/media/JWybLzXs7Hn0JKhSji/giphy.gif "Muito trabalhoso!")

Se você está começando do zero, saiba que criar um jogo é um processo trabalhoso. Sério. Você pode agilizá-lo com algumas das dicas deste texto, mas no geral, você vai passar alguns meses/anos trabalhando na arte, no código, no design de níveis, no som...
Inclusive, saiba que todo projeto de vida tem um custo muito alto: seu tempo. Entretanto, mesmo que você não se torne o novo [Eric Barone](https://pt.wikipedia.org/wiki/Eric_Barone), o conhecimento adquirido ao **criar um projeto seu**, é extremamente valioso.

## Game Engine
Se você estiver pensando em realizar o seu primeiro projeto, não comece por criar uma game engine. Há milhares de engines gratuitas por aí, então **não reinvente a roda**.
![Não reinvente a roda](https://media.giphy.com/media/3o6gE9BYreSsQyOD9C/giphy.gif "Não reinvente a roda")
Uma game engine é um software voltado ao desenvolvimento de jogos. Será nela que você criará seus níveis, scripts, mecânicas do jogo etc. A escolha de uma boa game engine basicamente ditará se o seu projeto será terminado ou não.
Há alguns pontos a serem considerados, como facilidade para gerar as versões finais (builds) do projeto, facilidade para criação de scripts, facilidade para trabalhar os objetos etc.
Pensando nisso, a minha escolha pessoal é a [Unity](https://unity.com/pt). É possível criar jogos 2D, 3D, jogos para realidade virtual e outros. Além disso, ela simplifica o processo de "buildar" seu jogo para celulares, pcs, consoles etc.
A linguagem de programação da Unity é o C#, uma linguagem vastamente consolidada no mercado. A documentação dela é completa e há tutoriais incríveis sobre ela na internet. Entretanto, **há um ponto de atenção**: por mais que a documentação da linguagem seja incrível, a documentação da unity não é muito boa, e muitas vezes você precisará recorrer a canais do Youtube (veja nas referências).
Eu tentei outras das engines que recomendam na internet mas não consegui avançar muito com elas. Caso queira saber mais, os links são:
* [GameMaker](https://gamemaker.io/pt-BR/download): você não precisa saber programar para conseguir fazer um jogo usando ela;
* [Godot](https://godotengine.org/);

## Arte visual
Essa tem sido a parte mais difícil nos meus projetos. Eu até consigo desenhar, mas criar artes que encham as pessoas de sensações não é uma tarefa fácil. E não se engane: muitas pessoas abandonarão seu game se a arte não interessá-las. 
Isso não significa que seu game precisa ser extremamente realista. Ele só precisa ser agradável aos olhos, e isso é bastante subjetivo (logo, não tente agradar todo mundo).

![Planet of Lana](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jhqos981uvtyvbw997i6.png "Planet of Lana")

![Child of light](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rre2f17oof9013frf2bc.png "Child of light")


![Will it Snail](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/evu1qasdw2z1raqgtzhx.png "Will it Snail")


![Stardew Valley](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/csnqjps8jbeunnhun0bf.png "Stardew Valley")

Você pode usar uma ou várias ferramentas para criar sua arte. Aqui estão algumas das minhas favoritas:
* [Krita](https://krita.org/): desenho e pintura digital;
* [Piskel](https://www.piskelapp.com/): pixel art;
* [Blender](https://www.blender.org/): arte e modelagem 3D;
* [Tiled](https://www.mapeditor.org/): criação de tilemaps e editor de níveis;
* [PureRef](https://www.pureref.com/): referência de imagens.

Entretanto, assim como com as Game Engines, você não precisa criar tudo do zero. Há sites que vendem assets ou até mesmo disponibilizam eles gratuitamente. O canal [Game Dev Unlocked](https://www.youtube.com/@DavidWehleGames) fala sobre como assets gratuitos foram importantes na criação do game [The First Tree](https://store.steampowered.com/app/555150/The_First_Tree/). Você pode encontrar esses assets no [itch](https://itch.io/game-assets), na [Unity Asset Store](https://assetstore.unity.com/) ou em outros sites.

## Som
O som pode fazer seu jogo ser incrível ou extremamente chato 
 -- inclusive, não consigo jogar Splatoon devido às músicas apresentadas nos trailers. 
Gosto bastante do [LMMS](https://lmms.io/download#windows) para criação de músicas. Entretanto, no meu projeto atual, decidi partir para algo mais manual, então comprei uma interface de áudio ([Teyun Q-22](https://pt.aliexpress.com/item/1005003093878428.html)) e estou utilizando o [Audacity](https://www.audacityteam.org/download/) e o Guitar Rig 5 para gravar músicas diretamente com a guitarra e o violão.
Você precisará entender alguns conceitos antes de compor para seu game. Não basta apenas pegar um instrumento e sair tocando notas de forma desgovernada. A palestra do [Leo Borges no GDTK 2021](https://www.youtube.com/watch?v=ELQoHDrv2hg) me ajudou a entender as necessidades sonoras do meu game, e recomendo que você a assista.

![Jacek Dylag](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4st7kmeh6q5ndjwpmwb1.png "Jacek Dylag")

Há outros tipos de áudio em jogos. Sons de passos, de portas abrindo (quem lembra das portas de Resident Evil do PS1 se abrindo?). Costumo usar o site [OpenGameArt](https://opengameart.org/) para buscar esse tipo de áudio.

## Fim?
Na verdade, **espero que esse seja um início. Pelo menos para o seu projeto**. Senta na cadeira, escolhe a engine, baixa uns assets e começa o teu game. Sério. Não espere mais tempo para isso. 
Se, assim como eu, você tiver dúvidas se vale ou não a pena criar um jogo, minha resposta é: **sempre vale**.

Se precisar de uma ajuda, uma mentoria, ou apenas quiser falar sobre game dev, siga-me no [twitter](https://twitter.com/cephalopodluke) ou envie um e-mail para lucas@blueflowergamelab.com.

Enfim, espero que este texto tenha te ajudado. Vou deixar uns links na sessão de **recursos** para você conseguir seguir adiante com seu projeto.

Até a próxima!

## Recursos
* [BlackThornProd - Youtube](https://www.youtube.com/@Blackthornprod);
* [Brackeys - Youtube](https://www.youtube.com/@Brackeys);
* [CodeMonkey - Youtube](https://www.youtube.com/@CodeMonkeyUnity);
* [Gamedev Toolkit - Youtube](https://www.youtube.com/@gamedevtoolkit);
* [OpenGameArt](https://opengameart.org/);
* [Seu jogo é MUITO grande! Como criar com limitações](https://www.youtube.com/watch?v=jqWJsacaEi4);