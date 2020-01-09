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
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446717"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Så här använder du en dynamisk ord lista

Om du redan vet vilken översättning du vill använda för ett ord eller en fras kan du ange den som markering i begäran. Den dynamiska ord listan är endast säker för sammansatta substantiv som rätt namn och produkt namn.

**Syntax:**

< msTrans: Ordbok översättning = "översättning av fras" > fras </msTrans: ordbok >

**Krav:**

* `From` och `To` språk måste innehålla engelska och ett annat språk som stöds. 
* Du måste inkludera parametern `From` i begäran om API-omvandling i stället för att använda funktionen för automatisk identifiering. 

**Exempel: sv-de:**

Käll ingången: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Mål utmatning: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Använd funktionen sparsamt. Ett bättre sätt att anpassa översättning är att använda anpassad översättare. Anpassad översättare ger fullständig användning av kontext och statistisk sannolikhet. Om du har eller kan skapa tränings data som visar ditt arbete eller en fras i sammanhanget får du mycket bättre resultat. Du hittar mer information om anpassad översättare på [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
