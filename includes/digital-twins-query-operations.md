---
author: baanders
description: inkludera fil för Azure digitala dubbla frågor
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 333a7ec4ae0e5c8cbc94a603e2ccf81ee92e7d48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078467"
---
## <a name="query-language-features"></a>Funktioner i frågespråk

Azure Digitals dubbla ger omfattande fråge funktioner mot den dubbla grafen. Frågor beskrivs med hjälp av SQL-like syntax, i ett frågespråk som liknar det [IoT Hub frågespråket](../articles/iot-hub/iot-hub-devguide-query-language.md) med många jämförbara funktioner.

> [!NOTE]
> Alla frågor om Azure Digitals dubbla frågor är Skift läges känsliga.

Här är de åtgärder som är tillgängliga i Azure Digitals-frågespråket.

Hämta digitala dubbla med sina...
* modell (med `IS_OF_MODEL` operator)
* egenskaper (inklusive [Taggegenskaper](../articles/digital-twins/how-to-use-tags.md))
* intervall
* relationer
  - egenskaper för relationerna

Du kan förbättra dina frågor ytterligare med följande åtgärder:
* Hämta dubblare över flera Relations typer ( `JOIN` frågor). 
  - Under för hands versionen tillåts upp till fem nivåer av `JOIN` .
* Välj endast de översta frågeresultaten ( `Select TOP` operator)
* Räkna antalet objekt i en resultat uppsättning med hjälp av `Select COUNT`
* Använda projektioner för att välja vilka kolumner en fråga ska returnera
* Använd skalära funktioner:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Använd jämförelse operatorer för frågor:,,,,, `IN` / `NIN` `=` `!=` `<` `>` `<=` , `>=` .
* Använd valfri kombination ( `AND` , `OR` , `NOT` operator) av `IS_OF_MODEL` , skalära funktioner och jämförelse operatorer.
* Använd fortsättning: frågespråket instansieras med en sid storlek (upp till 100). Du kan hämta den digitala gränsen en sida i taget genom att tillhandahålla en fortsättnings-token i efterföljande anrop till API: et.