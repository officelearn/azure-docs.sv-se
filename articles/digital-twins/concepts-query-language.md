---
title: Frågespråk
titleSuffix: Azure Digital Twins
description: Lär dig grunderna i det digitala Azure-lagrets frågespråk.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3196004015046b4d3d2789745c80d323bacdced9
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985249"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Om frågespråket för Azure Digitals dubbla

Kom ihåg att mitten av Azure Digitals flätas är den [**dubbla grafen**](concepts-twins-graph.md), som är konstruerad från **digitala dubbla** och **relationer**. Det här diagrammet kan tillfrågas om du vill ha information om de digitala dubbla och relationer som den innehåller. Dessa frågor skrivs i ett anpassat SQL-liknande frågespråk som kallas **Azure Digitals**-frågor för Query Store.

Om du vill skicka en fråga till tjänsten från en klient app använder du Azure Digital- **fråge-API: et**. Detta gör det möjligt för utvecklare att skriva frågor och applicera filter för att hitta uppsättningar digitala dubbla i det dubbla diagrammet och annan information om det digitala Azure-scenariot.

## <a name="query-language-features"></a>Funktioner i frågespråk

Azure Digitals dubbla ger omfattande fråge funktioner mot den dubbla grafen. Frågor beskrivs med hjälp av SQL-like syntax, i ett frågespråk som liknar det [IoT Hub frågespråket](../iot-hub/iot-hub-devguide-query-language.md) med många jämförbara funktioner.

Här är de åtgärder som är tillgängliga i Azure Digitals:
* Hämta dubbla "egenskaper för Digitals".
* Få dubbla med digitala dubbla gränssnitts gränssnitt.
* Hämta dubbla i Relations egenskaper.
* Hämta dubblare över flera Relations typer ( `JOIN` frågor). Det finns begränsningar för antalet s som `JOIN` tillåts (en nivå för offentlig för hands version).
* Använd anpassad funktion `IS_OF_MODEL(twinCollection, twinTypeName)` som tillåter filtrering baserat på den dubbla [modellen](concepts-models.md). Det stöder arv.
* Använd valfri kombination ( `AND` , `OR` `NOT` operator) av ovanstående.
* Använd skalära funktioner:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` , `ENDS_WITH` .
* Använd jämförelse operatorer för frågor:,,,,,,, `AND` / `OR` / `NOT` `IN` / `NOT IN` `STARTSWITH` / `ENDSWITH` `=` `!=` `<` `>` `<=` , `>=` .
* Använd fortsättning: frågespråket instansieras med en sid storlek (upp till 100). Du kan hämta den digitala gränsen en sida i taget genom att upprepa anrop till `nextAsTwin` metoden.

## <a name="next-steps"></a>Nästa steg

Lär dig att skriva frågor och se exempel på klient kod i [anvisningar: fråga det dubbla diagrammet](how-to-query-graph.md).