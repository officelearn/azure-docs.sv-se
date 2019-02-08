---
title: Priser och begränsningar – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Läs mer om begränsningar och kvoter för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: anroth
ms.openlocfilehash: 8e96447f2a1b5b68ebf99ab4006fe43937f4c75f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884434"
---
# <a name="pricing-and-limits"></a>Priser och begränsningar

Det finns tre nivåer av nycklar för Custom Vision-tjänsten. Begränsad utvärderingsversion projektresurser är kopplade till din Custom Vision-inloggning (det vill säga en Azure Active Directory-konto eller MSA-konto). De är avsedda att användas för kort utvärderingsversioner av tjänsten. Du kan registrera dig för en F0 (kostnadsfri) eller S0 (standard) prenumeration via Azure portal. Se motsvarande [prissättning för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för information om priser och transaktioner.

Konton som skapats under tidig kostnadsfri förhandsversion, före införandet av förhandsversioner av Azure (1 mars 2018), behåller sina tidigare kvoter för begränsad tester. 

Antalet inlärningsbilder per projekt och taggar per projekt förväntas öka med tiden för S0-projekt.

||**Begränsad utvärderingsversion**|**F0**|**S0**|
|-----|-----|-----|-----|
|Projekt|2|2|100|
|Inlärningsbilder per projekt, klassificering|5 000|5 000|50,000|
|Inlärningsbilder per projekt, objektidentifiering|5 000|5 000|10 000|
|Förutsägelser / månad|10 000 |10 000|Obegränsat|
|Taggar / project|50|50|250|
|Iterationer |10|10|10|
|Minst några taggade bilder per tagg, klassificering (50 + rekommenderas) |5|5|5|
|Minst några taggade bilder per tagg, objektidentifiering (50 + rekommenderas)|15|15|15|
|Hur länge förutsägelse bilder sparas|30 dagar|30 dagar|30 dagar|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) operationerna för lagring (transaktioner Per sekund)|2|2|10|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) åtgärder utan lagring (transaktioner Per sekund)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-anrop Per sekund)|2|2|10|
|[Andra API-anrop](https://go.microsoft.com/fwlink/?linkid=865446) (transaktioner Per sekund)|10|10|10|
|Maximal bildstorlek (utbildning bildöverföring) |6 MB|6 MB|6 MB|
|Maximal bildstorlek (förutsagda)|4 MB|4 MB|4 MB|


