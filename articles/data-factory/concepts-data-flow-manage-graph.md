---
title: Diagram över dataflöde
description: Så här arbetar du med datafabriksdataflödesdiagram
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 1618011dc3c281cb2ef06ef80cfc7c8e69c4e091
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415498"
---
# <a name="mapping-data-flow-graphs"></a>Mappa dataflödesdiagram

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Designytan för mappningsdataflöden är en "konstruktionsyta" där du bygger dataflöden uppifrån och ned, från vänster till höger. Det finns en verktygslåda kopplad till varje transform med en plussymbol (+). Koncentrera dig på din affärslogik istället för att ansluta noder via kanter i en fri form DAG miljö.

Nedan finns inbyggda mekanismer för att hantera dataflödesdiagrammet.

## <a name="move-nodes"></a>Flytta noder

![Alternativ för mängdomvandling](media/data-flow/agghead.png "aggregatorhuvud")

Utan ett dra-och-släpp-paradigm är sättet att "flytta" en omvandlingsnod att ändra den inkommande strömmen. I stället kommer du att flytta transformeringar runt genom att ändra den "inkommande strömmen".

## <a name="streams-of-data-inside-of-data-flow"></a>Strömmar av data inuti dataflödet

I Azure Data Factory Data Flow representerar strömmar dataflödet. I fönstret inställningar för omvandling visas fältet "Inkommande ström". Detta talar om för dig vilken inkommande dataström som matar den omvandlingen. Du kan ändra den fysiska platsen för transformnoden i diagrammet genom att klicka på namnet Inkommande ström och välja en annan dataström. Den aktuella omvandlingen tillsammans med alla efterföljande transformeringar på den strömmen flyttas sedan till den nya platsen.

Om du flyttar en omvandling med en eller flera omvandlingar efter den, kommer den nya platsen i dataflödet att anslutas via en ny gren.

Om du inte har några efterföljande omvandlingar efter den nod som du har valt flyttas bara den transformeringen till den nya platsen.

## <a name="hide-graph-and-show-graph"></a>Dölj diagram och visa diagram

Det finns en knapp längst till höger i det nedre konfigurationsfönstret där du kan expandera den nedre rutan till helskärm när du arbetar med omvandlingskonfigurationer. På så sätt kan du använda knapparna "föregående" och "nästa" för att navigera genom diagrammets konfigurationer. Om du vill gå tillbaka till diagramvyn klickar du på ned-knappen och går tillbaka till delad skärm.

## <a name="search-graph"></a>Sök diagram

Du kan söka i diagrammet med sökknappen på designytan.

![Sök](media/data-flow/search001.png "Sök diagram")

## <a name="next-steps"></a>Nästa steg

När du har slutfört designen för DataFlöde slår du på felsökningsknappen och testar den i felsökningsläge, antingen direkt i [dataflödesdesignern](concepts-data-flow-debug-mode.md) eller [pipelinedebug.](control-flow-execute-data-flow-activity.md)
