---
title: Ansluta till Dynamics 365
description: Skapa och hantera poster med Dynamics 365 (online) REST-API:er och Azure Logic Apps
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789889"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Hantera Dynamics 365-poster med Azure Logic Apps

Med Azure Logic Apps och Dynamics 365-anslutningsprogrammet kan du skapa automatiserade uppgifter och arbetsflöden baserat på dina poster i Dynamics 365. Dina arbetsflöden kan skapa poster, uppdatera objekt, returnera poster med mera i ditt Dynamics 365-konto. Du kan inkludera åtgärder i logikapparna som får svar från Dynamics 365 och gör utdata tillgängliga för andra åtgärder. När ett objekt till exempel uppdateras i Dynamics 365 kan du skicka ett e-postmeddelande med Office 365.

Den här artikeln visar hur du kan skapa en logikapp som skapar en uppgift i Dynamics 365 när en ny leadpost skapas i Dynamics 365.
Om du inte har tidigare i logikappar läser du [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [Dynamics 365-konto](https://dynamics.microsoft.com)

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Dynamics 365-konto. Om du vill starta logikappen med en Dynamics 365-utlösare behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Lägg till Dynamics 365-utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Lägg först till en Dynamics 365-utlösare som utlöses när en ny leadpost visas i Dynamics 365.

1. Öppna din tomma logikapp i Logic App Designer i [Azure-portalen,](https://portal.azure.com)om den inte redan är öppen.

1. Skriv "Dynamics 365" som filter i sökrutan. I det här exemplet väljer du den här utlösaren under listan utlösare: **När en post skapas**

   ![Välj utlösare](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Om du uppmanas att logga in på Dynamics 365 loggar du in nu.

1. Ange följande utlösande information:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Organisationens namn** | Ja | Namnet på organisationens Dynamics 365-instans för att övervaka, till exempel "Contoso" |
   | **Namn på entitet** | Ja | Namnet på entiteten som ska övervakas, till exempel "Leads" | 
   | **Frekvens** | Ja | Den tidsenhet som ska användas med intervaller när du söker efter uppdateringar relaterade till utlösaren |
   | **Intervall** | Ja | Antalet sekunder, minuter, timmar, dagar, veckor eller månader som går före nästa kontroll |
   ||| 

   ![Information om utlösare](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Lägg till Dynamics 365-åtgärd

Lägg nu till dynamics 365-åtgärden som skapar en aktivitetspost för den nya leadposten.

1. Under utlösaren väljer du **Nytt steg**.

1. Skriv "Dynamics 365" som filter i sökrutan. Välj den här åtgärden i åtgärdslistan: **Skapa en ny post**

   ![Välj åtgärd](./media/connectors-create-api-crmonline/select-action.png)

1. Ange följande åtgärdsinformation:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Organisationens namn** | Ja | Dynamics 365-instansen där du vill skapa posten, som inte behöver vara samma förekomst i utlösaren, men som är "Contoso" i det här exemplet |
   | **Namn på entitet** | Ja | Entiteten där du vill skapa posten, till exempel "Aktiviteter" |
   | | |

   ![Information om åtgärder](./media/connectors-create-api-crmonline/action-details.png)

1. När rutan **Ämne** visas i åtgärden klickar du i rutan **Ämne** så att listan med dynamiskt innehåll visas. I den här listan väljer du de fältvärden som ska inkluderas i uppgiftsposten som är associerad med den nya leadposten:

   | Field | Beskrivning |
   |-------|-------------|
   | **Efternamn** | Efternamnet från leadet som den primära kontakten i posten |
   | **Hjälpavsnitt** | Det beskrivande namnet på leadet i posten |
   | | |

   ![Information om uppgiftspost](./media/connectors-create-api-crmonline/create-record-details.png)

1. Välj **Spara** för logikappen i designerverktygsfältet. 

1. Om du vill starta logikappen manuellt väljer du **Kör**i designerverktygsfältet .

   ![Kör logikappen](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Skapa nu en leadpost i Dynamics 365 så att du kan utlösa logikappens arbetsflöde.

## <a name="add-filter-or-query"></a>Lägga till filter eller fråga

Om du vill ange hur data ska filtreras i en Dynamics 365-åtgärd väljer du **Visa avancerade alternativ** i den åtgärden. Du kan sedan lägga till ett filter eller en ordning efter fråga.
Du kan till exempel använda en filterfråga för att bara hämta de aktiva kontona och beställa dessa poster efter kontonamn. Gör så här för den här uppgiften:

1. Under **Filterfråga**anger du den här OData-filterfrågan:`statuscode eq 1`

2. Välj **Kontonamn**under **Order By**när listan med dynamiskt innehåll visas . 

   ![Ange filter och ordning](./media/connectors-create-api-crmonline/advanced-options.png)

Mer information finns i följande frågealternativ för Dynamics 365 Customer Engagement Web API-system:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Metodtips för avancerade alternativ

När du anger ett värde för ett fält i en åtgärd eller utlösare måste värdets datatyp matcha fälttypen oavsett om du manuellt anger värdet eller väljer värdet i listan med dynamiskt innehåll.

I den här tabellen beskrivs några av fälttyperna och de datatyper som krävs för deras värden.

| Fälttyp | Nödvändig datatyp | Beskrivning | 
|------------|--------------------|-------------|
| Textfält | Enskild rad med text | Dessa fält kräver en enda textrad eller dynamiskt innehåll som har texttypen. <p><p>*Exempelfält*: **Beskrivning** och **kategori** | 
| Heltalsfält | Heltal | Vissa fält kräver heltal eller dynamiskt innehåll som har heltalstypen. <p><p>*Exempelfält*: **Procent färdigt** och **varaktighet** | 
| Datumfält | Datum och tid | Vissa fält kräver ett datum med mm/dd/yyyy-format eller dynamiskt innehåll som har datumtypen. <p><p>*Exempelfält*: **Skapad på**, **Startdatum,** **Verklig start**, **Verkligt slut**och **Förfallodatum** | 
| Fält som kräver både post-ID och uppslagstyp | Primärnyckel | Vissa fält som refererar till en annan entitetspost kräver både ett post-ID och en uppslagstyp. | 
||||

Här expanderas på dessa fälttyper och här finns exempelfält i Dynamics 365-utlösare och åtgärder som kräver både ett post-ID och uppslagstypen. Det här kravet innebär att värden som du väljer från den dynamiska listan inte fungerar.

| Field | Beskrivning |
|-------|-------------|
| **Ägare** | Måste vara antingen ett giltigt användar-ID eller teampost-ID. |
| **Ägartyp** | Måste vara `systemusers` `teams`antingen eller . |
| **Angående** | Måste vara ett giltigt post-ID, till exempel ett konto-ID eller ett kontaktpost-ID. |
| **När det gäller typ** | Måste vara en uppslagstyp, till exempel `accounts` eller `contacts`. |
| **Kund** | Måste vara ett giltigt post-ID, till exempel ett konto-ID eller ett kontaktpost-ID. |
| **Kundtyp** | Måste vara uppslagstypen, till exempel `accounts` eller `contacts`. |
|||

I det här exemplet skapar åtgärden **Skapa en ny post** en ny uppgiftspost:

![Skapa uppgiftspost med post-ID och uppslagstyper](./media/connectors-create-api-crmonline/create-record-advanced.png)

Den här åtgärden tilldelar uppgiftsposten till ett visst användar-ID eller grupppost-ID, baserat på post-ID:t i fältet **Ägare** och uppslagstypen i fältet **Ägartyp:**

![Registrerings-ID och uppslagstyp för ägare](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Den här åtgärden lägger också till en kontopost som är associerad med post-ID som lagts till i fältet **Gällande** och uppslagstypen i fältet **Ämnestyp:**

![När det gäller post-ID och uppslagstyp](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Hitta post-ID

Så här hittar du ett post-ID:

1. Öppna en post i Dynamics 365, till exempel en kontopost.

2. Välj något av följande steg i verktygsfältet Åtgärder:

   * Välj **Pop Out**. ![popout-skiva](./media/connectors-create-api-crmonline/popout-record.png) 
   * Välj **E-post En länk** så att du kan kopiera den fullständiga webbadressen till ditt standardprogram för e-post.

   Post-ID:et visas `%7b` i `%7d` URL:en mellan tecknen och kodningstecknet:

   ![Hitta post-ID](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Felsöka misslyckade körningar

Om du vill hitta och granska misslyckade steg i logikappen kan du visa logikappens körningarhistorik, status, indata, utdata och så vidare.

1. Välj **Översikt**på logikappens huvudmeny i Azure-portalen . I avsnittet **Kör historik,** som visar alla körstatusar för logikappen, väljer du en misslyckad körning för mer information.

   ![Status för körning av logikapp](./media/connectors-create-api-crmonline/run-history.png)

1. Expandera ett misslyckat steg så att du kan visa mer information.

   ![Expandera misslyckades steg](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Granska stegets information, till exempel indata och utdata, som kan hjälpa dig att hitta orsaken bakom felet.

   ![Misslyckade steg - ingångar och utdata](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Mer information om felsökning av logikappar finns i [Diagnostisera fel på logikappar](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen i kopplingens OpenAPI-fil (tidigare Swagger) finns på [kopplingens referenssida](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
