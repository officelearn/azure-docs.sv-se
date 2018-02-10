---
title: "Konfigurera övervakning med Händelsehubbar i Azure för Logikappar i Azure | Microsoft Docs"
description: "Övervaka dataströmmar skicka händelser med dina logic apps med hjälp av Azure Event Hubs och ta emot händelser"
services: logic-apps
keywords: "dataströmmen, övervakning, händelsehubbar"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 076f7dd11ca8c153046727861ecb755e88f32b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Övervaka, ta emot och skicka händelser med Händelsehubbar connector

Om du vill konfigurera en Händelseövervakare så att din logikapp kan identifiera händelser, ta emot händelser och skicka händelser, ansluta till en [Azure Event Hub](https://azure.microsoft.com/services/event-hubs) från din logikapp. Lär dig mer om [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) och [hur priser fungerar för Logic Apps kopplingar](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Förutsättningar

Du måste ha objekten innan du kan använda Händelsehubbar kopplingen:

* En [Azure Event Hubs namnområde och Event Hub](../event-hubs/event-hubs-create.md)
* En [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Ansluta till Azure Event Hubs

Innan din logikapp kan komma åt någon tjänst, måste du skapa en [ *anslutning* ](./connectors-overview.md) mellan logikappen och tjänsten om du inte redan har gjort. Den här anslutningen ger logikappen åtkomst till data. Kontrollera dina behörigheter och hämta anslutningssträngen för Händelsehubbar namnområdet för din logikapp att få åtkomst till din Event Hub.

1.  Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). 

2.  Gå till din Event Hubs *namnområde*, inte en specifik Händelsehubb. På sidan namnområde under **inställningar**, Välj **principer för delad åtkomst**. Under **anspråk**, kontrollera att du har **hantera** behörigheter för det namnområdet.

    ![Hantera behörigheter för namnområdet Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Om du vill ange anslutningsinformationen senare manuellt kan du hämta anslutningssträngen för namnområdet Händelsehubbar. Välj **RootManageSharedAccessKey**. Välj kopieringsknappen bredvid din primära nyckel anslutningssträngen. Spara anslutningssträngen för senare användning.

    ![Kopiera anslutningssträngen för Händelsehubbar namnområde](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Sök för att bekräfta om anslutningssträngen är associerat med Händelsehubbar namnområdet eller med en specifik händelsehubb anslutningssträngen för den `EntityPath` parameter. Om du hittar den här parametern anslutningssträngen är för en specifik Händelsehubb ”enhet” och är inte rätt strängen som ska användas med din logikapp.

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>Utlöser arbetsflödet när din Event Hub hämtar nya händelser

En [ *utlösaren* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en händelse som startar ett arbetsflöde i din logikapp. Följ dessa steg för att lägga till utlösare som identifierar den här händelsen om du vill starta ett arbetsflöde när nya händelser skickas till din Event Hub.

1. I den [Azure-portalen](https://portal.azure.com "Azure-portalen"), gå till din befintliga logikapp eller skapa en tom logikapp.

2. Ange ”händelsehubbar” i sökrutan som filter i Logic Apps Designer. Välj den här utlösaren: **när händelser är tillgängliga i Event Hub**

   ![Välj utlösare för när din Event Hub tar emot nya händelser](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Om du inte redan har en anslutning till namnområdet Händelsehubbar, uppmanas du att skapa den här anslutningen nu. Namnge din anslutning och markera Händelsehubbar-namnområde som du vill använda.

      ![Skapa en anslutning till Händelsehubb](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      Om du vill ange anslutningssträngen manuellt välja **manuellt ange anslutningsinformationen**. 
      Läs [så att hitta anslutningssträngen](#permissions-connection-string).

   2. Nu väljer principen för Händelsehubbar att använda, och välj **skapa**.

      ![Skapa en anslutning till Händelsehubb, del 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Välj Händelsehubb för att övervaka och ställa in intervall och frekvens för när du vill söka Händelsehubben.

    ![Ange Händelsehubb eller konsumentgrupp](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

    > [!TIP]
    > Välj att du kan också välja en konsumentgrupp för att läsa händelser **visa avancerade alternativ**.

4. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Nu när logikappen kontrollerar valda Händelsehubben och hittar en ny händelse, körs utlösaren åtgärderna i din logikapp för händelsen hittades.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Skicka händelser till din Event Hub från din logikapp

En [*åtgärd*](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en aktivitet som utförs av logikapparbetsflödet. När du lägger till en utlösare i logikappen kan du lägga till en åtgärd för att utföra åtgärder med data som genereras av den utlösaren. Följ dessa steg om du vill skicka en händelse till din Event Hub från din logikapp.

1. I Logic Apps Designer utlösaren, Välj under **nytt steg** > **lägga till en åtgärd**.

2. I sökrutan anger du ”händelsehubbar” som filter.
Välj den här åtgärden: **Händelsehubbar - överföringshändelse**

   ![Välj ”Händelsehubbar - överföringshändelse”](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. Välj Händelsehubb för att skicka händelsen. Ange händelse-innehåll och annan information.

   ![Välj Event Hub-namn och ange händelse innehåll](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. Spara din logikapp.

Du har nu skapat en åtgärd som skickar händelser från din logikapp. 

## <a name="connector-specific-details"></a>Connector-specifik information

Om du vill veta mer om utlösare och åtgärder som definierats i Swagger-filen och alla gränser, granska den [connector information](/connectors/eventhubs/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [övriga kopplingar för Azure Logic apps](../connectors/apis-list.md)