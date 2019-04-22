---
title: Utlösare och bindningar i Azure Functions
description: Lär dig använda utlösare och bindningar för att ansluta din Azure-funktion till online-händelser och molnbaserade tjänster.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58889846"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-utlösare och bindningar begrepp

Lär dig avancerade begrepp omgivande functions-utlösare och bindningar i den här artikeln.

Utlösare är det orsaka en funktion som ska köras. En utlösare som definierar hur en funktion som anropas och en funktion måste ha exakt en utlösare. Utlösare har associerade data som ofta levereras som nyttolasten för funktionen. 

Bindning till en funktion är ett sätt att deklarativt ansluter en annan resurs till funktionen. bindningar kan anslutas som *ange bindningar*, *utdatabindningar*, eller båda. Data från bindningar har angetts för funktionen som parametrar.

Du kan blanda och matcha olika bindningar så att den passar dina behov. Bindningar är valfria och en funktion kan ha en eller flera indata och/eller utdatabindningar.

Utlösare och bindningar kan du undvika hardcoding åtkomst till andra tjänster. Funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du kan skicka data (t.ex, för att skapa ett kömeddelande) med hjälp av det returnera värdet för funktionen. 

Överväg att i följande exempel på hur du kan implementera olika funktioner.

| Exempel på ett scenario | Utlösare | Indatabindning | Utdata-bindning |
|-------------|---------|---------------|----------------|
| Ett nytt kömeddelande kommer som kör en funktion för att skriva till en annan kö. | kön<sup>*</sup> | *Ingen* | kön<sup>*</sup> |
|Ett schemalagt jobb läser Blob Storage-innehållet och skapar ett nytt Cosmos DB-dokument. | Timer | Blob Storage | Cosmos DB |
|Event Grid används för att läsa en avbildning från Blob Storage och ett dokument från Cosmos DB för att skicka ett e-postmeddelande. | Event Grid | BLOB-lagring och Cosmos DB | SendGrid |
| En webhook som använder Microsoft Graph för att uppdatera ett Excel-blad. | HTTP | *Ingen* | Microsoft Graph |

<sup>\*</sup> Representerar olika köer

De här exemplen är inte avsedda att ha grundlig, men tillhandahålls för att illustrera hur du kan använda utlösare och bindningar tillsammans.

###  <a name="trigger-and-binding-definitions"></a>Definitioner för utlösare och -bindning

Utlösare och bindningar har definierats på olika sätt beroende på utvecklingsmetoder.

| Plattform | Utlösare och bindningar har konfigurerats av... |
|-------------|--------------------------------------------|
| C#klassbibliotek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;att dekorera metoder och parametrar med C# attribut |
| Alla andra inklusive (Azure-portalen) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uppdaterar [function.json](./functions-reference.md) ([schemat](http://json.schemastore.org/function)) |

Portalen innehåller ett användargränssnitt för den här konfigurationen, men du kan redigera filen direkt genom att öppna den **Avancerad redigerare** tillgängligt via den **integrera** i din funktion.

I .NET definierar parametertypen datatypen för indata. Till exempel använda `string` att binda till texten i en kö-utlösare, en bytematris att läsa som binary och en anpassad typ att deserialisera till ett objekt.

Språk som har skrivits dynamiskt, till exempel JavaScript, använda den `dataType` -egenskapen i den *function.json* fil. Exempelvis om du vill läsa innehållet i en HTTP-förfrågan i binärt format, ställer du in `dataType` till `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andra alternativ för `dataType` är `stream` och `string`.

## <a name="binding-direction"></a>Riktning för bindning

Alla utlösare och bindningar har en `direction` -egenskapen i den [function.json](./functions-reference.md) fil:

- För utlösare är riktningen alltid `in`
- Bindningar för indata- och använda `in` och `out`
- Vissa bindningar stöd för en särskild riktning `inout`. Om du använder `inout`, endast den **Avancerad redigerare** är tillgängligt via den **integrera** i portalen.

När du använder [attribut i en klassbiblioteket](functions-dotnet-class-library.md) för att konfigurera utlösare och bindningar, riktning har angetts i en attributkonstruktör eller härleds från parametertypen.

## <a name="supported-bindings"></a>Stöds bindningar

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar finns i förhandsversion eller är godkända för användning i produktion finns i [språk som stöds](supported-languages.md).

## <a name="resources"></a>Resurser
- [Uttryck för bindning och mönster](./functions-bindings-expressions-patterns.md)
- [Med hjälp av Azure funktionsreturvärde](./functions-bindings-return-value.md)
- [Så här registrerar du ett uttryck för bindning](./functions-bindings-register.md)
- Testning:
  - [Strategier för att testa din kod i Azure Functions](functions-test-a-function.md)
  - [Manuellt köra en icke HTTP-utlöst funktion](functions-manually-run-non-http.md)
- [Hantering av Bindningsfel](./functions-bindings-errors.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Registrera Azure Functions tillägg av bindning](./functions-bindings-register.md)
