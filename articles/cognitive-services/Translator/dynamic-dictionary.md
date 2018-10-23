---
title: Dynamisk ordlista - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Hur du använder funktionen dynamiskt ordlista i Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 0b96325b2d29abd230e4c389b176e97542a70282
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648259"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Hur du använder funktionen dynamiskt ordlista i Translator Text API

Om du redan vet översättning som du vill använda på ett ord eller en fras kan ange du den som markup i begäran. Dynamisk ordlistan är endast säkra för sammansatt substantiv som egennamn och produktnamn.

**Syntax:**

< mstrans:dictionary translation = ”översättning av frasen” > frasen < / mstrans:dictionary >

**Exempel: en-de:**

Käll-indata: ordet < mstrans:dictionary translation =\"wordomatic\"> ord eller fraser < / mstrans:dictionary > är en ordlista-post.

Rikta utdata: Das med en ”wordomatic” ist ein Wörterbucheintrag.

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Funktionen bör användas sparsamt. Det lämpliga och mycket bättre sättet att anpassa translation är med hjälp av anpassade Translator. Anpassade Translator blir fullt ut utnyttja kontext och statistiska sannolikhet. Om du har eller kan skapa träningsdata som visar ditt arbete eller en fras i sammanhanget, får mycket bättre resultat. Du hittar mer information om anpassade Translator på [ http://aka.ms/CustomTranslator ](http://aka.ms/CustomTranslator).
