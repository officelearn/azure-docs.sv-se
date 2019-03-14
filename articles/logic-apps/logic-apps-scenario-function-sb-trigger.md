---
title: Scenario – utlösaren logic apps med Azure Functions och Azure Service Bus | Microsoft Docs
description: Skapa funktioner som utlöser logikappar med hjälp av Azure Functions och Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 1d3c4039ae823d3797e768af5892333d4d925268
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789949"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Scenario: Utlösaren logic apps med Azure Functions och Azure Service Bus

Du kan använda Azure Functions för att skapa en utlösare för en logic app när du behöver distribuera en lyssnare eller uppgift. Du kan till exempel skapa en funktion som lyssnar på en kö och omedelbart utlöses en logikapp som en push-utlösare.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Innan du kan skapa en Azure-funktion [skapa en funktionsapp](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Skapa en logikapp

I det här exemplet har du en funktion som körs för varje logikapp som behöver aktiveras. Skapa först en logikapp som har en HTTP-begäran-utlösare. Funktionen anropar slutpunkten när ett kömeddelande tas emot.  

1. Logga in på den [Azure-portalen](https://portal.azure.com), och skapa tom logikapp. 

   Om du är nybörjare till logic apps, granska [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Ange ”http-begäran” i sökrutan. Under listan över utlösare, väljer du den här utlösaren: **När en HTTP-begäran tas emot**

   ![Välj utlösare](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. För den **begära** utlösare, om du vill kan du ange en JSON-schema för användning med kömeddelandet. JSON-scheman att Logic App Designer förstå strukturen för inkommande data och gör utdata enklare för dig att välja i hela arbetsflödet. 

   Ange om du vill ange ett schema, schemat i den **begär JSON-Brödtextsschema** rutan, till exempel: 

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

1. Lägg till andra åtgärder som du vill ska hända när du har fått kömeddelandet. 

   Du kan till exempel skicka ett e-postmeddelande med Office 365 Outlook-anslutningen.

1. Spara logikappen, vilket genererar Motringnings-URL för utlösaren i den här logikappen. Denna URL visas i den **HTTP post-URL** egenskapen.

   ![Genererad Motringnings-URL för utlösare](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Skapa Azure-funktion

Skapa sedan den funktion som fungerar som utlösare och lyssnar efter kön. 

1. I Azure-portalen, öppna och expandera din funktionsapp, om inte redan är öppen. 

1. Under din funktionsappens namn, expandera **Functions**. På den **Functions** fönstret Välj **ny funktion**. Välj den här mallen: **Utlösare för Service Bus-kö-C#**
   
   ![Välj Azure Functions-portalen](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Ange ett namn för din utlösare och sedan konfigurera anslutningen till Service Bus-kö som använder Azure Service Bus SDK `OnMessageReceive()` lyssnare.

1. Skriv en grundläggande funktion för att anropa den tidigare skapade logic app-slutpunkten genom att använda kömeddelandet som en utlösare, till exempel: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   // Re-use instance of http clients if possible - https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

   Det här exemplet används den `application/json` innehållstypen för meddelandet, men du kan ändra den här typen kan vid behov.

1. Om du vill testa funktionen genom att lägga till ett kömeddelande genom att använda ett verktyg som de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). 

   Logic app utlöses omedelbart efter att funktionen tar emot meddelandet.

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

