---
title: Dynamisk ordlista - Translator Text API
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du använder funktionen för dynamisk ordlista i Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446717"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Så här använder du en dynamisk ordlista

Om du redan känner till den översättning som du vill använda på ett ord eller en fras kan du ange den som pålägg i begäran. Den dynamiska ordlistan är endast säker för sammansatta substantiv som riktiga namn och produktnamn.

**Syntax:**

<mstrans:dictionary translation="översättning av frasen">fras</mstrans:dictionary>

**Krav:**

* Språken `To` och språken `From` måste innehålla engelska och ett annat språk som stöds. 
* Du måste `From` inkludera parametern i din API-översättningsbegäran i stället för att använda funktionen för automatisk detekterect. 

**Exempel: en-de:**

Källinmatning:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Målutgång:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Använd funktionen sparsamt. Ett bättre sätt att anpassa översättningen är att använda Custom Translator. Custom Translator utnyttjar kontext och statistiska sannolikheter fullt ut. Om du har eller kan skapa träningsdata som visar ditt arbete eller din fras i sitt sammanhang får du mycket bättre resultat. Du hittar mer information om [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)Custom Translator på .
