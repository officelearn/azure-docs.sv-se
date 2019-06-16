---
title: Azure Data Factory-flöde flytta Datanoder
description: Flytta noder i ett Azure Data Factory mappning dataflödesdiagram
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 951a5d4fcbd561b085b0377bde48e820dc8972a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65519957"
---
# <a name="mapping-data-flow-move-nodes"></a>Mappningen Datanoder Flow flytta

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Aggregera omvandling alternativ](media/data-flow/agghead.png "aggregator rubrik")

Designyta Azure Data Factory-dataflöde är en ”utveckling” yta där du kan bygga data flöden uppifrån och ned, vänster till höger. Det är en verktygslåda som är kopplade till varje transformering med ett plustecken (+) symbol. Koncentrera dig på din affärslogik i stället för att ansluta noder via kanter i en miljö med fri form DAG.

Utan en dra och släpp-paradigm är därför sättet att ”flytta” en omvandling noden att ändra den inkommande dataströmmen. I stället ska du flytta transformeringar genom att ändra ”inkommande dataström”.

## <a name="streams-of-data-inside-of-data-flow"></a>Dataströmmar i dataflöde

I Azure Data Factory dataflöde representerar strömmar flödet av data. I fönstret omvandling inställningar visas ett ”inkommande Steam”-fält. Anger det vilken inkommande dataström vehicle omvandlingen. Du kan ändra den fysiska platsen för omvandling noden visas i diagrammet genom att klicka på inkommande Stream-namnet och välja en annan dataströmmen. Aktuella transformeringen tillsammans med alla efterföljande transformeringar på dataströmmen flyttas sedan till den nya platsen.

Om du flyttar en omvandling med en eller flera transformationer efter att det ska den nya platsen i dataflödet anslutas via en ny gren.

Om du har inga efterföljande omvandlingar när noden som du har valt flyttas endast den transformeringen till den nya platsen.

## <a name="next-steps"></a>Nästa steg

När du har slutfört utformningen dataflöde, aktivera knappen felsökning och testa det ut i felsökningsläge antingen direkt i den [data flödesdesignern](concepts-data-flow-debug-mode.md) eller [pipeline debug](control-flow-execute-data-flow-activity.md).
