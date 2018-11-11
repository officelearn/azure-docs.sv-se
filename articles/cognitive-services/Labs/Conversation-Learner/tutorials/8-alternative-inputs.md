---
title: Använda alternativa indata med Konversationsdeltagare - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder alternativa indata med Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3b38cc265b4adfb301dc1165e02b17a8aa7c9589
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252555"
---
# <a name="how-to-use-alternative-inputs"></a>Hur du använder alternativa indata

Den här självstudiekursen visar hur du använder fältet ”alternativa indata” för indata från användaren i gränssnittet undervisning.

## <a name="video"></a>Video

[![Självstudien 8 förhandsversion](https://aka.ms/cl-tutorial-08-preview)](https://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
”Alternativa indata” är alternativa användaren yttranden som användaren kan ha dessa vid en viss i en dialogruta för utbildning. Alternativa indata kan du ange mer compactly varianter av vad en användare kan det stå, utan att behöva alla varianter i en separata tränings-dialogruta.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange AlternativeInputs i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter och ny entitet.
2. Ange ort i enhetens namn.
3. Klicka på Skapa.

### <a name="create-three-actions"></a>Skapa tre åtgärder

1. Klicka på åtgärder och ny åtgärd
2. I svaret, skriver du ”vilken stad vill”?.
3. Ange $city i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden:

1. Klicka på åtgärder och ny åtgärd.
3. Skriv ”vädret i $city är förmodligen solig” som svar.
4. Nödvändiga entiteter, ange $city.
4. Klicka på Skapa.

Skapa den tredje åtgärden:

1. Klicka på åtgärder och ny åtgärd.
3. Ange försök efterfrågar vädret som svar.
    - Detta är som svar på användarnas frågor som ”vad kan systemet”?
4. Ange $city i diskvalificera entiteter.
4. Klicka på Skapa

Nu har du tre åtgärder.

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”vad blir vädret'.
3. Klicka på poäng åtgärder och välj ”vilken stad vill”?
2. Ange ”denver'.
3. Dubbelklicka på ”denver' och välj stad.
    - Den markerad som en stad-enhet.
5. Klicka på poäng åtgärder
    - 'denver ”finns nu i entiteten stad. 
6. Välj ”vädret i $city är förmodligen solig”.
7. Klicka på klar undervisning.

Lägg till ett annat exempel dialogrutan:

1. Klicka på ny åtgärd och sedan dialogrutan Ny träna.
2. Skriv ”vad kan du göra”?.
3. Klicka på poäng åtgärder och välj försök efterfrågar vädret
2. Ange ”vad blir vädret i seattle”.
3. Dubbelklicka på ”seattle” och välj stad.
    - Den markerad som en stad-enhet.
5. Klicka på poäng åtgärder
    - ”seattle” finns nu i entiteten stad. 
6. Välj ”vädret i $city är förmodligen solig”.
7. Klicka på klar undervisning.

Nu ska vi se vad som händer om användaren säger något semantiskt liknar det ovan:

1. Klicka på ny åtgärd och sedan dialogrutan Ny träna.
2. Skriv ”help”.
3. Klicka på poäng åtgärder.
    - Poängen för två potentiella svar är mycket nära. Detta talar om för oss modellen är svårt att förstå gränsen mellan de två åtgärderna.
6. Klicka på Abandon undervisar och bekräfta.

![](../media/tutorial8_closescores.png)

I det här fallet skulle det bidra till att lägga till alternativa indata till dialogrutor. Du kan lägga till dem som du gör undervisningen. Du kan också gå tillbaka och lägga till dem senare.

2. Klicka på ”vad kan du göra”? i träna dialogrutor.
2. I dialogrutan klickar du på ”vad kan du göra”? att välja den.
    1. I den högra rutan, under entitet identifiering i Lägg till alternativa indata anger du ett par alternativ:
    1. Ange ”vilka är Mina alternativ”?
    2. Ange ”berätta Mina alternativ”.
    3. Ange ”hjälp”
    1. Klicka på Skicka ändringar.


![](../media/tutorial8_helpalternates.png)

2. Klicka nu på ”vad blir vädret i seattle”.
    1. Ange i Lägg till alternativa indata, göra prognoser för seattle.
    2. Dubbelklicka på ”seattle” och välj stad. Entiteter för alternativa indata ska vara tillgängligt och har samma uppsättning enheter. Det är bra om innehållet i entiteterna är olika.
    3. I Lägg till alternativa indata, anger du ”kommer den rain idag i denver'.
    4. Klicka på ”denver' och välj stad.
    5. Klicka på Skicka ändringar och klar.


Nu ska vi lägga till alternativa indata till den första dialogrutan:

1. Klicka på träna dialogrutor.
2. Klicka på dialogrutan börjar med ”vad blir vädret'.
2. Klicka för att välja ”vad blir vädret” i den vänstra rutan:
    1. I Lägg till alternativa indata, anger du ”väderprognos'.
    2. Ange ”kommer den rain”?
    3. Klicka på Skicka ändringar.
4. Klicka för att välja ”denver” i den vänstra rutan:
    1. I Lägg till alternativa indata, anger du ”för denver'.
    2. Ange göra prognoser för austin.
        - Fullständig frasen är markerad. Klicka på den frasen och rött x. Välj austin, och klicka sedan på ort.
        - Klicka på Skicka ändringar
    1. Klicka på klar vilket gör att träna modellen.

![](../media/tutorial8_altcities.png)

Nu ska vi prova varianter:

1. Klicka på dialogrutan för nytt träna.
2. Skriv ”vilka använder du funktioner”.
3. Klicka på poäng åtgärder.
    - Poängen är nu mer avgörande på nästa åtgärd som anger säkerheten vid modellen.
2. Välj försök ber om väder.
6. Klickar du på klar undervisning

Du har nu sågs hur alternativa indata som kan användas för att ange andra saker som du kanske har sagt. De hjälper dig att undvika att skapa många dialogrutor som är likadana, genom att dölja dem till en enda dialog och räkna upp vad användaren kan du säga på många sätt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Log-dialogrutor](./9-log-dialogs.md)
