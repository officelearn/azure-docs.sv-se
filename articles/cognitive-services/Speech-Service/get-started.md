---
title: Testa Speech Services kostnadsfritt
titleSuffix: Azure Cognitive Services
description: Det är enkelt och prisvärt att komma igång med Speech-tjänsten. En 30-dagars kostnadsfri utvärderingsversion kan du identifiera vad tjänsten kan göra och bestämmer om det är rätt för programmets behov.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 69e2c094e1fca57d0dddc662cc1dae6453897c38
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079117"
---
# <a name="try-the-speech-service-for-free"></a>Prova Speech Service kostnadsfritt

Det är enkelt och prisvärt att komma igång med Speech-tjänsten. En 30-dagars kostnadsfri utvärderingsversion kan du identifiera vad tjänsten kan göra och bestämmer om det är rätt för programmets behov.

Om du behöver mer tid kan du registrera dig för en Microsoft Azure-konto – medföljer 200 USD i tjänstkredit som du kan använda mot en betald prenumeration på upp till 30 dagar gäller tal-tjänst.

Slutligen erbjuder Speech-tjänsten en nivå för ledigt, med låga volymer som lämpar sig för att utveckla program. Du kan behålla den här kostnadsfria prenumerationen, även när din tjänstkredit går ut.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

30-dagars kostnadsfri utvärderingsversion ger dig tillgång till standardprisnivån under en begränsad tid.

Att registrera dig för en 30-dagars kostnadsfri utvärderingsversion:

1. Gå till [testa kognitiva tjänster](https://azure.microsoft.com/try/cognitive-services/).

1. Välj den **tal-API: er** fliken.

   ![Fliken för tal-tjänster](media/index/try-speech-api-free-trial1.png)

1. Under **taltjänster**väljer den **hämta API-nyckel** knappen.

   ![API-nyckel](media/index/try-speech-api-free-trial2.png)

1. Godkänn villkoren och välj ditt språk från den nedrullningsbara menyn.

   ![Godkänn villkoren](media/index/try-speech-api-free-trial3.png)

1. Logga in med ditt Microsoft, Facebook, LinkedIn och GitHub-konto.

    Du kan registrera dig för ett kostnadsfritt microsoftkonto på den [Microsoft kontoportalen](https://account.microsoft.com/account). Kom igång genom att klicka på **logga in med Microsoft** och klicka sedan, när du ombeds logga in på **skapa ett.** Följ stegen för att skapa och verifiera ditt nya Microsoft-konto.

När du loggar in på prova Cognitive Services, börjar din kostnadsfria utvärderingsversion. Visade webbsidan visar alla Azure Cognitive Services-tjänster som du för närvarande har utvärderingsprenumerationer. Två prenumerationsnycklar visas bredvid **taltjänster**. Du kan använda någon av nycklarna i dina program.

> [!NOTE]
> Alla kostnadsfria utvärderingsversioner är i regionen USA, västra. När du gör förfrågningar måste du använda den `westus` slutpunkt.

## <a name="new-azure-account"></a>Nya Azure-konto

Nya Azure-konton får en kredit på 200 servicekrediter som är tillgänglig i upp till 30 dagar. Du kan använda krediten att utforska ytterligare Speech-tjänsten eller för att starta apputveckling.

Om du vill registrera dig för en ny Azure-konto går du till den [Azure registreringssidan](https://azure.microsoft.com/free/ai/), klickar du på **starta kostnadsfri** och skapa en ny Azure-konto med ditt Microsoft-konto.

Du kan registrera dig för ett kostnadsfritt microsoftkonto på den [Microsoft kontoportalen](https://account.microsoft.com/account). Kom igång genom att klicka på **logga in med Microsoft** och klicka sedan, när du ombeds logga in på **skapa ett.** Följ stegen för att skapa och verifiera ditt nya Microsoft-konto.

När du har skapat ditt Azure-konto, följer du stegen i nästa avsnitt för att starta en prenumeration på Speech-tjänsten.

## <a name="create-a-speech-resource-in-azure"></a>Skapa en tal-resurs i Azure

Lägga till en tal-tjänstresurs (kostnadsfritt eller betalt nivå) på Azure-kontot:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ditt Microsoft-konto.

1. Välj **skapa en resurs** på upp till vänster på portalen.

    ![Skapa en resurs](media/index/try-speech-api-create-speech1.png)

1. I den **New** letar **tal**.

1. I sökresultaten väljer **tal**.

    ![Välj tal](media/index/try-speech-api-create-speech2.png)

1. Under **tal**väljer den **skapa** knappen.

    ![Välj knappen Skapa](media/index/try-speech-api-create-speech3.png)

1. Under **skapa**, ange:

    * Ett namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer för samma tjänst.
    * Välj den prenumeration som den nya resursen är associerad med för att fastställa hur avgiften faktureras.
    * Välj den region där resursen ska användas. Tal-tjänsten är för närvarande tillgänglig i Asien, Europa, norra och västra USA-regioner.
    * Välj antingen en kostnadsfri eller betald prisnivå. Klicka på **Visa fullständiga prisuppgifter** fullständig information om priser och användning kvoter för varje nivå.
    * Skapa en ny resursgrupp för den här prenumerationen med tal eller tilldela prenumerationen till en befintlig resursgrupp. Kan du spara dina olika Azure-prenumerationer som ordnas resursgrupperna.
    * För åtkomst till prenumerationen i framtiden, väljer du den **fäst på instrumentpanelen** markerar du kryssrutan.
    * Välj **skapa.**

    ![Välj knappen Skapa](media/index/try-speech-api-create-speech4.png)

    Det tar en stund att skapa och distribuera din nya tal-resurs. Välj **snabbstarten** att se information om ny resurs.

    ![Snabbstart för panelen](media/index/try-speech-api-create-speech5.png)

1. Under **snabbstarten**, klickar du på den **nycklar** länken under steg 1 för att visa dina prenumerationsnycklar. Varje prenumeration har två nycklar; Du kan använda någon av nycklarna i ditt program. Välj knappen bredvid varje nyckel för att kopiera den till Urklipp för att klistra in i din kod.

> [!NOTE]
> Du kan skapa ett obegränsat antal prenumerationer för standard-nivån i en eller flera regioner. Du kan dock skapa endast en kostnadsfri nivå-prenumeration. Modelldistributioner på den kostnadsfria nivån som är oanvända för 7 dagar kommer automatiskt att avetablerats.

## <a name="switch-to-a-new-subscription"></a>Växla till en ny prenumeration

Om du vill växla från en prenumeration till en annan, Ersätt till exempel när din kostnadsfria utvärderingsversion upphör att gälla eller när du publicerar ditt program, nyckeln region och prenumeration i din kod med nyckeln region och prenumeration för den nya Azure-resursen.

> [!NOTE]
> Kostnadsfri utvärderingsversion nycklar skapas i västra USA (`westus`) region. En prenumeration som skapats via Azure-instrumentpanelen kanske vissa andra region om du vill.

* Om programmet använder ett [tal SDK](speech-sdk.md), ange regionskod, t.ex `westus`, när du skapar en tal-konfiguration.
* Om ditt program använder en av Speech-tjänsten [REST API: er](rest-apis.md), regionen är en del av slutpunkten URI som du använder när du gör förfrågningar.

Nycklar som har skapats för en region är endast giltiga i den regionen. Försök att använda dem med andra regioner leder autentiseringsfel.

## <a name="next-steps"></a>Nästa steg

Gör något av våra snabbstarter på 10 minuter eller Kolla in våra SDK-exempel:

> [!div class="nextstepaction"]
> [Snabbstart: Känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
> [tal SDK-exempel](speech-sdk.md#get-the-samples)
