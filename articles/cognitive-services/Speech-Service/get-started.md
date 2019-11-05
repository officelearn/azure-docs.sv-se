---
title: Testa tal tjänsten kostnads fritt
titleSuffix: Azure Cognitive Services
description: Att komma igång med tal tjänsten är enkelt och prisvärt. En 30-dagars kostnads fri utvärderings version låter dig upptäcka vad tjänsten kan göra och bestämma om den är rätt för ditt programs behov.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 46ada91060aa095b7c041ff75abb6256f3d05853
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464694"
---
# <a name="try-speech-services-for-free"></a>Prova tal tjänster kostnads fritt

Att komma igång med tal tjänsterna är enkelt och prisvärt. En 30-dagars kostnads fri utvärderings version låter dig upptäcka vad tjänsten kan göra och bestämma om den är rätt för ditt programs behov.

Om du behöver mer tid kan du registrera dig för ett Microsoft Azure konto – det levereras med $200 i service kredit som du kan använda mot en betald tal tjänst prenumeration i upp till 30 dagar.

Dessutom erbjuder tal tjänsterna en kostnads fri, låg volym nivå som är lämplig för att utveckla program. Du kan behålla den här kostnads fria prenumerationen även när din service kredit går ut.

## <a name="free-trial"></a>Kostnadsfri utvärderingsversion

Den kostnads fria 30-dagars utvärderings versionen ger dig till gång till standard pris nivån under en begränsad tid.

Registrera dig för en 30-dagars kostnads fri utvärderings version:

1. Gå till [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Välj fliken **Speech-API: er** .

   ![Fliken Speech API Speech Services](media/index/cognitive-services-speech-api-tab.png)

1. Under **tal tjänster**väljer du **Hämta API-nyckel**.

   ![Speech API – Hämta API-nyckel](media/index/speech-api-get-api-key.png)

1. Godkänn villkoren och välj nationella inställningar på den nedrullningsbara menyn.

   ![Speech API – Godkänn villkor](media/index/speech-api-agree-to-terms.png)

1. Logga in med ditt Microsoft-, Facebook-, LinkedIn-eller GitHub-konto.

    Du kan registrera dig för en kostnads fri Microsoft-konto på [Microsoft-konto portalen](https://account.microsoft.com/account). Kom igång genom att välja **Logga in med Microsoft** och sedan när du uppmanas att logga in, väljer du **skapa ett.** Följ stegen för att skapa och verifiera din nya Microsoft-konto.

När du har loggat in för att försöka Cognitive Services börjar din kostnads fria utvärderings version. Webb sidan som visas visar alla Azure Cognitive Services-tjänster som du för närvarande har utvärderings prenumerationer för. Två prenumerations nycklar visas bredvid **tal tjänster**. Du kan använda någon av nycklarna i dina program.

> [!NOTE]
> Alla kostnads fria utvärderings prenumerationer finns i regionen USA, västra. När du gör begär Anden måste du se till att använda `westus` slut punkten.

## <a name="new-azure-account"></a>Nytt Azure-konto

Nya Azure-konton får en $200-Tjänstekredit som är tillgänglig i upp till 30 dagar. Du kan använda den här krediten för att ytterligare utforska tal tjänsterna eller börja utveckla program.

Registrera dig för ett nytt Azure-konto genom att gå till [sidan för Azure-registrering](https://azure.microsoft.com/free/ai/), välja **Starta kostnads fritt** och skapa ett nytt Azure-konto med hjälp av din Microsoft-konto.

Du kan registrera dig för en kostnads fri Microsoft-konto på [Microsoft-konto portalen](https://account.microsoft.com/account). Kom igång genom att välja **Logga in med Microsoft** och sedan när du uppmanas att logga in, väljer du **skapa ett.** Följ stegen för att skapa och verifiera din nya Microsoft-konto.

När du har skapat ditt Azure-konto följer du stegen i nästa avsnitt för att starta en prenumeration på tal tjänsterna.

## <a name="create-a-speech-resource-in-azure"></a>Skapa en tal resurs i Azure

Så här lägger du till en resurs för tal tjänster (kostnads fri eller betald nivå) till ditt Azure-konto:

1. Logga in på [Azure Portal](https://portal.azure.com/) med din Microsoft-konto.

1. Välj **skapa en resurs** längst upp till vänster i portalen.

    ![Speech API – skapa en resurs](media/index/speech-api-create-resource.png)

1. Sök efter **tal**i det **nya** fönstret.

1. I Sök resultaten väljer du **tal**.

    ![Speech API-Välj tal](media/index/speech-api-select-speech.png)

1. Under **tal**väljer du knappen **skapa** .

    ![Knappen Speech API-skapa](media/index/speech-api-create-button.png)

1. Under **skapa**, ange:

   * Ett namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer på samma tjänst.
   * Välj den Azure-prenumeration som den nya resursen är kopplad till för att avgöra hur avgifterna faktureras.
   * Välj den [region](regions.md) där resursen ska användas.
   * Välj antingen en kostnads fri eller betald pris nivå. Välj **Visa fullständiga pris uppgifter** för fullständig information om priser och användnings kvoter för varje nivå.
   * Skapa en ny resurs grupp för den här tal prenumerationen eller tilldela prenumerationen till en befintlig resurs grupp. Med resurs grupper kan du hålla dina olika Azure-prenumerationer organiserade.
   * För bekväm åtkomst till din prenumeration i framtiden markerar du kryss rutan **Fäst på instrument panelen** .
   * Välj **skapa.**

     ![Speech API-Välj Skapa](media/index/speech-api-select-create.png)

     Det tar en stund att skapa och distribuera din nya tal resurs. Välj **snabb start** för att se information om den nya resursen.

     ![Speech API-distribuera resursen](media/index/speech-api-deploy-resource.png)

1. Under **snabb start**väljer du länken **nycklar** under steg 1 för att visa dina prenumerations nycklar. Varje prenumeration har två nycklar. Du kan använda någon av nycklarna i ditt program. Välj knappen bredvid varje nyckel för att kopiera den till Urklipp för att klistra in i koden.

> [!NOTE]
> Du kan skapa ett obegränsat antal prenumerationer på standard nivå i en eller flera regioner. Du kan dock bara skapa en prenumeration på den kostnads fria nivån. Modell distributioner på den kostnads fria nivån som fortsätter att vara oanvända i 7 dagar kommer att avetablerats automatiskt.

## <a name="switch-to-a-new-subscription"></a>Växla till en ny prenumeration

Om du vill växla från en prenumeration till en annan, till exempel när din kostnads fria utvärderings version upphör att gälla eller när du publicerar programmet, ersätter du region och prenumerations nyckel i koden med region och prenumerations nyckel för den nya Azure-resursen.

> [!NOTE]
> Kostnads fria utvärderings nycklar skapas i regionen USA, västra (`westus`). En prenumeration som skapats via Azure-instrumentpanelen kan finnas i en annan region om du så vill.

* Om programmet använder ett [tal-SDK](speech-sdk.md)anger du regions koden, till exempel `westus`, när du skapar en tal konfiguration.
* Om ditt program använder en av de olika tal tjänsternas [REST-API: er](rest-apis.md)är regionen en del av slut punkts-URI: n som du använder när du gör förfrågningar.

Nycklar som skapats för en region är bara giltiga i den regionen. Om du försöker använda dem med andra regioner resulterar det i autentiseringsfel.

## <a name="next-steps"></a>Nästa steg

Slutför en av våra snabb starter på 10 minuter eller kolla våra SDK-exempel:

> [!div class="nextstepaction"]
> [Snabb start: identifiera tal C# i](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [tal SDK-exempel](speech-sdk.md#get-the-samples)
