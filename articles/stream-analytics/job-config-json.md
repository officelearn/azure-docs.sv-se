---
title: Azure Stream Analytics JobConfig.json fält
description: I den här artikeln visas de fält som stöds för filen Azure Stream Analytics JobConfig.json som används för att skapa jobb i Visual Studio-kod.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617962"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.json fält

Följande fält stöds i filen *JobConfig.json* som används för att [skapa ett Azure Stream Analytics-jobb med Visual Studio Code](quick-create-vs-code.md).

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
|DataLocale (datalocale)|sträng|Inga|Datalokalen för dataanalysjobbet. Värde ska vara namnet på ett stöds. Standardvärdet är "en-US" om ingen har angetts.|
|OutputErrorPolicy|sträng|Inga|Anger vilken princip som ska tillämpas på händelser som kommer fram till utdata och inte kan skrivas till den externa lagringen på grund av att den är felaktig (saknade kolumnvärden, kolumnvärden av fel typ eller storlek). - Stoppa eller släpp|
|HändelserLateArrivalMaxDelayInSeconds|heltal|Inga|Den maximala acceptabla fördröjningen i sekunder där händelser som kommer för sent kan inkluderas. Intervallet stöds är -1 till 1814399 (20.23:59:59 dagar) och -1 används för att ange vänta på obestämd tid. Om egenskapen är frånvarande tolkas den ha värdet -1.|
|HändelserOutOfOrderMaxDelayInSeconds|heltal|Inga|Den maximala acceptabla fördröjningen i sekunder där händelser utanför ordningen kan justeras för att vara tillbaka i ordning.|
|HändelserOutOfOrderPolicy|sträng|Inga|Anger vilken princip som ska tillämpas på händelser som kommer i oordning i indatahändelseströmmen. - Justera eller släpp|
|StreamingUnits|heltal|Ja|Anger antalet strömningsenheter som direktuppspelningsjobbet använder.|
|KompatibilitetNivå|sträng|Inga|Styr vissa körningsbeteenden för direktuppspelningsjobbet. - Godtagbara värden är "1,0", "1,1", "1,2"|
|UseSystemAssigneradidentitet|boolean|Inga|Ange true så att det här jobbet kan kommunicera med andra Azure-tjänster som sig själv med hjälp av en hanterad Azure Active Directory-identitet.|
|GlobalStorage.AccountName|sträng|Inga|Globalt lagringskonto används för att lagra innehåll som är relaterat till ditt dataflödesanalysjobb, till exempel ögonblicksbilder av SQL-referensdata.|
|GlobalStorage.AccountKey|sträng|Inga|Motsvarande nyckel för globalt lagringskonto.|
|DataSourceCredentialDomain|sträng|Inga|Reserverad egenskap för lokal lagring av autentiseringsuppgifter.|
|Skripttyp|sträng|Ja|Reserverad egenskap för att ange typen av den här källfilen. Godtagbart värde är "JobConfig" för JobConfig.json.|
|Taggar|JSON nyckel-värde par|Inga|Taggar är namn-/värdepar som gör att du kan kategorisera resurser och visa samlad fakturering genom att använda samma tagg på flera resurser och resursgrupper. Taggnamn är skiftlägesokänsliga och taggvärden är skiftlägeskänsliga.|

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Stream Analytics-jobb i Visual Studio-kod](quick-create-vs-code.md)
* [Testa Stream Analytics-frågor lokalt med exempeldata med Visual Studio-kod](visual-studio-code-local-run.md)
* [Test Stream Analytics-frågor lokalt mot livestreamindata med hjälp av Visual Studio-kod](visual-studio-code-local-run-live-input.md)
*[Distribuera ett Azure Stream Analytics-jobb med CI/CD npm-paketet](setup-cicd-vs-code.md)