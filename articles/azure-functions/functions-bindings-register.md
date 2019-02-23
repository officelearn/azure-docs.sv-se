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
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739765"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrera Azure Functions tillägg av bindning

Azure Functions har stöd för HTTP och timer direkt ur lådan. Om du vill arbeta med andra tjänster du behöver installera eller *registrera* en [bindning](./functions-triggers-bindings.md) tillägget. Tillägg av bindning tillhandahålls via Azure Core Tools eller NuGet-paket. 

I följande tabell anger när och hur du registrerar bindningar.

| Utvecklingsmiljö |Registrering<br/> i funktioner 1.x  |Registrering<br/> i funktioner 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatisk|[Automatisk med Kommandotolken](#azure-portal-development)|
|Icke-.NET-språk eller lokal Azure Core Tools-utveckling|Automatisk|[Använda Core Tools CLI-kommandon](#local-development-azure-functions-core-tools)|
|C#-klassbibliotek har med hjälp av Visual Studio 2017|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2017)|[Använd NuGet-verktyg](#c-class-library-with-visual-studio-2017)|
|C#-klassbibliotek har med hjälp av Visual Studio Code|Gäller inte|[Använda .NET Core CLI](#c-class-library-with-visual-studio-code)|

Följande bindningstyper av är undantag som inte kräver registrering av explicita eftersom de registreras automatiskt i alla versioner och miljöer: HTTP och timer.

> [!IMPORTANT]
> Det här innehållet fungerar för resten av den här artikeln gäller endast 2.x. Tillägg av bindning uttryckligen är inte registrerade i Functions 1.x förutom när [skapar en C# med hjälp av Visual Studio 2017-klassbiblioteket](#local-csharp).

## <a name="azure-portal-development"></a>Azure portal-utveckling

När du skapar en funktion eller lägga till en bindning, uppmanas du när tillägget för lagringsutlösare eller bindning kräver registrering. Besvarar frågan genom att klicka på **installera** att registrera tillägget. Installationen kan ta upp till 10 minuter i en förbrukningsplan.

Du behöver bara installera varje tillägg en gång för en viss funktionsapp. För stöds bindningar som inte är tillgängliga i portalen eller för att uppdatera den ett installerade tillägg, du kan också [manuellt installera eller uppdatera Azure Functions bindande tillägg från portalen](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Lokal utveckling Azure Functions Core Tools

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>C#-klassbibliotek med Visual Studio 2017

I **Visual Studio 2017**, du kan installera paket från Package Manager-konsolen med hjälp av den [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) kommandot, som visas i följande exempel:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Namnet på paketet som ska användas för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<target_version>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

## <a name="c-class-library-with-visual-studio-code"></a>C#-klassbibliotek med Visual Studio Code

I **Visual Studio Code**, du kan installera paket från en kommandotolk med hjälp av den [dotnet lägga till paketet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) kommando i CLI för .NET Core, som visas i följande exempel:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core CLI kan endast användas för Azure Functions 2.x-utveckling.

Namnet på paketet som ska användas för en viss bindning har angetts i referensartikeln för bindningen. Ett exempel finns i den [paket i Service Bus-bindning referensartikeln](functions-bindings-service-bus.md#packages---functions-1x).

Ersätt `<target_version>` i det här exemplet med en specifik version av paketet, till exempel `3.0.0-beta5`. Giltigt versioner visas på sidorna enskilda paket på [NuGet.org](https://nuget.org). Huvudversioner som motsvarar funktionskörningen 1.x och 2.x har angetts i referensartikeln för bindningen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Azure Function-utlösare och bindningen exempel](./functions-bindings-example.md)

