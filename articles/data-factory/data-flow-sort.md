---
title: Mappa omformning av dataflödessortering
description: Azure Data Factory Mapping Data Sortera omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930215"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory Data Flow Sort Transformations



![Sorteringsinställningar](media/data-flow/sort.png "Sortera")

Med sorteringsomformningen kan du sortera inkommande rader i den aktuella dataströmmen. De utgående raderna från sorteringsomvandlingen följer därefter de ordningsregler som du anger. Du kan välja enskilda kolumner och sortera dem ASC eller DEC med hjälp av pilindikatorn bredvid varje fält. Om du behöver ändra kolumnen innan du använder sorteringen klickar du på "Beräknade kolumner" för att starta uttrycksredigeraren. Detta ger en möjlighet att skapa ett uttryck för sorteringsåtgärden i stället för att bara använda en kolumn för sorteringen.

## <a name="case-insensitive"></a>Skiftlägesokänsligt
Du kan aktivera "Ärendesokänsligt" om du vill ignorera skiftläge när du sorterar sträng- eller textfält.

"Sortera endast inom partitioner" utnyttjar Spark-datapartitionering. Genom att bara sortera inkommande data i varje partition kan dataflöden sortera partitionerade data i stället för att sortera hela dataströmmen.

Var och en av sorteringsvillkoren i sorteringsomvandlingen kan ordnas om. Så om du behöver flytta en kolumn högre i sorteringsprioriteten tar du tag i den raden med musen och flyttar den högre eller lägre i sorteringslistan.

Partitioneringseffekter på Sortera

ADF Data Flow körs på stordata Spark-kluster med data distribuerade över flera noder och partitioner. Det är viktigt att ha detta i åtanke när du utformar dataflödet om du är beroende av sortera transformering för att hålla data i samma ordning. Om du väljer att partitionera om dina data i en efterföljande omvandling kan du förlora din sortering på grund av att omfördelning av data.

## <a name="next-steps"></a>Nästa steg

Efter sortering kanske du vill använda [den sammanlagda omvandlingen](data-flow-aggregate.md)
