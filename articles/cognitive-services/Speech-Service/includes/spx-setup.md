---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: abfb4f6ba9452581811db1f462089cbafc771266
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544609"
---
## <a name="prerequisites"></a>Förutsättningar

Den enda förutsättningen är en Azure tal-prenumeration. Se [guiden](../get-started.md#new-resource) för att skapa en ny prenumeration om du inte redan har en.

## <a name="download-and-install"></a>Hämta och installera

#### <a name="windows-install"></a>[Windows-installation](#tab/windowsinstall)

Följ de här stegen för att installera tal-CLI i Windows:

1. Installera antingen [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) eller [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Hämta [zip-arkivet](https://aka.ms/speech/spx-zips.zip)för tal CLI och extrahera det sedan.
3. Gå till rot katalogen `spx-zips` som du extraherade från nedladdningen och extrahera den under katalog du behöver ( `spx-net471` för .NET Framework 4,7 eller `spx-netcore-win-x64` .net Core 3,0 på en x64-processor).

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

***

## <a name="create-subscription-config"></a>Skapa prenumerations konfiguration

För att börja använda tal-CLI måste du först ange din prenumerations nyckel och region information. Se sidan [region support](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regions-ID. När du har en prenumerations nyckel och region-ID (t. ex. `eastus`, `westus` ), kör följande kommandon.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Din prenumerations-autentisering lagras nu för framtida SPX-begäranden. Om du behöver ta bort något av dessa lagrade värden kör `spx config @region --clear` eller `spx config @key --clear` .
