---
title: Anropa eller logikappar med Azure Functions och Azure Service Bus-utlösare
description: Skapa Azure-funktioner som anropar eller utlösa logikappar med hjälp av Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494997"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Anropa eller utlösa logikappar med Azure Functions och Azure Service Bus

Du kan använda [Azure Functions](../azure-functions/functions-overview.md) att utlösa en logic app när du behöver distribuera en tidskrävande lyssnare eller uppgift. Du kan till exempel skapa en Azure-funktion som lyssnar i på en [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) kö och utlöses omedelbart en logikapp som en push-utlösare.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett Azure Service Bus-namnområde. Om du inte har ett namnområde [först skapa namnområdet](../service-bus-messaging/service-bus-create-namespace-portal.md).

* En Azure function-app, vilket är en behållare för Azure functions. Om du inte har en funktionsapp [först skapa din funktionsapp](../azure-functions/functions-create-first-azure-function.md), och kontrollera att du väljer .NET som körningsstack.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Skapa en logikapp

Det här scenariot har du en funktion som körs varje logikapp som du vill ska utlösa. Skapa först en logikapp som börjar med en HTTP-begäran-utlösare. Funktionen anropar slutpunkten när ett kömeddelande tas emot.  

1. Logga in på den [Azure-portalen](https://portal.azure.com), och skapa tom logikapp.

   Om du är nybörjare till logic apps, granska [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Ange ”http-begäran” i sökrutan. Välj den här utlösaren från listan över utlösare: **När en HTTP-begäran tas emot**

   ![Välj utlösare](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Du kan också ange en JSON-schema som ska användas med kömeddelandet med begäran-utlösare. JSON-scheman att Logic App Designer förstå för indata och underlätta utdata som du kan använda i ditt arbetsflöde.

1. Ange om du vill ange ett schema, schemat i den **begär JSON-Brödtextsschema** rutan, till exempel:

   ![Ange JSON-schema](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Om du inte har ett schema, men du har en exempelnyttolast i JSON-format, kan du generera ett schema från den nyttolasten.

   1. I begäran-utlösaren väljer **Använd exempel för att generera schemat**.

   1. Under **Skriv eller klistra in en JSON-exempelnyttolast**, ange din exempelnyttolast och välj sedan **klar**.

      ![Ange exempelnyttolast](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Den här exempelnyttolast genererar det här schemat som visas i utlösaren:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Lägg till alla andra åtgärder som du vill köra när du har fått kömeddelandet.

   Du kan till exempel skicka ett e-postmeddelande med Office 365 Outlook-anslutningen.

1. Spara logikappen, vilket genererar Motringnings-URL för utlösaren i den här logikappen. Senare kan använda du Motringnings-URL i koden för Azure Service Bus-kö-utlösare.

   Callback URL som visas i den **HTTP post-URL** egenskapen.

   ![Genererad Motringnings-URL för utlösare](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Skapa Azure-funktion

Skapa sedan den funktion som fungerar som utlösare och lyssnar efter kön.

1. I Azure-portalen, öppna och expandera din funktionsapp, om inte redan är öppen. 

1. Under din funktionsappens namn, expandera **Functions**. På den **Functions** fönstret Välj **ny funktion**.

   ![Expandera ”funktioner” och välj ”ny funktion”](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Välj den här mallen baserat på om du har skapat en ny funktionsapp där du har valt .NET som körningsstack eller du använder en befintlig funktionsapp.

   * Välj den här mallen för den nya funktionsappar: **Utlösare för Service Bus-kö**

     ![Välj mall för ny funktionsapp](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * För en befintlig funktionsapp väljer du den här mallen: **Utlösare för Service Bus-kö-C#**

     ![Välj mall för befintliga funktionsapp](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. På den **Azure Service Bus-kö utlösaren** rutan Ange ett namn för din utlösare och konfigurera den **Service Bus-anslutning** för kön som använder Azure Service Bus SDK `OnMessageReceive()` lyssnare, och välj **Skapa**.

1. Skriva en grundläggande funktion för att anropa den tidigare skapade logic app-slutpunkten med hjälp av kömeddelandet som en utlösare. Det här exemplet används den `application/json` innehållstypen för meddelandet, men du kan ändra den här typen kan vid behov. Om möjligt återanvända instansen av HTTP-klienter. Mer information finns i [hantera anslutningar i Azure Functions](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Om du vill testa funktionen genom att lägga till ett kömeddelande genom att använda ett verktyg som de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   Logic app utlöses omedelbart efter att funktionen tar emot meddelandet.

## <a name="next-steps"></a>Nästa steg

[Anropa, utlösa eller kapsla arbetsflöden med hjälp av HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md)