---
title: Använda alternativa indata med konversation deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om att använda alternativa indata med konversation deltagaren.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2214436b193932e5b3b80c190f7754a0436b7ed8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354120"
---
# <a name="how-to-use-alternative-inputs"></a>Hur du använder alternativa indata

Den här kursen visar hur du använder fältet ”alternativa indata” till användaren i gränssnittet lärare.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
”Alternativa indata” är alternativ utterances som användaren kan ha dessa vid en viss i dialogrutan för utbildning. Alternativa indata kan du ange mer compactly variationer av vilka en användare kan exempelvis utan att behöva alla varianter i separata tränings-dialogruta.

## <a name="steps"></a>Steg

### <a name="create-the-application"></a>Skapa programmet

1. Klicka på ny App i Webbgränssnittet,
2. Ange AlternativeInputs i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter sedan ny entitet.
2. Ange ort i entitetsnamn.
3. Klicka på Skapa.

### <a name="create-three-actions"></a>Skapa tre åtgärder

1. Klicka på åtgärder och sedan ny åtgärd
2. Skriv ”vilken stad vill du fortsätta?' svar.
3. Ange $city i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden:

1. Klicka på Åtgärder sedan ny åtgärd.
3. Skriv ”väder i $city är förmodligen Soligt” som svar.
4. Nödvändiga entiteter, ange $city.
4. Klicka på Skapa.

Skapa den tredje åtgärden:

1. Klicka på Åtgärder sedan ny åtgärd.
3. Skriv försök ber om väder svar.
    - Detta är som svar på användarnas frågor som ”vad kan systemet”?
4. Ange $city i diskvalificera entiteter.
4. Klicka på Skapa

Nu har du tre åtgärder.

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”vad är väder”.
3. Klicka på poäng åtgärder och välj ”vilken stad vill du fortsätta?'
2. Ange 'denver'.
3. Dubbelklicka på 'denver' och välj stad.
    - Den markerad som en stad enhet.
5. Klicka på poäng åtgärder
    - Observera att denver är finns i entiteten stad. 
6. Välj 'väder i $city är förmodligen Soligt'.
7. Klicka på klar lärare.

Lägg till en annan dialogruta för exempel:

1. Klicka på ny åtgärd och sedan dialogrutan Ny tåget.
2. Skriv ”vad kan du göra?'.
3. Klicka på poäng åtgärder och välj försök ber om väder
2. Ange ”vad är väder i seattle”.
3. Dubbelklicka på ”seattle” och välj stad.
    - Den markerad som en stad enhet.
5. Klicka på poäng åtgärder
    - Observera att seattle är finns i entiteten stad. 
6. Välj 'väder i $city är förmodligen Soligt'.
7. Klicka på klar lärare.

Nu ska vi se vad som händer om användaren säger semantiskt liknande i ovanstående:

1. Klicka på ny åtgärd och sedan dialogrutan Ny tåget.
2. Skriv ”help'.
3. Klicka på poäng åtgärder.
    - Observera att i resultaten för de två möjliga svar är mycket nära. Detta talar om för oss modellen du är osäker gränsen mellan de två åtgärderna.
6. Klicka på avbryta lära och bekräfta.

![](../media/tutorial8_closescores.png)

I så fall skulle det hjälpa att lägga till alternativa indata till dialogrutor. Du kan lägga till dem som du gör lärare. Du kan också gå tillbaka och lägga till dem senare.

2. Klicka på ”vad kan du göra”? i Train dialogrutor.
2. I dialogrutan klickar du på ”vad kan du göra”? att välja den.
    1. Ange ett antal alternativ i Lägg till alternativa indata i den högra rutan under entiteten identifiering:
    1. Ange, vilka är Mina val?'
    2. Ange 'Berätta min val'.
    3. Ange 'hjälp'
    1. Klicka på Skicka ändringar.


![](../media/tutorial8_helpalternates.png)

2. Klicka på ”vad är väder i seattle”.
    1. Ange i Lägg till alternativa indata, göra prognoser för seattle.
    2. Dubbelklicka på ”seattle” och välj stad. Observera att entiteter för alternativa indata ska vara tillgängligt och har samma uppsättning enheter. Det är bra om innehållet i enheterna är olika.
    3. Lägg till alternativa indata, ange, kommer den rain idag i denver'.
    4. Klicka på 'denver' och välj stad.
    5. Klicka på Skicka ändringar och klar.


Lägg till alternativa indata till dialogrutan första:

1. Klicka på tåget dialogrutor.
2. Klicka på dialogrutan börjar med ”vad är väder”.
2. Klicka på ”vad är väder” i den vänstra rutan:
    1. Lägg till alternativa indata, ange 'väder prognos'.
    2. Ange, kommer den rain ”?
    3. Klicka på Skicka ändringar.
4. Klicka på 'denver' i den vänstra rutan:
    1. I Lägg till alternativa indata, anger du ”för denver'.
    2. Ange göra prognoser för austin.
        - Observera att fullständiga frasen markeras. Klicka på frasen och rött x. Sedan väljer austin och klicka på stad.
        - Klicka på Skicka ändringar
    1. Klicka på klar som gör att modellen för att träna om.

![](../media/tutorial8_altcities.png)

Nu ska vi prova variationerna:

1. Klicka på ny Train-dialogruta.
2. Skriv ”vad är du funktioner'.
3. Klicka på poäng åtgärder.
    - Observera att poängen nu mer avgörande för nästa åtgärd som anger säkerhet i modellen.
2. Välj försök ber om väder.
6. Klicka på klar lärare

Du har nu visas hur alternativa indata kan användas för att ange andra saker som användaren kan ha sagt. De hjälper dig att undvika att skapa många dialogrutor som är likadana, genom att dölja dem till en enda dialog och räknar upp vad användaren kan säga på många olika sätt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Loggen dialogrutor](./9-log-dialogs.md)
