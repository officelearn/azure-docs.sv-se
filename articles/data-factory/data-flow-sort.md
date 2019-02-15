---
title: Azure Data Factory Data Flow sortera omvandling
description: Azure Data Factory Data sortera Join-transformering
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 55cd303399d34eecd8f787e1e5af09c5d904fb44
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272112"
---
# <a name="azure-data-factory-data-sort-transformations"></a>Azure Data Factory dataomvandlingar sortera

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Sortera inställningar](media/data-flow/sort.png "sortera")

Sortera transformeringen kan du sortera inkommande rader på aktuella data i dataströmmen. Utgående raderna från sortera transformeringen följer därefter de skrivordning regler som du anger. Du kan välja enskilda kolumner och sortera dem ASC eller DEC, med hjälp av indikatorn för pilen bredvid respektive fält. Om du behöver ändra kolumnen innan du tillämpar sorteringen, klicka på ”beräknade kolumner” för att starta uttrycksredigeraren. Detta ger möjlighet att skapa ett uttryck för sorteringsåtgärden i stället för bara tillämpas en kolumn för sorteringen.

Du kan aktivera ”skiftlägeskänsligt” om du vill ignorera skiftläge vid sortering sträng eller text fält.

”Sortera endast inom partitioner” utnyttjar Spark Datapartitionering. Om du sorterar inkommande data endast inom varje partition sortera Data flödar partitionerade data i stället för att sortera hela dataströmmen.

Sortera villkor omvandling sortera kan skrivas om. Så om du behöver flytta en kolumn som är högre upp i sortera prioritet, hämta den raden med musen och flytta den högre eller lägre i listan över sortering.

Partitionering inverkan på Sortera

Dataflöde för ADF körs på stordata Spark-kluster med data fördelade över flera noder och partitioner. Det är viktigt att ha detta i åtanke när utforma ditt dataflöde om du är beroende av sortera transformeringen att behålla data i den ordningen. Om du vill partitionera om dina data i en efterföljande omvandling kan du förlora sortering på grund av att reshuffling av data.
