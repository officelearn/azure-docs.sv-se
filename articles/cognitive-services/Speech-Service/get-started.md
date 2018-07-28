---
title: Prova Speech Service kostnadsfritt
description: Upptäck hur du kan prova Speech-tjänsten utan kostnad.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325215"
---
# <a name="try-the-speech-service-for-free"></a>Prova Speech Service kostnadsfritt

Det är enkelt och prisvärt att komma igång med Speech-tjänsten. En 30-dagars kostnadsfri utvärderingsversion kan du identifiera vad tjänsten kan göra och bestämmer om det är rätt för programmets behov.

Om du behöver mer tid kan du registrera dig för en Microsoft Azure-konto – medföljer 200 USD i tjänstkredit som du kan använda mot en betald prenumeration på upp till 30 dagar gäller tal-tjänst.

Slutligen erbjuder Speech-tjänsten en kostnadsfri, med låga volymer nivå som är lämpliga för att utveckla program. Du kan behålla den här kostnadsfria prenumerationen, även när din tjänstkredit går ut.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

30-dagars kostnadsfri utvärderingsversion ger dig tillgång till S0 standardprisnivån under en begränsad tid. Följ dessa steg för att registrera dig för en 30-dagars kostnadsfri utvärderingsversion.

1. Gå till den [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sidan.

1. Växla till fliken tal och klicka på den **hämta API-nyckel** knappen bredvid ”Speech services”.

   ![Fliken för tal-tjänster](media/index/try-speech-api-free-trial1.png)<br>
   ![API-nyckel](media/index/try-speech-api-free-trial2.png)

3. Godkänn villkoren och välj ditt språk från den nedrullningsbara menyn.

   ![Godkänn villkoren](media/index/try-speech-api-free-trial3.png)

4. Logga in med ditt Microsoft, Facebook, LinkedIn och GitHub. Eller du kan registrera dig för ett kostnadsfritt microsoftkonto:

    * Gå till den [Microsoft kontoportalen](https://account.microsoft.com/account).
    * Klicka på **logga in med Microsoft**.

    ![Logga in](media/index/try-speech-api-free-trial4.png)

    * När du ombeds logga in klickar du på ”Skapa en”.

    ![Skapa ett nytt konto](media/index/try-speech-api-free-trial5.png)

    * Tilldela ett lösenord i de steg som följer, ange din e-postadress eller telefonnummer, och följ anvisningarna för att verifiera ditt nya Microsoft-konto.

När du loggar in, börjar din kostnadsfria utvärderingsversion. Sidan visas listar alla kognitiva tjänster som du för närvarande har utvärderingsprenumerationer. Två prenumerationsnycklar visas bredvid ”Speech services”. Du kan använda någon av nycklarna i dina program.

> [!NOTE]
> Alla kostnadsfria utvärderingsversioner är i regionen USA, västra. Glöm inte att använda den slutpunkt som motsvarar din region vid begäranden.

## <a name="new-azure-account"></a>Nya Azure-konto

Nya Azure-konton får en kredit på 200 servicekrediter som gäller tar upp till 30 dagar. Krediten kan användas för att ytterligare utforska Speech-tjänsten och starta apputveckling.

Följ dessa steg för att registrera dig för en ny Azure-konto.

1. Gå till den [Azure registreringssidan](https://azure.microsoft.com/free/ai/). 

1. Klicka på **börja kostnadsfritt**.

    ![Börja kostnadsfritt](media/index/try-speech-api-new-azure1.png)

3. Logga in med ditt Microsoft-konto. Om du inte har någon:

    * Gå till den [Microsoft kontoportalen](https://account.microsoft.com/account).
    * Klicka på **logga in med Microsoft**.
    * När du ombeds logga in klickar du på ”Skapa en”.
    * Tilldela ett lösenord i de steg som följer, ange din e-postadress eller telefonnummer, och följ anvisningarna för att verifiera ditt nya Microsoft-konto.

1. Ange resten av den information som efterfrågas för att registrera dig för ett konto. Ange ditt land och ditt namn och ange ett telefonnummer och e-postadress.

    ![Ange information](media/index/try-speech-api-new-azure2.png)

    Verifiera din identitet via telefon och ange kreditkortsnummer och godkänna avtalet för Azure-användare. (Ditt kreditkort inte faktureras.)

    ![Godkänn avtalet](media/index/try-speech-api-new-azure3.png)

Ditt kostnadsfria Azure-konto har skapats. Följ stegen i nästa avsnitt för att starta en prenumeration på Speech-tjänsten.

## <a name="create-a-speech-resource-in-azure"></a>Skapa en tal-resurs i Azure

Följ dessa steg för att lägga till en resurs för tal-tjänst på Azure-kontot.

1. Logga in på den [Azure-portalen](https://ms.portal.azure.com/) med ditt Microsoft-konto.

1. Klicka på **skapa en resurs** (gröna **+** ikonen) på upp till vänster på portalen.

    ![Skapa resurs](media/index/try-speech-api-create-speech1.png)

1. I det nya fönstret, söker du efter tal.

    ![Klicka på tal](media/index/try-speech-api-create-speech2.png)

1. I sökresultaten klickar du på ”tal (förhandsversion)”.

1. Klicka på den **skapa** knappen längst ned på panelen Speech-tjänsten.

    ![Klicka på Skapa](media/index/try-speech-api-create-speech3.png)

1. Ange i panelen skapa:

    * Ett namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer för samma tjänst.
    * Välj den prenumeration som den nya resursen är associerad med för att fastställa hur avgiften faktureras.
    * Välj den region där resursen ska användas. Tal-tjänsten är för närvarande tillgänglig i Asien, Europa, norra och västra USA-regioner.
    * Välj prisnivå, antingen F0 (begränsat kostnadsfri prenumeration) eller S0 (standard-prenumeration). Klicka på **Visa fullständiga prisuppgifter** fullständig information om priser och användning kvoter för varje nivå.
    * Skapa en ny resursgrupp för den här prenumerationen med tal eller tilldela den till en befintlig. Kan du spara dina olika Azure-prenumerationer som ordnas resursgrupperna.
    * Bekväm åtkomst till din prenumeration i framtiden, markera den **fäst på instrumentpanelen** kryssrutan.
    * Klicka på **skapa.**

    ![Klicka på Skapa i panelen](media/index/try-speech-api-create-speech4.png)

    Det kan ta en stund att skapa och distribuera din nya tal-resurs. Snabbstart-ruta visas med information om ny resurs.

    ![Snabbstart för panelen](media/index/try-speech-api-create-speech5.png)

1. Klicka på den **nycklar** länken under steg 1 i panelen Snabbstart för att visa dina prenumerationsnycklar. Varje prenumeration har två nycklar; Du kan använda antingen i ditt program. Klicka på knappen bredvid varje nyckel för att kopiera den till Urklipp för att klistra in i din kod.

> [!NOTE]
> Du kan skapa valfritt antal prenumerationer för standard-nivån i en eller flera regioner. Du kan dock skapa endast en kostnadsfri nivå-prenumeration.

## <a name="next-steps"></a>Nästa steg

Gör något av våra snabbstarter på 10 minuter eller Kolla in våra SDK-exempel.

> [!div class="nextstepaction"]
> [Snabbstart: känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
> [tal SDK-exempel](speech-sdk.md#get-the-samples)
