---
title: Anpassa en personmodell i Video Indexer - Azure
titleSuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad som är en personmodell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838300"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Anpassa en personmodell i Video Indexer

Video Indexer stöder kändisigenkänning i dina videor. Funktionen kändisigenkänning täcker cirka en miljon ansikten baserat på vanliga datakällor som IMDB, Wikipedia och de främsta LinkedIn-influencers. Ansikten som inte känns igen av Video Indexer identifieras fortfarande men lämnas namnlösa. Kunder kan skapa anpassade personmodeller och aktivera Video Indexer för att känna igen ansikten som inte känns igen som standard. Kunder kan skapa dessa personmodeller genom att para ihop en persons namn med bildfiler i personens ansikte.  

Om ditt konto tillgodoser olika användningsfall kan du dra nytta av att kunna skapa modeller med flera personer per konto. Om innehållet i ditt konto till exempel är avsett att sorteras i olika kanaler kanske du vill skapa en separat personmodell för varje kanal. 

> [!NOTE]
> Varje personmodell stöder upp till 1 miljon personer och varje konto har en gräns på 50 Person-modeller. 

När en modell har skapats kan du använda den genom att ange modell-ID för en viss personmodell när du laddar upp/indexerar eller indexerar om en video. Utbildning ett nytt ansikte för en video, uppdaterar den specifika anpassade modellen som videon var associerad med. 

Om du inte behöver stöd för modellen med flera personer ska du inte tilldela ditt video-ID till en person när du laddar upp/indexerar eller indexerar om. I det här fallet använder Video Indexer standardmodellen person i ditt konto. 

Du kan använda webbplatsen Video Indexer för att redigera ansikten som har upptäckts i en video och hantera flera anpassade personmodeller i ditt konto, enligt beskrivningen i [modellen Anpassa en person med hjälp av ett webbplatsavsnitt.](customize-person-model-with-website.md) Du kan också använda API:et enligt beskrivningen i [Anpassa en personmodell med API:er](customize-person-model-with-api.md).
