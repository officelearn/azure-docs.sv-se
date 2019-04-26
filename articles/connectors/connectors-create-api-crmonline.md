---
title: Anslut till Dynamics 365 – Azure Logic Apps
description: 'Skapa och hantera poster med Dynamics 365 (online) REST API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: b81efba0ce860bea5fd68dd99ce52980e6816b7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313808"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Hantera Dynamics 365-poster med Azure Logic Apps

Med Azure Logic Apps och Dynamics 365-kopplingen kan skapa du automatiserade uppgifter och arbetsflöden baserat på posterna i Dynamics 365. Dina arbetsflöden kan skapa poster, uppdatera artiklar, returnerar poster med mera i ditt Dynamics 365-konto. Du kan inkludera åtgärder i dina logikappar som får svar från Dynamics 365 och göra utdata som är tillgänglig för andra åtgärder. När ett objekt uppdateras i Dynamics 365, kan du skicka ett e-postmeddelande med hjälp av Office 365.

Den här artikeln visar hur du kan skapa en logikapp som skapar en uppgift i Dynamics 365 när en ny lead-posten skapas i Dynamics 365.
Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* En [Dynamics 365-konto](https://dynamics.microsoft.com)

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Dynamics 365-konto. Om du vill börja din logikapp med en Dynamics 365-utlösare, som du behöver en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Add Dynamics 365 trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Lägg först till en Dynamics 365-utlösare som utlöses när en ny lead-posten visas i Dynamics 365.

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen tom i Logic App Designer, om inte redan är öppna.

1. Ange ”Dynamics 365” i sökrutan som filter. Välj den här utlösaren i det här exemplet under listan över utlösare: **När en post skapas**

   ![Välj utlösare](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Om du uppmanas att logga in på Dynamics 365, logga in nu.

1. Ange den här utlösarinformationen:

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Organisationens namn** | Ja | Namn för din organisations Dynamics 365-instansen ska övervakas, till exempel ”Contoso” |
   | **Entitetsnamn** | Ja | Namnet för entitet som ska övervakas, till exempel Leads ”” | 
   | **Frekvens** | Ja | Tidsenheten som ska användas med intervall vid sökning efter uppdateringar som rör utlösaren |
   | **Intervall** | Ja | Antal sekunder, minuter, timmar, dagar, veckor eller månader som ska gå innan nästa kontroll |
   ||| 

   ![Utlösarinformation](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Lägg till Dynamics 365-åtgärd

Lägg nu till Dynamics 365-åtgärd som skapar en uppgiftspost för den nya leadposten.

1. Under utlösaren och väljer **nytt steg**.

1. Ange ”Dynamics 365” i sökrutan som filter. Välj den här åtgärden från åtgärdslistan över: **Skapa en ny post**

   ![Välj åtgärd](./media/connectors-create-api-crmonline/select-action.png)

1. Ange den här informationen för åtgärden:

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Organisationens namn** | Ja | Dynamics 365-instans där du vill skapa den post som inte behöver vara samma instans i utlösaren, är men ”Contoso” i det här exemplet |
   | **Entitetsnamn** | Ja | Entiteten där du vill skapa posten, till exempel ”aktiviteter” |
   | | |

   ![Åtgärdsinformation](./media/connectors-create-api-crmonline/action-details.png)

1. När den **ämne** visas i åtgärden, klicka i den **ämne** rutan så visas listan med dynamiskt innehåll. Välj fältvärdena att inkludera i uppgiftsposten som är associerade med den nya leadposten från den här listan:

   | Fält | Beskrivning |
   |-------|-------------|
   | **Efternamn** | Efternamnet från lead som den primära kontakten i posten |
   | **Ämne** | Ett beskrivande namn för ditt lead i posten |
   | | |

   ![Information om posten](./media/connectors-create-api-crmonline/create-record-details.png)

1. I verktygsfältet för appdesignern väljer **spara** för din logikapp. 

1. För att manuellt starta logikappen, designerverktygsfältet, Välj **kör**.

   ![Kör logikappen](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Skapa nu en lead-post i Dynamics 365 så att du kan utlösa logikappens arbetsflöde.

## <a name="add-filter-or-query"></a>Lägg till filter och frågor

Om du vill ange hur du kan filtrera data i en Dynamics 365-åtgärd väljer **visa avancerade alternativ** i åtgärden. Du kan sedan lägga till ett filter eller sortera per fråga.
Du kan till exempel använda en filterfråga för att hämta endast aktiva konton och ordna de posterna efter kontonamn. Följ dessa steg för den här uppgiften:

1. Under **filterfråga**, ange den här OData-filterfråga: `statuscode eq 1`

2. Under **Order By**, när den dynamiska innehållslistan visas, Välj **kontonamn**. 

   ![Ange filter och sortering](./media/connectors-create-api-crmonline/advanced-options.png)

Mer information finns i dessa frågealternativ för webb-API för Dynamics 365 Customer Engagement system:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Metodtips för avancerade alternativ

När du anger ett värde för ett fält i en åtgärd eller utlösare måste värdetyp data matcha fälttypen oavsett om du manuellt ange värdet eller väljer du värdet från den dynamiska innehållslistan.

Den här tabellen beskriver några av fälttyper och vilka data som krävs för deras värden.

| Fälttyp | Krävs för datatyp | Beskrivning | 
|------------|--------------------|-------------|
| Textfält | Enskild rad med text | Dessa fält kräver en rad med text eller dynamiskt innehåll som har texttypen. <p><p>*Exempel fält*: **Beskrivning av** och **kategori** | 
| Heltalsfält | Heltal | Vissa fält kräver heltal eller dynamiskt innehåll som har typen heltal. <p><p>*Exempel fält*: **Procent färdigt** och **varaktighet** | 
| Datumfält | Datum och tid | Vissa fält kräver ett datum med mm/dd/åååå eller dynamiskt innehåll som är av typen date. <p><p>*Exempel fält*: **Skapad**, **startdatum**, **verklig Start**, **Verkligt slut**, och **förfallodatum** | 
| Ange fält som kräver både ett post-ID och en uppslagstyp | Primär nyckel | Vissa fält som refererar till en annan entitetspost kräver både ett post-ID och en uppslagstyp. | 
||||

Som ett led i dessa fälttyper, är här exempel fält i Dynamics 365-utlösare och åtgärder som kräver både ett post-ID och uppslagstyp. Det här kravet innebär värden som du väljer från listan med dynamiskt inte fungerar.

| Fält | Beskrivning |
|-------|-------------|
| **Ägare** | Måste vara antingen ett giltigt användar-ID eller team post-ID. |
| **Ägartyp** | Måste vara antingen **systemanvändare** eller **team**. |
| **Angående** | Måste vara ett giltigt post-ID, till exempel en konto-ID eller kontaktpost-ID. |
| **Angående typ** | Måste vara en uppslagstyp som **konton** eller **kontakter**. |
| **Kunden** | Måste vara ett giltigt post-ID, till exempel en konto-ID eller kontaktpost-ID. |
| **Kundtyp** | Måste vara Uppslagstypen, till exempel **konton** eller **kontakter**. |
|||

I det här exemplet åtgärden med namnet **skapa en ny post** skapar en ny uppgiftspost:

![Skapa uppgiftsposten med ID: N och lookup-typer](./media/connectors-create-api-crmonline/create-record-advanced.png)

Uppgiftsposten tilldelas till en specifik användar-ID eller team post-ID, baserat på post-ID i den **ägare** fält och sökfunktionens skriver i den **ägartyp** fält:

![Ägare och en uppslagstyp posttyp](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Den här åtgärden lägger också till en kontopost som är associerat med ID: T har lagts till i den **angående** fält och sökfunktionens skriver i den **angående typ** fält:

![Angående och en uppslagstyp posttyp](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Hitta post-ID

Följ dessa steg för att hitta en post-ID:

1. Öppna en post, till exempel en kontopost i Dynamics 365.

2. I verktygsfältet åtgärder väljer du något av följande:

   * Välj **frigörs**. ![popout-post](./media/connectors-create-api-crmonline/popout-record.png) 
   * Välj **e-POSTMEDDELANDET en länk** så att du kan kopiera den fullständiga URL: en till ditt e-postprogram.

   Post-ID som visas i URL-Adressen mellan de `%7b` och `%7d` kodningstecken:

   ![Hitta post-ID](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Felsöka misslyckade körningar

Om du vill söka efter och granska misslyckade åtgärder i din logikapp, kan du visa logikappens körnings, status, indata, utdata och så vidare.

1. Azure-portalen på huvudmenyn för din logikapp, Välj **översikt**. I den **Körningshistorik** avsnitt som visar alla kör status för din logikapp, Välj en misslyckad körning för mer information.

   ![Status för logikappskörningen](./media/connectors-create-api-crmonline/run-history.png)

1. Expandera ett steg som misslyckades så att du kan visa mer information.

   ![Expandera felande steget](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Granska det steget information, till exempel indata och utdata som kan hjälpa dig att hitta orsaken till felet.

   ![Misslyckade steg - in- och utdata](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Läs mer om hur du felsöker logikappar [diagnosticera logikappfel](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) fil, finns i den [anslutningsappens-referenssida](/connectors/dynamicscrmonline/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
