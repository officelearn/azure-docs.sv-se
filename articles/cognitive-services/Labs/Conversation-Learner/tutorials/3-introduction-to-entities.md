---
title: Hur du använder enheter med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder enheter med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f851d43d69999a848dea01c9457a379adb63353b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172389"
---
# <a name="introduction-to-entities"></a>Introduktion till entiteter

Den här självstudien introducerar entiteter och visar hur du använder ”Disqualifying entiteter” och ”krävs entiteter” fält i åtgärder.

## <a name="video"></a>Video

[![Självstudie 3 förhandsversion](http://aka.ms/cl-tutorial-03-preview)](http://aka.ms/blis-tutorial-03)

## <a name="requirements"></a>Krav

Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Den här självstudien visar två vanliga användningsområden för entiteter.  Entiteter kan först extrahera delsträngar från ett användarmeddelande, till exempel identifiera en stad i ”vad är vädret i Seattle”?.  Andra kan entiteter begränsa när åtgärder är tillgängliga.  Mer specifikt kan en åtgärd ange en entitet som ”måste” eller ”diskvalificera”:
- En åtgärd krävs entiteter måste finnas i robotens minne för att åtgärden ska vara tillgängliga
- Diskvalificera entiteter måste *inte* i robotens minnet för att åtgärden ska vara tillgängliga

Andra självstudier beskriver andra aspekter av enheter, till exempel färdiga entiteter, flera värden och negeras entiteter, programmässiga entiteter och manipulera entiteter i kod.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange IntroToEntities i namn. Klicka på Skapa.

### <a name="create-entity"></a>Skapa entitet

1. Klicka på entiteter och ny entitet.
2. Ange ort i enhetens namn.
3. Klicka på Skapa

> [!NOTE]
> Denna typ är 'custom' – Detta innebär att entiteten kan tränas.  Det finns även färdiga entiteter, vilket innebär att deras beteende inte kan ändras – dessa beskrivs i en annan självstudie.

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och ny åtgärd
2. I svaret, skriver du ”Jag vet inte vilken stad som du vill att”.
3. Ange $city i diskvalificera entiteter. Klicka på Spara.
    - Det innebär att om den här entiteten har definierats i minnet för robotens använder sedan den här åtgärden kommer *inte* vara tillgängliga.
2. Klicka på åtgärder och sedan ny åtgärd för att skapa en andra åtgärd.
3. Skriv ”vädret i $city är förmodligen solig” som svar.
4. I krävs entiteter lagts stad entitet automatiskt eftersom den var som anges.
5. Klicka på Spara

Nu har du två åtgärder.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”jag vet inte vilken stad som du vill”?
    - Svaret där entiteten stad krävs kan inte väljas eftersom stad entiteten inte har definierats i robotens minne.
2. Välj ”jag vet inte vilken stad som du vill att”.
4. Ange ”seattle”. Markera seattle och klicka på ort.
5. Klicka på poäng åtgärder
    - Värde för stad är nu i robotens minne.
    - ”I $city är förmodligen vackert väder” är nu tillgänglig som ett svar. 
6. Välj ”i $city är förmodligen vackert väder”.

Vi antar att användaren anger 'Upprepa som ”. 
1. Skriver som och anger. Stad entiteten och dess värde är i minnet och tillgängligt.
2. Välj ”i $city är förmodligen vackert väder”.

![](../media/tutorial3_entities.PNG)

Du har nu skapat en entitet och märkta instanser av meddelanden för användare.  Du har använt närvaro/avsaknad av entiteten i robotens minnet för att styra när åtgärder är tillgängliga via åtgärdens diskvalificera och nödvändiga entiteter fält.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förväntade entiteten](./4-expected-entity.md)
