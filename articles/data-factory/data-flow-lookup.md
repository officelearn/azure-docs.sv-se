---
title: Azure Data Factory mappning av data flödes uppslags omvandling
description: Azure Data Factory mappning av data flödes uppslags omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029338"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mappning av data flödes uppslags omvandling



Använd lookup för att lägga till referens data från en annan källa till ditt data flöde. Uppslags transformeringen kräver en definierad källa som pekar på din referens tabell och matchar nyckel fält.

![](media/data-flow/lookup1.png "Sökning efter") omvandling

Välj de nyckel fält som du vill matcha på mellan inkommande data Ströms fält och fälten från referens källan. Du måste först ha skapat en ny källa på data flödets design arbets yta som ska användas som den högra sidan för sökningen.

När matchningar hittas kommer de resulterande raderna och kolumnerna från referens källan att läggas till i ditt data flöde. Du kan välja vilka fält av intresse som du vill ta med i din mottagare i slutet av ditt data flöde.

## <a name="match--no-match"></a>Matcha/ingen matchning

Efter omvandlingen av sökningar kan du använda efterföljande omvandlingar för att granska resultaten av varje matchnings rad genom att använda Expression-funktionen `isMatch()` för att göra ytterligare val i din logik baserat på om sökningen resulterade i en rad matchning eller inte.

## <a name="optimizations"></a>Optimeringar

I Data Factory körs data flöden i uppskalade Spark-miljöer. Om din data uppsättning kan anpassas till arbets ytans minnes utrymme kan vi optimera dina uppslags prestanda.

![Sänd ansluta till](media/data-flow/broadcast.png "sändnings anslutning")

### <a name="broadcast-join"></a>Sändnings anslutning

Välj vänster och/eller höger sändnings anslutning för att begära ADF för att skicka hela data uppsättningen från endera sidan av Sök relationen till minnet.

### <a name="data-partitioning"></a>Datapartitionering

Du kan också ange partitionering av dina data genom att välja "Ange partitionering" på fliken Optimize i lookup-omvandlingen för att skapa uppsättningar med data som passar bättre i minnet per arbets plats.

## <a name="next-steps"></a>Nästa steg

[Anslut](data-flow-join.md) och [existerar](data-flow-exists.md) transformeringar utför liknande uppgifter i data flöden för ADF-mappning. Ta en titt på dessa omvandlingar härnäst.
