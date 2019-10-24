---
title: Flytta noder i Azure Data Factory data flöde
description: Så här flyttar du noder i ett diagram över Azure Data Factory mappnings data flöde
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 631a103491b70ca016b94af01995aeeb3f29c77a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754675"
---
# <a name="mapping-data-flow-move-nodes"></a>Mappa data flöde flytta noder



![Sammanställda omvandlings alternativ](media/data-flow/agghead.png "aggregator-rubrik")

Den Azure Data Factory data flödets design yta är en "konstruktion"-yta där du skapar data flödar uppifrån och ned, från vänster till höger. Det finns en verktygs låda som är kopplad till varje omvandling med plus tecknet (+). Fokusera på affärs logiken i stället för att ansluta noder via kanter i en kostnads fri form av en DAG miljö.

Så, utan att dra och släppa paradigm, så att du kan flytta en Transformations nod, är att ändra den inkommande data strömmen. I stället flyttar du transformeringar runt genom att ändra "inkommande ström".

## <a name="streams-of-data-inside-of-data-flow"></a>Data strömmar av data i data flödet

I Azure Data Factory data flöde representerar strömmar data flödet. I fönstret omvandlings inställningar visas fältet inkommande ström. Detta visar vilken inkommande data ström som används för att mata över omvandlingen. Du kan ändra den fysiska platsen för Transform-noden i diagrammet genom att klicka på namnet på den inkommande strömmen och välja en annan data ström. Den aktuella omvandlingen tillsammans med alla efterföljande transformeringar i den data strömmen kommer sedan att flyttas till den nya platsen.

Om du flyttar en omvandling med en eller flera transformeringar efter den, kommer den nya platsen i data flödet att anslutas via en ny gren.

Om du inte har några efterföljande omvandlingar efter noden som du har valt flyttas bara den omvandlingen till den nya platsen.

## <a name="next-steps"></a>Nästa steg

När du har slutfört din data flödes design aktiverar du knappen Felsök på och testar den i fel söknings läge, antingen direkt i [data flödes design](concepts-data-flow-debug-mode.md) eller [pipeline-felsökning](control-flow-execute-data-flow-activity.md).
