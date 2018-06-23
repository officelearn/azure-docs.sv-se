---
title: Gränser och kvoter för anpassad Vision Service - kognitiva Azure-tjänster | Microsoft Docs
description: Läs mer om gränser och kvoten för Azure-Cognitives tjänster anpassade Vision.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355053"
---
# <a name="limits-and-quotas"></a>Begränsningar och kvoter

Det finns tre nivåer av nycklar för anpassad Vision tjänsten. F0 och S0 resurser hämtas via Azure portal. Information om priser och transaktioner som finns på den [sida med priser](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  F0 projekt kan uppgraderas till S0 projekt.

Begränsade utvärderingsversion projektresurser som är kopplade till din anpassade Vision inloggning (det vill säga en AAD-konto eller MSA-konto.) De är avsedda att användas för kort tester för tjänsten.  Konton som har skapats under tidig kostnadsfri förhandsversion före införandet av Azure-förhandsgranskningar (1 mars 2018) behåller sina tidigare kvoter för begränsad försök. 

||**Begränsad utvärderingsversion**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projekt|2|2|100|
|Utbildning bilder per projekt|5 000|5 000|50,000|
|Förutsägelser / månad|10 000 |10 000|Obegränsat|
|Taggar / project|50|50|250|
|Upprepningar |10|10|10|
|Minsta etikett bilder per tagg, klassificering (50 + rekommenderas) |5|5|5|
|Minsta etikett bilder per tagg, identifiering av objekt (50 + rekommenderas)|15|15|15|
|Hur länge förutsägelse bilder sparas|30 dagar|30 dagar|30 dagar|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) åtgärder med lagring (transaktioner Per sekund)|2|2|10|
|[Förutsägelse](https://go.microsoft.com/fwlink/?linkid=865445) operations utan lagring (transaktioner Per sekund)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-anrop Per sekund)|2|2|10|
|[Andra API-anrop](https://go.microsoft.com/fwlink/?linkid=865446) (transaktioner Per sekund)|10|10|10|
|Maxstorlek (utbildning avbildningen överför) |6MB|6MB|6MB|
|Maxstorlek (förutsagda)|4MB|4MB|4MB|

Begränsningar för *# utbildning bilder per projekt* och *# taggar/projekt* förväntas vara ökat med tiden för S0 projekt. 
