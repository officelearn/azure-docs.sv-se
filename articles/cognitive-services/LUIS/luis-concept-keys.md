---
title: Så här använder du redigerings- och körningsnycklar med LUIS
titleSuffix: Azure Cognitive Services
description: LUIS använder två nycklar, redigeringsnyckeln för att skapa modellen och körningsnyckeln för att fråga förutsägelseslutpunkten med användaryttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220967"
---
# <a name="authoring-and-runtime-keys"></a>Redigering och körningsnycklar

Språk understanding (LUIS) har två tjänster och API-uppsättningar: 

* Redigering (tidigare känd som _programmatisk_)
* Förutsägelsekörning

Det finns flera nyckeltyper, beroende på vilken tjänst du vill arbeta med och hur du vill arbeta med den.

## <a name="non-azure-resources-for-luis"></a>Icke-Azure-resurser för LUIS

### <a name="starter-key"></a>Startnyckel

När du börjar använda LUIS skapas en **startnyckel** åt dig. Den här resursen innehåller:

* kostnadsfria begäranden om redigeringstjänst via LUIS-portalen eller API:er (inklusive SDK:er)
* kostnadsfria slutpunktsbegäranden för förutsägelse per månad via en webbläsare, ett API eller ett SDK:er

## <a name="azure-resources-for-luis"></a>Azure-resurser för LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS tillåter tre typer av Azure-resurser: 
 
|Nyckel|Syfte|Kognitiv tjänst`kind`|Kognitiv tjänst`type`|
|--|--|--|--|
|[Skapa nyckel](#programmatic-key)|Få tillgång till och hantera data för program med redigering, utbildning, publicering och testning. Skapa en LUIS-redigeringsnyckel om du tänker skapa LUIS-appar programmässigt.<br><br>Syftet med `LUIS.Authoring` nyckeln är att du ska kunna:<br>* programmässigt hantera språkförståelse appar och modeller, inklusive utbildning, och publicering<br> * kontrollera behörigheter till redigeringsresursen genom att tilldela personer till [deltagarrollen](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Prognosnyckel](#prediction-endpoint-runtime-key)| Slutpunktsbegäranden för frågeförutsägelse. Skapa en LUIS-förutsägelsenyckel innan klientappen begär förutsägelser utöver de 1 000 begäranden som tillhandahålls av startresursen. |`LUIS`|`Cognitive Services`|
|[Resursnyckel för cognitivetjänst med flera tjänster](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Frågeförutsägelseslutpunktsbegäranden som delas med LUIS och andra kognitiva tjänster som stöds.|`CognitiveServices`|`Cognitive Services`|

När processen för att skapa resurser är klar [tilldelar du nyckeln](luis-how-to-azure-subscription.md) till appen i LUIS-portalen.

Det är viktigt att du skapar LUIS-appar i [områden](luis-reference-regions.md#publishing-regions) där du vill publicera och fråga.

> [!CAUTION]
> För enkelhetens skull använder många av exemplen [startnyckeln](#starter-key) eftersom det ger några gratis förutsägelse slutpunktsanrop i sin [kvot](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Frågeutsägelsresurser

* Körningsnyckeln kan användas för alla LUIS-appar eller för specifika LUIS-appar. 
* Använd inte körningsnyckeln för att skapa LUIS-appar. 

Slutpunkten LUIS-körning accepterar två frågeformat, båda använder slutpunktskörningsnyckeln för förutsägelse, men på olika platser.

Slutpunkten som används för att komma åt körningen använder en underdomän som är `{region}` unik för resursens region, som anges med i följande tabell. 

## <a name="assignment-of-the-key"></a>Tilldelning av nyckeln

Du kan [tilldela](luis-how-to-azure-subscription.md) körningsnyckeln i [LUIS-portalen](https://www.luis.ai) eller via motsvarande API:er. 

## <a name="key-limits"></a>Nyckelgränser

Du kan skapa upp till 10 redigeringsnycklar per region och prenumeration. 

Se [Nyckelgränser](luis-boundaries.md#key-limits) och [Azure-regioner](luis-reference-regions.md). 

Publiceringsregioner skiljer sig från redigeringsregioner. Se till att du skapar en app i redigeringsregionen som motsvarar den publiceringsregion som du vill att klientprogrammet ska finnas.

## <a name="key-limit-errors"></a>Nyckelgränsfel
Om du överskrider din TPS-kvot (Transactions-per-second) visas ett HTTP 429-fel. Om du överskrider din TPS-kvot (Transaction per månad) visas ett HTTP 403-fel. 

## <a name="contributions-from-other-authors"></a>Bidrag från andra författare

**För [redigering av resurs migrerade](luis-migration-authoring.md) appar:** deltagare _hanteras_ i Azure-portalen för redigeringsresursen med hjälp av sidan **Åtkomstkontroll (IAM).** Läs om hur du [lägger till en användare](luis-how-to-collaborate.md)med hjälp av medarbetarens e-postadress och _deltagarrollen._ 

**För appar som inte har migrerat ännu**: alla _medarbetare hanteras_ i LUIS-portalen från sidan **Hantera -> medarbetare.**

## <a name="move-transfer-or-change-ownership"></a>Flytta, överföra eller byta ägarskap

En app definieras av dess Azure-resurser, som bestäms av ägarens prenumeration. 

Du kan flytta LUIS-appen. Använd följande dokumentationsresurser i Azure-portalen eller Azure CLI:

* [Flytta appen mellan LUIS-redigeringsresurser](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Flytta resurs till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Flytta resurs inom samma prenumeration eller mellan prenumerationer](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Så här överför [du äganderätten](../../cost-management-billing/manage/billing-subscription-transfer.md) till din prenumeration: 

**För användare som har migrerat - [skapa resurs migrerade](luis-migration-authoring.md) appar:** Som `contributor`ägare av resursen kan du lägga till en .

**För användare som inte har migrerat ännu**: Exportera din app som en JSON-fil. En annan LUIS-användare kan importera appen och därmed bli appägare. Den nya appen kommer att ha ett annat app-ID.  

## <a name="access-for-private-and-public-apps"></a>Åtkomst för privata och offentliga appar

För en **privat** app är körningsåtkomst tillgänglig för ägare och deltagare. För en **offentlig** app är körningsåtkomst tillgänglig för alla som har sin egen Azure [Cognitive Service-](../cognitive-services-apis-create-account.md) eller LUIS-körningsresurs och har den offentliga appens ID. [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 

För närvarande finns det inte en katalog med offentliga appar.

### <a name="authoring-access"></a>Åtkomst för redigering
Åtkomst till appen [LUIS](luis-reference-regions.md#luis-website) från LUIS-portalen eller [redigerings-API:erna](https://go.microsoft.com/fwlink/?linkid=2092087) styrs av Azure-redigeringsresursen. 

Ägaren och alla deltagare har åtkomst till appen. 

|Författaråtkomst omfattar|Anteckningar|
|--|--|
|Lägga till eller ta bort slutpunktsnycklar||
|Exportera version||
|Exportera slutpunktsloggar||
|Importera version||
|Gör appen offentlig|När en app är offentlig kan alla som har en redigerings- eller slutpunktsnyckel fråga appen.|
|Ändra modell|
|Publicera|
|Granska slutpunktsyttranden för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md)|
|Träna|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Förutsägelseslutpunktskörningsåtkomst

Åtkomsten till frågan om förutsägelseslutpunkten styrs av en inställning på sidan **Programinformation** i avsnittet **Hantera.** 

|[Privat slutpunkt](#runtime-security-for-private-apps)|[Offentlig slutpunkt](#runtime-security-for-public-apps)|
|:--|:--|
|Tillgänglig för ägare och bidragsgivare|Tillgänglig för ägare, bidragsgivare och alla andra som känner till app-ID|

Du kan styra vem som ser luis-körningsnyckeln genom att anropa den i en server-till-server-miljö. Om du använder LUIS från en bot är anslutningen mellan roboten och LUIS redan säker. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API på serversidan (till exempel en [Azure-funktion)](https://azure.microsoft.com/services/functions/)med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När API:et på serversidan anropas och autentiseras och auktoriseringen verifieras skickar du anropet till LUIS. Även om den här strategin inte förhindrar man-in-the-middle-attacker, fördunklar den din nyckel- och slutpunkts-URL från användarna, låter dig spåra åtkomst och låter dig lägga till slutpunktssvarsloggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Körningssäkerhet för privata appar

En privat apps körning är endast tillgänglig för följande:

|Nyckel och användare|Förklaring|
|--|--|
|Ägarens redigeringsnyckel| Upp till 1 000 slutpunktsträffar|
|Medarbetare/deltagarförfattare| Upp till 1 000 slutpunktsträffar|
|Alla nyckeler som tilldelats LUIS av en författare eller medarbetare/medarbetare|Baserat på nyckelanvändningsnivå|

#### <a name="runtime-security-for-public-apps"></a>Körningssäkerhet för offentliga appar

När en app har konfigurerats som offentlig kan _alla_ giltiga LUIS-redigeringsnyckel eller LUIS-slutpunktsnyckeln fråga appen så länge nyckeln inte har använt hela slutpunktskvoten.

En användare som inte är ägare eller deltagare kan bara komma åt en offentlig apps körning om den får app-ID: t. LUIS har ingen offentlig _marknad_ eller annat sätt att söka efter en offentlig app.  

En offentlig app publiceras i alla regioner så att en användare med en regionbaserad LUIS-resursnyckel kan komma åt appen i vilken region som är associerad med resursnyckeln.

## <a name="transfer-of-ownership"></a>Ägarbyte

LUIS har inte konceptet att överföra ägarskap för en resurs. 

## <a name="securing-the-endpoint"></a>Säkra slutpunkten 

Du kan styra vem som kan se din LUIS-förutsägelsekörningsslutpunktsnyckel genom att anropa den i en server-till-server-miljö. Om du använder LUIS från en bot är anslutningen mellan roboten och LUIS redan säker. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API på serversidan (till exempel en [Azure-funktion)](https://azure.microsoft.com/services/functions/)med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När API:et på serversidan anropas och autentisering och auktorisering verifieras skickar du anropet till LUIS. Även om den här strategin inte förhindrar man-in-the-middle-attacker, fördunklar den din slutpunkt från användarna, låter dig spåra åtkomst och låter dig lägga till slutpunktssvarsloggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Nästa steg

* Förstå [versionsbegrepp.](luis-concept-version.md) 
* Läs om hur du [skapar nycklar](luis-how-to-azure-subscription.md).
