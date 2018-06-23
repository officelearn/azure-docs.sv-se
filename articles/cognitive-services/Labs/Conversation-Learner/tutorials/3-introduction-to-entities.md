---
title: Hur du använder enheter med ett program i konversationen deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder enheter med ett samtal deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354003"
---
# <a name="introduction-to-entities"></a>Introduktion till entiteter

Den här kursen introducerar entiteter och visar hur du använder fälten ”Disqualifying entiteter” och ”krävs enheter” i åtgärder.

## <a name="requirements"></a>Krav

Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Den här kursen visar två vanliga användningsområden för entiteter.  Entiteter kan först extrahera delsträngar från ett användarmeddelande, till exempel identifiera en ort i ”vad är väder i Seattle”?.  Andra kan entiteter begränsa när åtgärder är tillgängliga.  Mer specifikt en åtgärd kan visa en lista med en entitet som är ”krävs” eller ”diskvalificera”:
- En åtgärd krävs entiteter måste finnas i den bot minne för att åtgärden ska vara tillgänglig
- Diskvalificera entiteter måste *inte* finnas i den bot minne för att åtgärden ska vara tillgänglig

Andra självstudier beskriver andra aspekter av entiteter, till exempel förskapad entiteter, Flervärde och negeras entiteter, programmässiga entiteter och manipulera entiteter i koden.

## <a name="steps"></a>Steg

### <a name="create-the-application"></a>Skapa programmet

1. Klicka på ny App i Webbgränssnittet,
2. Ange IntroToEntities i namn. Klicka på Skapa.

### <a name="create-entity"></a>Skapa en entitet

1. Klicka på entiteter sedan ny entitet.
2. Ange ort i entitetsnamn.
3. Klicka på Skapa

Observera entitetstypen är 'custom' – det innebär att enheten kan exempelvis tränas.  Det finns också förskapad entiteter, vilket innebär att deras beteende kan inte justeras--dessa beskrivs i en annan kursen.

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och sedan ny åtgärd
2. Skriv ”inte vet vilken stad som du vill' svar.
3. Ange $city i diskvalificera entiteter. Klicka på Spara.
    - Det innebär att om den här entiteten har definierats i bot's minnet, sedan den här åtgärden kommer *inte* vara tillgängliga.
2. Klicka på Åtgärder sedan ny åtgärd för att skapa en andra åtgärd.
3. Skriv ”väder i $city är förmodligen Soligt” som svar.
4. Observera att stad entitet lagts till automatiskt eftersom det var som anges i krävs entiteter.
5. Klicka på Spara

Nu har du två åtgärder.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj 'Inte vet vilken stad som du vill ”?
    - Observera att svaret där entiteten stad krävs inte kan väljas eftersom stad entiteten inte har definierats i bot's minne.
2. Välj 'Inte vet vilken stad som du vill'.
4. Ange ”seattle”. Markera seattle och klicka på stad.
5. Klicka på poäng åtgärder
    - Obs stad värdet är nu i den bot minne.
    - 'I $city är förmodligen vackert väder ”är nu tillgängligt som ett svar. 
6. Välj 'I $city är förmodligen vackert väder'.

Anta att användaren anger 'Upprepa som'. 
1. Skriver som och anger. Observera att stad entiteten och dess värde är i minnet och tillgängliga.
2. Välj 'I $city är förmodligen vackert väder'.

![](../media/tutorial3_entities.PNG)

Du har nu skapat en entitet och etiketter instanser av meddelanden om användare.  Du har använt förekomst/avsaknaden av entiteten i den bot minnet för att styra när åtgärder är tillgängliga via åtgärdens diskvalificera och nödvändiga entiteter fält.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förväntade entitet](./4-expected-entity.md)
