---
title: Prenumerations nycklar – LUIS
titleSuffix: Azure Cognitive Services
description: Du behöver inte skapa prenumerations nycklar för att använda dina kostnads fria första-1000-slut punkts frågor. Om du får ett fel _av kvoten_ i form av en HTTP 403 eller 429 måste du skapa en nyckel och tilldela den till din app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: c7e23c78b5d03b834d593bd2b53958c3379c51f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560517"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Använda prenumerationsnycklar med LUIS-appen

När du först använder Language Understanding (LUIS) behöver du inte skapa prenumerations nycklar. Du får 1000 slut punkts frågor att börja med. 

Testning och prototyper endast kan du använda den kostnadsfria nivån av (F0). Produktionssystem, använda en [betald](https://aka.ms/luis-price-tier) nivå. Använd inte den [redigering nyckeln](luis-concept-keys.md#authoring-key) för slutpunkten frågor i produktion.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Skapa en resurs för förutsägelse slut punkts körning i Azure Portal

Du skapar en [förutsägelse slut punkts resurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) i Azure Portal. Den här resursen bör endast användas för slut punkts förutsägelse frågor. Använd inte den här resursen för att redigera ändringar i appen.

Du kan skapa en Language Understanding resurs eller en Cognitive Services resurs. Om du skapar en Language Understanding resurs är det en bra idé att postpend resurs typen till resurs namnet. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>

### <a name="using-resource-from-luis-portal"></a>Använda resurser från LUIS-portalen

Om du använder resursen från LUIS-portalen behöver du inte känna till din nyckel och plats. I stället måste du känna till resursens klient organisation, prenumeration och resurs namn.

När du [](#assign-resource-key-to-luis-app-in-luis-portal) har tilldelat din resurs till din Luis-app i Luis-portalen tillhandahålls nyckeln och platsen som en del av slut punkten för fråge förutsägelsens slut punkt på sidan Hantera avsnitts **nycklar och slut punkts inställningar** .
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Använda resurser från REST API eller SDK

Om du använder resursen från REST APIen eller SDK: n måste du känna till din nyckel och plats. Den här informationen finns som en del av slut punkts-URL: en för fråge förutsägelse på sidan Hantera avsnittets **nycklar och slut punkts inställningar** , samt i Azure Portal på resursens översikts-och nyckel sidor.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Tilldela resurs nyckeln till LUIS-appen i LUIS-portalen

Varje gång du skapar en ny resurs för LUIS måste du [tilldela resursen till Luis-appen](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). När du har tilldelat dig behöver du inte göra detta steg igen om du inte skapar en ny resurs. Du kan skapa en ny resurs för att expandera regionerna i appen eller för att stödja ett större antal förutsägelse frågor.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Ta bort resurs
När du frigör slutpunktsnyckeln bort den inte från Azure. Det är bara avlänkas från LUIS. 

När en slutpunktsnyckeln otilldelade, eller inte har tilldelats appen, ett begärande till slutpunkten URL returnerar ett fel: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Inkludera alla avsikt förutsägelsepoängen
Den **inkludera alla förutse avsikt poäng** kryssrutan tillåter frågesvaret slutpunkten att inkludera förutsägelse poängen för varje avsikt. 

Den här inställningen kan din chattrobot eller LUIS-anropa program kan besluta programmässiga baserat på poäng för returnerade avsikter. I allmänhet är de främsta två avsikterna mest intressanta. Om den översta poängen är avsiktlig, din chattrobot kan du ställa en fråga för uppföljning som gör en slutgiltig val mellan avsikt ingen och andra flest poäng avsikten ingen. 

Avsikter och deras resultat är också inkluderat endpoint-loggarna. Du kan [exportera](luis-how-to-start-new-app.md#export-app) dessa loggar och analysera poängen. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Aktivera Bing-stavningskontroll 
I den **slutpunkts-url-inställningar**, **Bing-stavningskontroll** växlingen möjliggör LUIS för att korrigera felstavade ord. innan förutsägelse. Skapa en  **[stavningskontroll i Bing nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Lägg till parametern stavnings kontroll **= True** QueryString och **Bing-stavning-check-Subscription-Key = {YOUR_BING_KEY_HERE}** . Ersätt den `{YOUR_BING_KEY_HERE}` med din nyckel för Bing stavningskontroll för installation.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Publicera regioner

Läs mer om hur du publicerar [regioner](luis-reference-regions.md) inklusive publicering i [Europa](luis-reference-regions.md#publishing-to-europe), och [Australien](luis-reference-regions.md#publishing-to-australia). Publicera regioner skiljer sig från redigering regioner. Skapa en app i regionen redigering som motsvarar regionen publicerar du vill använda för frågan slutpunkten.

## <a name="assign-resource-without-luis-portal"></a>Tilldela resurs utan LUIS-portalen

För automation, till exempel en CI/CD-pipeline, kanske du vill automatisera tilldelningen av en LUIS-resurs till en LUIS-app. Du måste utföra följande steg för att göra det:

1. Skaffa en Azure Resource Manager-token från detta [webbplats](https://resources.azure.com/api/token?plaintext=true). Denna token upphör så Använd den omedelbart. Begäran returnerar en Azure Resource Manager-token.

    ![Begär Azure Resource Manager-token och ta emot Azure Resource Manager token](./media/luis-manage-keys/get-arm-token.png)

1. Använd token för att begära LUIS-resurser mellan prenumerationer, från den [hämta LUIS azure konton API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ditt användarkonto har åtkomst till. 

    Det här INLÄGGET API kräver följande inställningar:

    |Huvud|Värde|
    |--|--|
    |`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.| 
    |`Ocp-Apim-Subscription-Key`|Din [redigering nyckeln](luis-how-to-account-settings.md).|

    Den här API: T returnerar en matris av JSON-objekt LUIS prenumerationer, inklusive prenumerations-ID, resursgrupp och resursnamn, returneras som kontonamn. Hitta ett objekt i matrisen med LUIS resursen ska tilldelas LUIS-app. 

1. Tilldela token till LUIS-resursen med den [tilldelar en LUIS azure-konton till ett program](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API. 

    Det här INLÄGGET API kräver följande inställningar:

    |Typ|Inställning|Värde|
    |--|--|--|
    |Huvud|`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.|
    |Huvud|`Ocp-Apim-Subscription-Key`|Din [redigering nyckeln](luis-how-to-account-settings.md).|
    |Huvud|`Content-type`|`application/json`|
    |QueryString|`appid`|LUIS-app-ID. 
    |Innehåll||{”AzureSubscriptionId”: ”ddda2925-af7f-4b05-9ba1-2155c5fe8a8e”<br>”ResourceGroup”: ”resourcegroup-2”,<br>”AccountName”: ”luis-uswest-S0-2”}|

    När detta API lyckas returnerar 201 - skapade status. 

## <a name="change-pricing-tier"></a>Ändra prisnivå

1.  I [Azure](https://portal.azure.com), hitta LUIS-prenumerationen. Välj prenumerationen LUIS.
    ![Hitta din LUIS-prenumeration](./media/luis-usage-tiers/find.png)
1.  Välj **pris nivå** för att se tillgängliga pris nivåer. 
    ![Visa prisnivåer](./media/luis-usage-tiers/subscription.png)
1.  Välj pris nivå och välj **Välj** för att spara ändringen. 
    ![Ändra LUIS betalning nivå](./media/luis-usage-tiers/plans.png)
1.  När prissättningsändringen är klar, kontrollerar den nya prisnivån i ett popup-fönster. 
    ![Kontrollera din betalning LUIS-nivå](./media/luis-usage-tiers/updated.png)
1. Kom ihåg att [tilldela den här slutpunktsnyckeln](#assign-endpoint-key) på den **publicera** sidan och använda det på alla endpoint-frågor. 

## <a name="fix-http-status-code-403-and-429"></a>Korrigera HTTP-statuskod 403 och 429

Du får status koderna 403 och 429 när du överskrider transaktionerna per sekund eller transaktioner per månad för din pris nivå.

### <a name="when-you-receive-an-http-403-error-status-code"></a>När du får en HTTP 403-fel status kod

När du använder alla de kostnads fria 1000-slutpunkts frågorna eller om du överskrider din pris nivås kvot för månads transaktioner får du en HTTP 403-fel status kod. 

För att åtgärda det här felet måste du antingen [ändra pris nivån](luis-how-to-azure-subscription.md#change-pricing-tier) till en högre nivå eller [skapa en ny resurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) och [tilldela den till din app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Lösningar för det här felet är:

* I [Azure Portal](https://portal.azure.com)i language Understanding resurs, på **pris nivån resurs hantering->** , ändrar du pris nivån till en högre TPS-nivå. Du behöver inte göra något i Language Understanding portal om din resurs redan har tilldelats till din Language Understanding-app.
*  Om din användning överskrider den högsta pris nivån lägger du till fler Language Understanding resurser med en belastningsutjämnare framför dem. [Language Understanding containern](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.

### <a name="when-you-receive-an-http-429-error-status-code"></a>När du får en HTTP 429-fel status kod

Den här status koden returneras när transaktionerna per sekund överskrider pris nivån.  

Lösningarna omfattar:

* Du kan [öka pris nivån](#change-pricing-tier)om du inte är på den högsta nivån.
* Om din användning överskrider den högsta pris nivån lägger du till fler Language Understanding resurser med en belastningsutjämnare framför dem. [Language Understanding containern](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.
* Du kan Grinda klient program begär Anden med en [princip](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) för återförsök som du implementerar själv när du får den här status koden. 

## <a name="viewing-summary-usage"></a>Visa sammanfattning av användning
Du kan visa information om LUIS användning i Azure. Den **översikt** visar senaste sammanfattningsinformation inklusive anrop och fel. Om du gör en LUIS-slutpunktsförfrågan, sedan omedelbart titta på den **översiktssidan**, vänta fem minuter för användningen visas.

![Visa sammanfattning av användning](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Anpassa diagram för användning
Mått ger en mer detaljerad översikt över data.

![Standard-mått](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera din måttdiagram för lång tid och Måttyp. 

![Anpassade mått](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Totalt antal transaktioner tröskelvärde för avisering
Om du vill veta när du har nått ett visst transaktion tröskelvärde, till exempel 10 000 transaktioner, kan du skapa en avisering. 

![Standard-aviseringar](./media/luis-usage-tiers/alert-default.png)

Lägg till en måttavisering för den **Totalt antal anrop** mått för en viss tidsperiod. Lägg till e-postadresserna för alla personer som ska ta emot aviseringen. Lägg till webhooks för alla system som ska ta emot aviseringen. Du kan också köra en logikapp när aviseringen utlöses. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [versioner](luis-how-to-manage-versions.md) att hantera ändringar i LUIS-appen.
