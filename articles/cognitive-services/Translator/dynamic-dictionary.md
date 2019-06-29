---
title: Dynamisk ordlista - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Hur du använder funktionen dynamiskt ordlista i Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2ef1e474dd5d36f1967501ea7bdedc4736954a2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436016"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Hur du använder en dynamisk ordlista

Om du redan vet översättning som du vill använda på ett ord eller en fras kan ange du den som markup i begäran. Dynamisk ordlistan är endast säkra för sammansatt substantiv som egennamn och produktnamn.

**Syntax:**

< mstrans:dictionary translation = ”översättning av frasen” > frasen < / mstrans:dictionary >

**Exempel: en-de:**

Källa: Ordet < mstrans:dictionary translation =\"wordomatic\"> ord eller fraser < / mstrans:dictionary > är en ordlista-post.

Utdata: DAS med en ”wordomatic” ist ein Wörterbucheintrag.

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Funktionen bör användas sparsamt. Det lämpliga och mycket bättre sättet att anpassa translation är med hjälp av anpassade Translator. Anpassade Translator blir fullt ut utnyttja kontext och statistiska sannolikhet. Om du har eller kan skapa träningsdata som visar ditt arbete eller en fras i sammanhanget, får mycket bättre resultat. Du hittar mer information om anpassade Translator på [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
