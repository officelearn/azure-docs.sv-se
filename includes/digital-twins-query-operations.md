---
author: baanders
description: inkludera fil för Azure digitala dubbla frågor
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486669"
---
## <a name="query-language-features"></a>Funktioner i frågespråk

Azure Digitals dubbla ger omfattande fråge funktioner mot den dubbla grafen. Frågor beskrivs med hjälp av SQL-like syntax, i ett frågespråk som liknar det [IoT Hub frågespråket](../articles/iot-hub/iot-hub-devguide-query-language.md) med många jämförbara funktioner.

> [!NOTE]
> Alla frågor om Azure Digitals dubbla frågor är Skift läges känsliga.

Här är de åtgärder som är tillgängliga i Azure Digitals-lagringsplatsens språk för frågor.

Hämta digitala dubbla med sina...
* modell (med `IS_OF_MODEL` operator)
* egenskaper (inklusive [Taggegenskaper](../articles/digital-twins/how-to-use-tags.md))
* intervall
* relationer
  - egenskaper för relationerna

Du kan förbättra dina frågor ytterligare med följande åtgärder:
* Hämta dubblare över flera Relations typer ( `JOIN` frågor). 
  - Det finns begränsningar för antalet s som `JOIN` tillåts (en nivå för offentlig för hands version).
* Välj endast de översta frågeresultaten ( `Select TOP` operator)
* Använd skalära funktioner:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Använd jämförelse operatorer för frågor:,,,,, `IN` / `NIN` `=` `!=` `<` `>` `<=` , `>=` .
* Använd valfri kombination ( `AND` , `OR` , `NOT` operator) av `IS_OF_MODEL` , skalära funktioner och jämförelse operatorer.
* Använd fortsättning: frågespråket instansieras med en sid storlek (upp till 100). Du kan hämta den digitala gränsen en sida i taget genom att tillhandahålla en fortsättnings-token i efterföljande anrop till API: et.