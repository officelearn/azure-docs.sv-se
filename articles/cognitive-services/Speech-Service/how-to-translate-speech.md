---
title: Omvandla tal med Speech services
description: Lär dig hur du använder Talöversättning i Speech-tjänsten.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: d539fe5a1a031c196c0d40e989d83575715b278a
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285269"
---
# <a name="translate-speech-using-speech-service"></a>Omvandla tal med Speech-tjänsten

Den [tal SDK](speech-sdk.md) är det enklaste sättet att använda talöversättning i ditt program. SDK innehåller alla funktioner i tjänsten. Den grundläggande processen för att utföra talöversättning omfattar följande steg:

1. Skapa en tal-fabrik och ange en prenumerationsnyckel för tal-tjänst och [region](regions.md) eller en autentiseringstoken. Du också konfigurera språk för källa och mål translation nu samt ange om du vill att text eller tal utdata.

2. Få en identifierare från fabriken. Välj en översättning identifierare för översättning. (Andra identifierarna avser *tal till Text*.) Det finns olika varianter av översättning Igenkännande baserat på ljudkälla som du använder.

4. Koppla in händelser för asynkron åtgärd om du vill. Identifieraren anropar din händelsehanterare när den har tillfälliga och slutliga resultaten. Annars kan ditt program tar emot ett sista translation resultat.

5. Starta erkännande och översättning.

# <a name="sdk-samples"></a>SDK-exempel

Den senaste uppsättningen exempel, finns det [Cognitive Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
