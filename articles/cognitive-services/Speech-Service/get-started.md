---
title: Prova Speech Service kostnadsfritt
titleSuffix: Azure Cognitive Services
description: Kom igång med Speech service är enkelt och prisvärt. Det finns två tillgängliga alternativ kostnads fritt så att du kan identifiera vad tjänsten kan göra och bestämma om den passar dina behov.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 30bdbf9fa0ea346892622c3e7f24f9f31652a650
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280494"
---
# <a name="try-the-speech-service-for-free"></a>Prova Speech Service kostnadsfritt

Det är enkelt och prisvärt att använda tal tjänsten. Det finns två tillgängliga alternativ kostnads fritt så att du kan identifiera vad tjänsten kan göra och bestämma om den passar dina behov:

- Få en kostnads fri utvärderings version utan att ange någon kreditkorts information (du måste ha ett befintligt Azure-konto)
- Skapa ett nytt Azure-konto utan kostnad för en utvärderings period (kreditkorts information krävs)

I den här artikeln väljer du något av de här alternativen som passar dina behov bäst.

> [!NOTE]
> Tjänsten Speech har två tjänst nivåer: kostnads fri och prenumeration, som har olika begränsningar och fördelar. När du registrerar dig för ett kostnads fritt Azure-konto levereras det med $200 i service kredit som du kan använda mot en betald Speech service-prenumeration, som är giltig i upp till 30 dagar.
>
> Om du använder den kostnads fria, låga tal Service nivån kan du behålla den här kostnads fria prenumerationen även efter att din kostnads fria utvärderings version eller service kredit upphör att gälla.
>
> Mer information finns i [Cognitive Services priser – tal tjänster](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="try-the-speech-service-without-credit-card-info"></a>Prova tjänsten Speech service utan kreditkorts information

Även om vi rekommenderar att du provar röst tjänsten med hjälp av anvisningarna i nästa avsnitt, kan du överväga det här avsnittets instruktioner om följande gäller:

- Du har redan ett aktivt Azure-konto.
- Du vill utvärdera tal tjänsten utan att skapa ett nytt Azure-konto.
- Du föredrar inget kredit kort och inga data sparas efter utvärderings perioden.

> [!NOTE]
> Utvärderings perioden startar omedelbart när följande steg har slutförts.

1. Gå till [testa kognitiva tjänster](https://azure.microsoft.com/try/cognitive-services/).
1. Välj den **tal-API: er** fliken.
1. Välj **Hämta API-nyckel**.

Du kommer att visas med fakturerings alternativ. Välj alternativet gratis och Läs och godkänn användar avtalet. Du kommer att visas med nycklar som du kan använda för att testa tal tjänsten under en begränsad tid.

## <a name="try-the-speech-service-using-a-new-azure-account"></a>Prova röst tjänsten med ett nytt Azure-konto

Om du vill registrera dig för ett nytt Azure-konto behöver du en Microsoft-konto. Om du inte har en Microsoft-konto kan du registrera dig för en kostnads fri avgift på [Microsoft-konto portalen](https://account.microsoft.com/account). Välj **Logga in med Microsoft** och välj sedan **skapa en Microsoft-konto**när du uppmanas att logga in. Följ stegen för att skapa och verifiera ditt nya Microsoft-konto.

När du har en Microsoft-konto går du till [sidan för Azure-registrering](https://azure.microsoft.com/free/ai/), väljer **Starta kostnads fritt**och skapar ett nytt Azure-konto med hjälp av en Microsoft-konto.

### <a name="create-a-speech-resource-in-azure"></a>Skapa en tal-resurs i Azure

> [!NOTE]
> Du kan skapa ett obegränsat antal prenumerationer för standard-nivån i en eller flera regioner. Du kan dock skapa endast en kostnadsfri nivå-prenumeration. Modell distributioner på den kostnads fria nivån som fortsätter att vara oanvända i 7 dagar inaktive ras automatiskt.

Lägga till en tal-tjänstresurs (kostnadsfritt eller betalt nivå) på Azure-kontot:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ditt Microsoft-konto.

1. Välj **skapa en resurs** på upp till vänster på portalen. Om du inte ser **skapa en resurs**, kan du alltid hitta den genom att välja menyn komprimerad i det övre vänstra hörnet:

   ![minimerad navigerings knapp](media/index/collapsed-nav.png)

1. Skriv "tal" i rutan Sök i det **nya** fönstret och tryck på RETUR.

1. I sökresultaten väljer **tal**.

   ![tal Sök Resultat](media/index/speech-search.png)

1. Välj **skapa**och sedan:

   - Ange ett unikt namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer för samma tjänst.
   - Välj den prenumeration som den nya resursen är associerad med för att fastställa hur avgiften faktureras.
   - Välj den [region](regions.md) där resursen ska användas.
   - Välj antingen en kostnads fri (F0) eller betald (S0) pris nivå. Om du vill ha fullständig information om priser och användnings kvoter för varje nivå väljer du **Visa fullständig pris information**.
   - Skapa en ny resursgrupp för den här prenumerationen med tal eller tilldela prenumerationen till en befintlig resursgrupp. Kan du spara dina olika Azure-prenumerationer som ordnas resursgrupperna.
   - Välj **Skapa**. Detta tar dig till distributions översikten och visar meddelanden om distributions förlopp.

Det tar en stund att distribuera den nya tal resursen. När distributionen är klar väljer **du gå till resurs** och i det vänstra navigerings fönstret väljer du **nycklar** för att Visa prenumerations nycklar för din röst tjänst. Varje prenumeration har två nycklar; Du kan använda någon av nycklarna i ditt program. Om du snabbt vill kopiera eller klistra in en nyckel i kod redigeraren eller på en annan plats, väljer du kopierings knappen bredvid varje nyckel, växlar fönster för att klistra in innehållet i Urklipp till önskad plats.

> [!IMPORTANT]
> Dessa prenumerations nycklar används för att få åtkomst till ditt kognitiva tjänst-API. Dela inte dina nycklar. Lagra dem på ett säkert sätt – till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar nycklarna regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="switch-to-a-new-subscription"></a>Växla till en ny prenumeration

Om du vill växla från en prenumeration till en annan, Ersätt till exempel när din kostnadsfria utvärderingsversion upphör att gälla eller när du publicerar ditt program, nyckeln region och prenumeration i din kod med nyckeln region och prenumeration för den nya Azure-resursen.

## <a name="about-regions"></a>Om regioner

- Om programmet använder ett [tal SDK](speech-sdk.md), ange regionskod, t.ex `westus`, när du skapar en tal-konfiguration.
- Om ditt program använder en av Speech-tjänsten [REST API: er](rest-apis.md), regionen är en del av slutpunkten URI som du använder när du gör förfrågningar.
- Nycklar som har skapats för en region är endast giltiga i den regionen. Försök att använda dem med andra regioner leder autentiseringsfel.

## <a name="next-steps"></a>Nästa steg

Gör något av våra snabbstarter på 10 minuter eller Kolla in våra SDK-exempel:

> [!div class="nextstepaction"]
> [Snabbstart: Känna igen tal i C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [tal SDK-exempel](speech-sdk.md#get-the-samples)
