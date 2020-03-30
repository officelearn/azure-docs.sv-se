---
title: Utlösare och bindningar i Azure-funktioner
description: Lär dig att använda utlösare och bindningar för att ansluta din Azure-funktion till onlinehändelser och molnbaserade tjänster.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276509"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Utlösare och bindningar i Azure Functions

I den här artikeln lär du dig de begrepp på hög nivå kring funktioner som utlöser och bindningar.

Utlösare är det som orsakar att en funktion körs. En utlösare definierar hur en funktion anropas och en funktion måste ha exakt en utlösare. Utlösare har associerade data, vilka vanligtvis är nyttolasten för funktionen. 

Bindning till en funktion är ett sätt att deklarativt ansluta en annan resurs till funktionen. bindningar kan anslutas som *ingångsbindningar,* *utdatabindningar*eller båda. Data från bindningar anges för funktionen som parametrar.

Du kan blanda och matcha olika bindningar så att de passar dina behov. Bindningar är valfria och en funktion kan ha en eller flera indata- och/eller utdatabindningar.

Med utlösare och bindningar kan du undvika hårdkodning av åtkomst till andra tjänster. Funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du kan skicka data (t.ex. för att skapa ett kömeddelande) med hjälp av returvärdet för funktionen. 

Tänk på följande exempel på hur du kan implementera olika funktioner.

| Exempel på ett scenario | Utlösare | Indatabindning | Utdatabindning |
|-------------|---------|---------------|----------------|
| Ett nytt kömeddelande anländer som kör en funktion för att skriva till en annan kö. | Kö<sup>*</sup> | *Inget* | Kö<sup>*</sup> |
|Ett schemalagt jobb läser Blob Storage-innehåll och skapar ett nytt Cosmos DB-dokument. | Timer | Blob Storage | Cosmos DB |
|Händelserutnätet används för att läsa en bild från Blob Storage och ett dokument från Cosmos DB för att skicka ett e-postmeddelande. | Event Grid | Blob Lagring och Cosmos DB | SendGrid |
| En webhook som använder Microsoft Graph för att uppdatera ett Excel-blad. | HTTP | *Inget* | Microsoft Graph |

<sup>\*</sup>Representerar olika köer

Dessa exempel är inte avsedda att vara uttömmande, men tillhandahålls för att illustrera hur du kan använda utlösare och bindningar tillsammans.

###  <a name="trigger-and-binding-definitions"></a>Utlösare och bindande definitioner

Utlösare och bindningar definieras olika beroende på utvecklingsmetoden.

| Plattform | Utlösare och bindningar konfigureras av... |
|-------------|--------------------------------------------|
| C# klassbibliotek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dekorera metoder och parametrar med C# attribut |
| Alla andra (inklusive Azure-portalen) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uppdatera [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Portalen tillhandahåller ett användargränssnitt för den här konfigurationen, men du kan redigera filen direkt genom att öppna den **avancerade redigeraren** som är tillgänglig via fliken **Integrera** i din funktion.

I .NET definierar parametertypen datatypen för indata. Använd `string` till exempel för att binda till texten i en köutlösare, en bytematris som ska läsas som binär och en anpassad typ för att av serialisera till ett objekt.

För språk som är dynamiskt skrivna, `dataType` till exempel JavaScript, använder du egenskapen i *filen function.json.* Om du till exempel vill läsa innehållet i `dataType` en `binary`HTTP-begäran i binärt format anger du till :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andra alternativ `dataType` `stream` för `string`är och .

## <a name="binding-direction"></a>Bindningsriktning

Alla utlösare och bindningar har en `direction` egenskap i filen [function.json:](./functions-reference.md)

- För utlösare är riktningen alltid`in`
- Ingångs- och `in` utdatabindningar använder och`out`
- Vissa bindningar stöder `inout`en särskild riktning . Om du `inout`använder är endast **den avancerade redigeraren** tillgänglig via fliken **Integrera** i portalen.

När du använder [attribut i ett klassbibliotek](functions-dotnet-class-library.md) för att konfigurera utlösare och bindningar, anges riktningen i en attributkonstruktor eller härledas från parametertypen.

## <a name="supported-bindings"></a>Bindningar som stöds

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar som förhandsgranskas eller är godkända för produktionsanvändning finns i [Språk som stöds](supported-languages.md).

## <a name="resources"></a>Resurser
- [Bindande uttryck och mönster](./functions-bindings-expressions-patterns.md)
- [Med returvärdet för Azure-funktionen](./functions-bindings-return-value.md)
- [Så här registrerar du ett bindningsuttryck](./functions-bindings-register.md)
- Testning:
  - [Strategier för att testa din kod i Azure Functions](functions-test-a-function.md)
  - [Köra en funktion som inte utlösts av HTTP manuellt](functions-manually-run-non-http.md)
- [Hantera bindningsfel](./functions-bindings-errors.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Registrera bindningstillägg för Azure Functions](./functions-bindings-register.md)
