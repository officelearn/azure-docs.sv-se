---
title: Prova Speech Service kostnadsfritt
description: Upptäck hur du kan prova Speech-tjänsten utan kostnad.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: a0ff6c09eb0a6fffe0ce82fe9ccb3bc43970ad68
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024626"
---
# <a name="try-the-speech-service-for-free"></a>Prova Speech Service kostnadsfritt

Det är enkelt och prisvärt att komma igång med Speech-tjänsten. En 30-dagars kostnadsfri utvärderingsversion kan du identifiera vad tjänsten kan göra och bestämmer om det är rätt för programmets behov.

Om du behöver mer tid kan du registrera dig för en Microsoft Azure-konto – medföljer 200 USD i tjänstkredit som du kan använda mot en betald prenumeration på upp till 30 dagar gäller tal-tjänst.

Slutligen erbjuder Speech-tjänsten en nivå för ledigt, med låga volymer som lämpar sig för att utveckla program. Du kan behålla den här kostnadsfria prenumerationen, även när din tjänstkredit går ut.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

30-dagars kostnadsfri utvärderingsversion ger dig tillgång till S0 standardprisnivån under en begränsad tid. 

Att registrera dig för en 30-dagars kostnadsfri utvärderingsversion:

1. Gå till [testa kognitiva tjänster](https://azure.microsoft.com/try/cognitive-services/).

1. Välj den **tal-API: er** fliken.

   ![Fliken för tal-tjänster](media/index/try-speech-api-free-trial1.png)
   
1. Under **taltjänster <sup>FÖRHANDSVERSION</sup>** väljer den **hämta API-nyckel** knappen.

   ![API-nyckel](media/index/try-speech-api-free-trial2.png)

1. Godkänn villkoren och välj ditt språk från den nedrullningsbara menyn.

   ![Godkänn villkoren](media/index/try-speech-api-free-trial3.png)

1. Logga in med ditt Microsoft, Facebook, LinkedIn och GitHub-konto. Alternativt kan du kan registrera dig för ett kostnadsfritt microsoftkonto:

    * Gå till den [Microsoft kontoportalen](https://account.microsoft.com/account).
    * Välj **logga in med Microsoft**.

    ![Logga in](media/index/try-speech-api-free-trial4.png)

    * När du ombeds logga in, Välj **skapar ett**.

    ![Skapa ett nytt konto](media/index/try-speech-api-free-trial5.png)

    * Tilldela ett lösenord i de steg som följer, ange din e-postadress eller telefonnummer, och följ anvisningarna för att verifiera ditt nya Microsoft-konto.

När du loggar in, börjar din kostnadsfria utvärderingsversion. Visade webbsidan visar alla Azure Cognitive Services-tjänster som du för närvarande har utvärderingsprenumerationer. Två prenumerationsnycklar visas bredvid **taltjänster**. Du kan använda någon av nycklarna i dina program.

> [!NOTE]
> Alla kostnadsfria utvärderingsversioner är i regionen USA, västra. När du gör förfrågningar måste du använda den slutpunkt som motsvarar till din region.

## <a name="new-azure-account"></a>Nya Azure-konto

Nya Azure-konton får en kredit på 200 servicekrediter som är tillgänglig i upp till 30 dagar. Du kan använda krediten att utforska ytterligare Speech-tjänsten eller för att starta apputveckling.

Du registrerar dig för ett nytt Azure-konto:

1. Gå till den [Azure registreringssidan](https://azure.microsoft.com/free/ai/). 

1. Välj **börja kostnadsfritt**.

    ![Börja kostnadsfritt](media/index/try-speech-api-new-azure1.png)

1. Logga in med ditt Microsoft-konto. Om du inte har någon:

    * Gå till den [Microsoft kontoportalen](https://account.microsoft.com/account).
    * Välj **logga in med Microsoft**.
    * När du ombeds logga in, Välj **skapa ett.**
    * Tilldela ett lösenord i de steg som följer, ange din e-postadress eller telefonnummer, och följ anvisningarna för att verifiera ditt nya Microsoft-konto.

1. Ange resten av den information som har begärt för att registrera dig för ett konto. Ange ditt land och ditt namn och ange ett telefonnummer och e-postadress.

    ![Ange information](media/index/try-speech-api-new-azure2.png)

    Verifiera din identitet via telefon och genom att tillhandahålla ett kreditkortsnummer. (Ditt kreditkort inte faktureras.) är, Godkänn avtalet för Azure-användare. 

    ![Godkänn avtalet](media/index/try-speech-api-new-azure3.png)

Ditt kostnadsfria Azure-konto har skapats. Följ stegen i nästa avsnitt för att starta en prenumeration på Speech-tjänsten.

## <a name="create-a-speech-resource-in-azure"></a>Skapa en tal-resurs i Azure

Lägga till en resurs för tal-tjänst på Azure-kontot:

1. Logga in på den [Azure-portalen](https://ms.portal.azure.com/) med hjälp av ditt Microsoft-konto.

1. Välj **skapa en resurs** på upp till vänster på portalen.

    ![Skapa en resurs](media/index/try-speech-api-create-speech1.png)

1. I den **New** letar **tal**.

1. I sökresultaten väljer **tal (förhandsversion)**.

    ![Välj tal (förhandsversion)](media/index/try-speech-api-create-speech2.png)

1. Under **tal (förhandsversion)** väljer den **skapa** knappen.

    ![Välj knappen Skapa](media/index/try-speech-api-create-speech3.png)

1. Under **skapa**, ange:

    * Ett namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer för samma tjänst.
    * Välj den prenumeration som den nya resursen är associerad med för att fastställa hur avgiften faktureras.
    * Välj den region där resursen ska användas. Tal-tjänsten är för närvarande tillgänglig i Asien, Europa, norra och västra USA-regioner.
    * Välj prisnivå, antingen F0 (begränsat kostnadsfri prenumeration) eller S0 (standard-prenumeration). Välj **Visa fullständiga prisuppgifter** fullständig information om priser och användning kvoter för varje nivå.
    * Skapa en ny resursgrupp för den här prenumerationen med tal eller tilldela prenumerationen till en befintlig resursgrupp. Kan du spara dina olika Azure-prenumerationer som ordnas resursgrupperna.
    * För åtkomst till prenumerationen i framtiden, väljer du den **fäst på instrumentpanelen** markerar du kryssrutan.
    * Välj **skapa.**

    ![Välj knappen Skapa](media/index/try-speech-api-create-speech4.png)

    Det kan ta en stund att skapa och distribuera din nya tal-resurs. Välj **snabbstarten** att se information om ny resurs.

    ![Snabbstart för panelen](media/index/try-speech-api-create-speech5.png)

1. Under **snabbstarten**väljer den **nycklar** länken under steg 1 för att visa dina prenumerationsnycklar. Varje prenumeration har två nycklar; Du kan använda någon av nycklarna i ditt program. Välj knappen bredvid varje nyckel för att kopiera den till Urklipp för att klistra in i din kod.

> [!NOTE]
> Du kan skapa ett obegränsat antal prenumerationer för standard-nivån i en eller flera regioner. Du kan dock skapa endast en kostnadsfri nivå-prenumeration.

## <a name="next-steps"></a>Nästa steg

Gör något av våra snabbstarter på 10 minuter eller Kolla in våra SDK-exempel:

> [!div class="nextstepaction"]
> [Snabbstart: Känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
> [tal SDK-exempel](speech-sdk.md#get-the-samples)
