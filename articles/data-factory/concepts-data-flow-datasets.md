---
title: Azure Data Factory mappning av datauppsättningar för flödet av Data
description: Azure Data Factory mappning dataflöde har sepecific datauppsättning kompatibilitet
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408756"
---
# <a name="mapping-data-flow-datasets"></a>Mappning av datauppsättningar för flödet av Data

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Datauppsättningar är en Data Factory-konstruktion som definierar formen på de data som du arbetar med i din pipeline. I dataflöde kräver rad- och data på ett noggrant detaljerade datauppsättningsdefinitionen. Datauppsättningar som används i pipelines för kontrollflöden kräver inte samma djup förståelse av data.

Datauppsättningar i Flow datakälla och mottagare transformeringar används för att definiera grundläggande dataschemat. Om du inte har schemat i dina data, kan du ange schemat Drift på för din källa och mottagare. Du får med schema som definierats i datauppsättningen, relaterade datatyper, dataformat, plats och anslutningsinformationen från den associerade länkade tjänsten.

## <a name="dataset-types"></a>Datauppsättningstyperna

För närvarande i dataflödet hittar du fyra datauppsättningstyper:

* Azure SQL-databas
* Azure SQL DW
* Parquet
* Avgränsad Text

Data flow datauppsättningar avgränsa källan *typ* från den länkade tjänsttypen för anslutningen. Vanligtvis i Data Factory, Välj anslutningstyp (Blob, ADLS osv.) och definierar vilken typ av fil i datauppsättningen. Inuti dataflöde, ska du välja de typer av datakällor som kan associeras med olika anslutningstyper för länkad tjänst.

![Käll-omvandling alternativ](media/data-flow/dataset1.png "källor")

## <a name="data-flow-compatible-datasets"></a>Data Flow kompatibla datauppsättningar

När du skapar en ny datauppsättning, det finns en kryssruta med etiketten ”Data flöda kompatibel” längst upp till höger på panelen. Du klickar på knappen filtrerar bara de datauppsättningar som kan användas med Data flödar. 

## <a name="import-schemas"></a>Importera scheman

När du importerar schemat för dataflöde datauppsättningar, visas en Importschema-knappen. Du klickar på knappen visas du två alternativ: Importera från källan eller importera från en lokal fil. I de flesta fall kommer du importera schemat direkt från källan. Men om du har ett befintligt schema-fil (Parquet-filer eller CSV med rubriker) kan peka du på att lokala filer och Data Factory definierar schemat baserat på den schemafilen.

