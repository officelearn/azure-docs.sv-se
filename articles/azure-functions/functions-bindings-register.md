---
title: Registrera bindningstillägg för Azure Functions
description: Lär dig att registrera ett Azure Functions-bindningstillägg baserat på din miljö.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277523"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrera bindningstillägg för Azure Functions

I Azure Functions version 2.x är [bindningar](./functions-triggers-bindings.md) tillgängliga som separata paket från funktionernas körning. Medan .NET-funktioner använder åtkomstbindningar via NuGet-paket, tillåter tilläggspaket andra funktioner åtkomst till alla bindningar via en konfigurationsinställning.

Tänk på följande objekt som rör bindningstillägg:

- Bindningstillägg registreras inte uttryckligen i Functions 1.x förutom när [du skapar ett C#-klassbibliotek med Visual Studio](#local-csharp).

- HTTP- och timerutlösare stöds som standard och kräver inget tillägg.

I följande tabell visas när och hur du registrerar bindningar.

| Utvecklingsmiljö |Registrering<br/> i Funktioner 1.x  |Registrering<br/> i Funktioner 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisk|Automatisk|
|Non-.NET språk eller lokal utveckling av Azure Core Tools|Automatisk|[Använda Azure Functions Core Tools och tilläggspaket](#extension-bundles)|
|C#-klassbibliotek med Visual Studio|[Använd NuGet-verktyg](#vs)|[Använd NuGet-verktyg](#vs)|
|C#-klassbibliotek med Visual Studio-kod|Ej tillämpligt|[Använd .NET Core CLI](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Tilläggspaket för lokal utveckling

Tilläggspaket är en distributionsteknik som gör att du kan lägga till en kompatibel uppsättning funktionsbindningstillägg i din funktionsapp. En fördefinierad uppsättning tillägg läggs till när du skapar appen. Tilläggspaket som definieras i ett paket är kompatibla med varandra, vilket hjälper dig att undvika konflikter mellan paket. Du aktiverar tilläggspaket i appens host.json-fil.  

Du kan använda tilläggspaket med version 2.x och senare versioner av funktionskörningen. När du utvecklar lokalt, se till att du använder den senaste versionen av [Azure Functions Core Tools](functions-run-local.md#v2).

Använd tilläggspaket för lokal utveckling med Hjälp av Azure Functions Core Tools, Visual Studio Code och när du skapar på distans.

Om du inte använder tilläggspaket måste du installera .NET Core 2.x SDK på den lokala datorn innan du installerar några bindningstillägg. Tilläggspaket tar bort detta krav för lokal utveckling. 

Om du vill använda tilläggspaket uppdaterar du filen *host.json* så att den innehåller följande post för: `extensionBundle`
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>C-klassbibliotek\# med Visual Studio

I **Visual Studio**kan du installera paket från Package Manager Console med kommandot Installera [paket,](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) som visas i följande exempel:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Namnet på det paket som används för en viss bindning anges i referensartikeln för den bindningen. Ett exempel finns i [avsnittet Paket i referensartikeln för Service Bus-bindning](functions-bindings-service-bus.md#functions-1x).

Ersätt `<TARGET_VERSION>` i exemplet med en specifik version `3.0.0-beta5`av paketet, till exempel . Giltiga versioner visas på de enskilda paketsidorna på [NuGet.org](https://nuget.org). Huvudversionerna som motsvarar Funktioner körtid 1.x eller 2.x anges i referensartikeln för bindningen.

Om du `Install-Package` använder för att referera till en bindning behöver du inte använda [tilläggspaket](#extension-bundles). Den här metoden är specifik för klassbibliotek som är inbyggda i Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>C#-klassbibliotek med Visual Studio-kod

I **Visual Studio Code**installerar du paket för ett C#-klassbiblioteksprojekt från kommandotolken med kommandotolken med kommandot [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) i .NET Core CLI. I följande exempel visas hur du lägger till en bindning:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI kan endast användas för Azure Functions 2.x-utveckling.

Ersätt `<BINDING_TYPE_NAME>` med namnet på paketet som innehåller den bindning du behöver. Du hittar den önskade bindningsreferensartikeln i [listan över bindningar som stöds](./functions-triggers-bindings.md#supported-bindings).

Ersätt `<TARGET_VERSION>` i exemplet med en specifik version `3.0.0-beta5`av paketet, till exempel . Giltiga versioner visas på de enskilda paketsidorna på [NuGet.org](https://nuget.org). Huvudversionerna som motsvarar Funktioner körtid 1.x eller 2.x anges i referensartikeln för bindningen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Exempel på azure-funktionsutlösare och bindning](./functions-bindings-example.md)
