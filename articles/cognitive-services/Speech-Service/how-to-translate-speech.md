---
title: Översätta tal med tal services | Microsoft Docs
description: Lär dig mer om att använda tal översättning i tjänsten tal.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356286"
---
# <a name="translate-speech-using-speech-service"></a>Översätta tal med tjänsten för tal

Den [tal SDK](speech-sdk.md) är det enklaste sättet att använda tal översättning i ditt program. SDK innehåller alla funktioner i tjänsten. Den grundläggande processen för att utföra taligenkänning översättning omfattar följande steg:

1. Skapa en fabrik tal och ange ett tal prenumeration nyckel eller en autentiseringstoken. Du också konfigurera språk för källan och målet översättning nu samt att ange om du vill att text eller tal utdata.

2. Hämta en identifierare från fabriken. Välj en översättning identifierare för översättning. (Andra identifierarna gäller *tal till Text*.) Det finns olika varianter av översättning tolken baserat på den ljud källa som du använder.

4. Sysselsätta händelser för asynkron åtgärd om så önskas. Tolken anropar din händelsehanterare när den har tillfälligt och slutliga resultaten. Annars får program ett sista översättning resultat.

5. Starta recognition och översättning.

# <a name="sdk-samples"></a>SDK-exempel

Senaste uppsättning exempel, finns det [kognitiva Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

# <a name="next-steps"></a>Nästa steg

- [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
- [Identifiera tal i C#](quickstart-csharp-windows.md)
