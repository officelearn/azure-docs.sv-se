---
title: Gränser och kvoter - Service för anpassad syn
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs de olika typerna av licensnycklar och om begränsningar och kvoter för Tjänsten Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081785"
---
# <a name="limits-and-quotas"></a>Begränsningar och kvoter

Det finns två nivåer med nycklar för tjänsten Custom Vision. Du kan registrera dig för en F0-prenumeration (gratis) eller S0 (standard) via Azure-portalen. Mer information om priser och transaktioner finns på motsvarande sida för [kognitiva tjänster.](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)

Antalet utbildningsbilder per projekt och taggar per projekt förväntas öka med tiden för S0-projekt.

||**F0**|**S0**|
|-----|-----|-----|
|Projekt|2|100|
|Utbildning av bilder per projekt |5 000|100 000|
|Förutsägelser / månad|10 000 |Unlimited|
|Taggar / projekt|50|500|
|Iterationer |10|10|
|Min märkta bilder per tagg, Klassificering (50+ rekommenderas) |5|5|
|Minmärkta bilder per tagg, objektidentifiering (50+ rekommenderas)|15|15|
|Hur länge förutsägelsebilder lagras|30 dagar|30 dagar|
|[Förutsägelseåtgärder](https://go.microsoft.com/fwlink/?linkid=865445) med lagring (transaktioner per sekund)|2|10|
|[Förutsägelseåtgärder](https://go.microsoft.com/fwlink/?linkid=865445) utan lagring (transaktioner per sekund)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-anrop per sekund)|2|10|
|[Andra API-anrop](https://go.microsoft.com/fwlink/?linkid=865446) (transaktioner per sekund)|10|10|
|Godkända bildtyper|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Min bildhöjd/bredd i pixlar|256 (se anmärkning)|256 (se anmärkning)|
|Max bildhöjd/bredd i pixlar|obegränsat|obegränsat|
|Max bildstorlek (uppladdning av träningsbilder) |6 MB|6 MB|
|Max bildstorlek (förutsägelse)|4 MB|4 MB|
|Max utbildning av regioner per objektidentifiering|300|300|
|Max taggar per klassificeringsbild|100|100|

> [!NOTE]
> Bilder som är mindre än 256 pixlar accepteras men skalas upp.
