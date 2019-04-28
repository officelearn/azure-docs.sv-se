---
title: Begränsningar och kvoter – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Läs mer om begränsningar och kvoter för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 85abc4a50710629d3485d05115698e59a93fc96e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995659"
---
# <a name="limits-and-quotas"></a>Begränsningar och kvoter

Det finns två nivåer av nycklar för Custom Vision-tjänsten. Du kan registrera dig för en F0 (kostnadsfri) eller S0 (standard) prenumeration via Azure portal. Se motsvarande [prissättning för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för information om priser och transaktioner.

Antalet inlärningsbilder per projekt och taggar per projekt förväntas öka med tiden för S0-projekt.

||**F0**|**S0**|
|-----|-----|-----|
|Projekt|2|100|
|Inlärningsbilder per projekt |5 000|100 000|
|Förutsägelser / månad|10 000 |Obegränsat|
|Taggar / project|50|500|
|Iterationer |10|10|
|Min några taggade bilder per tagg, klassificering (50 + rekommenderas) |5|5|
|Min några taggade bilder per tagg, objektidentifiering (50 + rekommenderas)|15|15|
|Hur länge förutsägelse bilder sparas|30 dagar|30 dagar|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) operationerna för lagring (transaktioner Per sekund)|2|10|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) åtgärder utan lagring (transaktioner Per sekund)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-anrop Per sekund)|2|10|
|[Andra API-anrop](https://go.microsoft.com/fwlink/?linkid=865446) (transaktioner Per sekund)|10|10|
|Maximal bildstorlek (utbildning bildöverföring) |6 MB|6 MB|
|Maximal bildstorlek (förutsagda)|4 MB|4 MB|
