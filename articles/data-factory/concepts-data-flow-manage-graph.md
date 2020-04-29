---
title: Data flödes diagram
description: Arbeta med data flödes diagram i Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605357"
---
# <a name="mapping-data-flow-graphs"></a>Mappa data flödes diagram

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Design ytan för att mappa data flödar är en "konstruktion"-yta där du kan skapa data flödar uppifrån och ned från vänster till höger. Det finns en verktygs låda som är kopplad till varje omvandling med plus tecknet (+). Fokusera på affärs logiken i stället för att ansluta noder via kanter i en kostnads fri form av en DAG miljö.

Nedan finns inbyggda mekanismer för att hantera data flödes diagrammet.

## <a name="move-nodes"></a>Flytta noder

![Sammanställda omvandlings alternativ](media/data-flow/agghead.png "aggregator-rubrik")

När du använder ett paradigm-och-släpp-paradigm, är vägen för att "flytta" en Transformations nod att ändra den inkommande data strömmen. I stället flyttar du transformeringar runt genom att ändra "inkommande ström".

## <a name="streams-of-data-inside-of-data-flow"></a>Data strömmar av data i data flödet

I Azure Data Factory data flöde representerar strömmar data flödet. I fönstret omvandlings inställningar visas fältet inkommande ström. Detta visar vilken inkommande data ström som används för att mata över omvandlingen. Du kan ändra den fysiska platsen för Transform-noden i diagrammet genom att klicka på namnet på den inkommande strömmen och välja en annan data ström. Den aktuella omvandlingen tillsammans med alla efterföljande transformeringar i den data strömmen kommer sedan att flyttas till den nya platsen.

Om du flyttar en omvandling med en eller flera transformeringar efter den, kommer den nya platsen i data flödet att anslutas via en ny gren.

Om du inte har några efterföljande omvandlingar efter noden som du har valt flyttas bara den omvandlingen till den nya platsen.

## <a name="hide-graph-and-show-graph"></a>Dölj graf och Visa diagram

Det finns en knapp längst till höger i det nedre konfigurations fönstret där du kan expandera det nedre fönstret till full skärm när du arbetar med omvandlings konfigurationer. På så kan du använda knapparna "föregående" och "Nästa" för att navigera genom diagrammets konfigurationer. Om du vill gå tillbaka till diagramvyn klickar du på knappen ned och återgår till delnings skärmen.

## <a name="search-graph"></a>Sök diagram

Du kan söka i grafen med knappen Sök på design ytan.

![Sök](media/data-flow/search001.png "Sök diagram")

## <a name="next-steps"></a>Nästa steg

När du har slutfört din data flödes design aktiverar du knappen Felsök på och testar den i fel söknings läge, antingen direkt i [data flödes design](concepts-data-flow-debug-mode.md) eller [pipeline-felsökning](control-flow-execute-data-flow-activity.md).
