---
title: Azure Data Factory mappa data flödets surrogat nyckel omvandling
description: Så här använder du Azure Data Factory mappningen av data Flow surrogat Key för att generera sekventiella nyckel värden
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 45e2d35a3b0a3f3c89913bbe70d7c43c17cbcee0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029191"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mappa data flöde surrogat nyckel omvandling



Använd surrogat Key Transformation för att lägga till ett öknings bara icke-affärsgodtyckligt nyckel värde till data flödets rad uppsättning. Detta är användbart när du skapar dimensions tabeller i en analys data modell för stjärn schema där varje medlem i dimensions tabellerna måste ha en unik nyckel som är en icke-affärsnyckel, en del av Kimball DW-metoden.

![Transformering]av surrogat nyckel transformera(media/data-flow/surrogate.png "surrogat nyckel")

"Nyckel kolumn" är det namn som du kommer att ge din nya surrogat nyckel kolumn.

Start värde är start punkten för det stegvisa värdet.

## <a name="increment-keys-from-existing-sources"></a>Öka nycklar från befintliga källor

Om du vill starta sekvensen från ett värde som finns i en källa kan du använda en härledd kolumn omvandling direkt efter din surrogat nyckel omvandling och lägga till de två värdena tillsammans:

![Sk Lägg till maximal](media/data-flow/sk006.png "surrogat nyckel omvandling Lägg till max")

För att dirigera nyckelvärdet med föregående Max, finns det två tekniker som du kan använda:

### <a name="database-sources"></a>Databas källor

Använd alternativet fråga för att välja MAX () från källan med käll omvandlingen:

![Surrogat nyckel fråga](media/data-flow/sk002.png "surrogat nyckel omvandlings fråga")

### <a name="file-sources"></a>Fil källor

Om ditt föregående Max värde finns i en fil kan du använda din käll omvandling tillsammans med en aggregerad omvandling och använda funktionen MAX () för att hämta föregående Max värde:

Surrogat nyckel(media/data-flow/sk008.png "fil") för ![surrogat nyckel]

I båda fallen måste du ansluta inkommande nya data tillsammans med din källa som innehåller det föregående Max värdet:

![Surrogat nyckel Anslut till](media/data-flow/sk004.png "surrogat Key Join")

## <a name="next-steps"></a>Nästa steg

I de här exemplen används [kopplings](data-flow-join.md) -och [härledda kolumn](data-flow-derived-column.md) omvandlingar.
