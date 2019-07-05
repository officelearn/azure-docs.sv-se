---
title: Registrera Azure Functions tillägg av bindning
description: Lär dig att registrera ett tillägg för Azure Functions-bindning som baserat på din miljö.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 88ffd6ec24ed19dd3b1e57277884c8759cdac1f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480338"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrera Azure Functions tillägg av bindning

I Azure Functions version 2.x, [bindningar](./functions-triggers-bindings.md) är tillgängliga som separata paket från functions-körning. Även om .NET-funktioner åt bindningar via NuGet-paket, Tillåt tillägg paket andra funktioner åtkomst till alla bindningar via en konfigurationsinställning.

Överväg följande relaterat till bindning tillägg:

- Tillägg av bindning uttryckligen är inte registrerade i Functions 1.x förutom när [skapar en C# med Visual Studio-klassbiblioteket](#local-csharp).

- HTTP- och timer utlösare stöds som standard och kräver inte ett tillägg.

I följande tabell anger när och hur du registrerar bindningar.

| Utvecklingsmiljö |Registrering<br/> i funktioner 1.x  |Registrering<br/> i funktioner 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisk|Automatisk|
|Icke-.NET-språk eller lokal Azure Core Tools-utveckling|Automatisk|[Använd Azure Functions Core Tools och tillägget paket](#extension-bundles)|
|C#med hjälp av Visual Studio 2019-klassbiblioteket|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2019)|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2019)|
|C#-klassbibliotek har med hjälp av Visual Studio Code|Gäller inte|[Använda .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="extension-bundles"></a>Tillägget paket för lokal utveckling

Tillägget paket är en teknik för lokal utveckling för version 2.x-körningen som låter dig lägga till en kompatibel uppsättning funktioner som bindning tillägg till ditt funktionsappsprojekt. Paketen tillägget ingår sedan i distributionspaketet när du distribuerar till Azure. Paket gör alla bindningar som publicerats av Microsoft som är tillgängliga via en inställning i den *host.json* fil. Tilläggspaket som definierats i ett paket är kompatibla med varandra, vilket hjälper dig att undvika konflikter mellan paket. När utveckla lokalt, gör att du använder den senaste versionen av [Azure Functions Core Tools](functions-run-local.md#v2).

Använda tillägget paket för alla lokal utveckling med hjälp av Azure Functions Core Tools eller Visual Studio Code.

Om du inte använder tillägget paket, måste du installera .NET Core 2.x SDK på den lokala datorn innan du installerar alla tillägg av bindning. Paket tar bort det här kravet för lokal utveckling. 

För att använda tillägget paket måste du uppdatera den *host.json* filen för att inkludera följande post för `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Följande egenskaper är tillgängliga i `extensionBundle`:

| Egenskap | Beskrivning |
| -------- | ----------- |
| **`id`** | Namnområde för Microsoft Azure Functions tillägget paket. |
| **`version`** | Versionen av paket att installera. Functions-körning hämtar alltid den högsta tillåtna version av definieras av versionsintervall eller intervall. Versionsvärdet kan alla paket-versioner från 1.0.0 upp till men inte inklusive 2.0.0. Mer information finns i den [notation för intervall för att ange versionsintervall](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Paketera versioner ökningen som paket i paket ändringen. Huvudversion ändringar sker när paket i bunten ökar med en högre version som vanligtvis är i linje med en ändring i den huvudsakliga versionen av Functions-körning.  

Den aktuella uppsättningen av tillägg installerade som standard paketet räknas upp i det här [extensions.json filen](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio-2019"></a>C\# -klassbiblioteket med Visual Studio 2019

I **Visual Studio 2019**, du kan installera paket från Package Manager-konsolen med hjälp av den [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) kommandot, som visas i följande exempel:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Namnet på paketet som används för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<TARGET_VERSION>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

## <a name="c-class-library-with-visual-studio-code"></a>C#-klassbibliotek med Visual Studio Code

> [!NOTE]
> Vi rekommenderar att du använder [tillägget paket](#extension-bundles) har funktioner som automatiskt ska installera en kompatibel uppsättning bindning tilläggspaket.

I **Visual Studio Code**, paket installeras för en C# klassbiblioteksprojektet från en kommandotolk med hjälp av den [dotnet lägga till paketet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) kommando i CLI för .NET Core, som visas i följande exempel:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core CLI kan endast användas för Azure Functions 2.x-utveckling.

Namnet på paketet som ska användas för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<TARGET_VERSION>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Azure Function-utlösare och bindningen exempel](./functions-bindings-example.md)

