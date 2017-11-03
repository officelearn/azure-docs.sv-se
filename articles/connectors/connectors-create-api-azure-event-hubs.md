---
title: "Konfigurera Händelseövervakare med Händelsehubbar i Azure för Logikappar i Azure | Microsoft Docs"
description: "Övervaka dataströmmar skicka händelser för Logikappar i Azure med Azure Event Hubs och ta emot händelser"
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
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.openlocfilehash: 2ca27fb8269d1796fb1181fc4d0a8744a592d548
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Övervaka, ta emot och skicka händelser med Händelsehubbar connector

Om du vill konfigurera en Händelseövervakare så att din logikapp kan identifiera händelser, ta emot händelser och skicka händelser, ansluta till en [Azure Event Hub](https://azure.microsoft.com/services/event-hubs) från din logikapp. Lär dig mer om [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

## <a name="requirements"></a>Krav

* Du måste ha en [Händelsehubbar namnområde och Event Hub](../event-hubs/event-hubs-create.md) i Azure. Läs [hur du skapar ett namnområde för Händelsehubbar och Händelsehubb](../event-hubs/event-hubs-create.md). 

* Att använda [alla anslutningar](https://docs.microsoft.com/azure/connectors/apis-list) i din logikapp, behöver du först skapa en logikapp. Läs [hur du skapar en logikapp](../logic-apps/logic-apps-create-a-logic-app.md).

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Kontrollera behörigheter för Händelsehubbar namnområde och hitta anslutningssträngen

För din logikapp att få åtkomst till alla tjänster måste du skapa en [ *anslutning* ](./connectors-overview.md) mellan logikappen och tjänsten om du inte redan har gjort. Den här anslutningen ger logikappen åtkomst till data.
För din logikapp att få åtkomst till din Event Hub du måste ha **hantera** behörigheter och anslutningssträngen för namnområdet Händelsehubbar.

Följ dessa steg för att kontrollera dina behörigheter och hämta anslutningssträngen.

1.  Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). 

2.  Gå till din Event Hubs *namnområde*, inte specifik Händelsehubb. I bladet namnområde under **inställningar**, Välj **principer för delad åtkomst**. Under **anspråk**, kontrollera att du har **hantera** behörigheter för det namnområdet.

    ![Hantera behörigheter för namnområdet Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Om du vill kopiera anslutningssträngen för Händelsehubbar namnområdet, Välj **RootManageSharedAccessKey**. Välj kopieringsknappen bredvid din primära nyckel anslutningssträngen.

    ![Kopiera anslutningssträngen för Händelsehubbar namnområde](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Sök för att bekräfta om anslutningssträngen är associerat med Händelsehubbar namnområdet eller med en specifik Händelsehubb anslutningssträngen för den `EntityPath` parameter. Om du hittar den här parametern anslutningssträngen är för en specifik Händelsehubb ”enhet” och är inte rätt strängen som ska användas med din logikapp.

4.  Nu när du uppmanas ange autentiseringsuppgifter när du lägger till en Händelsehubbar utlösare eller åtgärd till din logikapp, kan du ansluta till namnområdet Händelsehubbar. Namnge din anslutning, ange anslutningssträngen som du kopierade och välj **skapa**.

    ![Ange anslutningssträng för Händelsehubbar namnområde](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    När du har skapat din anslutning ska Anslutningsnamnet visas i hubbar utlösare eller åtgärd. 
    Du kan fortsätta med andra steg i din logikapp.

    ![Event Hubs namnområde anslutning som skapats](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>Starta arbetsflödet när din Event Hub tar emot nya händelser

En [ *utlösaren* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) är en händelse som startar ett arbetsflöde i din logikapp. Följ dessa steg för att lägga till utlösare som identifierar den här händelsen om du vill starta ett arbetsflöde när nya händelser skickas till din Event Hub.

1.  I den [Azure-portalen](https://portal.azure.com "Azure-portalen"), gå till din befintliga logikapp eller skapa en tom logikapp.

2.  Skriv i sökrutan för logik App Designer `event hubs` för filtret. Välj den här utlösaren: **när händelser är tillgängliga i Event Hub**

    ![Välj utlösare för när din Event Hub tar emot nya händelser](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Om du inte redan har en anslutning till namnområdet Händelsehubbar, uppmanas du att skapa den här anslutningen nu. Namnge din anslutning och ange anslutningssträngen för namnområdet Händelsehubbar. 
    Lär dig mer om det behövs [så att hitta anslutningssträngen](#permissions-connection-string).

    ![Ange anslutningssträng för Händelsehubbar namnområde](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    När du har skapat anslutningen, inställningarna för den **när en händelse i finns i en Händelsehubb** utlösaren visas.

    ![Med hjälp av inställningar för när din Event Hub tar emot nya händelser](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  Ange eller välj namnet på Händelsehubben som du vill övervaka. Välj frekvensen och intervall för hur ofta du vill kontrollera Händelsehubben.

    > [!TIP]
    > Välj att du kan också välja en konsumentgrupp för att läsa händelser **visa avancerade alternativ**. 

    ![Ange Händelsehubb eller konsumentgrupp](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    Du har nu konfigurerat en utlösare för att starta ett arbetsflöde för din logikapp. 
    Din logikapp kontrollerar angivna Händelsehubben enligt det schema som du anger. 
    Om din app söker efter nya händelser i hubben, utlösaren eller körs på andra åtgärder utlöser i din logikapp.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Skicka händelser till din Event Hub från din logikapp

En [*åtgärd*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) är en aktivitet som utförs av logikapparbetsflödet. När du lägger till en utlösare i logikappen kan du lägga till en åtgärd för att utföra åtgärder med data som genereras av den utlösaren. Följ dessa steg om du vill skicka en händelse till din Event Hub från din logikapp.

1.  I logik App Designer under din logik apputlösare väljer **nytt steg** > **lägga till en åtgärd**.

    ![Sedan Välj ”nytt steg”, ”Lägg till en åtgärd”](./media/connectors-create-api-azure-event-hubs/add-action.png)

    Nu kan du hitta och välja en åtgärd som ska utföras. 
    Men du kan välja en åtgärd, t.ex, vill vi Händelsehubbar åtgärden att skicka händelser.

2.  I sökrutan anger `event hubs` för filtret.
Välj den här åtgärden: **överföringshändelse**

    ![Välj ”Händelsehubbar - överföringshändelse” åtgärd](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  Ange nödvändig information för händelsen, till exempel namnet på Händelsehubben där du vill skicka händelsen. Ange andra valfria information om händelsen, till exempel innehållet för den händelsen.

    > [!TIP]
    > Du kan också ange partition i Händelsehubben var att skicka händelsen, välja **visa avancerade alternativ**. 

    ![Ange Event Hub-namn och valfri händelseinformation](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  Spara ändringarna.

    ![Spara din logikapp](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    En åtgärd har konfigurerats att skicka händelser från din logikapp. 

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/eventhubs/). 

## <a name="get-help"></a>Få hjälp

I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor och sedan vad andra Azure Logic Apps-användare håller på med.

På [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Logic Apps och anslutningsapparna genom att rösta på förslag eller komma med egna förslag på förbättringar.

## <a name="next-steps"></a>Nästa steg

*  [Sök efter andra kopplingar till Azure logikappar](./apis-list.md)