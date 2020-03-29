---
title: Prova Speech Service kostnadsfritt
titleSuffix: Azure Cognitive Services
description: Det är enkelt och prisvärt att komma igång med taltjänsten. Det finns två alternativ tillgängliga gratis så att du kan upptäcka vad tjänsten kan göra och avgöra om det är rätt för dina behov.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219669"
---
# <a name="try-the-speech-service-for-free"></a>Prova Speech Service kostnadsfritt

I den här artikeln väljer du ett alternativ för att enkelt testa taltjänsten utan kostnad så att du kan upptäcka vad tjänsten kan göra och bestämma om det är rätt för dina behov. Välj ett av följande två alternativ beroende på din situation och användningsfall:

- [Alternativ 1:](#no-card)Få omedelbart **kostnadsfria API-nycklar** utan att ange någon kreditkortsinformation (du måste ha ett befintligt Azure-konto). Den **kostnadsfria utvärderingsversionen** varar i 30 dagar och data raderas i slutet. Det här alternativet är bäst för snabb experiment med tjänsten.
- [Alternativ 2:](#new-resource)Skapa en ny talresurs i Azure utan kostnad med hjälp av en **kostnadsfri prenumeration** (kreditkortsinformation krävs). Ett **gratisabonnemang** har huvudsakligen strängare räntegränser än en betald prenumeration. Det här alternativet är bäst om du vill testa tjänsten, men också planerar att uppgradera till en betald prenumeration i framtiden och inte vill förlora data.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Prova taltjänsten utan kreditkortsinformation

Slutför följande steg för att aktivera en 30-dagars kostnadsfri utvärderingsversion och få API-nycklar. Provperioden börjar omedelbart när följande steg är klara.

1. Gå till [Prova kognitiv tjänst](https://azure.microsoft.com/try/cognitive-services/).
1. Välj fliken **Tal-API:er.**
1. Välj **Hämta API-nyckel**.

Du kommer att få faktureringsalternativ. Välj det kostnadsfria alternativet och läs och godkänn sedan användaravtalet. Du kommer att presenteras med nycklar som du kan använda för att prova taltjänsten gratis i 30 dagar.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Prova taltjänsten genom att skapa en Azure-resurs

För följande steg behöver du både ett Microsoft-konto och ett Azure-konto. Om du inte har ett Microsoft-konto kan du registrera dig för ett kostnadsfritt på [Microsoft-kontoportalen](https://account.microsoft.com/account). Välj **Logga in med Microsoft** och välj sedan Skapa ett **Microsoft-konto**när du blir ombedd att logga in . Följ stegen för att skapa och verifiera ditt nya Microsoft-konto.

När du har ett Microsoft-konto går du till [sidan för Azure-registrering,](https://azure.microsoft.com/free/ai/)väljer **Startfritt**och skapar ett nytt Azure-konto med ett Microsoft-konto.

> [!NOTE]
> Taltjänsten har två tjänstnivåer: kostnadsfri och prenumeration, som har olika begränsningar och fördelar. När du registrerar dig för ett kostnadsfritt Azure-konto levereras det med 200 USD i tjänstkredit som du kan använda mot en betald taltjänstprenumeration som är giltig i upp till 30 dagar.
>
> Om du använder den kostnadsfria taltjänstnivån med låg volym kan du behålla den här kostnadsfria prenumerationen även efter att din kostnadsfria prov- eller tjänstkredit har gått ut.
>
> Mer information finns i [Cognitive Services prissättning - Taltjänst](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Skapa resursen

Så här lägger du till en taltjänstresurs (kostnadsfri eller betald nivå) i ditt Azure-konto:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Microsoft-konto.

1. Välj **Skapa en resurs** längst upp till vänster i portalen. Om du inte ser **Skapa en resurs**kan du alltid hitta den genom att välja den komprimerade menyn längst upp till vänster:

   ![komprimerad navigeringsknapp](media/index/collapsed-nav.png)

1. Skriv "tal" i sökrutan i fönstret **Nytt** och tryck på RETUR.

1. Välj **Tal**i sökresultaten .

   ![ta ett talsökresultat](media/index/speech-search.png)

1. Välj **Skapa**och sedan:

   - Ge ett unikt namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer som är knutna till samma tjänst.
   - Välj den Azure-prenumeration som den nya resursen är associerad med för att avgöra hur avgifterna faktureras.
   - Välj den [region](regions.md) där resursen ska användas.
   - Välj antingen en kostnadsfri (F0) eller betald (S0) prisnivå. Om du vill ha fullständig information om pris- och användningskvoter för varje nivå väljer du **Visa fullständig prisinformation**.
   - Skapa en ny resursgrupp för den här Talprenumerationen eller tilldela prenumerationen till en befintlig resursgrupp. Resursgrupper hjälper dig att hålla ordning på dina olika Azure-prenumerationer.
   - Välj **Skapa**. Detta tar dig till distributionsöversikten och visar meddelanden om förlopp för distributionen.

> [!NOTE]
> Du kan skapa ett obegränsat antal prenumerationer på standardnivå i en eller flera regioner. Du kan dock bara skapa en prenumeration på en kostnadsfri nivå. Modelldistributioner på den kostnadsfria nivån som förblir oanvända i 7 dagar inaktiveras automatiskt.

Det tar en stund att distribuera den nya talresursen. När distributionen är klar väljer du **Gå till resurs** och väljer alternativ i det vänstra navigeringsfönstret **för** att visa prenumerationsnycklarna för taltjänsten. Varje prenumeration har två nycklar. Du kan använda någon av tangenterna i programmet. Om du snabbt vill kopiera/klistra in en nyckel till kodredigeraren eller på annan plats markerar du kopieringsknappen bredvid varje tangent, byter fönster för att klistra in innehållet i Urklipp till önskad plats.

> [!IMPORTANT]
> Dessa prenumerationsnycklar används för att komma åt ditt Cognitive Service API. Dela inte dina nycklar. Lagra dem säkert, till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar dessa nycklar regelbundet. Endast en nyckel är nödvändig för att ringa ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="switch-to-a-new-subscription"></a>Byta till en ny prenumeration

Om du vill växla från en prenumeration till en annan, till exempel när din kostnadsfria utvärderingsperiod går ut eller när du publicerar ditt program, ersätter du regionen och prenumerationsnyckeln i koden med regionen och prenumerationsnyckeln för den nya Azure-resursen.

## <a name="about-regions"></a>Om regioner

- Om programmet använder en [Tal-SDK](speech-sdk.md)anger du `westus`regionkoden, till exempel när du skapar en talkonfiguration.
- Om programmet använder en av taltjänstens [REST-API:er](rest-apis.md)är regionen en del av slutpunkts-URI som du använder när du gör begäranden.
- Nycklar som skapas för en region är endast giltiga i den regionen. Om du försöker använda dem med andra regioner uppstår autentiseringsfel.

## <a name="next-steps"></a>Nästa steg

Fyll i en av våra 10-minuters snabbstarter eller kolla in våra SDK-prover:

> [!div class="nextstepaction"]
> [Snabbstart: Känna igen tal i C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Tal SDK-exempel](speech-sdk.md#get-the-samples)
