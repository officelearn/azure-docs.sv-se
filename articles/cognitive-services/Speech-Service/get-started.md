---
title: Prova tal tjänsten gratis | Microsoft Docs
description: Identifiera hur du kan försöka tjänsten tal utan kostnad.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: 3feef04694336d9173b419285a96fcaef543e18f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356241"
---
# <a name="try-the-speech-service-for-free"></a>Prova tal tjänsten gratis

Komma igång med tal-tjänsten är enkelt och prisvärt. En kostnadsfri 30-dagars utvärderingsversion kan du identifiera vad tjänsten kan göra och välja om den passar dina programbehov.

Om du behöver mer tid kan du registrera dig för Microsoft Azure-konto – den medföljer 200 USD i servicekrediter som du kan tillämpa mot ett tal service betalprenumeration för upp till 30 dagar.

Slutligen erbjuder tal-tjänsten en nivå för ledigt, små volymer som lämpar sig för att utveckla program. Du kan behålla den här kostnadsfria prenumerationen trots din tjänstekredit upphör att gälla.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

30-dagars utvärderingsversion ger dig tillgång till S0 standardprisnivån under en begränsad tid. Följ dessa steg för att registrera dig för en kostnadsfri 30-dagars utvärderingsversion.

1. Gå till den [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/) sidan.

1. Gå till fliken tal och klicka på den **hämta API-nyckel** knappen bredvid ”tal tjänster”.

   ![Fliken för tal-tjänster](media/index/try-speech-api-free-trial1.png)<br>
   ![API-nyckel](media/index/try-speech-api-free-trial2.png)

3. Acceptera villkoren och välj ditt språk från den nedrullningsbara menyn.

   ![Godkänn villkoren](media/index/try-speech-api-free-trial3.png)

4. Logga in med ditt Microsoft, Facebook, LinkedIn eller GitHub-konto. Eller du kan registrera dig för en kostnadsfri Microsoft-konto:

    * Gå till den [Microsoft kontoportalen](https://account.microsoft.com/account).
    * Klicka på **logga in med Microsoft**.

    ![Logga in](media/index/try-speech-api-free-trial4.png)

    * När du uppmanas att logga in, klickar du på ”Skapa en”.

    ![Skapa ett nytt konto](media/index/try-speech-api-free-trial5.png)

    * Tilldela ett lösenord i de steg som följer, ange din e-postadress eller telefonnummer, och följ instruktionerna för att verifiera ditt nya Microsoft-konto.

När du loggar in börjar den kostnadsfria utvärderingsversionen. Sidan visas en lista över alla kognitiva tjänster som du för närvarande har provprenumerationer. Två prenumeration nycklar visas bredvid ”tal tjänster”. Du kan använda antingen nyckel i dina program.

> [!NOTE]
> Alla kostnadsfria utvärderingsversioner finns i USA, västra region. Se till att använda den slutpunkt som motsvarar din region vid begäranden.

## <a name="new-azure-account"></a>Nya Azure-konto

Nya Azure-konton får en 200 USD i rabatt servicekrediter som pågår i upp till 30 dagar. Den här kredit kan användas för att ytterligare utforska tjänsten tal och börja utveckla program.

Följ dessa steg för att registrera dig för en ny Azure-konto.

1. Gå till den [Azure inloggningssidan](https://azure.microsoft.com/free/ai/). 

1. Klicka på **starta ledigt**.

    ![Börja kostnadsfritt](media/index/try-speech-api-new-azure1.png)

3. Logga in med ditt Microsoft-konto. Om du inte har någon:

    * Gå till den [Microsoft kontoportalen](https://account.microsoft.com/account).
    * Klicka på **logga in med Microsoft**.
    * När du uppmanas att logga in, klickar du på ”Skapa en”.
    * Tilldela ett lösenord i de steg som följer, ange din e-postadress eller telefonnummer, och följ instruktionerna för att verifiera ditt nya Microsoft-konto.

1. Ange resten av de uppgifter som krävs för att registrera dig för ett konto. Ange ditt land och ditt namn och ange ett telefonnummer och e-postadress.

    ![Ange information](media/index/try-speech-api-new-azure2.png)

    Verifiera din identitet via telefon och genom att tillhandahålla ett kreditkortsnummer och sedan godkänna avtalet Azure användare. (Ditt kreditkort kommer inte att debiteras.)

    ![Acceptera avtalet](media/index/try-speech-api-new-azure3.png)

Din kostnadsfria Azure-konto har skapats. Följ stegen i nästa avsnitt för att starta en prenumeration på tjänsten tal.

## <a name="create-a-speech-resource-in-azure"></a>Skapa en resurs för tal i Azure

Följ dessa steg om du vill lägga till ett tal tjänstresurs i ditt Azure-konto.

1. Logga in på den [Azure-portalen](https://ms.portal.azure.com/) med ditt Microsoft-konto.

1. Klicka på **skapar du en resurs** (gröna **+** ikonen) på upp till vänster på portalen.

    ![Skapa resurs](media/index/try-speech-api-create-speech1.png)

1. I det nya fönstret, söka efter tal.

    ![Klicka på tal](media/index/try-speech-api-create-speech2.png)

1. I sökresultaten klickar du på ”tal (förhandsversion)”.

1. Klicka på den **skapa** knappen längst ned i panelen service tal.

    ![Klicka på Skapa](media/index/try-speech-api-create-speech3.png)

1. Ange i panelen skapa:

    * Ett namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer för samma tjänst.
    * Välj den Azure-prenumeration som den nya resursen är kopplad till att avgöra hur avgifterna debiteras.
    * Välj den region där resursen ska användas. Tal-tjänsten är för närvarande tillgänglig i östra Asien, Norra Europa och USA, västra regioner.
    * Välj prisnivå antingen F0 (begränsat kostnadsfri prenumeration) eller S0 (standard prenumeration). Klicka på **Visa fullständig prisinformation** fullständig information om priser och användning av kvoter för varje nivå.
    * Skapa en ny resursgrupp för den här prenumerationen tal eller tilldela den till en befintlig. Resursgrupper kan du spara dina olika Azure-prenumerationer ordnade.
    * Åtkomst till din prenumeration i framtiden, markera den **fäst på instrumentpanelen** kryssrutan.
    * Klicka på **skapa.**

    ![Klicka på Skapa Kontrollpanelen](media/index/try-speech-api-create-speech4.png)

    Det kan ta en stund att skapa och distribuera din nya tal-resurs. Panelen Quickstart visas med information om ny resurs.

    ![Snabbstart panelen](media/index/try-speech-api-create-speech5.png)

1. Klicka på den **nycklar** länken under steg 1 i panelen Snabbstart för att visa dina nycklar för prenumerationen. Varje prenumeration har två nycklar; Du kan använda antingen i ditt program. Klicka på knappen bredvid varje nyckel för att kopiera den till Urklipp och klistra in i din kod.

> [!NOTE]
> Du kan skapa valfritt antal standard-nivån prenumerationer i en eller flera regioner. Du kan dock skapa endast en kostnadsfri nivå prenumeration.

## <a name="next-steps"></a>Nästa steg

Hämta en SDK och vissa exempelkod för att uppleva tjänsten tal.

> [!div class="nextstepaction"]
> [Tal SDK](speech-sdk.md)

> [!div class="nextstepaction"]
> [Exempelkod](samples.md)
