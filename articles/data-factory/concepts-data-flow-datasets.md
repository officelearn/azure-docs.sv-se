---
title: Azure Data Factory mappning av datauppsättningar för flödet av Data
description: Azure Data Factory mappning dataflöde har sepecific datauppsättning kompatibilitet
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ccf4273489d739bb9b0d802b79944efefcd02ff4
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331291"
---
# <a name="mapping-data-flow-datasets"></a>Mappning av datauppsättningar för flödet av Data

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Datauppsättningar är en Data Factory-konstruktion som definierar formen på de data som du arbetar med i din pipeline. I dataflödet kräver rad- och data på en mycket mer noggrant detaljerade definition än vad som krävs av datauppsättningar i pipeline Kontrollflöde.

Använda datauppsättningar i Flow datakälla och mottagare transformeringar för att definiera grundläggande dataschemat. Om du inte har schemat i dina data, kan du ange schemat Drift på för din källa och mottagare. Du får med schema som definierats i datauppsättningen, relaterade datatyper, dataformat, plats och anslutningsinformationen från den associerade länkade tjänsten.

För närvarande i dataflödet hittar du fyra datauppsättningar:

* Azure SQL-databas
* Azure SQL DW
* Parquet
* Avgränsad Text

Data flow datauppsättningar avgränsa källan *typ* från den länkade tjänsttypen för anslutningen. Vanligtvis i Data Factory, Välj anslutningstyp (Blob, ADLS osv.) och definierar vilken typ av fil i datauppsättningen. Inuti dataflöde, ska du välja de typer av datakällor som kan associeras med olika anslutningstyper för länkad tjänst.

![Käll-omvandling alternativ](media/data-flow/dataset1.png "källor")

När du skapar en ny datauppsättning, det finns en kryssruta med etiketten ”Data flöda kompatibel” längst upp till höger på panelen. Du klickar på knappen filtrerar bara de datauppsättningar som kan användas med Data flödar. 

Importera scheman

När du importerar schemat för dataflöde datauppsättningar, visas en Importschema-knappen. Du klickar på knappen visas du två alternativ: Importera från källan eller importera från en lokal fil. I de flesta fall kommer du importera schemat direkt från källan. Om du har en befintlig schemafil kan du peka på den lokala filen och Data Factory definierar schemat baserat på den schemafilen.

