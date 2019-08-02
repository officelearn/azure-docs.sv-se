---
title: Använda entiteter med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder entiteter med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: cba12b6c09c1bdbf4e8f7841676a609c34109d93
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707341"
---
# <a name="introduction-to-entities"></a>Introduktion till entiteter

I den här självstudien beskrivs entiteter, diskvalificerande entiteter, obligatoriska entiteter och deras användning inom Conversation Learner.

## <a name="video"></a>Video

[![Förhands granskning av introduktion till entiteter](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Krav

Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information

Entiteter samlar in de delar av information som bot-roboten behöver för att utföra uppgiften, antingen genom extrahering från användare yttranden eller tilldelning efter anpassad kod. Entiteter kan också begränsa åtgärdens tillgänglighet genom att uttryckligen klassificeras som "nödvändig" eller "dekvalificerande".

- Obligatoriska entiteter måste finnas i modellens minne för att åtgärden ska vara tillgänglig
- Diskvalificerande entiteter får *inte* finnas i modellens minne för att åtgärden ska vara tillgänglig

Den här självstudien fokuserar på anpassade entiteter. Förtränade, flervärdesfält, negation bara entiteter och programmatiska enheter introduceras i andra självstudier.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I webb gränssnittet klickar du på "ny modell".
2. Skriv "IntroToEntities" i fältet "namn" och tryck på RETUR.
3. Klicka på knappen "skapa".

### <a name="entity-creation"></a>Skapa entitet

1. Klicka på entiteter i den vänstra panelen, sedan på knappen ny enhet.
2. Välj anpassad tränad för enhets typen.
3. Skriv "stad" för "entitetsnamnet".
4. Klicka på knappen "skapa".

> [!NOTE]
> Entitetstypen anpassad enhets typ innebär att den här entiteten kan tränas, till skillnad från andra typer av entiteter.

### <a name="create-the-actions"></a>Skapa åtgärder

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." anger du "Jag vet inte vilken stad du vill ha."
3. Skriv "ort" i fältet "diskvalificera entiteter".
4. Klicka på knappen "skapa".

> [!NOTE]
> Om du lägger till entiteten "ort" i "diskvalificerande entiteter" skulle den här åtgärden tas från botens övervägande när entiteten "ort" definieras i robotens minne.

Nu skapar du en andra åtgärd.

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." anger du "Väder i $city är förmodligen solig".
3. Klicka på knappen "skapa".

> [!NOTE]
> Entiteten "ort" har lagts till automatiskt i listan över obligatoriska entiteter efter referens i svaret.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Träna modellen

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "Hej".
    - Detta simulerar användarens sida av konversationen.
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "Jag vet inte vilken stad du vill ha."
5. Som användaren svarar med, "Seattle".
6. Klicka på knappen "Poäng åtgärder".
7. Välj svaret "Väder i $city är förmodligen solig".
8. Klicka på knappen Spara.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förväntad entitet](./05-expected-entity.md)
