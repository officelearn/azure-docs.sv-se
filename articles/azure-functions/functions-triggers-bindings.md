---
title: Utlösare och bindningar i Azure Functions
description: Lär dig att använda utlösare och bindningar för att ansluta din Azure-funktion till online-händelser och molnbaserade tjänster.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276509"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Metoder för Azure Functions utlösare och bindningar

I den här artikeln lär du dig de övergripande koncepten som omger funktions utlösare och bindningar.

Utlösare är orsaken till att en funktion körs. En utlösare definierar hur en funktion anropas och en funktion måste ha exakt en utlösare. Utlösare har associerade data som ofta tillhandahålls som nytto last för funktionen. 

Bindning till en funktion är ett sätt att på ett sätt ansluta en annan resurs till funktionen. bindningar kan vara anslutna som *indata-bindningar*, *utgående bindningar*eller både och. Data från bindningar tillhandahålls till funktionen som parametrar.

Du kan blanda och matcha olika bindningar så att de passar dina behov. Bindningar är valfria och en funktion kan ha en eller flera bindningar för indata och/eller utdata.

Med utlösare och bindningar kan du undvika hårdkoda åtkomst till andra tjänster. Din funktion tar emot data (till exempel innehållet i ett Queue meddelande) i funktions parametrar. Du skickar data (till exempel för att skapa ett Queue meddelande) genom att använda funktionens retur värde. 

Tänk på följande exempel på hur du kan implementera olika funktioner.

| Exempel på ett scenario | Utlösare | Binda in | Utgående bindning |
|-------------|---------|---------------|----------------|
| Ett nytt Queue-meddelande anländer som kör en funktion för att skriva till en annan kö. | Queue<sup>*</sup> | *Alternativet* | Queue<sup>*</sup> |
|Ett schemalagt jobb läser Blob Storage innehåll och skapar ett nytt Cosmos DB-dokument. | Timer | Blob Storage | Cosmos DB |
|Event Grid används för att läsa en avbildning från Blob Storage och ett dokument från Cosmos DB för att skicka ett e-postmeddelande. | Event Grid | Blob Storage och Cosmos DB | SendGrid |
| En webhook som använder Microsoft Graph för att uppdatera ett Excel-blad. | HTTP | *Alternativet* | Microsoft Graph |

<sup>\*</sup> Representerar olika köer

Dessa exempel är inte avsedda att vara uttömmande, men de tillhandahålls för att illustrera hur du kan använda utlösare och bindningar tillsammans.

###  <a name="trigger-and-binding-definitions"></a>Utlösare och bindnings definitioner

Utlösare och bindningar definieras på olika sätt beroende på utvecklings metoden.

| Plattform | Utlösare och bindningar konfigureras av... |
|-------------|--------------------------------------------|
| C#klass bibliotek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dekorera metoder och parametrar med C# attribut |
| Alla andra (inklusive Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uppdatera [Function. JSON](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Portalen innehåller ett användar gränssnitt för den här konfigurationen, men du kan redigera filen direkt genom att öppna den **avancerade redigeraren** som är tillgänglig via fliken **integrera** i din funktion.

I .NET definierar parameter typen data typen för indata. Använd till exempel `string` för att binda till texten i en Queue-utlösare, en byte mat ris som ska läsas som binär och en anpassad typ för deserialisering till ett objekt.

För språk som är dynamiskt skrivna, till exempel Java Script, använder du egenskapen `dataType` i filen *Function. JSON* . Om du till exempel vill läsa innehållet i en HTTP-begäran i binärformat anger `dataType` till `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andra alternativ för `dataType` är `stream` och `string`.

## <a name="binding-direction"></a>Bindnings riktning

Alla utlösare och bindningar har en `direction`-egenskap i [Function. JSON](./functions-reference.md) -filen:

- För utlösare är riktningen alltid `in`
- Med indata och utdata-bindningar används `in` och `out`
- Vissa bindningar har stöd för en speciell riktning `inout`. Om du använder `inout`är endast **avancerad redigerare** tillgänglig via fliken **integrera** i portalen.

När du använder [attribut i ett klass bibliotek](functions-dotnet-class-library.md) för att konfigurera utlösare och bindningar, anges riktningen i en attributhierarki eller härleds från parameter typen.

## <a name="supported-bindings"></a>Bindningar som stöds

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar som är i för hands version eller som godkänns för användning av produktion finns i [språk som stöds](supported-languages.md).

## <a name="resources"></a>Resurser
- [Bindnings uttryck och mönster](./functions-bindings-expressions-patterns.md)
- [Använda Azures funktions retur värde](./functions-bindings-return-value.md)
- [Så här registrerar du ett bindnings uttryck](./functions-bindings-register.md)
- Prestandatester
  - [Strategier för att testa din kod i Azure Functions](functions-test-a-function.md)
  - [Köra en icke-HTTP-utlöst funktion manuellt](functions-manually-run-non-http.md)
- [Hanterar bindnings fel](./functions-bindings-errors.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Registrera Azure Functions bindnings tillägg](./functions-bindings-register.md)
