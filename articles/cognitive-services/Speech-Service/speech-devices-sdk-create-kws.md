---
title: Skapa ett anpassat wake ord
description: Lär dig hur du skapar en anpassad aktivering word för tal enheter SDK.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: e1a74828213b595e6955a750904de7a3c2a5a865
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719094"
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

2. Skapa ett nytt konto med den e-postadress som du har fått inbjudan för Azure Active Directory. 

    ![Skapa ett nytt konto](media/speech-devices-sdk/wake-word-1.png)
 
3.  När du har loggat in, Fyll i formuläret och välj sedan **starta min resa mot**.

    ![har loggat in](media/speech-devices-sdk/wake-word-3.png)
 
4. Den **anpassad Wake Word** sidan är inte tillgänglig för allmänheten, så det finns ingen länk som leder det. Välj eller klistra in i den här länken i stället: https://cris.ai/customkws.

    ![Anpassad Wake Word-sida är dold](media/speech-devices-sdk/wake-word-4.png)
 
6. Skriver wake ordet valfritt och väljer sedan **skicka ordet**.

    ![Ange ditt wake ord](media/speech-devices-sdk/wake-word-5.png)
 
7. Det kan ta några minuter för filer som ska genereras. Du bör se en snurrande cirkel i webbläsarfönstret. Efter en stund visas ett informationsfält, där du kan ladda ned en ZIP-fil.

    ![Ta emot .zip-filen](media/speech-devices-sdk/wake-word-6.png)

8. Spara ZIP-filen till datorn. Du behöver den här filen för att distribuera anpassade wake ordet development Kit. Om du vill distribuera anpassad aktivering ordet, följer du anvisningarna i [Kom igång med tal enheter SDK](speech-devices-sdk-qsg.md).

9. Välj **logga ut.**

## <a name="next-steps"></a>Nästa steg

Kom igång genom att hämta en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) och registrera dig för tal Devices SDK.

> [!div class="nextstepaction"]
> [Registrera dig för tal Devices SDK](get-speech-devices-sdk.md)

