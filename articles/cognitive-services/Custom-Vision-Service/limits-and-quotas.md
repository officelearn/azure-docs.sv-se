---
title: Begränsningar och kvoter – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Läs mer om begränsningar och kvoter för Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: ce06effbce12abb6271e050829d3218f4fbbfbf4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902870"
---
# <a name="limits-and-quotas"></a>Begränsningar och kvoter

Det finns tre nivåer av nycklar för Custom Vision Service. F0 och S0 resurser hämtas via Azure portal. Information om priser och transaktioner finns på den [prissättningssidan](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  F0 projekt kan uppgraderas till S0-projekt.

Begränsad utvärderingsversion projektresurser som är kopplade till din Custom Vision-inloggning (det vill säga en AAD-konto eller MSA-konto.) De är avsedda att användas för kort utvärderingsversioner av tjänsten.  Konton som skapats under tidig kostnadsfri förhandsversion innan introduktionen av förhandsversioner av Azure (1 mars 2018) behåller sina tidigare kvoter för begränsad tester. 

||**Begränsad utvärderingsversion**|**F0 (Azure)**|**S0 (Azure)**|
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
|Maximal bildstorlek (utbildning bildöverföring) |6MB|6MB|6MB|
|Maximal bildstorlek (förutsagda)|4MB|4MB|4MB|

Begränsningar för *# inlärningsbilder per projekt* och *# taggar/projektet* förväntas vara ökat med tiden för S0-projekt. 
