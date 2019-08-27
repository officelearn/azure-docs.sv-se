---
title: Ansluta till Dynamics 365 – Azure Logic Apps
description: 'Skapa och hantera poster med Dynamics 365-REST-API: er (online) och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
manager: carmonm
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: ce83e6b1847a8f08467cb7877e517bdaace27953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051023"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Hantera Dynamics 365-poster med Azure Logic Apps

Med Azure Logic Apps och Dynamics 365-anslutningsprogrammet kan du skapa automatiserade uppgifter och arbetsflöden baserat på dina poster i Dynamics 365. Dina arbetsflöden kan skapa poster, uppdatera objekt, returnera poster med mera i ditt Dynamics 365-konto. Du kan inkludera åtgärder i dina Logi Kap par som får svar från Dynamics 365 och göra utdata tillgängliga för andra åtgärder. När ett objekt till exempel uppdateras i Dynamics 365 kan du skicka ett e-postmeddelande med Office 365.

Den här artikeln visar hur du kan skapa en Logic app som skapar en uppgift i Dynamics 365 när en ny leadpost skapas i Dynamics 365.
Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [Dynamics 365-konto](https://dynamics.microsoft.com)

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt ditt Dynamics 365-konto. Om du vill starta din Logic-app med en Dynamics 365-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Lägg till Dynamics 365-utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Lägg först till en Dynamics 365-utlösare som utlöses när en ny lead-post visas i Dynamics 365.

1. Öppna din tomma Logic-app i Logic App Designer, om den inte redan är öppen, i [Azure Portal](https://portal.azure.com).

1. I rutan Sök anger du "Dynamics 365" som filter. I det här exemplet går du till listan utlösare och väljer den här utlösaren: **När en post skapas**

   ![Välj utlösare](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Logga in nu om du uppmanas att logga in på Dynamics 365.

1. Ange följande information om utlösaren:

   | Egenskap | Obligatorisk | Beskrivning |
   |----------|----------|-------------|
   | **Organisations namn** | Ja | Namnet på organisationens Dynamics 365-instans som ska övervakas, till exempel "contoso" |
   | **Entitetsnamn** | Ja | Namnet på entiteten som ska övervakas, till exempel "leads" | 
   | **Frekvens** | Ja | Den tidsenhet som ska användas med intervall vid sökning efter uppdateringar som rör utlösaren |
   | **Intervall** | Ja | Antalet sekunder, minuter, timmar, dagar, veckor eller månader som ska passera innan nästa kontroll |
   ||| 

   ![Information om utlösare](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Lägg till Dynamics 365-åtgärd

Lägg nu till den Dynamics 365-åtgärd som skapar en uppgifts post för den nya lead-posten.

1. Under utlösaren väljer du **nytt steg**.

1. I rutan Sök anger du "Dynamics 365" som filter. I listan åtgärder väljer du den här åtgärden: **Skapa en ny post**

   ![Välj åtgärd](./media/connectors-create-api-crmonline/select-action.png)

1. Ange följande åtgärds information:

   | Egenskap | Obligatorisk | Beskrivning |
   |----------|----------|-------------|
   | **Organisations namn** | Ja | Den Dynamics 365-instans där du vill skapa posten, som inte behöver vara samma instans i utlösaren, men är "contoso" i det här exemplet |
   | **Entitetsnamn** | Ja | Entiteten där du vill skapa posten, till exempel "aktiviteter" |
   | | |

   ![Åtgärdsinformation](./media/connectors-create-api-crmonline/action-details.png)

1. När rutan **ämne** visas i din åtgärd klickar du i rutan **ämne** så visas listan med dynamiskt innehåll. I den här listan väljer du de fält värden som ska ingå i uppgifts posten som är kopplad till den nya lead-posten:

   | Fält | Beskrivning |
   |-------|-------------|
   | **Efter namn** | Efter namnet från leadet som den primära kontakten i posten |
   | **Avsnittet** | Beskrivande namn för leadet i posten |
   | | |

   ![Information om uppgifts post](./media/connectors-create-api-crmonline/create-record-details.png)

1. I verktygsfältet designer väljer du **Spara** för din Logic Apps. 

1. Starta Logic-appen manuellt genom att klicka på **Kör**i verktygsfältet i designern.

   ![Kör logikapp](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Skapa nu en leadpost i Dynamics 365 så att du kan utlösa din Logic app-arbetsflöde.

## <a name="add-filter-or-query"></a>Lägg till filter eller fråga

Om du vill ange hur data ska filtreras i en Dynamics 365-åtgärd väljer du **Visa avancerade alternativ** i den åtgärden. Du kan sedan lägga till ett filter eller en order by-fråga.
Du kan till exempel använda en filter fråga för att bara hämta aktiva konton och sortera posterna efter konto namn. För den här uppgiften följer du de här stegen:

1. Under **filter fråga**anger du den här OData-filter frågan:`statuscode eq 1`

2. Välj **konto namn**under **Sortera efter**när listan med dynamiskt innehåll visas. 

   ![Ange filter och ordning](./media/connectors-create-api-crmonline/advanced-options.png)

Mer information finns i dessa frågealternativ för Dynamics 365 Customer Engagement Web API system:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Metod tips för avancerade alternativ

När du anger ett värde för ett fält i en åtgärd eller utlösare måste värdens datatyp Matcha fält typen om du anger värdet manuellt eller väljer värdet från listan med dynamiskt innehåll.

I den här tabellen beskrivs några av fält typerna och de data typer som krävs för deras värden.

| Fälttyp | Datatyp som krävs | Beskrivning | 
|------------|--------------------|-------------|
| Textfält | Enskild rad med text | De här fälten kräver en enskild rad med text eller dynamiskt innehåll som har text typen. <p><p>*Exempel fält*: **Beskrivning** och **kategori** | 
| Heltals fält | Heltal | Vissa fält kräver heltal eller dynamiskt innehåll som har heltals typ. <p><p>*Exempel fält*: **Procent klart** och **varaktighet** | 
| Datum fält | Datum och tid | Vissa fält kräver ett datum med formatet mm/dd/åååå eller dynamiskt innehåll som har datum typen. <p><p>*Exempel fält*: **Skapad på**, **start datum**, **faktisk start**, **faktisk slut**och **förfallo datum** | 
| Fält som kräver både ett post-ID och en uppslags typ | Primär nyckel | Vissa fält som refererar till en annan enhets post kräver både ett post-ID och en uppslags typ. | 
||||

Om du expanderar dessa fält typer är exempel fält i Dynamics 365-utlösare och åtgärder som kräver både ett post-ID och en uppslags typ. Detta krav innebär att värden som du väljer från den dynamiska listan inte fungerar.

| Fält | Beskrivning |
|-------|-------------|
| **Ägare** | Måste vara antingen ett giltigt användar-ID eller en team post-ID. |
| **Ägar typ** | Måste vara antingen `systemusers` eller `teams`. |
| **Rör** | Måste vara ett giltigt post-ID, till exempel ett konto-ID eller ett kontakt post-ID. |
| **Angående typ** | Måste vara en uppslags typ, `accounts` till `contacts`exempel eller. |
| **Kunden** | Måste vara ett giltigt post-ID, till exempel ett konto-ID eller ett kontakt post-ID. |
| **Typ av kund** | Måste vara en uppslags typ, `accounts` till `contacts`exempel eller. |
|||

I det här exemplet skapar åtgärden som heter **skapa en ny post** en ny aktivitets post:

![Skapa en aktivitets post med post-ID: n och Sök typer](./media/connectors-create-api-crmonline/create-record-advanced.png)

Den här åtgärden tilldelar uppgifts-ID: t till ett särskilt användar-ID eller en team post-ID, baserat på post-ID i fältet **ägare** och söknings typen i fältet **ägar typ** :

![Ägar post-ID och Sök typ](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Den här åtgärden lägger också till en konto post som är associerad med post-ID: t som har lagts till i fältet **ämne** och söknings typen i fältet **ämnes typ** :

![Angående post-ID och Sök typ](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Sök efter post-ID

Följ dessa steg om du vill hitta ett post-ID:

1. Öppna en post i Dynamics 365, till exempel en konto post.

2. I verktygsfältet Åtgärder väljer du något av följande steg:

   * Välj **plocka ut**. ![popout-post](./media/connectors-create-api-crmonline/popout-record.png) 
   * Välj **e-posta en länk** så att du kan kopiera den fullständiga URL: en till ditt standard-e-postprogram.

   Post-ID: t visas i URL: `%7b` en `%7d` mellan kodnings tecknen och:

   ![Sök efter post-ID](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Felsöka misslyckade körningar

Om du vill söka efter och granska misslyckade steg i din Logic app kan du Visa din Logic Apps körnings historik, status, indata, utdata och så vidare.

1. I Azure Portal på din Logic Apps huvud meny väljer du **Översikt**. I avsnittet **körnings historik** som visar alla körnings statuser för din Logic app väljer du en misslyckad körning för mer information.

   ![Körnings status för Logic app](./media/connectors-create-api-crmonline/run-history.png)

1. Expandera ett misslyckat steg så att du kan visa mer information.

   ![Steget expandera misslyckades](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Granska informationen i steget, till exempel indata och utdata, som kan hjälpa dig att hitta orsaken till felet.

   ![Misslyckade steg-indata och utdata](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Mer information om hur du felsöker Logic Apps finns i [diagnostisera fel i Logic app](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs i filens OpenAPI-fil (tidigare Swagger), finns på [kopplingens referens sida](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
