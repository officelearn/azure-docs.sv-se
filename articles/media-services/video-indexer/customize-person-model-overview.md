---
title: Anpassa en Person-modell i Video Indexer - Azure
titlesuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad är en Person-modell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: b491120639421d85d2fbb1a0efb2b6dd09ec1d4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794656"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Anpassa en Person-modell i Video Indexer

Video Indexer stöder kändisigenkänning i dina videor. Funktionen för igenkänning av kändisar täcker ungefär en miljon ansikten baserat på vanliga datakälla, till exempel IMDB och Wikipedia övre LinkedIn Påverkare. Ansikten som inte känns igen av Video Indexer identifieras fortfarande men lämnas Namnlös. Kunder kan skapa anpassade Person modeller och aktivera Video Indexer att känna igen ansikten som inte känns igen som standard. Kunder kan skapa dessa modeller för Person genom att länka ett namn med avbildningar av personens ansikte.  

Om ditt konto caters till olika användningsfall, kan du dra nytta av att kunna skapa flera Person modeller per konto. Om innehållet i ditt konto är avsedd att sorteras i olika kanaler, kan du till exempel vill skapa en separat Person-modell för varje kanal. 

> [!NOTE]
> Varje Person modell stöder upp till 1 miljon personer och varje konto har en gräns på 50 Person modeller. 

När en modell har skapats kan använda du den genom att ange modell-ID för en specifik Person modell när du överför/indexera eller omindexering en video. Utbildning utseende för en video och uppdaterar specifik anpassade modellen som videon var associerad med. 

Om du inte behöver stöd för flera Person-modellen kan inte tilldela någon modell-ID till videon vid uppladdning/indexera eller omindexering. I det här fallet använder Video Indexer Person standardmodellen i ditt konto. 

Du kan använda Video Indexer-webbplatsen att redigera ansikten som upptäcktes på en video och hantera flera anpassade Person modeller i ditt konto, enligt beskrivningen i den [anpassa en Person modell med hjälp av en webbplats](customize-person-model-with-website.md) avsnittet. Du kan också använda API: et, enligt beskrivningen i [anpassa en Person modell med API: er](customize-person-model-with-api.md).