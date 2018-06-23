---
title: Microsoft Translator Text API dynamiska ordlistan | Microsoft Docs
description: Hur du använder funktionen dynamiskt ordlista i Microsoft översättare Text API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355650"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Hur du använder funktionen dynamiskt ordlista i Microsoft översättare Text-API

Om du redan vet översättning som du vill tillämpa på ett ord eller en fras kan du ange den som markeringar i begäran. Dynamisk ordlistan är endast säkra för sammansatta substantiv som namn och produktnamn. 

**Syntax:** 

< mstrans:dictionary översättning = ”översättning av frasen” > fras < / mstrans:dictionary >

**Exempel: SV-de:**

Datakällan indata: ordet < mstrans:dictionary översättning =\"wordomatic\"> ord eller en fras < / mstrans:dictionary > är en ordlista.

Rikta utdata: Das med en ”wordomatic” ist ein Wörterbucheintrag.

Den här funktionen fungerar på samma sätt med och utan HTML-läge. 

Funktionen bör användas sparsamt. Lämpliga och mycket bättre sätt för att anpassa översättningen är med hjälp av Microsoft översättare Hub. Hubben blir full användning av kontexten och statistiska sannolikhet. Om du har eller har råd att skapa träningsdata som visar ditt arbete eller en fras i kontexten får mycket bättre resultat. Du hittar mer information om navet på [ http://hub.microsofttranslator.com ](http://hub.microsofttranslator.com).

