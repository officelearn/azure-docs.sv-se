---
title: Anpassa en språkmodell i Video Indexer - Azure
titlesuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad är en språkmodell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: c4ccc189c0f8ed3de868c9965d7068ad9670cbcb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535418"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Anpassa en språkmodell med Video Indexer

Video Indexer stöder automatisk taligenkänning genom integration med Microsofts [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Du kan anpassa språkmodellen genom att ladda upp anpassning text, nämligen text från domänen vars ordförråd som motorn efter behov. När du träna din modell, nya ord visas i texten anpassning känns igen, förutsatt att standard uttal och språkmodellen kommer att lära dig nya sannolika ordsekvenser. Anpassade språkmodeller som har stöd för engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska, portugisiska (Brasilien), Hindi och koreanska. 

Låt oss ta ett ord som är specifika, som ”Kubernetes” (i samband med Azure Kubernetes service), som exempel. Eftersom ordet är ny i Video Indexer kan identifieras den som ”communities”. Du behöver träna modellen ska kunna identifiera den som ”Kubernetes”. Orden finns i andra fall, men språkmodellen förväntade inte de ska visas i ett visst kontext. Till exempel är ”container service” inte en 2-word-sekvens som en icke specialiserade språkmodell skulle känna igen som en specifik uppsättning orden.

Du har möjlighet att ladda upp ord utan kontext i en lista i en textfil. Detta anses partiella anpassning. Du kan också ladda upp fil saknas text i dokumentationen eller meningar som rör ditt innehåll för bättre anpassning.

Du kan använda API: er för Video Indexer eller webbplatsen att skapa och redigera anpassade språkmodeller som beskrivs i avsnitten i den [nästa steg](#next-steps) i det här avsnittet.

## <a name="best-practices-for-custom-language-models"></a>Metodtips för anpassade språkmodeller

Video Indexer lär sig beroende på sannolikheten för word kombinationer, så att lära dig bäst:

* Ge tillräckligt med verkliga exempel på meningar som de skulle läsas.
* Placera endast en mening per rad, inte mer. Annars systemet lära dig sannolikhet över meningar.
* Det är Okej att placera ett ord som en mening att öka ordet mot andra, men systemet lär sig bäst från hela meningar.
* När Vi presenterar nya ord eller förkortningar, om möjligt ger så många exempel på användning i en fullständig mening att tilldela lika mycket kontext som möjligt i systemet.
* Försök att placera flera alternativ för anpassning och se hur de fungerar för dig.
* Undvika upprepning av exakt samma mening flera gånger. Detta kan skapa bias mot resten av indata.
* Undvika inklusive ovanliga symboler (~, # @ % &) som de kommer få tas bort. Meningar som de visas hämta även ignoreras.
* Undvik att placera för stor indata som meningar, hundratusentals eftersom så kommer Späd effekten av att öka.

## <a name="next-steps"></a>Nästa steg

[Anpassa språkmodellen med API: er](customize-language-model-with-api.md)

[Anpassa språkmodellen med webbplats](customize-language-model-with-website.md)
