---
title: Azure Stream Analytics JobConfig. JSON-fält
description: Den här artikeln innehåller de fält som stöds för den Azure Stream Analytics JobConfig. JSON-fil som används för att skapa jobb i Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617962"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig. JSON-fält

Följande fält stöds i filen *JobConfig. JSON* som används för att [skapa ett Azure Stream Analytics jobb med Visual Studio Code](quick-create-vs-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Namn|Typ|Krävs|Värde|
|----|----|--------|-----|
|DataLocale|sträng|Nej|Data språket för Stream Analytics-jobbet. Värdet ska vara namnet på ett stödda. Standardvärdet är "en-US" om inget anges.|
|OutputErrorPolicy|sträng|Nej|Anger den princip som ska tillämpas på händelser som kommer till utdata och som inte kan skrivas till den externa lagringen på grund av att de inte är felformaterade (kolumn värden som saknas, kolumn värden av fel typ eller storlek). -Stoppa eller släpp|
|EventsLateArrivalMaxDelayInSeconds|heltal|Nej|Den maximala tillåtna fördröjningen i sekunder där händelser som kommer sent kan tas med. Intervallet som stöds är-1 till 1814399 (20.23:59:59 dagar) och-1 används för att ange vänte tid. Om egenskapen saknas tolkas den som att ha värdet-1.|
|EventsOutOfOrderMaxDelayInSeconds|heltal|Nej|Högsta tillåtna fördröjning i sekunder där händelser som ligger utanför ordningen kan justeras så att de är i ordning.|
|EventsOutOfOrderPolicy|sträng|Nej|Anger den princip som ska användas för händelser som anländer i indata-händelsens data ström. -Justera eller släpp|
|StreamingUnits|heltal|Ja|Anger antalet enheter för strömning som används av direkt uppspelnings jobbet.|
|CompatibilityLevel|sträng|Nej|Kontrollerar vissa körnings beteenden i direkt uppspelnings jobbet. -Acceptabla värden är "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|boolesk|Nej|Ange sant om du vill att det här jobbet ska kommunicera med andra Azure-tjänster som en hanterad Azure Active Directory identitet.|
|GlobalStorage. AccountName|sträng|Nej|Globalt lagrings konto används för att lagra innehåll som är relaterat till Stream Analytics-jobbet, till exempel ögonblicks bilder av SQL-Datadata.|
|GlobalStorage. AccountKey|sträng|Nej|Motsvarande nyckel för globalt lagrings konto.|
|DataSourceCredentialDomain|sträng|Nej|Reserverad egenskap för lokal lagring för autentiseringsuppgifter.|
|ScriptType|sträng|Ja|Reserverad egenskap som anges som typ för den här käll filen. Acceptabelt värde är "JobConfig" för JobConfig. JSON.|
|Taggar|JSON-nyckel/värde-par|Nej|Taggar är namn/värde-par som låter dig kategorisera resurser och Visa konsol IDE rad fakturering genom att tillämpa samma tagg på flera resurser och resurs grupper. Taggnamn är Skift läges känsliga och märkes värden är Skift läges känsliga.|

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-vs-code.md)
* [Testa Stream Analytics frågor lokalt med exempel data med Visual Studio Code](visual-studio-code-local-run.md)
* [Testa Stream Analytics frågor lokalt mot Real tids indata med hjälp av Visual Studio Code](visual-studio-code-local-run-live-input.md)
*[distribuera ett Azure Stream Analytics jobb med CI/CD NPM-paketet](setup-cicd-vs-code.md)