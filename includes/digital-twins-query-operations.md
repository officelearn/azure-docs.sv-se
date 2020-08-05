---
author: baanders
description: inkludera fil för Azure digitala dubbla frågor
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 82639a19ef728c22a74381d24754992e0f647976
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562486"
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
  - Det finns begränsningar för antalet s som `JOIN` tillåts (en nivå för offentlig för hands version).
* Välj endast de översta frågeresultaten ( `Select TOP` operator)
* Använd skalära funktioner:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Använd jämförelse operatorer för frågor:,,,,, `IN` / `NIN` `=` `!=` `<` `>` `<=` , `>=` .
* Använd valfri kombination ( `AND` , `OR` , `NOT` operator) av `IS_OF_MODEL` , skalära funktioner och jämförelse operatorer.
* Använd fortsättning: frågespråket instansieras med en sid storlek (upp till 100). Du kan hämta den digitala gränsen en sida i taget genom att tillhandahålla en fortsättnings-token i efterföljande anrop till API: et.