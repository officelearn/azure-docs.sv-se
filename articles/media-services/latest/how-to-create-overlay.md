---
title: Så här skapar du ett överlägg med Media Encoder Standard
description: Lär dig hur du skapar ett överlägg med Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 743fe146042c7b52394cc4ee8ced49a0f540e79c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844292"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Så här skapar du ett överlägg med Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Media Encoder Standard kan du täcka över en bild, ljudfil eller en annan video till en annan video. Indata måste ange exakt en fil. Du kan ange en bildfil i JPG-, PNG-, GIF-eller BMP-format eller en ljudfil (till exempel WAV-, MP3-, WMA-eller M4A-fil) eller en videofil.


## <a name="prerequisites"></a>Förutsättningar

* Samla in den konto information som du behöver för att konfigurera *appsettings.jspå* filen i exemplet. Om du inte är säker på hur du gör det, se [snabb start: registrera ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md). Följande värden förväntas i *appsettings.jsi* filen.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Om du inte redan är bekant med transformationer, rekommenderar vi att du utför följande aktiviteter:

* Läs [kodning av video och ljud med Media Services](encoding-concept.md)
* Läs [hur du kodar med en anpassad transformering – .net](customize-encoder-presets-how-to.md). Följ anvisningarna i artikeln för att konfigurera .NET som krävs för att arbeta med transformeringar och gå sedan tillbaka hit för att testa ett för inställnings exempel för överlägg.
* Se [transformerings-referens dokumentet](/rest/api/media/transforms).

När du är bekant med transformationer kan du hämta exempel på överlägg.

## <a name="overlays-preset-sample"></a>Överlägg för Förvals exempel

Hämta [exemplet med medie tjänster – överlägg](https://github.com/Azure-Samples/media-services-overlays) för att komma igång med överlägg.

## <a name="next-steps"></a>Nästa steg

* [Under klipp en video vid kodning med Media Services-.NET](subclip-video-dotnet-howto.md)
