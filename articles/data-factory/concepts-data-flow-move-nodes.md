---
title: Azure Data Factory-flöde flytta Datanoder
description: Flytta noder i ett Azure Data Factory dataflödesdiagram
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: f600d6113fd52829b7ad22134f3fc208776eadf2
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267163"
---
# <a name="data-factory-data-flow-move-nodes"></a>Data Factory-flöde flytta Datanoder

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Aggregera omvandling alternativ](media/data-flow/agghead.png "aggregator rubrik")

Designyta Azure Data Factory-dataflöde är en ”utveckling” yta där du kan bygga data flöden uppifrån och ned, vänster till höger. Det är en verktygslåda som är kopplade till varje transformering med ett plustecken (+) symbol. Koncentrera dig på din affärslogik i stället för att ansluta noder via kanter i en miljö med fri form DAG.

Utan en dra och släpp-paradigm är därför sättet att ”flytta” en omvandling noden att ändra den inkommande dataströmmen. I stället ska du flytta transformeringar genom att ändra ”inkommande dataström”.

I Azure Data Factory dataflöde representerar strömmar flödet av data. I fönstret omvandling inställningar visas ett ”inkommande Steam”-fält. Anger det vilken inkommande dataström vehicle omvandlingen. Du kan ändra den fysiska platsen för omvandling noden visas i diagrammet genom att klicka på inkommande Stream-namnet och välja en annan dataströmmen. Aktuella transformeringen tillsammans med alla efterföljande transformeringar på dataströmmen flyttas sedan till den nya platsen.

Om du flyttar en omvandling med en eller flera transformationer efter att det ska den nya platsen i dataflödet anslutas via en ny gren.

Om du har inga efterföljande omvandlingar när noden som du har valt flyttas endast den transformeringen till den nya platsen.
