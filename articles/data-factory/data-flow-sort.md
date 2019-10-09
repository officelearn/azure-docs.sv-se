---
title: Azure Data Factory mappa data flödes sorterings omvandling
description: Azure Data Factory mappa data sorterings omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: f9d2699c870de21daea9ce421c79f089c04b99fa
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029117"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory sorterings omvandlingar för data flöde



Sorterings ![Inställningar](media/data-flow/sort.png "Sortera")

Med sorterings omvandlingen kan du sortera inkommande rader i den aktuella data strömmen. Utgående rader från sorterings omvandlingen kommer sedan att följa de ordnings regler som du har angett. Du kan välja enskilda kolumner och sortera dem med hjälp av pilen med pilen bredvid respektive fält. Om du behöver ändra kolumnen innan du tillämpar sorteringen klickar du på beräknade kolumner för att starta uttrycks redigeraren. Detta ger möjlighet att skapa ett uttryck för sorterings åtgärden i stället för att helt enkelt använda en kolumn för sorteringen.

## <a name="case-insensitive"></a>Skiftlägesokänsligt
Du kan aktivera "Skift läges okänslig" om du vill ignorera Skift läge vid sortering av sträng-eller textfält.

"Sortera endast inom partitioner" utnyttjar Spark-datapartitionering. Genom att bara sortera inkommande data inom varje partition kan data flöden sortera partitionerade data i stället för att sortera hela data strömmen.

Vart och ett av sorterings villkoren i sortera-omvandlingen kan ordnas om. Så om du behöver flytta en kolumn högre upp i sorterings prioriteten tar du bort raden med musen och flyttar den högre eller lägre i sorterings listan.

Partitionerings effekter vid sortering

ADF-dataflödet körs på Big data Spark-kluster med data som distribueras över flera noder och partitioner. Det är viktigt att tänka på detta när du skapar ditt data flöde om du är beroende av sorteringen för att bevara data i samma ordning. Om du väljer att partitionera om dina data i en efterföljande omvandling kan du förlora din sortering på grund av den reshuffling av data.

## <a name="next-steps"></a>Nästa steg

Efter sorteringen kanske du vill använda den [sammanställda omvandlingen](data-flow-aggregate.md)
