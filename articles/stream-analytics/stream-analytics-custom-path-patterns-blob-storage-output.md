---
title: Anpassat datum/tid-sökvägsmönster för Azure Stream Analytics blob storage-utdata (förhandsversion)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: da29c6bd8ddc1e2f62a78fb683df5e1784141722
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452572"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Anpassat datum/tid-sökvägsmönster för Azure Stream Analytics blob storage-utdata (förhandsversion)

Azure Stream Analytics stöder anpassade datum och tid-format specificerare i sökvägen till filen för blob storage-utdata. Anpassade DateTime-sökvägsmönster kan du ange utdataformat som överensstämmer med Hive-direktuppspelning konventioner, vilket gör Azure Stream Analytics kan skicka data till Azure HDInsight och Azure Databricks för nedströms bearbetning. Anpassat datum/tid-sökvägsmönster är enkelt implementeras med hjälp av den `datetime` nyckelord i fältet Sökvägsprefix för din blob-utdata, tillsammans med en formatangivelse. Till exempel `{datetime:yyyy}`.

Använd den här länken för [Azure-portalen](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) att växla flaggan funktionen som gör att de anpassade DateTime sökvägsmönster för förhandsversionen för blob storage-utdata. Den här funktionen kommer att aktiveras snart i main-portalen.

## <a name="supported-tokens"></a>Token som stöds

Följande format specificerare token kan användas fristående eller i kombination för att uppnå anpassade DateTime-format:

|Formatspecifierare   |Beskrivning   |Resultat på exempel tid 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|År som ett fyrsiffrigt tal|2018|
|{datetime:MM}|Månad från 01 till 12|01|
|{datetime:M}|Månad från 1 till 12|1|
|{datetime:dd}|Dag från 01-31|02|
|{datetime:d}|Dag från 1 till 12|2|
|{datetime:HH}|Timme med 24-timmarsformat, från 00 och 23|10|
|{datetime:mm}|Minuter från 00-24|06|
|{datetime:m}|Minuter från 0 till och med 24|6|
|{datetime:ss}|Sekunder från 00 60|08|

Om du inte vill använda anpassade DateTime mönster du kan lägga till {date} och/eller {time} token till Sökvägsprefix att generera en listruta med inbyggda DateTime-format.

![Stream Analytics gamla DateTime-format](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>Utökningsbarhet och begränsningar

Du kan använda så många token `{datetime:<specifier>}`, som du precis som i mönstret sökväg tills du når gränsen Sökvägsprefix tecken. Formatet specificerare kan inte kombineras i en enskild token utöver kombinationer som redan visas genom att listrutorna för datum och tid. 

För en sökväg partition av `logs/MM/dd`:

|Giltigt uttryck   |Ogiltigt uttryck   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Du kan använda samma formatspecifierare flera gånger i den sökväg som Prefix. Token ska upprepas varje gång.

## <a name="hive-streaming-conventions"></a>Konventioner för hive-direktuppspelning

Anpassad sökvägsmönster för blob storage kan användas med konventionen Hive-direktuppspelning, som förväntar sig mappar ska förses med `column=` i mappnamnet.

Till exempel `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Anpassade utdata eliminerar behovet av att ändra tabeller och manuellt lägga till partitioner i portdata mellan Azure Stream Analytics och Hive. I stället kan många mappar läggas till automatiskt med:

```
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exempel

Skapa ett lagringskonto, en resursgrupp, ett Stream Analytics-jobb och en indatakälla enligt den [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) snabbstartsguiden. Använda samma exempeldata som används i snabbstartsguiden även tillgänglig på [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Skapa en blob-utdatamottagare med följande konfiguration:

![Skapa blob utdatamottagare för Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Fullständig sökväg mönstret är följande:

```
year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}
```

När du startar jobbet, skapas en mappstruktur baserat på sökvägsmönster i blob-behållare. Du kan öka detaljnivån till nivån dag.

![Stream Analytics blob-utdata med anpassade sökvägar](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
