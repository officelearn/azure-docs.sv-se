---
title: Integrera och hantera säkerhetsåtgärder & Microsoft Graph Security
description: Förbättra appens hotskydd, identifiering och svar med Microsoft Graph Security & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598841"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Förbättra skydd mot hot genom att integrera säkerhetsåtgärder med Microsoft Graph Security & Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Microsoft Graph [Security](https://docs.microsoft.com/graph/security-concept-overview) Connector kan du förbättra hur appen identifierar, skyddar och svarar på hot genom att skapa automatiserade arbetsflöden för att integrera Microsofts säkerhetsprodukter, tjänster och partner. Du kan till exempel skapa [Azure Security Center-spelböcker](../security-center/security-center-playbooks.md) som övervakar och hanterar Microsoft Graph Security-entiteter, till exempel aviseringar. Här är några scenarier som stöds av Microsoft Graph Security connector:

* Få aviseringar baserat på frågor eller aviserings-ID. Du kan till exempel få en lista som innehåller aviseringar om hög allvarlighetsgrad.

* Uppdatera aviseringar. Du kan till exempel uppdatera aviseringstilldelningar, lägga till kommentarer i aviseringar eller taggaviseringar.

* Övervaka när aviseringar skapas eller ändras genom att skapa [varningsprenumerationer (webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).

* Hantera dina varningsprenumerationer. Du kan till exempel hämta aktiva prenumerationer, förlänga förfallotiden för en prenumeration eller ta bort prenumerationer.

Logikappens arbetsflöde kan använda åtgärder som får svar från Microsoft Graph Security-anslutningsappen och göra utdata tillgängligt för andra åtgärder i arbetsflödet. Du kan också låta andra åtgärder i arbetsflödet använda utdata från microsoft graph security-anslutningsåtgärderna. Om du till exempel får varningar om hög allvarlighetsgrad via Microsoft Graph Security-anslutningsappen kan du skicka dessa aviseringar i ett e-postmeddelande med hjälp av Outlook-anslutningsappen. 

Mer information om Microsoft Graph Security finns i [översikten över Microsoft Graph Security API](https://aka.ms/graphsecuritydocs). Om du inte har tidigare i logikappar läser du [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) Om du letar efter Microsoft Flow eller PowerApps, se [Vad är Flow?](https://flow.microsoft.com/) eller [Vad är PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Om du vill använda anslutningsappen för Microsoft Graph-säkerhet måste du ha *uttryckligt medgivande* från innehavaradministratören för Azure Active Directory (AD), vilket är ett av [autentiseringskraven för Microsoft Graph-säkerhet](https://aka.ms/graphsecurityauth). Detta samtycke kräver Microsoft Graph Security-anslutningens program-ID och namn, som du också kan hitta i [Azure-portalen:](https://portal.azure.com)

  | Egenskap | Värde |
  |----------|-------|
  | **Programmets namn** | `MicrosoftGraphSecurityConnector` |
  | **Program-ID:t** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Om du vill ge medgivande för anslutningen kan din Azure AD-klientadministratör följa följande steg:

  * [Ge innehavaradministratörens medgivande för Azure AD-program](../active-directory/develop/v2-permissions-and-consent.md).

  * Under den första körningen av din logikapp kan appen begära godkännande från Azure AD-innehavaradministratören via [funktionen för programmedgivande](../active-directory/develop/application-consent-experience.md).
   
* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt microsoft graph-säkerhetsentiteter, till exempel aviseringar. Om du vill använda en Microsoft Graph Security-utlösare behöver du en tom logikapp. Om du vill använda en microsoft graph-säkerhetsåtgärd behöver du en logikapp som börjar med rätt utlösare för ditt scenario.

## <a name="connect-to-microsoft-graph-security"></a>Ansluta till Microsoft Graph Security 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com/)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar lägger du till utlösaren och andra åtgärder som du vill innan du lägger till en säkerhetsåtgärd i Microsoft Graph.

   ELLER

   För befintliga logikappar väljer du **Nytt steg**under det sista steget där du vill lägga till en microsoft graph-säkerhetsåtgärd .

   ELLER

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Markera plustecknet (+) som visas och välj **Lägg till en åtgärd**.

1. I sökrutan anger du "microsoft graph security" som filter. Välj den åtgärd du vill använda i åtgärdslistan.

1. Logga in med dina Microsoft Graph-säkerhetsuppgifter.

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="add-triggers"></a>Lägga till utlösare

I Azure Logic Apps måste varje logikapp starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett visst villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans och börjar köra appens arbetsflöde.

> [!NOTE] 
> När en utlösare utlöses bearbetar utlösaren alla nya aviseringar. Om inga aviseringar tas emot hoppas utlösarkörningen över. Nästa utlösaravsökning sker baserat på upprepningsintervallet som du anger i utlösarens egenskaper.

Det här exemplet visar hur du kan starta ett arbetsflöde för logikappar när nya aviseringar skickas till din app.

1.  Skapa en tom logikapp i Azure-portalen eller Visual Studio som öppnar Logic App Designer. I det här exemplet används Azure-portalen.

1.  Ange "microsoft graph security" som filter i sökrutan i sökrutan. Välj den här utlösaren i listan utlösare: **På alla nya aviseringar**

1.  I utlösaren anger du information om de aviseringar som du vill övervaka. Om du vill ha fler egenskaper öppnar du listan **Lägg till ny parameter** och väljer en parameter för att lägga till egenskapen i utlösaren.

   | Egenskap | Egenskap (JSON) | Krävs | Typ | Beskrivning |
   |----------|-----------------|----------|------|-------------|
   | **Intervall** | `interval` | Ja | Integer | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på frekvensen. Här är de lägsta och högsta intervallen: <p><p>- Månad: 1-16 månader <br>- Dag: 1-500 dagar <br>- Timme: 1-12 000 timmar <br>- Minut: 1-72 000 minuter <br>- Andra: 1-9,999,999 sekunder <p>Om intervallet till exempel är 6 och frekvensen är "Månad", är upprepningen var sjätte månad. |
   | **Frekvens** | `frequency` | Ja | String | Tidsenheten för upprepningen: **Sekund**, **Minut,** **Timme,** **Dag,** **Vecka**eller **Månad** |
   | **Tidszon** | `timeZone` | Inga | String | Gäller endast när du anger en starttid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Markera den tidszon som du vill använda. |
   | **Starttid** | `startTime` | Inga | String | Ange ett startdatum och en starttid i det här formatet: <p><p>YYYY-MM-DDThh:mm:ss om du väljer en tidszon <p>ELLER <p>Å ÅYY-MM-DDThh:mm:ssZ om du inte väljer en tidszon <p>Om du till exempel vill ha 18 september 2017 klockan 14:00 anger du "2017-09-18T14:00:00" och väljer en tidszon som Stillahavsstandardtid. Du kan också ange "2017-09-18T14:00:00Z" utan tidszon. <p>**Anm.:** Denna starttid har högst 49 år i framtiden och måste följa [ISO 8601 datum tidsspecifikationen](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC datumtid format](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC offset](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon måste du lägga till bokstaven "Z" i slutet utan blanksteg. Detta "Z" avser motsvarande [nautisk tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman är starttiden den första förekomsten, medan utlösaren inte utlöses tidigare än starttiden för komplexa scheman. [*Vilka sätt kan jag använda startdatum och starttid?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  När du är klar väljer du **Spara**i designerverktygsfältet .

1.  Fortsätt nu att lägga till en eller flera åtgärder i logikappen för de uppgifter som du vill utföra med utlösarresultaten.

## <a name="add-actions"></a>Lägga till åtgärder

Här finns mer specifik information om hur du använder de olika åtgärder som är tillgängliga med Microsoft Graph Security connector.

### <a name="manage-alerts"></a>Hantera aviseringar

Om du vill filtrera, sortera eller hämta de senaste resultaten anger *du bara* [ODATA-frågeparametrarna som stöds av Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Ange inte* den fullständiga bas-URL:en eller `https://graph.microsoft.com/v1.0/security/alerts`HTTP-åtgärden, till exempel , eller `GET` åtgärden eller åtgärden. `PATCH` Här är ett specifikt exempel som visar parametrarna för en **Get-aviseringar** åtgärd när du vill ha en lista med hög allvarlighetsgrad varningar:

`Filter alerts value as Severity eq 'high'`

Mer information om de frågor du kan använda med den här anslutningen finns i [referensdokumentationen för Microsoft Graph Security Alerts](https://docs.microsoft.com/graph/api/alert-list). Om du vill skapa förbättrade upplevelser med den här kopplingen kan du läsa mer om [de schemaegenskaper](https://docs.microsoft.com/graph/api/resources/alert) som anslutningen stöder.

| Åtgärd | Beskrivning |
|--------|-------------|
| **Få aviseringar** | Få aviseringar filtrerade baserat på en eller flera `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` [aviseringsegenskaper,](https://docs.microsoft.com/graph/api/resources/alert)till exempel . | 
| **Få avisering genom ID** | Få en specifik avisering baserat på aviserings-ID: et. | 
| **Uppdatera avisering** | Uppdatera en specifik avisering baserat på aviserings-ID: n. Om du vill vara säker på att du skickar de obligatoriska och redigerbara egenskaperna i din begäran läser du de [redigerbara egenskaperna för aviseringar](https://docs.microsoft.com/graph/api/alert-update). Om du till exempel vill tilldela en avisering till en säkerhetsanalytiker så att de kan undersöka kan du uppdatera aviseringens **tilldelad egenskap.** |
|||

### <a name="manage-alert-subscriptions"></a>Hantera aviseringar

Microsoft Graph stöder [*prenumerationer*](https://docs.microsoft.com/graph/api/resources/subscription)eller [*webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Om du vill hämta, uppdatera eller ta bort prenumerationer anger du [ODATA-frågeparametrarna som stöds av Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) till microsoft graph-entitetskonstruktionen och inkluderar `security/alerts` följt av ODATA-frågan. *Ta inte med* bas-URL: `https://graph.microsoft.com/v1.0`et, till exempel . Använd i stället formatet i det här exemplet:

`security/alerts?$filter=status eq 'New'`

| Åtgärd | Beskrivning |
|--------|-------------|
| **Skapa prenumerationer** | [Skapa en prenumeration](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) som meddelar dig om eventuella ändringar. Du kan filtrera den här prenumerationen för de specifika aviseringstyper du vill använda. Du kan till exempel skapa en prenumeration som meddelar dig om aviseringar om hög allvarlighetsgrad. |
| **Få aktiva prenumerationer** | [Få oexpirerade prenumerationer](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Uppdatera prenumeration** | [Uppdatera en prenumeration](https://docs.microsoft.com/graph/api/subscription-update) genom att tillhandahålla prenumerations-ID. Om du till exempel vill utöka prenumerationen `expirationDateTime` kan du uppdatera prenumerationens egendom. | 
| **Ta bort prenumeration** | [Ta bort en prenumeration](https://docs.microsoft.com/graph/api/subscription-delete) genom att ange prenumerations-ID. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Hantera indikatorer för hotinformation

Om du vill filtrera, sortera eller hämta de senaste resultaten anger *du bara* [ODATA-frågeparametrarna som stöds av Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Ange inte* den fullständiga bas-URL:en eller `https://graph.microsoft.com/beta/security/tiIndicators`HTTP-åtgärden, till exempel , eller `GET` åtgärden eller åtgärden. `PATCH` Här är ett specifikt exempel som visar parametrarna för en **Get tiIndicators-åtgärd** när du vill ha en lista med hottypen: `DDoS`

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Mer information om de frågor som du kan använda med den här kopplingen finns [i referensdokumentationen för valfria frågeparametrar i Microsoft Graph Security threat intelligence-referensdokumentationen](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http). Om du vill skapa förbättrade upplevelser med den här kopplingen kan du läsa mer om den indikator för [schemaegenskaper som informationsindikatorn för hotinformation](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) som anslutningen stöder.

| Åtgärd | Beskrivning |
|--------|-------------|
| **Få indikatorer för hotinformation** | Få tiIndicators filtrerade baserat på en eller flera [tiIndicator egenskaper,](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta)till exempel,`threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Hämta indikator för hotinformation efter ID** | Få en specifik tiIndicator baserat på tiIndicator ID. | 
| **Skapa indikator för hotinformation** | Skapa en ny tiIndicator genom att publicera till tiIndicators-samlingen. Om du vill vara säker på att du skickar de nödvändiga egenskaperna i din begäran läser du de egenskaper som krävs för att [skapa tiIndicator](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http). |
| **Skicka in flera indikatorer för hotinformation** | Skapa flera nya tiIndicators genom att publicera en tiIndicators samling. Om du vill vara säker på att du skickar de nödvändiga egenskaperna i din begäran läser du de egenskaper som krävs för att [skicka flera tiIndicatorer](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http). |
| **Uppdatera indikator för hotinformation** | Uppdatera en specifik tiIndicator baserat på tiIndicator-ID. Om du vill vara säker på att du skickar de obligatoriska och redigerbara egenskaperna i din begäran läser du de [redigerbara egenskaperna för tiIndicator](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http). Om du till exempel vill uppdatera åtgärden så att den gäller om indikatorn matchas inifrån säkerhetsverktyget för målProdukt kan du uppdatera egenskapen tiIndicator's **action.** |
| **Uppdatera flera indikatorer för hotinformation** | Uppdatera flera tiIndicators. Om du vill vara säker på att du skickar de nödvändiga egenskaperna i din begäran läser du de [egenskaper som krävs för att uppdatera flera tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http). |
| **Ta bort indikator för hotinformation efter ID** | Ta bort en specifik tiIndicator baserat på tiIndicator-ID. |
| **Ta bort flera indikatorer för hotinformation efter ID** | Ta bort flera tiIndicators med deras ID. Om du vill vara säker på att du skickar de nödvändiga egenskaperna i din begäran läser du de egenskaper som krävs för att [ta bort flera tiIndicators med ID.](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http) |
| **Ta bort flera indikatorer för hotinformation med externa ID:er** | Ta bort flera tiIndicators av de externa ID:na. Om du vill vara säker på att du skickar de nödvändiga egenskaperna i din begäran läser du de egenskaper som krävs för att [ta bort flera tiIndicatorer med externa ID:er](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http). |
|||

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](https://aka.ms/graphsecurityconnectorreference).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
