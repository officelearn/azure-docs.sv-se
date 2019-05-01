---
title: Registrera Azure Functions tillägg av bindning
description: Lär dig att registrera ett tillägg för Azure Functions-bindning som baserat på din miljö.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 802e177b6f3844abe4d24c26b7ea2d0d4fb1688c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697002"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrera Azure Functions tillägg av bindning

Från och med Azure Functions version 2.x, [bindningar](./functions-triggers-bindings.md) är tillgängliga som separata paket från functions-körning. Även om .NET-funktioner åt bindningar via NuGet-paket, Tillåt tillägg paket andra funktioner åtkomst till alla bindningar via en konfigurationsinställning.

Överväg följande objekt som hör till bindande tillägg:

- Tillägg av bindning uttryckligen är inte registrerade i Functions 1.x förutom när [skapar en C# med hjälp av Visual Studio 2017-klassbiblioteket](#local-csharp).

- Utlösare för HTTP och timer som stöds som standard och kräver inte ett tillägg.

I följande tabell anger när och hur du registrerar bindningar.

| Utvecklingsmiljö |Registrering<br/> i funktioner 1.x  |Registrering<br/> i funktioner 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisk|Automatisk|
|Icke-.NET-språk eller lokal Azure Core Tools-utveckling|Automatisk|[Använd Azure Functions Core Tools och tillägget paket](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#-klassbibliotek har med hjälp av Visual Studio 2017|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2017)|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2017)|
|C#-klassbibliotek har med hjälp av Visual Studio Code|Gäller inte|[Använda .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Lokal utveckling med Azure Functions Core Tools och tillägget paket

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>C#-klassbibliotek med Visual Studio 2017

I **Visual Studio 2017**, du kan installera paket från Package Manager-konsolen med hjälp av den [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) kommandot, som visas i följande exempel:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Namnet på paketet som används för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<TARGET_VERSION>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

## <a name="c-class-library-with-visual-studio-code"></a>C#-klassbibliotek med Visual Studio Code

I **Visual Studio Code**, du kan installera paket från en kommandotolk med hjälp av den [dotnet lägga till paketet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) kommando i CLI för .NET Core, som visas i följande exempel:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core CLI kan endast användas för Azure Functions 2.x-utveckling.

Namnet på paketet som ska användas för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<TARGET_VERSION>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Azure Function-utlösare och bindningen exempel](./functions-bindings-example.md)

