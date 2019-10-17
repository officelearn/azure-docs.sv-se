---
title: Azure Data Factory mappa data flödets surrogat nyckel omvandling
description: Så här använder du Azure Data Factory mappningen av data Flow surrogat Key för att generera sekventiella nyckel värden
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387166"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mappa data flöde surrogat nyckel omvandling



Använd surrogat Key Transformation för att lägga till ett öknings bara icke-affärsgodtyckligt nyckel värde till data flödets rad uppsättning. Detta är användbart när du skapar dimensions tabeller i en analys data modell för stjärn schema där varje medlem i dimensions tabellerna måste ha en unik nyckel som är en icke-affärsnyckel, en del av Kimball DW-metoden.

![Transformering av surrogat nyckel](media/data-flow/surrogate.png "Transformering av surrogat nyckel")

"Nyckel kolumn" är det namn som du kommer att ge din nya surrogat nyckel kolumn.

Start värde är start punkten för det stegvisa värdet.

## <a name="increment-keys-from-existing-sources"></a>Öka nycklar från befintliga källor

Om du vill starta sekvensen från ett värde som finns i en källa kan du använda en härledd kolumn omvandling direkt efter din surrogat nyckel omvandling och lägga till de två värdena tillsammans:

![SK Lägg till max](media/data-flow/sk006.png "Lägg till max i surrogat Key Transformation")

För att dirigera nyckelvärdet med föregående Max, finns det två tekniker som du kan använda:

### <a name="database-sources"></a>Databas källor

Använd alternativet fråga för att välja MAX () från källan med käll omvandlingen:

![Surrogat nyckel fråga](media/data-flow/sk002.png "Omvandlings fråga för surrogat nyckel")

### <a name="file-sources"></a>Fil källor

Om ditt föregående Max värde finns i en fil kan du använda din käll omvandling tillsammans med en aggregerad omvandling och använda funktionen MAX () för att hämta föregående Max värde:

![Surrogat nyckel fil](media/data-flow/sk008.png "Surrogat nyckel fil")

I båda fallen måste du ansluta inkommande nya data tillsammans med din källa som innehåller det föregående Max värdet:

![Surrogat nyckel koppling](media/data-flow/sk004.png "Surrogat nyckel koppling")

## <a name="next-steps"></a>Nästa steg

I de här exemplen används [kopplings](data-flow-join.md) -och [härledda kolumn](data-flow-derived-column.md) omvandlingar.
