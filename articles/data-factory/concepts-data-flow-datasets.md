---
title: Azure Data Factory mappning av datauppsättningar för flödet av Data
description: Azure Data Factory mappning dataflöde har sepecific datauppsättning kompatibilitet
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: c704fdba22138eb8e4610a7bc34bd4c2cc98f4ca
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432098"
---
# <a name="mapping-data-flow-datasets"></a>Mappning av datauppsättningar för flödet av Data

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Datauppsättningar är en Data Factory-konstruktion som definierar formen på de data som du arbetar med i din pipeline. I dataflöde kräver rad- och data på ett noggrant detaljerade datauppsättningsdefinitionen. Datauppsättningar som används i pipelines för kontrollflöden kräver inte samma djup förståelse av data.

Datauppsättningar i dataflöde används i källan och mottagaren transformationer. De används för att definiera scheman för grundläggande data. Om du inte har schemat i dina data, kan du ange schemat Drift på för din källa och mottagare. Du får med schema som definierats i datauppsättningen, relaterade datatyper, dataformat, plats och anslutningsinformationen från den associerade länkade tjänsten. Metadata från datauppsättningarna som visas i käll-transformeringen som källa ”projektion”.

## <a name="dataset-types"></a>Datauppsättningstyperna

För närvarande i dataflödet hittar du fyra datauppsättningstyper:

* Azure SQL-databas
* Azure SQL DW
* Parquet (från ADLS & Blob)
* Avgränsad Text (från ADLB & Blob)

Data flow datauppsättningar separat den *källtyp* från den *länkade tjänsten anslutningstypen*. Vanligtvis i Data Factory, Välj anslutningstyp (Blob, ADLS osv.) och definierar vilken typ av fil i datauppsättningen. Inuti dataflöde, ska du välja de typer av datakällor som kan associeras med olika anslutningstyper för länkad tjänst.

![Käll-omvandling alternativ](media/data-flow/dataset1.png "källor")

## <a name="data-flow-compatible-datasets"></a>Data Flow kompatibla datauppsättningar

När du skapar en ny datauppsättning, det finns en kryssruta med etiketten ”Data flöda kompatibel” längst upp till höger på panelen. Du klickar på knappen filtrerar bara de datauppsättningar som kan användas med Data flödar. 

## <a name="import-schemas"></a>Importera scheman

När du importerar schemat för dataflöde datauppsättningar, visas en Importschema-knappen. Du klickar på knappen visas du två alternativ: Importera från källan eller importera från en lokal fil. I de flesta fall kommer du importera schemat direkt från källan. Men om du har ett befintligt schema-fil (Parquet-filer eller CSV med rubriker) kan peka du på att lokala filer och Data Factory definierar schemat baserat på den schemafilen.

## <a name="next-steps"></a>Nästa steg

Börja med att [skapar ett nytt dataflöde](data-flow-create.md) och Lägg till en transformering av källa. Konfigurera sedan datauppsättningen för källan.

