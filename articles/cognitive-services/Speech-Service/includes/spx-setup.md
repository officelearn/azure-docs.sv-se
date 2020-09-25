---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 3c176f103371bfb1b35231f222b2045f1f4a3ef9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327054"
---
## <a name="download-and-install"></a>Hämta och installera

#### <a name="windows-install"></a>[Windows-installation](#tab/windowsinstall)

Följ de här stegen för att installera tal-CLI i Windows:

1. Hämta [zip-arkivet](https://aka.ms/speech/spx-zips.zip)för tal CLI och extrahera det sedan.
2. Gå till rot katalogen `spx-zips` som du extraherade från nedladdningen och extrahera den under katalog du behöver ( `spx-net471` för .NET Framework 4,7 eller `spx-netcore-win-x64` .net Core 3,0 på en x64-processor).

I kommando tolken ändrar du katalog till den här platsen och skriver sedan `spx` för att se hjälp för tal-cli.

> [!NOTE]
> I Windows kan tal-CLI bara visa teckensnitt som är tillgängliga för kommando tolken på den lokala datorn.
> [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) stöder alla teckensnitt som genereras interaktivt av tal-cli.
> Om du skriver ut till en fil kan en text redigerare som anteckningar eller en webbläsare som Microsoft Edge även Visa alla teckensnitt.

> [!NOTE]
> PowerShell kontrollerar inte den lokala katalogen vid sökning efter ett kommando. I PowerShell ändrar du katalogen till platsen för `spx` och anropar verktyget genom att ange `.\spx` .
> Om du lägger till den här katalogen i sökvägen kommer PowerShell och kommando tolken i Windows att hitta `spx` från vilken katalog som helst utan att inkludera `.\` prefixet.

#### <a name="linux-install"></a>[Linux-installation](#tab/linuxinstall)

Följ de här stegen för att installera Speech CLI på Linux på en x64-processor:

1. Installera [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Hämta [zip-arkivet](https://aka.ms/speech/spx-zips.zip)för tal CLI och extrahera det sedan.
3. Gå till rot katalogen `spx-zips` som du extraherade från nedladdningen och extrahera `spx-netcore-30-linux-x64` till en ny `~/spx` katalog.
4. I en Terminal skriver du följande kommandon:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Skriv `spx` för att se hjälp för tal-cli.

#### <a name="docker-install"></a>[Docker-installation](#tab/dockerinstall)

> [!NOTE]
> <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop för din plattform <span class="docon docon-navigate-external x-hidden-focus"></span> </a> måste vara installerat.

Följ de här stegen för att installera tal-CLI i en Docker-behållare:

1. Skriv följande kommando i en ny kommando tolk eller Terminal:  `docker pull msftspeech/spx`
2. Skriv det här kommandot. Du bör se hjälp information för tal-CLI: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Montera en katalog i behållaren

Tal CLI-verktyget sparar konfigurations inställningar som filer och läser in dessa filer när du utför något av kommandona (förutom hjälp kommandon).
När du använder tal-CLI i en Docker-behållare måste du montera en lokal katalog från behållaren, så att verktyget kan lagra eller hitta konfigurations inställningarna och även så att verktyget kan läsa eller skriva filer som krävs av kommandot, till exempel ljudfiler av tal.

I Windows skriver du det här kommandot för att skapa en lokal katalog tal CLI kan använda från behållaren:

`mkdir c:\spx-data`

Eller på Linux eller Mac skriver du det här kommandot i en Terminal för att skapa en katalog och se dess absoluta sökväg:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Du kommer att använda den absoluta sökvägen när du anropar tal-CLI.

### <a name="run-speech-cli-in-the-container"></a>Kör tal-CLI i behållaren

Den här dokumentationen visar det tal CLI- `spx` kommando som används i installationer som inte är Docker.
När du anropar `spx` kommandot i en Docker-behållare måste du montera en katalog i behållaren till ditt fil system där tal-CLI kan lagra och hitta konfigurations värden och läsa och skriva filer.
I Windows kommer dina kommandon att starta så här:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

På Linux eller Mac kommer dina kommandon att starta ungefär så här:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Ersätt `/ABSOLUTE_PATH` med den absoluta sökvägen som visas `pwd` i kommandot i avsnittet ovan.

Om du vill använda `spx` kommandot som är installerat i en behållare, anger du alltid det fullständiga kommandot som visas ovan, följt av parametrarna för din begäran.
I Windows anger det här kommandot till exempel din nyckel:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Du kan inte använda datorns mikrofon eller högtalare när du kör tal-CLI i en Docker-behållare.
> Om du vill använda dessa enheter skickar du ljudfiler till och från tal-CLI för inspelning/uppspelning utanför Docker-behållaren.
> Tal CLI-verktyget kan komma åt den lokala katalogen som du ställer in i stegen ovan.

***

## <a name="create-subscription-config"></a>Skapa prenumerations konfiguration

För att börja använda tal-CLI måste du först ange din prenumerations nyckel och region information. Se sidan [region support](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regions-ID. När du har en prenumerations nyckel och region-ID (t. ex. `eastus`, `westus` ), kör följande kommandon.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Din prenumerations-autentisering lagras nu för framtida SPX-begäranden. Om du behöver ta bort något av dessa lagrade värden kör `spx config @region --clear` eller `spx config @key --clear` .
