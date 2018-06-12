---
title: Scenario - utlösaren logikappar med Azure Functions och Azure Service Bus | Microsoft Docs
description: Skapa en funktion för att utlösa en logikapp med hjälp av Azure Functions och Azure Service Bus
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 89fcd88643bd793935e7476ef32641ffa5ff4713
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299801"
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>Scenario: Utlösa en logikapp med Azure Functions och Azure Service Bus

Du kan använda Azure Functions för att skapa en utlösare för en logikapp när du behöver distribuera en tidskrävande lyssnare eller aktivitet. Du kan till exempel skapa en funktion som avlyssnar en kö och omedelbart eller en logikapp som en push-utlösare.

## <a name="build-the-logic-app"></a>Skapa logikappen
I det här exemplet har en funktion som körs för varje logikappen som måste aktiveras. Först skapa en logikapp som har en utlösare för HTTP-begäran. Funktionen anropar denna slutpunkt när ett kömeddelande tas emot.  

1. Skapa en logikapp.
2. Välj den **manuellt - när en HTTP-begäran tas emot** utlösare.
   Alternativt kan du ange en JSON-schema för att använda kömeddelandet med hjälp av ett verktyg som [jsonschema.net](http://jsonschema.net). Klistra in schemat i utlösaren. Scheman med hjälp av designern förstå formen av egenskaperna data och flödar enklare genom arbetsflödet.
2. Lägg till eventuella ytterligare steg som ska inträffa när ett meddelande i kön har tagits emot. Till exempel skicka ett e-postmeddelande via Office 365.  
3. Spara logikapp för att generera återanrop-URL: en för utlösaren till den här logikapp. URL-Adressen visas på kortet utlösare.

![Återanropet URL-Adressen visas på kortet utlösare][1]

## <a name="build-the-function"></a>Skapa funktionen
Därefter måste skapa du en funktion som fungerar som utlösare och lyssnar till kön.

1. I den [Azure Functions-portalen](https://functions.azure.com/signin)väljer **nya funktionen**, och välj sedan den **ServiceBusQueueTrigger - C#** mall.
   
    ![Azure Functions-portalen][2]
2. Konfigurera anslutningen till Service Bus-kö som använder Azure Service Bus SDK `OnMessageReceive()` lyssnare.
3. Skriva en grundläggande funktion för att anropa logik app slutpunkten (som skapats tidigare) med hjälp av kömeddelandet som en utlösare. Här är ett fullständigt exempel på en funktion. I exemplet används en `application/json` innehållstyp för meddelandet, men du kan ändra den här typen efter behov.
   
   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
   
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Testa genom att lägga till ett kömeddelande via ett verktyg som [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Se logikappen eller omedelbart efter funktionen tar emot meddelandet.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png
