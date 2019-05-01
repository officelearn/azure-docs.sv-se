---
title: Ansluta till Azure Event Hubs – Azure Logic Apps
description: Hantera och övervaka händelser med Azure Event Hubs och Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 882bae14678d8bfff15b35c63c666a20aeee3d1d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720054"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Övervaka, ta emot och skicka händelser med Azure Event Hubs och Azure Logic Apps

Den här artikeln visar hur du kan övervaka och hantera händelser som skickas till [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) från inuti en logikapp med Azure Event Hubs-anslutningsprogrammet. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att kontrollera, skicka och ta emot händelser från Event Hub. Specifika teknisk information finns i den [referens för Azure Event Hubs-anslutningsapp](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* En [Event Hubs-namnområde och Händelsehubb](../event-hubs/event-hubs-create.md)

* Logikappen där du vill få åtkomst till din Event Hub. Du behöver för att starta din logikapp med en utlösare för Azure Event Hubs, en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Kontrollera behörigheter och hämta anslutningssträngen

Kontrollera din behörighet för din logikapp att få åtkomst till din Event Hub och hämta anslutningssträngen för Event Hubs-namnområdet.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till Händelsehubbar *namnområde*, inte en specifik Händelsehubb. 

1. På menyn i namnområdet under **inställningar**väljer **principer för delad åtkomst**. Under **anspråk**, kontrollera att du har **hantera** behörigheter för det namnområdet.

   ![Hantera behörigheter för namnområdet för Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Om du vill ange information om din senare manuellt kan du hämta anslutningssträngen för Event Hubs-namnområdet.

   1. Under **princip**, Välj **RootManageSharedAccessKey**.

   1. Hitta anslutningssträngen för den primära nyckeln. Välj kopieringsknappen och spara anslutningssträngen för senare användning.

      ![Kopiera anslutningssträngen för Event Hubs-namnområde](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > För att bekräfta om din anslutningssträng är associerat med Event Hubs-namnområdet eller med en specifik händelsehubb, kontrollera att anslutningssträngen inte har den `EntityPath`  parametern. Om du hittar den här parametern anslutningssträngen för en specifik Händelsehubb ”enhet” och är inte rätt strängen som ska användas med din logikapp.

1. Nu fortsätter du med [Lägg till ett Event Hubs-utlösare](#add-trigger) eller [Lägg till en åtgärd för Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Lägg till Event Hubs-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

Det här exemplet visar hur du kan starta en logikapparbetsflöde när nya händelser skickas till din Event Hub. 

1. I Azure portal eller Visual Studio, skapar du en tom logikapp som öppnas Logic Apps Designer. Det här exemplet används Azure-portalen.

1. I sökrutan anger du ”händelsehubbar” som filter. Välj den här utlösaren från listan över utlösare: **När händelser är tillgängliga i Event Hub - Event Hubs**

   ![Välj utlösare](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Om du uppmanas anslutningsinformation [skapa Händelsehubbar anslutningen nu](#create-connection). 

1. Ange information om den Händelsehubb som du vill övervaka i utlösaren. Fler egenskaper, öppna den **Lägg till ny parameter** lista. Att välja en parameter lägger till egenskapen utlösarkortet.

   ![Egenskaper för utlösare](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn på Händelsehubb** | Ja | Namnet på den Händelsehubb som du vill övervaka |
   | **Innehållstyp** | Nej | Händelsens innehållstyp. Standardvärdet är `application/octet-stream`. |
   | **Namn på konsumentgrupp** | Nej | Den [för Händelsehubbens konsumentgrupp](../event-hubs/event-hubs-features.md#consumer-groups) du använder för att läsa händelser. Om den inte anges används standard-konsumentgrupp. |
   | **Händelser som maximalt antal** | Nej | Det maximala antalet händelser. Utlösaren returnerar mellan en och antalet händelser som anges av den här egenskapen. |
   | **Intervall** | Ja | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på åtkomstfrekvensen |
   | **Frekvens** | Ja | Tidsenhet för upprepningen |
   ||||

   **Ytterligare egenskaper**

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Innehåll schema** | Nej | JSON content-schemat att läsa från Event Hub-händelser. Om du anger innehåll schemat, kan du utlösa logikappen för de händelser som matchar schemat. |
   | **Minsta partitionsnyckel** | Nej | Ange lägsta [partition](../event-hubs/event-hubs-features.md#partitions) ID att läsa. Som standard läses alla partitioner. |
   | **Maximal partitionsnyckel** | Nej | Ange högsta [partition](../event-hubs/event-hubs-features.md#partitions) ID att läsa. Som standard läses alla partitioner. |
   | **Tidszon** | Nej | Gäller bara när du anger en starttid eftersom den här utlösaren inte acceptera UTC-förskjutning. Välj den tidszon som du vill använda. <p>Mer information finns i [skapa och kör återkommande uppgifter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Starttid** | Nej | Ange en starttid i följande format: <p>ÅÅÅÅ-MM-ddTHH om du väljer en tidszon<p>ELLER<p>ÅÅÅÅ-MM-: ssZ om du inte väljer en tidszon<p>Mer information finns i [skapa och kör återkommande uppgifter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. När du är klar på verktygsfältet för appdesignern väljer **spara**.

1. Nu ska du fortsätta att lägga till en eller flera åtgärder i din logikapp för uppgifter som du vill utföra med utlösare resultaten. 

   Till exempel för att filtrera händelser baserat på ett specifikt värde, till exempel en kategori, du kan lägga till ett villkor så att den **skicka händelse – Event Hubs** sätt skickas endast de händelser som uppfyller villkoret. 

> [!NOTE]
> Alla Event Hub-utlösare är *-longpolling* utlösare, vilket innebär att när en utlösare utlöses utlösaren bearbetar alla händelser och väntar sedan på 30 sekunder för fler händelser ska visas i din Händelsehubb.
> Om inga händelser tas emot i 30 sekunder, hoppas utlösarkörning. I annat fall fortsätter utlösaren läsningen tills din Event Hub är tom.
> Nästa utlösaren omröstningen sker baserat på upprepningsintervallet som du anger i utlösarens egenskaper.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Lägg till Event Hubs-åtgärd

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar logic app med ett Event Hubs-utlösare som söker efter nya händelser i din Event Hub.

1. Öppna logikappen i Logic Apps Designer i Azure portal eller Visual Studio. Det här exemplet används Azure-portalen.

1. Under utlösaren eller åtgärden, väljer **nytt steg**.

   Flytta musen över den anslutande pilen för att lägga till en åtgärd mellan befintliga steg. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du ”händelsehubbar” som filter.
Välj den här åtgärden från åtgärdslistan över: **Skicka händelse – Event Hubs**

   ![Välj åtgärden ”Skicka händelse”](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Om du uppmanas anslutningsinformation [skapa Händelsehubbar anslutningen nu](#create-connection). 

1. I åtgärden, anger du information om de händelser som du vill skicka. Fler egenskaper, öppna den **Lägg till ny parameter** lista. Markerar en parameter läggs egenskapen till åtgärd-kort.

   ![Välj Event Hub-namn och ange händelse-innehåll](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn på Händelsehubb** | Ja | Event Hub där du vill skicka händelsen |
   | **Innehåll** | Nej | Innehåll för händelsen som du vill skicka |
   | **Egenskaper** | Nej | Egenskaper och värden för att skicka |
   | **Partitionsnyckel** | Nej | Den [partition](../event-hubs/event-hubs-features.md#partitions) ID att skicka händelsen |
   ||||

   Du kan exempelvis skicka utdata från Event Hubs-utlösare till en annan Händelsehubb:

   ![Skicka händelse-exempel](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. När du är klar på verktygsfältet för appdesignern väljer **spara**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ansluta till din Event Hub

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. När du uppmanas att ange anslutningsinformationen Tillhandahåll följande information:

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutningsnamn** | Ja | <*connection-name*> | Namn för att skapa för anslutningen |
   | **Event Hubs Namespace** | Ja | <*event-hubs-namespace*> | Välj Event Hubs-namnområde som du vill använda. |
   |||||  

   Exempel:

   ![Skapa anslutning till Händelsehubb](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Om du vill ange anslutningssträngen manuellt välja **ange anslutningsinformationen manuellt**. 
   Lär dig [hitta anslutningssträngen](#permissions-connection-string).

2. Välj Event Hubs-princip om du vill använda, om inte redan är valt. Välj **Skapa**.

   ![Skapa anslutning till Händelsehubb, del 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. När du skapar anslutningen, fortsätter du med [Lägg till Event Hubs utlösa](#add-trigger) eller [Lägg till Event Hubs åtgärd](#add-action).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) fil, finns i den [anslutningsappens-referenssida](/connectors/eventhubs/).

## <a name="next-steps"></a>Nästa steg

Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)