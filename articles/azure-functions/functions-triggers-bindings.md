---
title: Utlösare och bindningar i Azure Functions
description: Lär dig att använda utlösare och bindningar för att ansluta din Azure-funktion till online-händelser och molnbaserade tjänster.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276509"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Utlösare och bindningar i Azure Functions

I den här artikeln lär du dig de övergripande koncepten som omger funktions utlösare och bindningar.

Utlösare är orsaken till att en funktion körs. En utlösare definierar hur en funktion anropas och en funktion måste ha exakt en utlösare. Utlösare har associerade data, vilka vanligtvis är nyttolasten för funktionen. 

Bindning till en funktion är ett sätt att på ett sätt ansluta en annan resurs till funktionen. bindningar kan vara anslutna som *indata-bindningar*, *utgående bindningar*eller både och. Data från bindningar anges för funktionen som parametrar.

Du kan blanda och matcha olika bindningar så att de passar dina behov. Bindningar är valfria och en funktion kan ha en eller flera indata- och/eller utdatabindningar.

Med utlösare och bindningar kan du undvika hårdkoda åtkomst till andra tjänster. Funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du kan skicka data (t.ex. för att skapa ett kömeddelande) med hjälp av returvärdet för funktionen. 

Tänk på följande exempel på hur du kan implementera olika funktioner.

| Exempelscenario | Utlösare | Binda in | Utgående bindning |
|-------------|---------|---------------|----------------|
| Ett nytt Queue-meddelande anländer som kör en funktion för att skriva till en annan kö. | Köjobb<sup>*</sup> | *Inga* | Köjobb<sup>*</sup> |
|Ett schemalagt jobb läser Blob Storage innehåll och skapar ett nytt Cosmos DB-dokument. | Timer | Blob Storage | Cosmos DB |
|Event Grid används för att läsa en avbildning från Blob Storage och ett dokument från Cosmos DB för att skicka ett e-postmeddelande. | Event Grid | Blob Storage och Cosmos DB | SendGrid |
| En webhook som använder Microsoft Graph för att uppdatera ett Excel-blad. | HTTP | *Inga* | Microsoft Graph |

<sup>\*</sup>Representerar olika köer

Dessa exempel är inte avsedda att vara uttömmande, men de tillhandahålls för att illustrera hur du kan använda utlösare och bindningar tillsammans.

###  <a name="trigger-and-binding-definitions"></a>Utlösare och bindnings definitioner

Utlösare och bindningar definieras på olika sätt beroende på utvecklings metoden.

| Plattform | Utlösare och bindningar konfigureras av... |
|-------------|--------------------------------------------|
| C#-klass bibliotek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metoder och parametrar för dekorera med C#-attribut |
| Alla andra (inklusive Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uppdaterar [Function. JSON](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Portalen innehåller ett användar gränssnitt för den här konfigurationen, men du kan redigera filen direkt genom att öppna den **avancerade redigeraren** som är tillgänglig via fliken **integrera** i din funktion.

I .NET definierar parameter typen data typen för indata. Använd `string` till exempel för att binda till texten i en Queue-utlösare, en byte mat ris som ska läsas som binär och en anpassad typ för deserialisering till ett objekt.

För språk som är dynamiskt skrivna, till exempel Java Script, `dataType` använder du egenskapen i *Function. JSON* -filen. Om du till exempel vill läsa innehållet i en HTTP-begäran i binärt format `dataType` , `binary`ange till:

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

Alla utlösare och bindningar `direction` har en egenskap i filen [Function. JSON](./functions-reference.md) :

- För utlösare är riktningen alltid`in`
- Indata och utgående bindningar `in` använder och`out`
- Vissa bindningar har stöd för en `inout`speciell riktning. Om du använder `inout`är det bara **avancerad redigerare** som är tillgänglig via fliken **integrera** i portalen.

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
  - [Köra en funktion som inte utlösts av HTTP manuellt](functions-manually-run-non-http.md)
- [Hanterar bindnings fel](./functions-bindings-errors.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Registrera Azure Functions bindnings tillägg](./functions-bindings-register.md)
