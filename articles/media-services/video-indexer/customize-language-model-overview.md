---
title: Anpassa en språkmodell i Video Indexer - Azure
titleSuffix: Azure Media Services
description: Den här artikeln innehåller en översikt över vad som är en språkmodell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838323"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Anpassa en språkmodell med Video Indexer

Video Indexer stöder automatisk taligenkänning genom integrering med Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Du kan anpassa språkmodellen genom att ladda upp anpassningstext, nämligen text från den domän vars ordförråd du vill att motorn ska anpassa sig till. När du tränar din modell, nya ord som visas i anpassningstexten kommer att kännas igen, förutsatt standard uttal, och språkmodellen kommer att lära sig nya sannolika sekvenser av ord. Anpassade språkmodeller stöds för engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska, brasiliansk portugisiska, hindi och koreanska. 

Låt oss ta ett ord som är mycket specifikt, till exempel "Kubernetes" (i samband med Azure Kubernetes-tjänsten), som ett exempel. Eftersom ordet är nytt för Video Indexer, är det erkänt som "samhällen". Du måste träna modellen för att känna igen den som "Kubernetes". I andra fall finns orden, men språkmodellen förväntar sig inte att de ska visas i ett visst sammanhang. Till exempel är "behållartjänst" inte en 2-ordssekvens som en icke-specialiserad språkmodell skulle känna igen som en specifik uppsättning ord.

Du kan välja att ladda upp ord utan sammanhang i en lista i en textfil. Detta anses vara partiell anpassning. Du kan också ladda upp textfiler med dokumentation eller meningar som är relaterade till ditt innehåll för bättre anpassning.

Du kan använda API:erna för videoindexerare eller webbplatsen för att skapa och redigera anpassade språkmodeller, enligt beskrivningen i avsnitten [Nästa steg](#next-steps) i det här avsnittet.

## <a name="best-practices-for-custom-language-models"></a>Metodtips för anpassade språkmodeller

Video Indexer lär sig baserat på sannolikheter för ordkombinationer, så att lära sig bäst:

* Ge tillräckligt med verkliga exempel på meningar som de skulle talas.
* Sätt bara en mening per rad, inte mer. Annars kommer systemet att lära sig sannolikheter över meningar.
* Det är okej att sätta ett ord som en mening för att öka ordet mot andra, men systemet lär sig bäst från fullständiga meningar.
* När du inför nya ord eller förkortningar, om möjligt, ge så många exempel på användning i en fullständig mening för att ge så mycket sammanhang som möjligt till systemet.
* Försök att sätta flera anpassningsalternativ, och se hur de fungerar för dig.
* Undvik upprepning av exakt samma mening flera gånger. Det kan skapa partiskhet mot resten av indata.
* Undvik att inkludera ovanliga symboler (~, # @ % &) eftersom de kommer att bli kasserade. De meningar som de visas i kommer också att kastas.
* Undvik att sätta för stora ingångar, till exempel hundratusentals meningar, eftersom detta kommer att späda ut effekten av att öka.

## <a name="next-steps"></a>Nästa steg

[Anpassa språkmodell med API:er](customize-language-model-with-api.md)

[Anpassa språkmodell med hjälp av webbplatsen](customize-language-model-with-website.md)
