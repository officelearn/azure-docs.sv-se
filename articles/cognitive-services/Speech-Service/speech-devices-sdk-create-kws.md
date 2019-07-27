---
title: Skapa en anpassad tjänsten för aktivering av ord i tal
titleSuffix: Azure Cognitive Services
description: Din enhet lyssnar alltid för ett wake ord (eller ett uttryck). När du visar wake ordet har skickar enheten alla efterföljande ljud till molnet, tills användaren slutar talar. Anpassa ditt wake ord är ett effektivt sätt att skilja mellan enheten och Stärk din företagsanpassning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553105"
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

Innan du kan använda ett anpassat aktiverings ord med din enhet måste du skapa ett aktiverings ord med Microsoft Customer generations-tjänsten för automatisk aktivering. När du har angett ett aktiverings ord skapar tjänsten en fil som du distribuerar till ditt utvecklings paket för att aktivera aktiverings ordet på enheten.

1. Gå till [Custom Speech service portal](https://aka.ms/sdsdk-speechportal) och **Logga** in eller om du inte har en tal prenumeration väljer du [**skapa en prenumeration**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Custom Speech Service Portal](media/speech-devices-sdk/wake-word-4.png)

1. På sid typen [anpassad aktiverings ord](https://aka.ms/sdsdk-wakewordportal) i det aktiverings ord du väljer och klicka på **Lägg till aktivering Word**. Vi har några [rikt linjer](#choose-an-effective-wake-word) som hjälper dig att välja ett effektivt nyckelord. För närvarande stöder vi bara ett-US-språk.

    ![Ange ditt wake ord](media/speech-devices-sdk/wake-word-5.png)

1. Tre alternativa uttal av ditt aktiverings ord kommer att skapas. Du kan välja alla uttal som du vill. Välj sedan **Skicka** för att generera aktiverings ordet. Om du vill ändra aktiverings ordet, tar du bort det befintliga, när du hovrar på ututtals raden. då visas ikonen Ta bort.

    ![Granska ditt aktiverings ord](media/speech-devices-sdk/wake-word-6.png)

1. Det kan ta upp till en minut innan modellen genereras. Du uppmanas att ladda ned filen.

    ![Hämta ditt aktiverings ord](media/speech-devices-sdk/wake-word-7.png)

1. Spara ZIP-filen till datorn. Du behöver den här filen för att distribuera ditt anpassade aktiverings ord till utvecklings paketet.

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade aktiverings ord med [tal enheter SDK snabb start](https://aka.ms/sdsdk-quickstart).
