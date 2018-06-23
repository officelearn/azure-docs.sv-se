---
title: Skapa en anpassad aktivering word | Microsoft Docs
description: Skapa en anpassad aktivering word för tal enheter SDK.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2575ed24bb931ca4da05dd6663b976406af590e6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355668"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Skapa en anpassad aktivering word tal-tjänsten

Enheten lyssnar alltid för ett wake word (eller en fras). Till exempel är ”artikel från Hey Cortana” ett wake word för assistenten Cortana. När användaren säger wake word, startar enheten skickar alla efterföljande ljud till molnet förrän användaren slutar talar. Anpassa wake-word är ett effektivt sätt att skilja mellan enheten och förbättra din anpassning.

Lär dig hur du skapar en anpassad aktivering word för din enhet i den här artikeln.

## <a name="choosing-an-effective-wake-word"></a>Om du väljer en effektiv wake word

Tänk på följande när du väljer ett ord för aktivering.

* Wake-ord ska ett ord eller en fras. Det bör ta längre än två sekunder för att säga.

* Ord av 4 – 7 stavelser fungerar bäst. ”Artikel från Hey, datorn” är till exempel ett bra wake ord, även om bara ”Hey” är en dålig.

* Wake ord bör följa den gemensamma engelska uttal regler.

* Ett unikt eller även sydda ord som följer reglerna för vanliga engelska uttal kan minska falska positiva identifieringar. ”Computerama” kan till exempel vara en bra wake word.

* Välj inte vanliga ord. Till exempel ”äta” och ”gå” är ord som det ofta sägs i vanlig konversationen. De kan vara false utlösare för din enhet.

* Undvik att använda wake ord som kan ha alternativa uttal. Användare måste känna till att enheten ska svara ”höger” uttal. Till exempel ”509” kan vara i samband med ”fem noll nio” ”, fem OJ nio”, eller ”fem hundratals och nio”. ”R.E.I.” Det gick uttalas som ”R E jag” eller ”Ray”. ”Live” gick uttalas som [līv] eller [liv].

* Använd inte specialtecken, symboler och siffror. Till exempel skulle ”gå #” och ”20 + katt” inte bra wake ord. Dock ”gå skarpa” eller ”20 plus katt” kan fungera. Du kan fortfarande använda symbolerna i din företagsanpassning och använda dokumentationen och marknadsföring för att förstärka rätt uttal.

> [!NOTE]
> Om du väljer ett varumärkesskyddat ord som wake-word Glöm inte att du äger den varumärken, eller så har behörighet från varumärke ägare till den. Microsoft ansvarar inte för eventuella juridiska problem som kan uppstå i valet av wake word.

## <a name="creating-your-wake-word"></a>Skapa wake-word

Innan du kan använda en anpassad aktivering word med din enhet, måste du skapa den med hjälp av tjänsten Microsoft anpassade Wake Word Generation. När du har angett ett wake ord, tjänsten ger en fil som distribuerar du sedan till ditt dev kit för att aktivera din wake word på enheten.

1. Gå till den [anpassade tal tjänstportalen](https://cris.ai/).

2. Skapa ett nytt konto med den e-postadress som du fick inbjudan för Azure Active Directory. 

    ![Skapa nytt konto](media/speech-devices-sdk/wake-word-1.png)
 
3.  Efter loggat in kan fylla i formuläret och klicka sedan på **starta transporten.**

    ![loggat in](media/speech-devices-sdk/wake-word-3.png)
 
4. Den **anpassad Wake Word** sidan är inte tillgänglig för allmänheten, så det finns ingen länk som tar dig det. Klicka på eller klistra in i den här länken i stället: https://cris.ai/customkws.

    ![dold sida](media/speech-devices-sdk/wake-word-4.png)
 
6. Ange ordet wake för ditt val kan sedan **skicka** den.

    ![Ange ditt wake ord](media/speech-devices-sdk/wake-word-5.png)
 
7. Det kan ta några minuter för filer som ska genereras. En snurrande cirkel visas på fliken i din webbläsare. Efter en stund visas ett informationsfält där du kan ladda ned en `.zip` fil.

    ![ta emot ZIP-fil](media/speech-devices-sdk/wake-word-6.png)

8. Spara den `.zip` filen till datorn. Du behöver den här filen för att distribuera en anpassad aktivering word development Kit följa anvisningarna i [Kom igång med tal enheter SDK](speech-devices-sdk-qsg.md).

9. Du kan nu **logga ut.**

## <a name="next-steps"></a>Nästa steg

Kom igång genom att hämta en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) och registrera dig för tal enheter SDK.

> [!div class="nextstepaction"]
> [Registrera dig för tal enheter SDK](get-speech-devices-sdk.md)

