---
title: Redigerings-och körnings nycklar – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS använder två nycklar, redigerings nyckeln för att skapa din modell och körnings nyckeln för att skicka frågor till förutsägelse slut punkten med User yttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 70e58077fa40ce685324cd24b447886ec3411034
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703188"
---
# <a name="authoring-and-runtime-keys"></a>Redigering och körningsnycklar


>[!NOTE]
>[Migrera](luis-migration-authoring.md) alla appar, som inte använder Azures redigerings resurs, innan du fortsätter.

LUIS använder två typer av Azure-resurser. varje typ har nycklar: 
 
* [Redigering](#programmatic-key) för att skapa avsikter, entiteter och etikett yttranden, träna och publicera. När du är redo att publicera LUIS-appen behöver du en [förutsägelse slut punkts nyckel för körningen som är](luis-how-to-azure-subscription.md) tilldelad till appen.
* [Förutsägelse slut punkt nyckel för körnings miljön](#prediction-endpoint-runtime-key). Klient program, till exempel en chatt-robot, behöver åtkomst till körnings **frågans slut punkt för frågans förutsägelse** via den här nyckeln. 

|Nyckel|Syfte|Kognitiv tjänst `kind`|Kognitiv tjänst `type`|
|--|--|--|--|
|[Redigera nyckel](#programmatic-key)|Redigering, utbildning, publicering, testning.|`LUIS.Authoring`|`Cognitive Services`|
|[Körnings nyckel för förutsägelse slut punkt](#prediction-endpoint-runtime-key)| Med en uttryck kan du fastställa intentor och entiteter med en användar-.|`LUIS`|`Cognitive Services`|

LUIS tillhandahåller också en [Start nyckel](luis-how-to-azure-subscription.md#starter-key) med 1000 transaktioner per månads förutsägelse slut punkt kvot. 

Även om du inte behöver skapa båda nycklarna samtidigt är det mycket enklare om du gör det.

Det är viktigt att du skapar LUIS-appar i [regioner](luis-reference-regions.md#publishing-regions) där du vill publicera och fråga.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Redigera nyckel

En redigerings nyckel skapas automatiskt när du skapar ett LUIS-konto och det är kostnads fritt. När du börjar med LUIS har du en start nyckel över alla dina LUIS-appar för varje redigerings [region](luis-reference-regions.md). Syftet med redigerings nyckeln är att tillhandahålla autentisering för att hantera din LUIS-app eller för att testa förutsägelse slut punkts frågor. 

Genom att skapa redigerings nycklar i Azure Portal kan du kontrol lera behörigheterna till redigerings resursen genom att tilldela användare [rollen deltagare](#contributions-from-other-authors). Du måste ha behörighet på Azures prenumerations nivå för att lägga till bidrags givare. 

Om du vill hitta redigerings nyckeln loggar du in på [Luis](luis-reference-regions.md#luis-website) och klickar på konto namnet i det övre högra navigerings fältet för att öppna **konto inställningar**.

![Redigera nyckel](./media/luis-concept-keys/authoring-key.png)

Skapa Azure [Luis-resursen](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)när du vill göra **körnings frågor**. 

> [!CAUTION]
> Av praktiska skäl använder många av exemplen [Start nyckeln](#starter-prediction-endpoint-runtime-key) , eftersom det innehåller några kostnads fria förutsägelse slut punkter i [kvoten](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Körnings nyckel för förutsägelse slut punkt 

När du behöver **körnings slut punkts frågor**skapar du en language Understanding-resurs (Luis) och tilldelar den till Luis-appen. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

När du har skapat en resurs, [tilldelar du nyckeln](luis-how-to-azure-subscription.md) till appen. 

* Med nyckeln Runtime (Query förutsägelse slut punkt) kan du använda en kvot för slut punkts träffar baserat på den användnings plan som du angav när du skapade körnings nyckeln. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) information om priser.

* Körnings nyckeln kan användas för alla dina LUIS-appar eller för vissa LUIS-appar. 
* Använd inte körnings nyckeln för att redigera LUIS-appar. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Slut punkts körnings nyckel för start förutsägelse

Slut punkts nyckeln **starter** förutsägelse tillhandahåller kostnads fri och innehåller 1000 förutsägelse slut punkts frågor. När frågorna har använts bör du skapa en egen förutsägelse slut punkt resurs för Language Understanding.  

Det här är en särskild resurs som skapas åt dig. Den visas inte i listan över Azure-resurser eftersom den är avsedd som en tillfällig start nyckel. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Använd körnings nyckel i fråga
LUIS runtime-slutpunkten godkänner två typer av frågor, både med körnings nyckeln för förutsägelse slut punkt, men på olika platser.

Slut punkten som används för att få åtkomst till körnings miljön använder en under domän som är unik för din resurs region, som betecknas med `{region}` i följande tabell. 


#### <a name="v2-prediction-endpointtabv2"></a>[V2-förutsägelse slut punkt](#tab/V2)

|verb|Exempel på placering av url och nyckel|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`|

#### <a name="v3-prediction-endpointtabv3"></a>[V3 förutsägelse slut punkt](#tab/V3)

|verb|Exempel på placering av url och nyckel|
|--|--|
|[GET](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a91e54c9db63d589f433)|`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?runtime-key=your-endpoint-key-here&query=turn%20on%20the%20lights`|
|[POST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a5830f741b27cd03a061)| `https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict`| 

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

* * * 

**HÄMTA**: Ändra din fråga slutpunktsvärdet för den `runtime-key` från den redigering (starter)-nyckeln till den nya slutpunktsnyckeln för att kunna använda LUIS endpoint viktiga kvot hastighet. Om du skapar nyckeln, och tilldela nyckeln men inte ändra frågan slutpunktsvärdet för `runtime-key`, du inte använder din viktiga kvot för slutpunkten.

**PUBLICERA**: Ändra huvud värde för `Ocp-Apim-Subscription-Key`<br>Om du skapar körnings nyckeln och tilldelar körnings nyckeln men inte ändrar värdet för slut punkts fråga för `Ocp-Apim-Subscription-Key`, använder du inte din körnings nyckel.

App-ID som används i de föregående URL: er, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, är den offentliga IoT-app som används för den [interaktiv demonstration](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Tilldelning av körnings nyckel

Du kan [tilldela](luis-how-to-azure-subscription.md) körnings nyckeln i [Luis-portalen](https://www.luis.ai) eller via motsvarande API: er. 

## <a name="key-limits"></a>Viktiga begränsningar

Du kan skapa upp till 10 redigerings nycklar per region per prenumeration. 

Se [nyckel gränser](luis-boundaries.md#key-limits) och [Azure-regioner](luis-reference-regions.md). 

Publicera regioner skiljer sig från redigering regioner. Se till att du skapar en app i den redigerings region som motsvarar den publicerings region som du vill att ditt klient program ska finnas i.

## <a name="key-limit-errors"></a>Gräns för fel
Om du överskrider kvoten för transaktioner per sekund (TPS) får du ett HTTP 429-fel. Om du överskrider kvoten för din transaktion per månad (TPS) får du ett HTTP 403-fel. 

## <a name="contributions-from-other-authors"></a>Bidrag från andra författare



Hantering av bidrag från medarbetare beror på appens aktuella status.

**För [redigering av resurs migrerade](luis-migration-authoring.md) appar**: _deltagare_ hanteras i Azure Portal för redigerings resursen med hjälp av **åtkomst kontroll (IAM)** sidan. Lär dig [hur du lägger till en användare](luis-how-to-collaborate.md)med hjälp av medarbetares e-postadress och _deltagar_ rollen. 

**För appar som inte har migrerats än**: alla _medarbetare_ hanteras i Luis-portalen på sidan för att **Hantera > samarbets partners** .

### <a name="contributor-roles-vs-entity-roles"></a>Deltagar roller jämfört med enhets roller

[Enhets roller](luis-concept-roles.md) gäller för Luis-appens data modell. Rollen medarbetare/medarbetare gäller nivåer av redigerings åtkomst. 

## <a name="moving-or-changing-ownership"></a>Flytta eller ändra ägarskap

En app definieras av sina Azure-resurser, vilket bestäms av ägarens prenumeration. 

Du kan flytta LUIS-appen. Använd följande dokumentations resurser i Azure Portal eller Azure CLI:

* [Flytta appen mellan LUIS Authoring-resurser](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Flytta resurs till ny resurs grupp eller prenumeration](../../azure-resource-manager/resource-group-move-resources.md)
* [Flytta en resurs inom samma prenumeration eller mellan prenumerationer](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [Överför ägarskapet](../../billing/billing-subscription-transfer.md) för din prenumeration 

## <a name="access-for-private-and-public-apps"></a>Åtkomst för privata och offentliga appar

För en **privat** app är körnings åtkomst tillgänglig för ägare och deltagare. För en **offentlig** app är körnings åtkomst tillgänglig för alla som har sin egen Azure [-tjänst](../cognitive-services-apis-create-account.md) eller [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) runtime-resurs och har den offentliga appens ID. 

För närvarande finns det inte någon katalog med offentliga appar.

### <a name="authoring-access"></a>Redigera åtkomst
Åtkomst till appen från [Luis](luis-reference-regions.md#luis-website) -portalen eller redigerings- [API: erna](https://go.microsoft.com/fwlink/?linkid=2092087) styrs av Azure Authoring-resursen. 

Ägaren och alla deltagare har åtkomst för att redigera appen. 

|Redigera åtkomst innehåller|Anteckningar|
|--|--|
|Lägg till eller ta bort slutpunkten nycklar||
|Exportera version||
|Exportloggar slutpunkt||
|Importerar version||
|Gör appen offentlig|När en app är offentlig kan alla med en nyckel för redigering eller slutpunkt fråga appen.|
|Ändra modellen|
|Publicera|
|Granska endpoint yttranden för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md)|
|Träna|

### <a name="prediction-endpoint-runtime-access"></a>Åtkomst till förutsägelse slut punkts körning

Åtkomst för att fråga förutsägelse slut punkten styrs av en inställning på sidan **program information** i avsnittet **Hantera** . 

![Set-app till att vara offentligt](./media/luis-concept-security/set-application-as-public.png)

|[Privat slutpunkt](#runtime-security-for-private-apps)|[Offentlig slutpunkt](#runtime-security-for-public-apps)|
|:--|:--|
|Tillgängligt för ägare och deltagare|Tillgängligt för ägare, deltagare och någon annan som känner till app-ID|

Du kan styra vem som ska se din LUIS körnings nyckel genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot och LUIS redan säker. Om du anropar slutpunkten LUIS direkt, bör du skapa ett API för serversidan (till exempel en Azure [funktionen](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När API: et för Server sidan anropas och autentiseras och auktoriseringen verifieras, skicka anropet till LUIS. Även om den här strategin inte hindrar människan-in-the-middle-attacker, obfuscates din nyckel-och slut punkts-URL från dina användare, kan du spåra åtkomst och du kan lägga till slut punkts svars loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Körnings säkerhet för privata appar

En privat Apps körnings miljö är bara tillgänglig för följande:

|Nyckel och användare|Förklaring|
|--|--|
|Ägarens redigering nyckel| Upp till 1000 endpoint träffar|
|Redigerings nycklar för medarbetare/deltagare| Upp till 1000 endpoint träffar|
|Alla nycklar som tilldelats LUIS av en författare eller medarbetare/deltagare|Baserat på nivån för nyckelanvändning|

#### <a name="runtime-security-for-public-apps"></a>Körnings säkerhet för offentliga appar

När en app har konfigurerats som offentliga, _alla_ giltig LUIS redigering nyckel eller LUIS slutpunktsnyckeln kan fråga din app, så länge nyckeln inte har använt hela slutpunkt kvoten.

En användare som inte är ägare eller deltagare, kan bara komma åt en offentlig Apps körnings miljö om det har fått app-ID. LUIS saknar en offentlig _marknaden_ eller annat sätt att söka efter en offentlig app.  

En offentlig app publiceras i alla regioner så att en användare med en region-baserade LUIS Resursnyckeln har åtkomst till appen i regionen som är associerad med resursnyckel.

## <a name="transfer-of-ownership"></a>Överlåtelse av äganderätt

**För [redigering av resurs migrerade](luis-migration-authoring.md) appar**: Som ägare till resursen kan du lägga till en `contributor`.

**För appar som inte har migrerats än**: Exportera din app som en JSON-fil. En annan LUIS-användare kan importera appen så att den blir appens ägare. Den nya appen har ett annat app-ID.  

## <a name="securing-the-endpoint"></a>Skydda slutpunkten 

Du kan styra vem som kan se slut punkts nyckeln för LUIS förutsägelse körning genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot och LUIS redan säker. Om du anropar slutpunkten LUIS direkt, bör du skapa ett API för serversidan (till exempel en Azure [funktionen](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När anropas från serversidan API och autentisering och auktorisering har verifierats, skicka anropet till LUIS. När den här strategin inte hindra man-in-the-middle-attacker, den obfuscates slutpunkten från användarna, kan du spåra åtkomst, och kan du lägga till slutpunkten svar loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Nästa steg

* Förstå [versionshantering](luis-concept-version.md) begrepp. 
* Lär dig [hur du skapar nycklar](luis-how-to-azure-subscription.md).
