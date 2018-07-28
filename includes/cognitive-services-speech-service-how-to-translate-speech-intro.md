---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 4b823a8dc15d33e0537fae348b34c3a3fee84558
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330923"
---
<!-- N.B. no header, language-agnostic -->

Den [tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) är det enklaste sättet att använda talöversättning i ditt program.
SDK innehåller alla funktioner i tjänsten.
Den grundläggande processen för att utföra talöversättning omfattar följande steg:

1. Skapa en tal-fabrik, vilket ger en prenumerationsnyckel för tal-tjänst och en [region](~/articles/cognitive-services/speech-service/regions.md).
   Du kan konfigurera språk för käll- och översättning, samt att ange om du vill att text eller tal utdata.

1. Få en översättning identifierare från tal fabriken.
   Det finns olika varianter av översättning identifierare baserat på ljudkälla som du använder.

1. Koppla in händelser för asynkron åtgärd om du vill.
   Identifieraren anropar sedan din händelsehanterare när den har tillfälliga och slutliga resultaten.
   Annars kan får ditt program en slutlig avskrift resultatet.

1. Starta erkännande och översättning.

Nedan beskrivs flera kodfragment för speech translation-scenarier med hjälp av tal-SDK.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
