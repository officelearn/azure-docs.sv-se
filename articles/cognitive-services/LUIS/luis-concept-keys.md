---
title: Använda redigerings-och körnings nycklar med LUIS
titleSuffix: Azure Cognitive Services
description: LUIS använder två nycklar, redigerings nyckeln för att skapa din modell och körnings nyckeln för att skicka frågor till förutsägelse slut punkten med User yttranden.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220967"
---
# <a name="authoring-and-runtime-keys"></a>Redigering och körningsnycklar

Language Understanding (LUIS) har två tjänster och API-uppsättningar: 

* Redigering (kallades tidigare _program mässig_)
* Förutsägelse körning

Det finns flera olika typer av nycklar, beroende på vilken tjänst du vill arbeta med och hur du vill arbeta med den.

## <a name="non-azure-resources-for-luis"></a>Icke-Azure-resurser för LUIS

### <a name="starter-key"></a>Start nyckel

Första gången du börjar använda LUIS skapas en **Start nyckel** åt dig. Den här resursen tillhandahåller:

* kostnads fria redigerings tjänst begär Anden via LUIS-portalen eller API: er (inklusive SDK: er)
* kostnads fria 1 000 förutsägelse slut punkter per månad via en webbläsare, API eller SDK

## <a name="azure-resources-for-luis"></a>Azure-resurser för LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS tillåter tre typer av Azure-resurser: 
 
|Nyckel|Syfte|Kognitiv tjänst `kind`|Kognitiv tjänst `type`|
|--|--|--|--|
|[Redigerings nyckel](#programmatic-key)|Få åtkomst till och hantera data för program med redigering, utbildning, publicering och testning. Skapa en LUIS redigerings nyckel om du tänker program mässigt redigera LUIS-appar.<br><br>Syftet med `LUIS.Authoring` nyckeln är att du ska kunna:<br>* hantera Language Understanding appar och modeller program mässigt, inklusive utbildning och publicering<br> * kontrol lera behörigheterna till redigerings resursen genom att tilldela användare [rollen deltagare](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Förutsägelse nyckel](#prediction-endpoint-runtime-key)| Slut punkts begär Anden för fråga. Skapa en LUIS-förutsägelse innan klient programmet begär förutsägelser utöver de 1 000-begäranden som tillhandahålls av start resursen. |`LUIS`|`Cognitive Services`|
|[Resurs nyckel för multi-service för kognitiva tjänster](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Slut punkts begär Anden för förfrågan som delas med LUIS och andra Cognitive Services som stöds.|`CognitiveServices`|`Cognitive Services`|

När du har skapat en resurs, [tilldelar du nyckeln](luis-how-to-azure-subscription.md) till appen i Luis-portalen.

Det är viktigt att du skapar LUIS-appar i [regioner](luis-reference-regions.md#publishing-regions) där du vill publicera och fråga.

> [!CAUTION]
> Av praktiska skäl använder många av exemplen [Start nyckeln](#starter-key) , eftersom det innehåller några kostnads fria förutsägelse slut punkter i [kvoten](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Fråga förutsägelse resurser

* Körnings nyckeln kan användas för alla dina LUIS-appar eller för vissa LUIS-appar. 
* Använd inte körnings nyckeln för att redigera LUIS-appar. 

LUIS runtime-slutpunkten godkänner två typer av frågor, både med körnings nyckeln för förutsägelse slut punkt, men på olika platser.

Slut punkten som används för att komma åt körningen använder en under domän som är unik för din resurs region, med `{region}` i följande tabell. 

## <a name="assignment-of-the-key"></a>Tilldelning av nyckeln

Du kan [tilldela](luis-how-to-azure-subscription.md) körnings nyckeln i [Luis-portalen](https://www.luis.ai) eller via motsvarande API: er. 

## <a name="key-limits"></a>Viktiga begränsningar

Du kan skapa upp till 10 redigerings nycklar per region per prenumeration. 

Se [nyckel gränser](luis-boundaries.md#key-limits) och [Azure-regioner](luis-reference-regions.md). 

Publicera regioner skiljer sig från redigering regioner. Se till att du skapar en app i den redigerings region som motsvarar den publicerings region som du vill att ditt klient program ska finnas i.

## <a name="key-limit-errors"></a>Gräns för fel
Om du överskrider kvoten för transaktioner per sekund (TPS) får du ett HTTP 429-fel. Om du överskrider kvoten för din transaktion per månad (TPS) får du ett HTTP 403-fel. 

## <a name="contributions-from-other-authors"></a>Bidrag från andra författare

**För [redigering av resurs migrerade](luis-migration-authoring.md) appar**: _deltagare_ hanteras i Azure Portal för redigerings resursen med hjälp av **åtkomst kontroll (IAM)** sidan. Lär dig [hur du lägger till en användare](luis-how-to-collaborate.md)med hjälp av medarbetares e-postadress och _deltagar_ rollen. 

**För appar som inte har migrerats än**: alla _medarbetare_ hanteras i Luis-portalen på sidan för att **Hantera > samarbets partners** .

## <a name="move-transfer-or-change-ownership"></a>Flytta, överföra eller ändra ägarskap

En app definieras av sina Azure-resurser, vilket bestäms av ägarens prenumeration. 

Du kan flytta LUIS-appen. Använd följande dokumentations resurser i Azure Portal eller Azure CLI:

* [Flytta appen mellan LUIS Authoring-resurser](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Flytta resurs till ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Flytta en resurs inom samma prenumeration eller mellan prenumerationer](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Så här överför du [ägarskapet](../../cost-management-billing/manage/billing-subscription-transfer.md) för din prenumeration: 

**För användare som har migrerat- [redigering av resurs migrerade](luis-migration-authoring.md) appar**: som ägare till resursen kan du lägga till en `contributor`.

**För användare som inte har migrerats än**: exportera din app som en JSON-fil. En annan LUIS-användare kan importera appen så att den blir appens ägare. Den nya appen har ett annat app-ID.  

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
|Granska slut punkts yttranden för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md)|
|Träna|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Åtkomst till förutsägelse slut punkts körning

Åtkomst för att fråga förutsägelse slut punkten styrs av en inställning på sidan **program information** i avsnittet **Hantera** . 

|[Privat slut punkt](#runtime-security-for-private-apps)|[Offentlig slut punkt](#runtime-security-for-public-apps)|
|:--|:--|
|Tillgängligt för ägare och deltagare|Tillgängligt för ägare, deltagare och någon annan som känner till app-ID|

Du kan styra vem som ska se din LUIS körnings nyckel genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot och LUIS redan säker. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API för Server sidan (till exempel en Azure- [funktion](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När API: et för Server sidan anropas och autentiseras och auktoriseringen verifieras, skicka anropet till LUIS. Även om den här strategin inte hindrar människan-in-the-middle-attacker, obfuscates din nyckel-och slut punkts-URL från dina användare, kan du spåra åtkomst och du kan lägga till slut punkts svars loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Körnings säkerhet för privata appar

En privat Apps körnings miljö är bara tillgänglig för följande:

|Nyckel och användare|Förklaring|
|--|--|
|Ägarens redigering nyckel| Upp till 1000 endpoint träffar|
|Redigerings nycklar för medarbetare/deltagare| Upp till 1000 endpoint träffar|
|Alla nycklar som tilldelats LUIS av en författare eller medarbetare/deltagare|Baserat på nivån för nyckelanvändning|

#### <a name="runtime-security-for-public-apps"></a>Körnings säkerhet för offentliga appar

När en app har kon figurer ATS som offentlig kan _alla_ giltiga Luis redigerings nycklar eller Luis-slutpunkt-nyckeln fråga din app, förutsatt att nyckeln inte har använt hela slut punkts kvoten.

En användare som inte är ägare eller deltagare, kan bara komma åt en offentlig Apps körnings miljö om det har fått app-ID. LUIS har inte någon offentlig _marknad_ eller annat sätt att söka efter en offentlig app.  

En offentlig app publiceras i alla regioner så att en användare med en region-baserade LUIS Resursnyckeln har åtkomst till appen i regionen som är associerad med resursnyckel.

## <a name="transfer-of-ownership"></a>Överlåtelse av äganderätt

LUIS har inte konceptet att överföra ägarskapet för en resurs. 

## <a name="securing-the-endpoint"></a>Skydda slutpunkten 

Du kan styra vem som kan se slut punkts nyckeln för LUIS förutsägelse körning genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot och LUIS redan säker. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API för Server sidan (till exempel en Azure- [funktion](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När anropas från serversidan API och autentisering och auktorisering har verifierats, skicka anropet till LUIS. Även om den här strategin inte hindrar människan-in-the-middle-attacker, obfuscates din slut punkt från dina användare, så att du kan spåra åtkomst och du kan lägga till slut punkts svars loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Nästa steg

* Förstå metoder för [versions hantering](luis-concept-version.md) . 
* Lär dig [hur du skapar nycklar](luis-how-to-azure-subscription.md).
