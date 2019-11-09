---
title: Dynamisk ord lista – Translator Text API
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du använder funktionen för dynamisk ord lista i Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5f007660b98a22ff9ffad072991a39cf59518b6a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837342"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Så här använder du en dynamisk ord lista

Om du redan vet vilken översättning du vill använda för ett ord eller en fras kan du ange den som markering i begäran. Den dynamiska ord listan är bara säker för sammansatta substantiv som rätt namn och produkt namn.

**Uttryck**

< msTrans: Ordbok översättning = "översättning av fras" > fras </msTrans: ordbok >

**Signaturkrav**

* `From` och `To` språk måste vara olika. 
* Du måste inkludera parametern `From` i begäran om API-omvandling i stället för att använda funktionen för automatisk identifiering. 

**Exempel: sv-de:**

Käll inmatning: ordet < msTrans: ord lista översättning =\"WordOMatic\"> ord eller fras </msTrans: ordbok > är en post i ord listan.

Mål utmatning: Das Wort "WordOMatic" ist Ein Wörterbucheintrag.

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Funktionen bör användas sparsamt. Ett lämpligt och mycket bättre sätt att anpassa översättning är genom att använda anpassad översättare. Anpassad översättare ger fullständig användning av kontext och statistisk sannolikhet. Om du har eller kan skapa tränings data som visar ditt arbete eller en fras i sammanhanget får du mycket bättre resultat. Du hittar mer information om anpassad översättare på [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
