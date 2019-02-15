---
title: Azure Data Factory mappning Dataomvandling Flow källa
description: Azure Data Factory mappning Dataomvandling Flow källa
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35f4e794caf84aba860b98e68eadcdcd88e77952
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272118"
---
# <a name="azure-data-factory-mapping-data-flow-source-transformation"></a>Azure Data Factory mappning Dataomvandling Flow källa

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Käll-transformeringen konfigurerar en datakälla som du vill använda för att hämta data till ditt dataflöde. Du kan ha fler än 1 källa transformera i en enda dataflöde. Alltid börja utforma dina Data flödar med källan.

> [!NOTE]
> Varje dataflöde kräver minst en transformering av källa. Lägga till så många ytterligare källor som du behöver

![Käll-omvandling alternativ](media/data-flow/source.png "källa")

Källan dataflöde måste vara kopplad till exakt en ADF datamängd som definierar formen och var dina data att skriva till eller läsa från.

## <a name="data-flow-staging-areas"></a>Dataflöde Mellanlagringsområden

Dataflöde för ADF har rad med för att se till 5 huvudsakliga ”mellanlagring” områden i Azure för att utföra din dataomvandlingar: Azure-Blob, ADLS generation 1, ADLS Gen 2, Azure SQL DB och Azure SQL DW. ADF har åtkomst till nästan 80 olika interna anslutningar, så för att inkludera dessa andra datakällor i din dataflöde, först mellanlagra data i en av dessa fem primära dataflöde mellanlagringsområden först med hjälp av Kopieringsaktiviteten:

## <a name="options"></a>Alternativ

### <a name="allow-schema-drift"></a>Tillåt schemat drift
Välj Tillåt schemat Drift om källkolumner ändras ofta. Den här inställningen gör att alla inkommande fält från källan kan passera transformeringar som mottagaren.

### <a name="fail-if-columns-in-the-dataset-are-not-found"></a>Misslyckas om kolumner i datauppsättningen inte hittas
Välj alternativet för att framtvinga en käll-schemavalideringen som misslyckas ditt dataflöde om kolumner som förväntas från källan inte finns.

### <a name="sampling"></a>Samling
Använda Sampling för att begränsa antalet rader från källan.  Detta är användbart när du behöver bara ett exempel på dina källdata för testning och felsökning.

### <a name="define-schema"></a>Definiera Schema

![Käll-omvandling](media/data-flow/source2.png "datakällan 2")

### <a name="you-can-modify-the-name-of-the-source-columns-and-their-associated-data-types"></a>Du kan ändra namnet på källkolumner och deras associerade datatyper

Du bör definiera datatyperna för varje fält här i käll-transformering inte i datauppsättningen för källan filtyper som inte är starkt typifierad (d.v.s. flata filer istället för Parquet-filer).

Om du inte ser kolumnnamn och typer i dina dataflöde, är det sannolikt eftersom du inte har definierat dem i avsnittet definierar schemat för mottagaren. Du behöver bara göra detta om du inte använder Data flöda schemat Drift-hantering.

Här i ”Definiera schemat” är fliken på käll-transformeringen där du kan ange de datatyper och format:

![Käll-omvandling](media/data-flow/source003.png "datatyper")

### <a name="optimize"></a>Optimera

![Käll-partitioner](media/data-flow/sourcepart.png "partitionering")

På fliken Optimize för käll-omvandling visas en ytterligare partitionering typ med namnet ”källa”. Detta kommer endast Ljus upp när du har valt Azure SQL DB som källan. Det beror på att ADF kommer vill parallellisera anslutningar för att köra stora frågor mot Azure SQL DB-källa.

Partitionera data på din SQL DB-källan är valfritt, men det är användbart för stora frågor. Du kan välja mellan två alternativ:

### <a name="column"></a>Kolumn

Markera en kolumn till partition på från din källtabellen. Du måste också ange det maximala antalet anslutningar.

### <a name="query-condition"></a>Frågevillkoret

Du kan välja att partitionera anslutningar baserat på en fråga. För det här alternativet kan du enkelt uttryckt i innehållet i en WHERE-predikat. T.ex. år > 1980
