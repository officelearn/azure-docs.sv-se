---
title: Anropa Logic Apps med Azure Functions
description: Skapa Azure-funktioner som anropar eller utlöser logikappar genom att lyssna på Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428713"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Anropa eller utlösa logikappar med hjälp av Azure Functions och Azure Service Bus

Du kan använda [Azure Functions](../azure-functions/functions-overview.md) för att utlösa en logikapp när du behöver distribuera en långvarig lyssnare eller uppgift. Du kan till exempel skapa en Azure-funktion som lyssnar i en [Azure Service Bus-kö](../service-bus-messaging/service-bus-messaging-overview.md) och omedelbart utlöser en logikapp som en push-utlösare.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett namnområde för Azure Service Bus. Om du inte har något namnområde [skapar du namnområdet först](../service-bus-messaging/service-bus-create-namespace-portal.md).

* En Azure-funktionsapp, som är en behållare för Azure-funktioner. Om du inte har en funktionsapp [skapar du funktionsappen först](../azure-functions/functions-create-first-azure-function.md)och kontrollerar att du väljer .NET som körningsstack.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Skapa en logikapp

I det här scenariot har du en funktion som kör varje logikapp som du vill utlösa. Skapa först en logikapp som börjar med en HTTP-begärandeutlösare. Funktionen anropar slutpunkten när ett kömeddelande tas emot.

1. Logga in på [Azure-portalen](https://portal.azure.com)och skapa tom logikapp.

   Om du inte har tidigare i logikappar läser du [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Skriv `http request` i sökrutan. Välj **utlösaren När en HTTP-begäran tas emot i** listan utlösare.

   ![Välj utlösare](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Med utlösaren Begäran kan du också ange ett JSON-schema som du kan använda med kömeddelandet. JSON-scheman hjälper Logic App Designer att förstå strukturen för indata och göra utdata lättare för dig att använda i arbetsflödet.

1. Om du vill ange ett schema anger du schemat i rutan **Begärandetext JSON Schema,** till exempel:

   ![Ange JSON-schema](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Om du inte har ett schema, men du har ett exempel nyttolast i JSON-format, kan du generera ett schema från den nyttolasten.

   1. I utlösaren Begäran väljer du **Använd exempelnyttolast för att generera schema**.

   1. Under **Ange eller klistra in ett prov på JSON-nyttolast**anger du exempelnyttolasten och väljer sedan **Klar**.

      ![Ange exempelnyttolast](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Det här exemplet nyttolast genererar det här schemat som visas i utlösaren:

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

1. Lägg till andra åtgärder som du vill köra när du har tagit emot kömeddelandet.

   Du kan till exempel skicka ett e-postmeddelande med Office 365 Outlook-anslutningsappen.

1. Spara logikappen, som genererar motringnings-URL:en för utlösaren i den här logikappen. Senare använder du den här motringnings-URL:en i koden för Azure Service Bus Queue-utlösaren.

   Motringnings-URL:en visas i egenskapen **HTTP POST URL.**

   ![Genererad motringnings-URL för utlösare](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Skapa Azure-funktion

Skapa sedan funktionen som fungerar som utlösare och lyssnar på kön.

1. Öppna och expandera din funktionsapp i Azure-portalen, om den inte redan är öppen. 

1. Expandera **Funktioner**under funktionsappens namn . Välj **Ny funktion**i fönstret **Funktioner** .

   ![Expandera "Funktioner" och välj "Ny funktion"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Välj den här mallen baserat på om du har skapat en ny funktionsapp där du valde .NET som körningsstapel eller om du använder en befintlig funktionsapp.

   * För nya funktionsappar väljer du den här mallen: **Utlösaren för servicebusskö**

     ![Välj mall för ny funktionsapp](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * För en befintlig funktionsapp väljer du den här mallen: **Service Bus Queue-utlösare - C#**

     ![Välj mall för befintlig funktionsapp](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. I **utlösarfönstret för Azure Service Bus Queue** anger du ett namn för utlösaren och konfigurerar **servicebussanslutningen** för kön, som använder Azure Service Bus SDK-lyssnaren, `OnMessageReceive()` och väljer **Skapa**.

1. Skriv en grundläggande funktion för att anropa den tidigare skapade logikappslutpunkten med hjälp av kömeddelandet som utlösare. Innan du skriver din funktion, granska dessa överväganden:

   * I det `application/json` här exemplet används meddelandeinnehållstypen, men du kan ändra den här typen efter behov.
   
   * På grund av möjliga samtidiga funktioner som körs samtidigt, stora volymer eller tunga belastningar undviker du att instansiera [klassen HTTPClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) med `using` satsen och direkt skapa HTTPClient-instanser per begäran. Mer information finns i [Använda HttpClientFactory för att implementera fjädrande HTTP-begäranden](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Om möjligt kan du återanvända instansen av HTTP-klienter. Mer information finns [i Hantera anslutningar i Azure Functions](../azure-functions/manage-connections.md).

   I det [ `Task.Run` ](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) här exemplet används metoden i [asynkront](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) läge. Mer information finns i [Asynkron programmering med asynkron och väntar](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Om du vill testa funktionen lägger du till ett kömeddelande med hjälp av ett verktyg som [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   Logikappen utlöses omedelbart efter att funktionen har tagit emot meddelandet.

## <a name="next-steps"></a>Nästa steg

* [Anropa, utlösa eller kapsla arbetsflöden med hjälp av HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md)
