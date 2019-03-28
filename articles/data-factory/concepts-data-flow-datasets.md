---
title: Azure Data Factory mappning av datauppsättningar för flödet av Data
description: Azure Data Factory mappning dataflöde har specifik datauppsättning kompatibilitet
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: efb82c57a5620ef3eace8b39f6f27f2286202f84
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521847"
---
# <a name="mapping-data-flow-datasets"></a>Mappning av datauppsättningar för flödet av Data

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Datauppsättningar är en Data Factory-konstruktion som definierar formen på de data som du arbetar med i din pipeline. I dataflöde kräver rad- och data på ett noggrant detaljerade datauppsättningsdefinitionen. Datauppsättningar som används i pipelines för kontrollflöden kräver inte samma djup förståelse av data.

Datauppsättningar i dataflöde används i källan och mottagaren transformationer. De används för att definiera scheman för grundläggande data. Om du inte har schemat i dina data, kan du ange schemat Drift på för din källa och mottagare. Du får med schema som definierats i datauppsättningen, relaterade datatyper, dataformat, plats och anslutningsinformationen från den associerade länkade tjänsten. Metadata från datauppsättningarna som visas i käll-transformeringen som källa ”projektion”. Schemat i datauppsättningen representerar den fysiska datatypen och forma medan projektionen omvandling källa representerar datarepresentation för flödet av data med definierade namn och typer.

## <a name="dataset-types"></a>Datauppsättningstyperna

För närvarande i dataflödet hittar du fyra datauppsättningstyper:

* Azure SQL-databas
* Azure SQL DW
* Parquet (från ADLS & Blob)
* Avgränsad Text (från ADLS & Blob)

Data flow datauppsättningar separat den *källtyp* från den *länkade tjänsten anslutningstypen*. Vanligtvis i Data Factory, Välj anslutningstyp (Blob, ADLS osv.) och definierar vilken typ av fil i datauppsättningen. Inuti dataflöde, ska du välja de typer av datakällor som kan associeras med olika anslutningstyper för länkad tjänst.

![Käll-omvandling alternativ](media/data-flow/dataset1.png "källor")

## <a name="data-flow-compatible-datasets"></a>Data Flow kompatibla datauppsättningar

När du skapar en ny datauppsättning, det finns en kryssruta med etiketten ”Data flöda kompatibel” längst upp till höger på panelen. Du klickar på knappen filtrerar bara de datauppsättningar som kan användas med Data flödar. 

## <a name="import-schemas"></a>Importera scheman

När du importerar schemat för dataflöde datauppsättningar, visas en Importschema-knappen. Du klickar på knappen visas du två alternativ: Importera från källan eller importera från en lokal fil. I de flesta fall kommer du importera schemat direkt från källan. Men om du har ett befintligt schema-fil (Parquet-filer eller CSV med rubriker) kan peka du på att lokala filer och Data Factory definierar schemat baserat på den schemafilen.

## <a name="create-new-table"></a>Skapa ny tabell

I dataflöde, kan du be ADF om du vill skapa en ny tabelldefinition i måldatabasen genom att ange en datauppsättning i transformeringen mottagare som har ett nytt tabellnamn. Klicka på ”Redigera” nedan tabellnamnet och ange ett nytt tabellnamn i SQL-datauppsättning. Omvandling mottagare aktivera sedan ”Tillåt schemat Drift”. Seth ”Importschema” inställningen ingen.

![Källans omvandling schema](media/data-flow/dataset2.png "SQL-schemat")

## <a name="choose-your-type-of-data-first"></a>Välj typ av data först

### <a name="delimited-text"></a>Avgränsad text

I datauppsättningen avgränsad text, ställer du avgränsaren som ska hantera antingen enkel avgränsare (”\t” för TSV ”,”, för CSV-fil, ' |'...) eller flera tecken för avgränsare. Huvudet rad växla och går sedan till transformeringen av källa för att automatiskt identifiera datatyper. Om du använder en avgränsad Text-datauppsättning för att placera data i en mottagare kan bara välja en målmapp. Du kan definiera namnet på utdatafilerna i inställningarna för mottagare.

### <a name="parquet"></a>Parquet

Använd Parquet som tillfälliga datauppsättning standardtypen i ADF dataflöden. Parquet lagrar omfattande metadataschema tillsammans med data.

### <a name="database-types"></a>Typer av databaser

Du kan välja Azure SQL DB eller Azure SQL DW.

För övriga ADF datauppsättningen, Använd Kopieringsaktiviteten för att mellanlagra dina data. Det finns en mall för ADF i mallgalleriet att hjälpa dig att skapa det här mönstret.

![Kopiera mellanlagring](media/data-flow/templatedf.png "kopiera mellanlagring")

## <a name="choose-your-connection-type"></a>Välj vilken typ av anslutning

Om du använder Parquet eller avgränsad Text datauppsättningar, kan du sedan välja platsen för dina data: ADLS eller Blob.

## <a name="next-steps"></a>Nästa steg

Börja med att [skapar ett nytt dataflöde](data-flow-create.md) och Lägg till en transformering av källa. Konfigurera sedan datauppsättningen för källan.

Använd den [Kopieringsaktiviteten](copy-activity-overview.md) hämta data från alla ADF datakälla och mellanlagra dem i ADLS- eller Blob för åtkomst av dataflöde.

