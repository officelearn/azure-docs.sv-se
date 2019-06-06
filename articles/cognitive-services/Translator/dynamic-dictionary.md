---
title: Dynamisk ordlista - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Hur du använder funktionen dynamiskt ordlista i Translator Text API.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: d3a77ee9273e9f587b6157bb6ed2294865b1fca5
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514260"
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
