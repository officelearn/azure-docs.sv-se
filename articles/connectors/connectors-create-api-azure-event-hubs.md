---
title: Ansluta till Azure Event Hubs – Azure Logic Apps | Microsoft Docs
description: Hantera och övervaka händelser med Azure Event Hubs och Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: a91daf08a56470e4d1e112e37b51150c2c5f00ef
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230326"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Övervaka, ta emot och skicka händelser med Azure Event Hubs och Azure Logic Apps 

Den här artikeln visar hur du kan övervaka och hantera händelser som skickas till [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) från inuti en logikapp med Azure Event Hubs-anslutningsprogrammet. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att kontrollera, skicka och ta emot händelser från Event Hub. 

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Specifika teknisk information finns i den <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">referens för Azure Event Hubs-anslutningsapp</a>.

## <a name="prerequisites"></a>Förutsättningar

* En [Event Hubs-namnområde och Händelsehubb](../event-hubs/event-hubs-create.md)

* Logikappen där du vill få åtkomst till din Event Hub. Du behöver för att starta din logikapp med en utlösare för Azure Event Hubs, en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Kontrollera behörigheter och hämta anslutningssträngen

Kontrollera din behörighet för din logikapp att få åtkomst till din Event Hub och hämta anslutningssträngen för Event Hubs-namnområdet.

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. 

2. Gå till Händelsehubbar *namnområde*, inte en specifik Händelsehubb. På sidan namnområde under **inställningar**, Välj **principer för delad åtkomst**. Under **anspråk**, kontrollera att du har **hantera** behörigheter för det namnområdet.

   ![Hantera behörigheter för namnområdet för Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Om du vill ange information om din senare manuellt kan du hämta anslutningssträngen för Event Hubs-namnområdet. 

   1. Under **princip**, Välj **RootManageSharedAccessKey**. 

   2. Hitta anslutningssträngen för den primära nyckeln. Välj kopieringsknappen och spara anslutningssträngen för senare användning.

      ![Kopiera anslutningssträngen för Event Hubs-namnområde](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > För att bekräfta om din anslutningssträng är associerat med Event Hubs-namnområdet eller med en specifik händelsehubb, kontrollera att anslutningssträngen inte har den `EntityPath`  parametern. Om du hittar den här parametern anslutningssträngen för en specifik Händelsehubb ”enhet” och är inte rätt strängen som ska användas med din logikapp.

4. Nu fortsätter du med [Lägg till ett Event Hubs-utlösare](#add-trigger) eller [Lägg till en åtgärd för Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Lägg till ett Event Hubs-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

Det här exemplet visar hur du kan starta en logikapparbetsflöde när nya händelser skickas till din Event Hub. 

1. I Azure portal eller Visual Studio, skapar du en tom logikapp som öppnas Logic Apps Designer. Det här exemplet används Azure-portalen.

2. I sökrutan anger du ”händelsehubbar” som filter. Välj utlösaren som du vill använda från listan över utlösare. 

   Det här exemplet används den här utlösaren: 
   **Event Hubs – när händelser är tillgängliga i Event Hub**

   ![Välj utlösare](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Om du uppmanas anslutningsinformation [skapa Händelsehubbar anslutningen nu](#create-connection). Eller om anslutningen redan finns, ange nödvändig information för utlösaren.

   1. Från den **händelsehubbnamn** väljer du den Händelsehubb som du vill övervaka.

      ![Ange Event Hub eller konsumentgrupp](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera Event Hub.
 
   3. Välj för att du kan också välja några av de avancerade utlösarvillkor **visa avancerade alternativ**.
   
      ![Aktivera avancerade alternativ](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Egenskap  | Information | 
      |----------|---------| 
      | Innehållstyp  | Välj händelsens innehållstyp. Standardvärdet är ”application/octet-ström”. |
      | Innehåll schema | Ange innehåll schemat i JSON för de händelser som läses från Händelsehubben. |
      | Konsumentgruppens namn | Ange Event Hub [konsument gruppnamn](../event-hubs/event-hubs-features.md#consumer-groups) för att läsa händelser. Om den inte anges används standard-konsumentgrupp. |
      | Minsta partitionsnyckel | Ange lägsta [partition](../event-hubs/event-hubs-features.md#partitions) ID att läsa. Som standard läses alla partitioner. |
      | Maximal partitionsnyckel | Ange högsta [partition](../event-hubs/event-hubs-features.md#partitions) ID att läsa. Som standard läses alla partitioner. |
      | Händelser som maximalt antal | Ange ett värde för det maximala antalet händelser. Utlösaren returnerar mellan en och antalet händelser som anges av den här egenskapen. |
      |||

4. När du är klar på verktygsfältet för appdesignern väljer **spara**.

5. Nu ska du fortsätta att lägga till en eller flera åtgärder i din logikapp för uppgifter som du vill utföra med utlösare resultaten.

> [!NOTE]
> Alla Event Hub-utlösare är *-longpolling* utlösare, vilket innebär att när en utlösare utlöses utlösaren bearbetar alla händelser och väntar sedan på 30 sekunder för fler händelser ska visas i din Händelsehubb.
> Om inga händelser tas emot i 30 sekunder, hoppas utlösarkörning. I annat fall fortsätter utlösaren läsningen tills din Event Hub är tom.
> Nästa utlösaren omröstningen sker baserat på upprepningsintervallet som du anger i utlösarens egenskaper.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Lägg till en åtgärd för Event Hubs

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar logic app med ett Event Hubs-utlösare som söker efter nya händelser i din Event Hub. 

1. Öppna logikappen i Logic Apps Designer i Azure portal eller Visual Studio. Det här exemplet används Azure-portalen.

2. Under utlösaren eller åtgärden, väljer **nytt steg** > **Lägg till en åtgärd**.

   Flytta musen över den anslutande pilen för att lägga till en åtgärd mellan befintliga steg. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”händelsehubbar” som filter.
Välj vilken åtgärd du önska från åtgärdslistan över. 

   I det här exemplet väljer du den här åtgärden: **Event Hubs - överföringshändelse**

   ![Välj ”Event Hubs - överföringshändelse”](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Om du uppmanas anslutningsinformation [skapa Händelsehubbar anslutningen nu](#create-connection). Eller om anslutningen redan finns, ange informationen som krävs för åtgärden.

   | Egenskap  | Krävs | Beskrivning | 
   |----------|----------|-------------|
   | Namn på händelsehubb | Ja | Välj Event Hub där du vill skicka händelsen | 
   | Händelse-innehåll | Nej | Innehåll för händelsen som du vill skicka | 
   | Egenskaper | Nej | Egenskaper och värden för att skicka | 
   |||| 

   Exempel: 

   ![Välj Event Hub-namn och ange händelse-innehåll](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. När du är klar på verktygsfältet för appdesignern väljer **spara**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ansluta till din Event Hub

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. När du uppmanas att ange anslutningsinformationen Tillhandahåll följande information:

   | Egenskap  | Krävs | Värde | Beskrivning | 
   |----------|----------|-------|-------------|
   | Anslutningsnamn | Ja | <*Anslutningens namn*> | Namn för att skapa för anslutningen |
   | Event Hubs-namnområde | Ja | <*namnområde för Event hubs*> | Välj Event Hubs-namnområde som du vill använda. | 
   |||||  

   Exempel:

   ![Skapa anslutning till Händelsehubb](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Om du vill ange anslutningssträngen manuellt välja **ange anslutningsinformationen manuellt**. 
   Lär dig [hitta anslutningssträngen](#permissions-connection-string).

2. Välj Event Hubs-princip om du vill använda, om inte redan är valt. Välj **Skapa**.

   ![Skapa anslutning till Händelsehubb, del 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. När du skapar anslutningen, fortsätter du med [Lägg till Event Hubs utlösa](#add-trigger) eller [Lägg till Event Hubs åtgärd](#add-action).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, enligt beskrivningen av kopplingens Swagger-fil, finns i den [anslutningsappens-referenssida](/connectors/eventhubs/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)