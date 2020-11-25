---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6f80d41001d11c52a00454ea2a593f3f1fce32db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026649"
---
## <a name="download-and-install"></a>Hämta och installera

#### <a name="windows-install"></a>[Windows-installation](#tab/windowsinstall)

Följ de här stegen för att installera tal-CLI i Windows:

1. I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform. Det kan krävas en omstart för att installera det första gången.
2. Hämta [zip-arkivet](https://aka.ms/speech/spx-zips.zip)för tal CLI och extrahera det sedan.
3. Gå till den katalog där du extraherade `spx-zips` . Den här mappen innehåller program filer för tal-CLI på olika plattformar. 
4. Extrahera filerna för din plattform ( `spx-net471` för .NET Framework 4,7 eller `spx-netcore-win-x64` .net Core 3,0 på en x64-processor). Kom ihåg att du kör `spx` från den här katalogen.

### <a name="run-the-speech-cli"></a>Kör tal-CLI

1. Öppna kommando tolken eller PowerShell och navigera sedan till den katalog där du extraherade tal-CLI.  
2. Skriv `spx` för att se hjälp kommandon för tal-cli.

> [!NOTE]
> PowerShell kontrollerar inte den lokala katalogen vid sökning efter ett kommando. I PowerShell ändrar du katalogen till platsen för `spx` och anropar verktyget genom att ange `.\spx` .
> Om du lägger till den här katalogen i sökvägen kommer PowerShell och kommando tolken i Windows att hitta `spx` från vilken katalog som helst utan att inkludera `.\` prefixet.

### <a name="font-limitations"></a>Tecken begränsningar

I Windows kan tal-CLI bara visa teckensnitt som är tillgängliga för kommando tolken på den lokala datorn.
[Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) stöder alla teckensnitt som genereras interaktivt av tal-cli.

Om du skriver ut till en fil kan en text redigerare som anteckningar eller en webbläsare som Microsoft Edge även Visa alla teckensnitt.

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

Om du vill börja använda tal-CLI måste du ange din röst prenumerations nyckel och regions-ID. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../overview.md#try-the-speech-service-for-free).
När du har en prenumerations nyckel och region-ID (t. ex. `eastus`, `westus` ), kör följande kommandon.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Din prenumerations-autentisering lagras nu för framtida SPX-begäranden. Om du behöver ta bort något av dessa lagrade värden kör `spx config @region --clear` eller `spx config @key --clear` .
