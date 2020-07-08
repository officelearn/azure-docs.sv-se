---
title: Azure Stream Analytics JobConfig.jspå fält
description: Den här artikeln innehåller de fält som stöds för Azure Stream Analytics JobConfig.jspå filen som används för att skapa jobb i Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0676b987725a33049d9da3256bdd4e6dc8028d00
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045186"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.jspå fält

Följande fält stöds i *JobConfig.jspå* filen som används för att [skapa ett Azure Stream Analytics jobb med Visual Studio Code](quick-create-vs-code.md).

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

|Name|Typ|Obligatorisk|Värde|
|----|----|--------|-----|
|DataLocale|sträng|No|Data språket för Stream Analytics-jobbet. Värdet ska vara namnet på ett stödda. Standardvärdet är "en-US" om inget anges.|
|OutputErrorPolicy|sträng|No|Anger den princip som ska tillämpas på händelser som kommer till utdata och som inte kan skrivas till den externa lagringen på grund av att de inte är felformaterade (kolumn värden som saknas, kolumn värden av fel typ eller storlek). -Stoppa eller släpp|
|EventsLateArrivalMaxDelayInSeconds|heltal|No|Den maximala tillåtna fördröjningen i sekunder där händelser som kommer sent kan tas med. Intervallet som stöds är-1 till 1814399 (20.23:59:59 dagar) och-1 används för att ange vänte tid. Om egenskapen saknas tolkas den som att ha värdet-1.|
|EventsOutOfOrderMaxDelayInSeconds|heltal|No|Högsta tillåtna fördröjning i sekunder där händelser som ligger utanför ordningen kan justeras så att de är i ordning.|
|EventsOutOfOrderPolicy|sträng|No|Anger den princip som ska användas för händelser som anländer i indata-händelsens data ström. -Justera eller släpp|
|StreamingUnits|heltal|Ja|Anger antalet enheter för strömning som används av direkt uppspelnings jobbet.|
|CompatibilityLevel|sträng|No|Kontrollerar vissa körnings beteenden i direkt uppspelnings jobbet. -Acceptabla värden är "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|boolean|No|Ange sant om du vill att det här jobbet ska kommunicera med andra Azure-tjänster som en hanterad Azure Active Directory identitet.|
|GlobalStorage. AccountName|sträng|No|Globalt lagrings konto används för att lagra innehåll som är relaterat till Stream Analytics-jobbet, till exempel ögonblicks bilder av SQL-Datadata.|
|GlobalStorage. AccountKey|sträng|No|Motsvarande nyckel för globalt lagrings konto.|
|DataSourceCredentialDomain|sträng|No|Reserverad egenskap för lokal lagring för autentiseringsuppgifter.|
|ScriptType|sträng|Ja|Reserverad egenskap som anges som typ för den här käll filen. Acceptabelt värde är "JobConfig" för JobConfig.jspå.|
|Taggar|JSON-nyckel/värde-par|No|Taggar är namn/värde-par som låter dig kategorisera resurser och Visa konsol IDE rad fakturering genom att tillämpa samma tagg på flera resurser och resurs grupper. Taggnamn är Skift läges känsliga och märkes värden är Skift läges känsliga.|

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-vs-code.md)
* [Testa Stream Analytics frågor lokalt med exempel data med Visual Studio Code](visual-studio-code-local-run.md)
* [Testa Stream Analytics frågor lokalt mot Real tids indata med hjälp av Visual Studio Code](visual-studio-code-local-run-live-input.md) 
* [Distribuera ett Azure Stream Analytics jobb med CI/CD NPM-paket](setup-cicd-vs-code.md)