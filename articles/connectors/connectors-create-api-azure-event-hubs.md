---
title: Ansluta till Azure Event Hubs - Azure Logikappar | Microsoft Docs
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
ms.openlocfilehash: 86fc1c3542bea1be840041bb73df15631c066c7e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294980"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Övervaka, ta emot och skicka händelser med Azure Event Hubs och Azure Logic Apps 

Den här artikeln visar hur du kan övervaka och hantera händelser som skickats till [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) från inuti en logikapp med Händelsehubbar i Azure-anslutningen. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att kontrollera, skicka och ta emot händelser från Event Hub. 

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du har använt logikappar granska [vad är Azure Logikappar](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Connector-specifik teknisk information finns i <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">Azure Event Hubs connector referens</a>.

## <a name="prerequisites"></a>Förutsättningar

* En [Azure Event Hubs namnområde och Event Hub](../event-hubs/event-hubs-create.md)

* Logikappen där du vill ha åtkomst till din Event Hub. Om du vill starta din logikapp med en utlösare för Azure Event Hubs, behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Kontrollera behörigheter och hämta anslutningssträngen

Kontrollera dina behörigheter och hämta anslutningssträngen för Händelsehubbar namnområdet för din logikapp att få åtkomst till din Event Hub.

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>. 

2. Gå till din Event Hubs *namnområde*, inte en specifik Händelsehubb. På sidan namnområde under **inställningar**, Välj **principer för delad åtkomst**. Under **anspråk**, kontrollera att du har **hantera** behörigheter för det namnområdet.

   ![Hantera behörigheter för namnområdet Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Om du vill ange anslutningsinformationen senare manuellt kan du hämta anslutningssträngen för namnområdet Händelsehubbar. 

   1. Under **princip**, Välj **RootManageSharedAccessKey**. 

   2. Hitta din primära nyckel anslutningssträngen. Välj kopieringsknappen och spara anslutningssträngen för senare användning.

      ![Kopiera anslutningssträngen för Händelsehubbar namnområde](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > För att bekräfta om anslutningssträngen är associerat med Händelsehubbar namnområdet eller med en specifik händelsehubb, kontrollera att anslutningssträngen inte har den `EntityPath` parameter. Om du hittar den här parametern anslutningssträngen för en specifik Händelsehubb ”enhet” och är inte rätt strängen som ska användas med din logikapp.

4. Fortsätt med [lägga till en utlösare för Händelsehubbar](#add-trigger) eller [lägga till en åtgärd för Händelsehubbar](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Lägg till en utlösare för Händelsehubbar

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett specifikt villkor uppfylls. Varje gång utlösaren-utlöses Logic Apps motorn skapar en logik app-instansen och börjar köras appens arbetsflöde.

Det här exemplet visar hur du kan starta ett arbetsflöde för logik app när nya händelser skickas till din Event Hub. 

1. Skapa en tom logikapp, vilket öppnar Designer för Logic Apps i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. I sökrutan anger du ”händelsehubbar” som filter. Välj utlösare som du vill använda från listan över utlösare. 

   Det här exemplet används den här utlösaren: **Händelsehubbar – när händelser är tillgängliga i Event Hub**

   ![Välj utlösare](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Om du uppmanas anslutningsinformation [skapar anslutningen Händelsehubbar nu](#create-connection). Eller, om det finns redan en anslutning, ange nödvändig information för utlösaren.

   1. Från den **Event Hub-namn** Välj Händelsehubb som du vill övervaka.

      ![Ange Händelsehubb eller konsumentgrupp](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera Händelsehubben.
 
   3. Välj för att du kan också välja några av de avancerade utlösarvillkor **visa avancerade alternativ**.
   
      ![Aktivera avancerade alternativ](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Egenskap  | Information | 
      |----------|---------| 
      | Innehållstyp  | Välj den händelse content-type. Standardvärdet är ”program/oktett-ström”. |
      | Innehåll schema | Ange innehåll schemat i JSON efter de händelser som läses från Event Hub. |
      | Konsumenten gruppnamn | Ange Händelsehubben [konsumenten gruppnamn](../event-hubs/event-hubs-features.md#consumer-groups) för att läsa händelser. Om inget anges används standard konsumentgrupp. |
      | Minsta partitionsnyckel | Ange minst [partition](../event-hubs/event-hubs-features.md#partitions) ID att läsa. Som standard är alla partitioner skrivskyddade. |
      | Maximal partitionsnyckel | Ange maximalt [partition](../event-hubs/event-hubs-features.md#partitions) ID att läsa. Som standard är alla partitioner skrivskyddade. |
      | Antal största händelser | Ange ett värde för högsta antalet händelser. Utlösaren returnerar mellan ett och antalet händelser som anges av egenskapen. |
      |||

4. När du är klar i verktygsfältet designer väljer **spara**.

5. Fortsätt att lägga till en eller flera åtgärder i din logikapp för de aktiviteter som du vill utföra med Utlösare resultat.

> [!NOTE]
> Ingen Händelsehubb utlösare *lång avsökning* utlösare, vilket innebär att när en utlösare utlöses utlösaren bearbetar alla händelser och väntar sedan på 30 sekunder för fler händelser ska visas i din Event Hub.
> Om inga händelser tas emot i 30 sekunder ignoreras utlösare för körning. Annars fortsätter utlösaren läsa händelser tills Event Hub är tom.
> Nästa utlösaren omröstningen sker baserat på återkommande intervall som du anger i egenskaperna för den utlösaren.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Lägg till en åtgärd för Händelsehubbar

I Azure Logikappar en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet logikappen som börjar med en utlösare för Händelsehubbar som söker efter nya händelser i din Event Hub. 

1. Öppna logikappen i designern för Logic Apps i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. Välj under utlösare eller åtgärd, **nytt steg** > **lägga till en åtgärd**.

   Om du vill lägga till en åtgärd mellan befintliga steg, håller du muspekaren över anslutande pilen. 
   Välj på plustecknet (**+**) som visas och sedan välja **lägga till en åtgärd**.

3. I sökrutan anger du ”händelsehubbar” som filter.
Välj den åtgärd som du vill använda från listan över åtgärder. 

   I det här exemplet väljer du den här åtgärden: **Händelsehubbar - överföringshändelse**

   ![Välj ”Händelsehubbar - överföringshändelse”](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Om du uppmanas anslutningsinformation [skapar anslutningen Händelsehubbar nu](#create-connection). Eller, om det finns redan en anslutning, ange nödvändig information för åtgärden.

   | Egenskap  | Krävs | Beskrivning | 
   |----------|----------|-------------|
   | Namn på händelsehubb | Ja | Välj Händelsehubb där du vill skicka händelsen | 
   | Händelsen innehåll | Nej | Innehållet för händelsen som du vill skicka | 
   | Egenskaper | Nej | Egenskaper för appen och värden för att skicka | 
   |||| 

   Exempel: 

   ![Välj Event Hub-namn och ange händelse innehåll](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. När du är klar i verktygsfältet designer väljer **spara**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ansluta till din Event Hub

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. När du uppmanas att ange anslutningsinformation Tillhandahåll följande information:

   | Egenskap  | Krävs | Värde | Beskrivning | 
   |----------|----------|-------|-------------|
   | Anslutningsnamn | Ja | <*Anslutningens namn*> | Namn för att skapa för din anslutning |
   | Event Hubs-namnområde | Ja | <*händelse-hubs-namnområde*> | Markera det namnområde för Händelsehubbar som du vill använda. | 
   |||||  

   Exempel:

   ![Skapa en anslutning till Händelsehubb](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Om du vill ange anslutningssträngen manuellt välja **manuellt ange anslutningsinformationen**. 
   Läs [så att hitta anslutningssträngen](#permissions-connection-string).

2. Välj princip för Händelsehubbar ska användas, om du inte redan har markerats. Välj **Skapa**.

   ![Skapa en anslutning till Händelsehubb, del 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. När du skapar anslutningen fortsätta med [lägga till Händelsehubbar utlösa](#add-trigger) eller [åtgärden Lägg till Händelsehubbar](#add-action).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder som gränser, enligt beskrivningen av kopplingens Swagger-filen finns den [kopplingens referenssida](/connectors/eventhubs/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps kopplingar](../connectors/apis-list.md)