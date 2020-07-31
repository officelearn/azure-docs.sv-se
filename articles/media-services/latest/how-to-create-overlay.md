---
title: Så här skapar du ett överlägg med Media Encoder Standard
description: Lär dig hur du skapar ett överlägg med Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 0d1a6d5626e081ff50f65b3a4396e223b33f920d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433636"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Så här skapar du ett överlägg med Media Encoder Standard

Med Media Encoder Standard kan du täcka över en bild till en befintlig video. För närvarande stöds följande format: PNG, jpg, GIF och BMP.

## <a name="prerequisites"></a>Förutsättningar

* Samla in den konto information som du behöver för att konfigurera *appsettings.jspå* filen i exemplet. Om du inte är säker på hur du gör det, se [snabb start: registrera ett program med Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Följande värden förväntas i *appsettings.jsi* filen.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Om du inte redan är bekant med transformationer, rekommenderar vi att du utför följande aktiviteter:

* Läs [kodning av video och ljud med Media Services](encoding-concept.md)
* Läs [hur du kodar med en anpassad transformering – .net](customize-encoder-presets-how-to.md). Följ anvisningarna i artikeln för att konfigurera .NET som krävs för att arbeta med transformeringar och gå sedan tillbaka hit för att testa ett för inställnings exempel för överlägg.
* Se [transformerings-referens dokumentet](https://docs.microsoft.com/rest/api/media/transforms).

När du är bekant med transformationer kan du hämta exempel på överlägg.

## <a name="overlays-preset-sample"></a>Överlägg för Förvals exempel

Hämta [exemplet med medie tjänster – överlägg](https://github.com/Azure-Samples/media-services-overlays) för att komma igång med överlägg.

## <a name="next-steps"></a>Nästa steg

* [Under klipp en video vid kodning med Media Services-.NET](subclip-video-dotnet-howto.md)