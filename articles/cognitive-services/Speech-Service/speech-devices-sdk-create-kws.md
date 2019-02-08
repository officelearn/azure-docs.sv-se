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
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 22d270d9bc337b9d7ad776baf5dd35f877c05eae
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856435"
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

Innan du kan använda ett anpassat wake ord med din enhet, måste du skapa wake ordet med hjälp av Microsoft för anpassade Wake Word-Generation-tjänsten. När du har angett ett ord på wake, tjänsten ger en fil som distribuerar du din development Kit för att aktivera din wake word på enheten.

1. Gå till den [Custom Speech service-portalen](https://cris.ai/).

1. Skapa ett nytt konto med den e-postadress som du har fått inbjudan för Azure Active Directory.

    ![Skapa ett nytt konto](media/speech-devices-sdk/wake-word-1.png)

1. Den **anpassad Wake Word** sidan är inte tillgänglig för allmänheten, så det finns ingen direkt koppling som tar dig det. Anpassad talfunktionen kräver en Azure-prenumerationer, men funktionen anpassade Wake Word inte. Om du har fått den **inga prenumerationer hittades.** felsidan, bara Ersätt den **”prenumerationer? errorMessage = ingen % 20Subscriptions % 20found”** med ”**customkws**” i URL: en och träffar på RETUR. URL: en ska vara något av följande: https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws eller https://northeurope.cris.ai/customkws, beroende på var din region är.

    ![Anpassad Wake Word-sida är dold](media/speech-devices-sdk/wake-word-4.png)

1. Skriver wake ordet valfritt och väljer sedan **skicka ordet**.

    ![Ange ditt wake ord](media/speech-devices-sdk/wake-word-5.png)

1. Det kan ta några minuter för filer som ska genereras. Du bör se en snurrande cirkel i webbläsarfönstret. Efter en stund visas ett informationsfält, där du kan ladda ned en ZIP-fil.

    ![Ta emot .zip-filen](media/speech-devices-sdk/wake-word-6.png)

1. Spara ZIP-filen till datorn. Du behöver den här filen för att distribuera anpassade wake ordet development Kit. Om du vill distribuera anpassad aktivering ordet, följer du anvisningarna i [Kom igång med tal enheter SDK](speech-devices-sdk-qsg.md).

1. Välj **logga ut.**

## <a name="next-steps"></a>Nästa steg

Kom igång genom att hämta en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) och registrera dig för tal Devices SDK.

> [!div class="nextstepaction"]
> [Registrera dig för tal Devices SDK](get-speech-devices-sdk.md)
