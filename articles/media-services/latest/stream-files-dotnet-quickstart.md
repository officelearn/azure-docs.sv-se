---
title: Strömma videofiler med Azure Media Services – .NET | Microsoft Docs
description: Följ stegen i den här snabbstarten för att skapa ett nytt Azure Media Services-konto, koda en fil och strömma den till Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, strömma
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 92321b5e919f6703cb481d88f312a20fc7c62826
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49375470"
---
# <a name="quickstart-stream-video-files---net"></a>Snabbstart: Strömma videofiler – .NET

Den här snabbstarten visar hur lätt det är att koda och börja strömma video på en mängd olika webbläsare och enheter med Azure Media Services. Ett indatainnehåll kan anges med HTTP-URL:er, SAS-URL:er eller sökvägar till filer i Azure Blob Storage.
Exemplet i det här ämnet kodar innehåll som du gör tillgängligt via en HTTPS-URL. Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er.

I slutet av snabbstarten kommer du att kunna strömma en video.  

![Spela upp videon](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har Visual Studio installerat kan du hämta [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone http://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Exemplet finns i mappen [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Exemplet utför följande åtgärder:

1. Skapar en transformering (kontrollerar först om den angivna transformeringen finns). 
2. Skapa en utdatatillgång som används som kodningsjobbets utdata.
3. Skapar jobbets indata som baseras på en HTTPS-URL.
4. Skickar kodningsjobbet med de indata och utdata som skapades tidigare.
5. Kontrollerar jobbets status.
6. Skapar en StreamingLocator.
7. Skapar strömnings-URL:er.

Du kan få beskrivningar av varje funktion i exemplet, undersöka koden och titta på kommentarer i [den här källfilen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](http://portal.azure.com).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="run-the-sample-app"></a>Kör exempelappen

När du kör appen visas URL:er som kan användas för uppspelning av video med olika protokoll. 

1. Tryck på Ctrl+F5 för att köra programmet *EncodeAndStreamFiles*.
2. Välj Apples **HLS**-protokoll (slutar med *manifest(format=m3u8-aapl)*) och kopiera strömnings-URL:en från konsolen.

![Resultat](./media/stream-files-tutorial-with-api/output.png)

I exemplets [källkod](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs), kan du se hur URL:en är byggd. För att skapa den, måste du sammanfoga strömningsslutpunktens värdnamn och sökvägen för strömningslokaliseraren.  

## <a name="test-with-azure-media-player"></a>Testa med Azure Media Player

I den här artikeln används Azure Media Player för att testa dataströmmen. 

> [!NOTE]
> Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

1. Öppna en webbläsare och navigera till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. I rutan **URL:** klistrar du in ett av de strömmande URL-värden som du fick när du körde programmet. 
3. Tryck på **Uppdatera spelare**.

Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och lagringskontona som du skapade för snabbstarten, tar du bort resursgruppen. Du kan använda verktyget **CloudShell**.

Kör följande kommando i **CloudShell**:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Granska koden

Du kan få beskrivningar av varje funktion i exemplet, undersöka koden och titta på kommentarer i [den här källfilen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

I självstudien för att [ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md) får du ett mer avancerat strömningsexempel med detaljerade förklaringar. 

## <a name="multithreading"></a>Flertrådsteknik

SDK:erna i Azure Media Services v3 är inte trådsäkra. När du arbetar med flertrådade program bör du skapa ett nytt AzureMediaServicesClient-objekt per tråd.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md)
