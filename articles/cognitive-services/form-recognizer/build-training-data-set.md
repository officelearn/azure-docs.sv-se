---
title: Hur du skapar en datauppsättning för träning för en anpassad modell - formuläret Igenkännande
titleSuffix: Azure Cognitive Services
description: Lär dig att se till att din datauppsättning för träning är optimerad för att träna en modell för formuläret Igenkännande.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 611d5f7983c61fab12c55a46fedf35a3c420c4c8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454822"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Skapa en datauppsättning för träning för en anpassad modell

När du använder formuläret Igenkännande anpassade modellen kan ange du dina egna utbildningsdata så att modellen kan träna dina branschspecifika former. Du kan träna en modell med fem fyllts i formulär eller ett tomt formulär (inkludera ordet ”Töm” i filnamnet) plus två fyllts i formulär. Även om du har tillräckligt med fyllts i formulär för att träna med kan att lägga till ett tomt formulär i datauppsättningen utbildning förbättra modellen.

## <a name="training-data-tips"></a>Tips för utbildning-data

Det är viktigt att du använder en datauppsättning som är optimerad för utbildning. Använd följande tips för att säkerställa att du får bästa resultat från den [Träningsmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) igen:

* Använd om möjligt textbaserade PDF-dokument i stället för avbildningsbaserad dokument. Genomsökt PDF-filer hanteras som bilder.
* Använd ett tomt formulär och två ifyllda formulär om du har dem tillgängliga.
* Använd exemplen som har alla deras fält som fyllts för fyllts i formulär.
* Använd formulär med olika värden i varje fält.
* Om dina formulär-avbildningar har lägre kvalitet, använda en större mängd data (till exempel 10 – 15 avbildningar).

## <a name="general-input-requirements"></a>Allmänna krav för indata

Kontrollera att din datauppsättning för träning också följer inkommande kraven för allt innehåll i formuläret Igenkännande.
[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar en datauppsättning för träning, följa en Snabbstart för att träna en modell med anpassade formulär Igenkännande och börja använda det på dina formulär.

* [Snabbstart: Träna en modell och extrahera formulärdata med hjälp av cURL](./quickstarts/curl-train-extract.md)
* [Snabbstart: Träna en modell och extrahera formulärdata med hjälp av REST-API med Python](./quickstarts/python-train-extract.md)

