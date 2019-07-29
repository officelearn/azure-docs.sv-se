---
title: Dynamisk ord lista – Translator Text API
titleSuffix: Azure Cognitive Services
description: Hur du använder funktionen för dynamisk ord lista i Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f586702350b38c6d546767f64a8e2f6115b59817
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595301"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Så här använder du en dynamisk ord lista

Om du redan vet vilken översättning du vill använda för ett ord eller en fras kan du ange den som markering i begäran. Den dynamiska ord listan är bara säker för sammansatta substantiv som rätt namn och produkt namn.

**Syntax:**

< msTrans: Ordbok översättning = "översättning av fras" > fras </msTrans: ordbok >

**Signaturkrav**

* Språken `From` och`To` måste vara olika. 
* Du måste inkludera `From` parametern i API-förflyttningsverktyget i stället för att använda funktionen för automatisk identifiering. 

**Exempel: sv-de:**

Käll ineffekt: Ordet < msTrans: ord lista översättning =\"WordOMatic\"> ord eller fras </msTrans: ord lista > är en post i ord listan.

Mål utmatning: Das Wort "WordOMatic" ist Ein Wörterbucheintrag.

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Funktionen bör användas sparsamt. Ett lämpligt och mycket bättre sätt att anpassa översättning är genom att använda anpassad översättare. Anpassad översättare ger fullständig användning av kontext och statistisk sannolikhet. Om du har eller kan skapa tränings data som visar ditt arbete eller en fras i sammanhanget får du mycket bättre resultat. Du hittar mer information om anpassad översättare på [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
