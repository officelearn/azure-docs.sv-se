---
title: Skapa ett anpassat wake ord
description: Skapa en anpassad aktivering word för tal Devices SDK.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 615a901c70fff92141442699ea6e4b8fce1c9ace
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282581"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Skapa ett anpassat wake ord med Speech-tjänsten

Din enhet lyssnar alltid för ett wake ord (eller ett uttryck). Till exempel är ”Hey Cortana” ett wake word för Cortana-Installationsassistenten. När användaren säger wake ordet, startar enheten skickar alla efterföljande ljud till molnet tills användaren slutar talar. Anpassa ditt wake ord är ett effektivt sätt att skilja mellan enheten och Stärk din företagsanpassning.

I den här artikeln får du lära dig hur du skapar en anpassad aktivering word för din enhet.

## <a name="choosing-an-effective-wake-word"></a>Välja ett gällande wake ord

Tänk på följande när du väljer ett wake ord.

* Wake-word ska vara ett ord eller en fras. Det bör ta högst två sekunder för att säga.

* Ord av 4 – 7 stavelser fungerar bäst. Till exempel är ”Hey, datorn” ett bra wake ord, medan bara ”Hey” är en dålig.

* Wake ord bör följa den vanliga engelska uttal av regler.

* Ett unikt eller även sydda ord som följer den vanliga engelska uttal av reglerna kan du minska falska positiva identifieringar. ”Computerama” kan till exempel vara en bra wake word.

* Välj inte ett vanligt ord. Till exempel ”äta” och ”gå” är ord som personer säga ofta i vanlig konversationen. De kan vara falskt utlösare för din enhet.

* Undvik att använda ett wake-ord som kan ha alternativ uttal. Användare behöver veta ”höger” uttal att få sin enhet för att svara. Till exempel ”509” kan vara i samband med ”fem noll nio” ”, fem ojsan nio”, eller ”fem hundra och nio”. ”R.E.I.” Det gick att uttalas ”R E jag” eller ”Ray”. ”Live” kan vara uttalas [līv] eller [liv].

* Använd inte specialtecken, symboler och siffror. Till exempel skulle ”Go #” och ”20 + katt” inte vara bra wake orden. Dock ”gå sharp” eller ”tjugo plus katt” skulle kunna fungera. Du kan fortfarande använda symbolerna i din företagsanpassning och använder marknadsföring och dokumentation för att säkerställa rätt uttal.

> [!NOTE]
> Om du väljer ett varumärkesskyddat ord som wake-word, Tänk på att du äger den varumärken, eller så har behörighet från varumärke som ägare till den. Microsoft ansvarar inte för eventuella juridiska problem som kan uppstå i ditt val av wake word.

## <a name="creating-your-wake-word"></a>Skapa ditt wake ord

Innan du kan använda ett anpassat wake ord med din enhet, måste du skapa den med hjälp av tjänsten Microsoft anpassade Wake Word Generation. När du har angett ett ord på wake, tjänsten ger en fil som distribuerar du sedan till ditt dev kit för att aktivera din wake word på enheten.

1. Gå till den [Custom Speech Service portal](https://cris.ai/).

2. Skapa ett nytt konto med den e-postadress som du har fått inbjudan för Azure Active Directory. 

    ![Skapa nytt konto](media/speech-devices-sdk/wake-word-1.png)
 
3.  När du loggat in, Fyll i formuläret och klicka sedan på **starta vägen.**

    ![lyckades logga in](media/speech-devices-sdk/wake-word-3.png)
 
4. Den **anpassad Wake Word** sidan är inte tillgänglig för allmänheten, så det finns ingen länk som leder det. Klicka på eller klistra in i den här länken i stället: https://cris.ai/customkws.

    ![dold sida](media/speech-devices-sdk/wake-word-4.png)
 
6. Ange ordet wake för ditt val kan sedan **skicka** den.

    ![Ange ditt wake ord](media/speech-devices-sdk/wake-word-5.png)
 
7. Det kan ta några minuter för filer som ska genereras. En snurrande cirkel visas på fliken i webbläsaren. Efter en stund visas ett informationsfält där du kan ladda ned en `.zip` fil.

    ![ta emot .zip-fil](media/speech-devices-sdk/wake-word-6.png)

8. Spara den `.zip` filen till datorn. Du behöver den här filen för att distribuera anpassade wake ordet i development Kit följa anvisningarna i [Kom igång med tal enheter SDK](speech-devices-sdk-qsg.md).

9. Du kan nu **logga ut.**

## <a name="next-steps"></a>Nästa steg

Kom igång genom att hämta en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) och registrera dig för tal Devices SDK.

> [!div class="nextstepaction"]
> [Registrera dig för tal Devices SDK](get-speech-devices-sdk.md)

