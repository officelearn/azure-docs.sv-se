---
title: Azure-funktion-aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder Azure Function-aktiviteten för att köra en Azure-funktion i Data Factory-pipeline
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 82786b8f01ce409179f4ddd37127679f9357cd0e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727047"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Function-aktivitet i Azure Data Factory

Azure Function-aktiviteten kan du köra [Azure Functions](../azure-functions/functions-overview.md) i Data Factory-pipeline. Om du vill köra en Azure-funktion, måste du skapa en länkad tjänst-anslutning och en aktivitet som anger den Azure-funktion som du planerar att köra.

Titta på följande videoklipp för ett 8-minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure funktion länkad tjänst

Returtypen för Azure-funktionen måste vara en giltig `JObject`. (Tänk på att [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) är *inte* en `JObject`.) Någon returtyp än `JObject` misslyckas och genererar användarfel *svar innehåll är inte en giltig JObject*.

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| typ   | Type-egenskapen måste anges till: **AzureFunction** | ja |
| funktionen app-url | URL för Azure Functions-App. Formatet är `https://<accountname>.azurewebsites.net`. Denna URL är värdet under **URL** avsnittet när du visar Funktionsappen i Azure portal  | ja |
| Funktionsnyckel | Åtkomstnyckel för Azure-funktion. Klicka på den **hantera** för respektive funktion och kopiera antingen den **funktionsnyckel** eller **värdnyckeln**. Läs mer här: [Azure Functions HTTP-utlösare och bindningar](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Function-aktivitet

| **Egenskap**  | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| namn  | Namnet på aktiviteten i pipelinen  | String | ja |
| typ  | Aktiviteten är 'AzureFunctionActivity' | String | ja |
| Länkad tjänst | Azure-funktion som är länkad tjänst för motsvarande Azure Function-App  | Länkade tjänstreferensen | ja |
| Funktionsnamn  | Namnet på funktionen i Azure Function-App som anropar den här aktiviteten | String | ja |
| metod  | REST API-metoden för funktionsanropet | Sträng typer som stöds: ”HÄMTA”, ”POST”, ”PUT”   | ja |
| sidhuvud  | Rubriker som skickas till begäran. Till exempel vill ange språk och typ för en begäran: ”headers”: {”Accept-språk” ”: en-us”, ”Content-Type”: ”application/json”} | Sträng (eller ett uttryck med resultType av sträng) | Nej |
| brödtext  | texten som skickas tillsammans med begäran till funktionen api-metoden  | Sträng (eller ett uttryck med resultType av sträng) eller ett objekt.   | Krävs för PUT/POST-metoder |
|   |   |   | |

Se schemat för nyttolasten i begäran i [begäran nyttolast schemat](control-flow-web-activity.md#request-payload-schema) avsnittet.

## <a name="routing-and-queries"></a>Routning och frågor

Aktivitet för Azure-funktionen stöder **routning**. Exempel: om din Azure-funktion har slutpunkten `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, kommer `functionName` ska använda i Azure-Funktionsaktiviteten `<functionName>/<value>`. Du kan Parameterisera den här funktionen för att ge önskat `functionName` vid körning.

Aktivitet för Azure-funktionen stöder även **frågor**. En fråga måste ingå som en del av den `functionName`. Till exempel när funktionsnamnet är `HttpTriggerCSharp` och den fråga som du vill inkludera är `name=hello`, och sedan kan du skapa den `functionName` i aktivitet för Azure-funktion som `HttpTriggerCSharp?name=hello`. Den här funktionen kan parameteriseras så värdet kan fastställas vid körning.

## <a name="timeout-and-long-running-functions"></a>Timeout och tidskrävande funktioner

Azure Functions tidsgränsen ut efter 230 sekunder oavsett den `functionTimeout` inställning som du har konfigurerat i inställningarna. Mer information finns i [den här artikeln](../azure-functions/functions-versions.md#timeout). Undvik problemet genom att följer ett mönster för asynkrona eller Använd varaktiga funktioner. Fördelen med varaktiga funktioner är att de erbjuder sina egna tillstånd spårnings-mekanism så du behöver att implementera dina egna.

Mer information om varaktiga funktioner i [i den här artikeln](../azure-functions/durable/durable-functions-overview.md). Du kan ställa in en aktivitet för Azure-funktion för att anropa funktionen varaktiga som returnerar ett svar med en annan URI som [det här exemplet](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Eftersom `statusQueryGetUri` returnerar HTTP-Status 202 när funktionen körs, du kan söka status för funktionen med hjälp av en webbaktivitet. Helt enkelt ställa in en webbaktivitet med den `url` fältet inställt på `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. När funktionen varaktiga är klar blir resultatet av funktionen utdata för Web-aktivitet.


## <a name="next-steps"></a>Nästa steg

Mer information om aktiviteter i Data Factory i [Pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md).
