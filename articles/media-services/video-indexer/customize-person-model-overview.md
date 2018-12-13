---
title: Anpassa en Person-modell i Video Indexer - Azure
titlesuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad är en Person-modell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285027"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Anpassa en Person-modell i Video Indexer


Video Indexer stöder ansiktsigenkänning och kändisigenkänning för videoinnehåll. Funktionen för igenkänning av kändisar täcker ungefär 1 000 000 ansikten baserat på vanliga datakälla, till exempel IMDB och Wikipedia övre LinkedIn Påverkare. Ansikten som inte känns igen av funktionen för igenkänning av kändisar identifieras. men de är kvar Namnlös. När du laddar upp din video till Video Indexer och få tillbaka resultat kan du gå tillbaka och namnge ansikten som inte tolkats. När du sätter en etikett ett ansikte med ett namn, läggs ansikte och namn till i ditt konto Person modellen. Video Indexer ser sedan den här ansikte i framtida videor och senaste videor.

Du kan använda Video Indexer-webbplats eller API: et för att redigera ansikten som upptäcktes på en video i ditt konto, enligt beskrivningen i följande avsnitt:

- [Anpassa Person modell med API: er](customize-person-model-with-api.md)
- [Anpassa Person modell med hjälp av webbplatsen](customize-person-model-with-website.md)
