---
title: Azure-funktion-aktiviteten i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder Azure Function-aktiviteten för att köra en Azure-funktion i Data Factory-pipeline
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: ee99733440d74424f98a2ed16de83c88bae53ff1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321797"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Function-aktivitet i Azure Data Factory

Azure Function-aktiviteten kan du köra [Azure Functions](../azure-functions/functions-overview.md) i Data Factory-pipeline. Om du vill köra en Azure-funktion, måste du skapa en länkad tjänst-anslutning och en aktivitet som anger den Azure-funktion som du planerar att köra.

Titta på följande videoklipp för ett 8-minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure funktion länkad tjänst

Returtypen för Azure-funktionen måste vara en giltig `JObject`. (Tänk på att [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) är *inte* en `JObject`.) Någon returtyp än `JObject` misslyckas och genererar en allmän användarfel *fel anropande endpoint*.

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| typ   | Type-egenskapen måste anges till: **AzureFunction** | ja |
| funktionen app-url | URL för Azure Functions-App. Formatet är `https://<accountname>.azurewebsites.net`. Denna URL är värdet under **URL** avsnittet när du visar Funktionsappen i Azure portal  | ja |
| Funktionsnyckel | Åtkomstnyckel för Azure-funktion. Klicka på den **hantera** för respektive funktion och kopiera antingen den **funktionsnyckel** eller **värdnyckeln**. Läs mer här: [Azure Functions HTTP-utlösare och bindningar](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Function-aktivitet

| **Egenskap**  | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| namn  | Namnet på aktiviteten i pipelinen  | Sträng | ja |
| typ  | Aktiviteten är 'AzureFunctionActivity' | Sträng | ja |
| Länkad tjänst | Azure-funktion som är länkad tjänst för motsvarande Azure Function-App  | Länkade tjänstreferensen | ja |
| Funktionsnamn  | Namnet på funktionen i Azure Function-App som anropar den här aktiviteten | Sträng | ja |
| metod  | REST API-metoden för funktionsanropet | Sträng typer som stöds: ”HÄMTA”, ”POST”, ”PUT”   | ja |
| sidhuvud  | Rubriker som skickas till begäran. Till exempel vill ange språk och typ för en begäran: ”headers”: {”Accept-språk” ”: en-us”, ”Content-Type”: ”application/json”} | Sträng (eller ett uttryck med resultType av sträng) | Nej |
| brödtext  | texten som skickas tillsammans med begäran till funktionen api-metoden  | Sträng (eller ett uttryck med resultType av sträng) eller ett objekt.   | Krävs för PUT/POST-metoder |
|   |   |   | |

Se schemat för nyttolasten i begäran i [begäran nyttolast schemat](control-flow-web-activity.md#request-payload-schema) avsnittet.

## <a name="more-info"></a>Mer information

Aktivitet för Azure-funktionen stöder **routning**. Exempel: om din app använder följande routning - `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` - kommer `functionName` är `functionName/{value}`, som du kan Parameterisera ange önskad `functionName` vid körning.

Aktivitet för Azure-funktionen stöder även **frågor**. En fråga måste vara en del av den `functionName` , till exempel `HttpTriggerCSharp2?name=hello` – där den `function name` är `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Nästa steg

Mer information om aktiviteter i Data Factory i [Pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md).
