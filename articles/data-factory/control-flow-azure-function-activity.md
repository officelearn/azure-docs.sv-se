---
title: Azure-funktionsaktivitet i Azure Data Factory
description: Lär dig hur du använder Azure-funktionsaktiviteten för att köra en Azure-funktion i en Data Factory-pipeline
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ffb610634399594788afcb9b600ba00c6803dfdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207033"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure-funktionsaktivitet i Azure Data Factory

Med Azure-funktionsaktiviteten kan du köra [Azure-funktioner](../azure-functions/functions-overview.md) i en Data Factory-pipeline. Om du vill köra en Azure-funktion måste du skapa en länkad tjänstanslutning och en aktivitet som anger den Azure-funktion som du planerar att köra.

För en åtta minuters introduktion och demonstration av den här funktionen, titta på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure-funktionslänkade tjänst

Returtypen för Azure-funktionen måste `JObject`vara en giltig . (Tänk på att [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) `JObject` *inte* är en .) Alla returtyper `JObject` som inte fungerar som den misslyckas och höjer användarfelet *Svarsinnehåll är inte ett giltigt JObject*.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| typ   | Typegenskapen måste anges till: **AzureFunction** | ja |
| url till funktionsapp | URL för Azure Function App. Formatet `https://<accountname>.azurewebsites.net`är . Den här URL:en är värdet under **URL-avsnittet** när du visar din funktionsapp i Azure-portalen  | ja |
| funktionsnyckel | Åtkomstnyckel för Azure-funktionen. Klicka på avsnittet **Hantera** för respektive funktion och kopiera antingen **funktionsnyckeln** eller **värdnyckeln**. Läs mer här: [Azure Functions HTTP-utlösare och bindningar](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Azure-funktionsaktivitet

| **Egenskap**  | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| namn  | Namnet på den verksamhet som är på gång  | String | ja |
| typ  | Typ av aktivitet är "AzureFunctionActivity" | String | ja |
| länkad tjänst | Azure-funktionslänkade tjänsten för motsvarande Azure Function App  | Länkad tjänstreferens | ja |
| funktionsnamn  | Namnet på funktionen i Azure Function App som den här aktiviteten anropar | String | ja |
| metod  | REST API-metod för funktionsanropet | Typer som stöds av sträng: "GET", "POST", "PUT"   | ja |
| sidhuvud  | Rubriker som skickas till begäran. Om du till exempel vill ange språk och skriva på en begäran: "rubriker": { "Acceptera-språk": "en-us", "Content-Type": "application/json" } | Sträng (eller uttryck med resultType av sträng) | Inga |
| body  | som skickas tillsammans med begäran till funktion api-metoden  | Sträng (eller uttryck med resultType av sträng) eller objekt.   | Krävs för PUT/POST-metoder |
|   |   |   | |

Se schemat för nyttolasten för begäran i  [schemaavsnittet Begär nyttolastschema.](control-flow-web-activity.md#request-payload-schema)

## <a name="routing-and-queries"></a>Routning och frågor

Azure-funktionsaktiviteten stöder **routning**. Om din Azure-funktion till exempel `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`har `functionName` slutpunkten är `<functionName>/<value>`att använda i Azure-funktionsaktiviteten . Du kan parameterisera den här `functionName` funktionen för att ge önskad vid körning.

Azure-funktionsaktiviteten stöder också **frågor**. En fråga måste inkluderas som `functionName`en del av . Till `HttpTriggerCSharp` exempel när funktionsnamnet är och frågan som `name=hello`du vill ta `functionName` med är , `HttpTriggerCSharp?name=hello`kan du konstruera i Azure-funktionsaktiviteten som . Den här funktionen kan parameteriseras så att värdet kan bestämmas vid körning.

## <a name="timeout-and-long-running-functions"></a>Timeout och långvariga funktioner

Azure Functions time out efter 230 `functionTimeout` sekunder oavsett den inställning du har konfigurerat i inställningarna. Mer information finns i [den här artikeln](../azure-functions/functions-versions.md#timeout). För att komma runt detta beteende, följ ett async mönster eller använda varaktiga funktioner. Fördelen med varaktiga funktioner är att de erbjuder sin egen tillståndsspårningsmekanism, så du behöver inte implementera din egen.

Läs mer om varaktiga funktioner i [den här artikeln](../azure-functions/durable/durable-functions-overview.md). Du kan ställa in en Azure-funktionsaktivitet för att anropa funktionen Varaktig funktion, som returnerar ett svar med en annan URI, till exempel [det här exemplet](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Eftersom `statusQueryGetUri` returnerar HTTP-status 202 medan funktionen körs kan du avsöka funktionens status med hjälp av en webbaktivitet. Ställ bara in en `url` webbaktivitet `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`med fältet inställt på . När funktionen Varaktig funktion är klar kommer utdata för funktionen att vara utdata från webbaktiviteten.


## <a name="sample"></a>Exempel

Du kan hitta ett exempel på en Data Factory som använder en Azure-funktion för att extrahera innehållet i en tar-fil [här](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Nästa steg

Läs mer om aktiviteter i Data Factory i [pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md).
