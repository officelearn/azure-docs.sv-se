---
title: Skapa en anpassad aktivering word - Speech Services
titleSuffix: Azure Cognitive Services
description: Din enhet lyssnar alltid för ett wake ord (eller ett uttryck). När du visar wake ordet har skickar enheten alla efterföljande ljud till molnet, tills användaren slutar talar. Anpassa ditt wake ord är ett effektivt sätt att skilja mellan enheten och Stärk din företagsanpassning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020506"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Skapa ett anpassat wake ord med Speech-tjänsten

Din enhet lyssnar alltid för ett wake ord (eller ett uttryck). Till exempel är ”Hey Cortana” ett wake word för Cortana-Installationsassistenten. När du visar wake ordet har skickar enheten alla efterföljande ljud till molnet, tills användaren slutar talar. Anpassa ditt wake ord är ett effektivt sätt att skilja mellan enheten och Stärk din företagsanpassning.

I den här artikeln får du lära dig hur du skapar en anpassad aktivering word för din enhet.

## <a name="choose-an-effective-wake-word"></a>Välj ett gällande wake ord

Tänk på följande när du väljer ett wake ord:

* Wake-word ska vara ett ord eller en fras. Det bör ta högst två sekunder för att säga.

* Ord av 4-7 stavelser fungerar bäst. Till exempel är ”Hey, datorn” ett bra wake-word. Bara ”Hey” är dålig.

* Wake ord bör följa den vanliga engelska uttal av regler.

* Ett unikt eller ett sydda ord som följer den vanliga engelska uttal reglerna kan minska falska positiva identifieringar. ”Computerama” kan till exempel vara en bra wake word.

* Välj inte ett vanligt ord. Till exempel ”äta” och ”gå” är ord som personer säga ofta i vanlig konversationen. De kan vara falskt utlösare för din enhet.

* Undvik att använda ett wake-ord som kan ha alternativ uttal. Användare behöver veta ”höger” uttal att få sin enhet för att svara. Till exempel ”509” förstärkas ”fem noll nio”, ”fem ojsan nio”, eller ”fem hundra och nio”. ”R.E.I.” förstärkas ”r-e-i” eller ”ray”. ”Live” förstärkas ”/līv/” eller ”/liv/”.

* Använd inte specialtecken, symboler och siffror. Till exempel skulle ”Go #” och ”20 + katt” inte vara bra wake orden. Dock ”gå sharp” eller ”tjugo plus katt” fungerar. Du kan fortfarande använda symbolerna i din företagsanpassning och använda marknadsföring och dokumentation för att säkerställa rätt uttal.

> [!NOTE]
> Om du väljer ett varumärkesskyddat ord som wake-word, Tänk på att du äger den varumärken eller att du har behörighet från varumärke ägare att använda ordet. Microsoft ansvarar inte för eventuella juridiska problem som kan uppstå från ditt val av wake word.

## <a name="create-your-wake-word"></a>Skapa ditt wake ord

Innan du kan använda ett anpassat wake ord med din enhet, måste du skapa ett wake ord med tjänsten Microsoft anpassade Wake Word Generation. När du har angett ett ord på wake, tjänsten ger en fil som distribuerar du din development Kit för att aktivera wake word på enheten.

1. Gå till den [Custom Speech Service Portal](https://aka.ms/sdsdk-speechportal) och **logga in** eller om du inte har en tal-prenumeration väljer [ **skapar du en prenumeration**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Custom Speech Service-portalen](media/speech-devices-sdk/wake-word-4.png)

1. På den [anpassad Wake Word](https://aka.ms/sdsdk-wakewordportal) sidan Ange ordet wake för ditt val och klickar på **Lägg till wake word**. Vi har ett [riktlinjer](#choose-an-effective-wake-word) hjälpa dig att välja en effektiv nyckelord. Vi stöder för närvarande endast en-US språk.

    ![Ange ditt wake ord](media/speech-devices-sdk/wake-word-5.png)

1. Tre alternativ uttal av wake-ord kommer att skapas. Du kan välja alla uttal som helst. Välj sedan **skicka** att generera wake ordet. Om du vill ändra wake ordet ta bort den befintliga påverkas först när du håller muspekaren på raden uttal visas ikonen Ta bort.

    ![Granska ditt wake ord](media/speech-devices-sdk/wake-word-6.png)

1. Det kan ta upp till en minut för modellen som ska genereras. Du uppmanas att hämta filen.

    ![Ladda ned ditt wake ord](media/speech-devices-sdk/wake-word-7.png)

1. Spara ZIP-filen till datorn. Du behöver den här filen för att distribuera dina anpassade wake word development Kit.

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade wake ord med [tal enheter SDK Snabbstart](https://aka.ms/sdsdk-quickstart).
