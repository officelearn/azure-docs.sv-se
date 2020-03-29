---
title: Omformning av ersättningsnyckel för dataflöde
description: Så här använder du Azure Data Factorys mappningsdataflöde Surrogatnyckelomvandling för att generera sekventiella nyckelvärden
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930204"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Omformning av ersättningsnyckel för dataflöde



Använd surrogatnyckelomvandlingen för att lägga till ett stegvis godtyckligt nyckelvärde som inte är affärsmässigt i dataflödesraduppsättningen. Detta är användbart när du utformar dimensionstabeller i en analysdatamodell för stjärnschema där varje medlem i dimensionstabellerna måste ha en unik nyckel som är en icke-affärsnyckel, en del av Kimball DW-metoden.

![Transformera surrogatnyckel](media/data-flow/surrogate.png "Omvandling av surrogatnyckel")

"Nyckelkolumn" är det namn som du ska ge till den nya kolumnen för surrogatnyckel.

"Startvärde" är början på det inkrementella värdet.

## <a name="increment-keys-from-existing-sources"></a>Öka nycklar från befintliga källor

Om du vill starta sekvensen från ett värde som finns i en källa kan du använda en härledd kolumnomvandling direkt efter omformningen av surrogatnyckeln och lägga till de två värdena tillsammans:

![SK lägga till Max](media/data-flow/sk006.png "Summering av surrogatnyckel")

För att så nyckelvärdet med föregående max finns det två tekniker som du kan använda:

### <a name="database-sources"></a>Databaskällor

Använd alternativet "Fråga" för att välja MAX() från källan med hjälp av källomvandlingen:

![Fråga om surrogatnyckel](media/data-flow/sk002.png "Fråga om omvandling av surrogatnyckel")

### <a name="file-sources"></a>Filkällor

Om ditt tidigare maxvärde finns i en fil kan du använda källomvandlingen tillsammans med en mängdomvandling och använda uttrycksfunktionen MAX() för att få det tidigare maxvärdet:

![Nyckelfil för surrogat](media/data-flow/sk008.png "Nyckelfil för surrogat")

I båda fallen måste du koppla dina inkommande nya data tillsammans med källan som innehåller det tidigare maxvärdet:

![Anslutning av surrogatnyckel](media/data-flow/sk004.png "Anslutning av surrogatnyckel")

## <a name="next-steps"></a>Nästa steg

I de här exemplen används omvandlingarna [Koppla och](data-flow-join.md) [härledd kolumn.](data-flow-derived-column.md)
