---
title: Ansluta till Azure-händelsehubbar
description: Skapa automatiserade uppgifter och arbetsflöden som övervakar och hanterar händelser med hjälp av Azure Event Hubs och Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 32fa54ef0d8eccaf8745ee37cb028d4f3c6d73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247298"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Övervaka, ta emot och skicka händelser med Azure Event Hubs och Azure Logic Apps

Den här artikeln visar hur du kan övervaka och hantera händelser som skickas till [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) inifrån en logikapp med Azure Event Hubs-kopplingen. På så sätt kan du skapa logikappar som automatiserar aktiviteter och arbetsflöden för att kontrollera, skicka och ta emot händelser från Event Hub. Anslutningsspecifik teknisk information finns i [Azure Event Hubs-anslutningsreferensen](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett [namnområde och händelsenav för Azure-händelsehubben](../event-hubs/event-hubs-create.md)

* Logikappen där du vill komma åt din eventhubb. Om du vill starta logikappen med en Azure Event Hubs-utlösare behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Kontrollera behörigheter och hämta anslutningssträng

Kontrollera dina behörigheter och hämta anslutningssträngen för namnområdet Event Hubs för att se till att logikappen kan komma åt din händelsehubb.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till *namnområdet*Event Hubs , inte en specifik händelsehubb. 

1. Välj Principer för **delad åtkomst**under **Inställningar**på namnområdesmenyn . Under **Anspråk**kontrollerar du att du har **hantera** behörigheter för det namnområdet.

   ![Hantera behörigheter för namnområdet Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Om du senare vill ange anslutningsinformation manuellt hämtar du anslutningssträngen för namnområdet Event Hubs.

   1. Under **Princip**väljer du **RootManageSharedAccessKey**.

   1. Hitta huvudnyckelns anslutningssträng. Välj kopieringsknappen och spara anslutningssträngen för senare användning.

      ![Kopiera namnområdesanslutningssträng för händelsehubbar](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Kontrollera om anslutningssträngen är associerad med namnområdet Event Hubs eller med en specifik `EntityPath`  händelsehubb kontrollerar du att anslutningssträngen inte har parametern. Om du hittar den här parametern är anslutningssträngen för en specifik händelsehubb "entitet" och är inte rätt sträng som ska användas med logikappen.

1. Fortsätt nu med [Lägg till en utlösare av händelsehubbar](#add-trigger) eller [lägg till en åtgärd för händelsehubbar](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Lägga till utlösare för händelsehubbar

I Azure Logic Apps måste varje logikapp starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett visst villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans och börjar köra appens arbetsflöde.

Det här exemplet visar hur du kan starta ett arbetsflöde för logikappar när nya händelser skickas till händelsehubben. 

1. Skapa en tom logikapp i Azure-portalen eller Visual Studio som öppnar Logic Apps Designer. I det här exemplet används Azure-portalen.

1. I sökrutan anger du "händelsehubbar" som filter. Välj den här utlösaren i listan utlösare: **När händelser är tillgängliga i Event Hub - Event Hubs**

   ![Välj utlösare](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Om du uppmanas att ange anslutningsinformation [skapar du anslutningen eventhubbar nu](#create-connection). 

1. Ange information om händelsehubben som du vill övervaka i utlösaren. Om du vill ha fler egenskaper öppnar du listan **Lägg till ny parameter.** Om du väljer en parameter läggs egenskapen till i utlösarkortet.

   ![Egenskaper för utlösare](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn på händelsehubben** | Ja | Namnet på händelsehubben som du vill övervaka |
   | **Innehållstyp** | Inga | Händelsens innehållstyp. Standardvärdet är `application/octet-stream`. |
   | **Konsumentgruppens namn** | Inga | [Namnet på konsumentgruppen Event Hub](../event-hubs/event-hubs-features.md#consumer-groups) som ska användas för att läsa händelser. Om inget anges används standardkonsumentgruppen. |
   | **Antal maximala händelser** | Inga | Det maximala antalet händelser. Utlösaren returnerar mellan en och antalet händelser som anges av den här egenskapen. |
   | **Intervall** | Ja | Ett positivt heltal som beskriver hur ofta arbetsflödet körs baserat på frekvensen |
   | **Frekvens** | Ja | Tidsenheten för upprepningen |
   ||||

   **Ytterligare egenskaper**

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Innehållsschema** | Inga | JSON-innehållsschemat för händelserna som ska läsas från händelsehubben. Om du till exempel anger innehållsschemat kan du utlösa logikappen för endast de händelser som matchar schemat. |
   | **Minsta partitionsnyckel** | Inga | Ange det minsta [partitions-ID](../event-hubs/event-hubs-features.md#partitions) som ska läsas. Som standard läss alla partitioner. |
   | **Maximal partitionsnyckel** | Inga | Ange det maximala [partitions-ID](../event-hubs/event-hubs-features.md#partitions) som ska läsas. Som standard läss alla partitioner. |
   | **Tidszon** | Inga | Gäller endast när du anger en starttid eftersom den här utlösaren inte accepterar UTC-förskjutning. Markera den tidszon som du vill använda. <p>Mer information finns i [Skapa och köra återkommande uppgifter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Starttid** | Inga | Ange en starttid i det här formatet: <p>YYYY-MM-DDThh:mm:ss om du väljer en tidszon<p>ELLER<p>Å ÅYY-MM-DDThh:mm:ssZ om du inte väljer en tidszon<p>Mer information finns i [Skapa och köra återkommande uppgifter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. När du är klar väljer du **Spara**i designerverktygsfältet .

1. Fortsätt nu att lägga till en eller flera åtgärder i logikappen för de uppgifter som du vill utföra med utlösarresultaten. 

   Om du till exempel vill filtrera händelser baserat på ett visst värde, till exempel en kategori, kan du lägga till ett villkor så att åtgärden **Skicka händelse** endast skickar de händelser som uppfyller ditt villkor. 

> [!NOTE]
> Alla Event Hub-utlösare är *utlösare för långavsökning,* vilket innebär att utlösaren bearbetar alla händelser när en utlösare utlöses och sedan väntar i 30 sekunder på att fler händelser ska visas i händelsehubben.
> Om inga händelser tas emot inom 30 sekunder hoppas utlösarkörningen över. Annars fortsätter utlösaren att läsa händelser tills händelsehubben är tom.
> Nästa utlösaravsökning sker baserat på upprepningsintervallet som du anger i utlösarens egenskaper.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Åtgärden Lägg till händelsehubbar

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. Logikappen börjar i det här exemplet med en händelsehubbar-utlösare som söker efter nya händelser i händelsehubben.

1. Öppna logikappen i Logic Apps Designer i Azure-portalen eller Visual Studio. I det här exemplet används Azure-portalen.

1. Under utlösaren eller åtgärden väljer du **Nytt steg**.

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över anslutningspilen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "händelsehubbar" som filter.
Välj den här åtgärden i åtgärdslistan: **Skicka händelse - Event Hubs**

   ![Välj åtgärden Skicka händelse](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Om du uppmanas att ange anslutningsinformation [skapar du anslutningen eventhubbar nu](#create-connection). 

1. Ange information om de händelser som du vill skicka i åtgärden. Om du vill ha fler egenskaper öppnar du listan **Lägg till ny parameter.** Om du väljer en parameter läggs egenskapen till i åtgärdskortet.

   ![Välj namn på händelsehubben och ange händelseinnehåll](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn på händelsehubben** | Ja | Händelsehubben där du vill skicka händelsen |
   | **Innehåll** | Inga | Innehållet för den händelse som du vill skicka |
   | **Egenskaper** | Inga | Appens egenskaper och värden som ska skickas |
   | **Partitionsnyckeln** | Inga | [Partitions-ID](../event-hubs/event-hubs-features.md#partitions) för var händelsen ska skickas |
   ||||

   Du kan till exempel skicka utdata från händelsehubbar till en annan eventhubb:

   ![Skicka händelseexempel](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ansluta till din händelsehubb

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Ange följande information när du uppmanas att ange anslutningsinformation:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutningsnamn** | Ja | <*anslutningsnamn*> | Namnet som ska skapas för anslutningen |
   | **Namnområde för händelsehubbar** | Ja | <*händelse-hubbar-namnområde*> | Välj det namnområde för händelsehubbar som du vill använda. |
   |||||  

   Ett exempel:

   ![Skapa anslutning till händelsehubb](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Om du vill ange anslutningssträngen manuellt väljer du **Ange anslutningsinformation manuellt**. 
   Läs om hur du [hittar anslutningssträngen](#permissions-connection-string).

2. Välj den princip för händelsehubbar som ska användas, om den inte redan är markerad. Välj **Skapa**.

   ![Skapa anslutning till eventhubb, del 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. När du har skapat anslutningen fortsätter du med [Åtgärden Lägg till händelsehubbar](#add-trigger) eller [åtgärd för Lägg till händelsehubbar](#add-action).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/eventhubs/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)