---
title: Registrera Azure Functions bindnings tillägg
description: Lär dig att registrera ett Azure Functions bindnings tillägg baserat på din miljö.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 43bc278ea3cbd14690f1a9ac9263872536b5b174
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224789"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrera Azure Functions bindnings tillägg

Från och med Azure Functions version 2. x är [bindningar](./functions-triggers-bindings.md) tillgängliga som separata paket från Functions-körningen. .NET-funktioner har åtkomst till bindningar via NuGet-paket, men tilläggs paket ger andra funktioner åtkomst till alla bindningar via en konfigurations inställning.

Tänk på följande saker som rör bindnings tillägg:

- Bindnings tillägg registreras inte explicit i functions 1. x, förutom när du [skapar ett C#-klass bibliotek med Visual Studio](#local-csharp).

- HTTP-och timer-utlösare stöds som standard och kräver inte något tillägg.

I följande tabell visas när och hur du registrerar bindningar.

| Utvecklingsmiljö |Registrering<br/> i funktioner 1. x  |Registrering<br/> i functions 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatiskt|Autokorrigering<sup>*</sup>|
|Non-.NET-språk eller utveckling av lokala Azure Core-verktyg|Automatiskt|[Använda Azure Functions Core Tools-och paket paket](#extension-bundles)|
|C#-klass bibliotek med Visual Studio|[Använda NuGet-verktyg](#vs)|[Använda NuGet-verktyg](#vs)|
|C#-klass bibliotek med Visual Studio Code|Ej tillämpligt|[Använd .NET Core CLI](#vs-code)|

<sup>*</sup> Portalen använder tilläggs paket.

## <a name="extension-bundles"></a><a name="extension-bundles"></a>Paket för tillägg

Paket för tillägg är ett sätt att lägga till en kompatibel uppsättning funktions bindnings tillägg i din Function-app. När du använder paket läggs en fördefinierad uppsättning tillägg till när du skapar din app. Tilläggs paket som definieras i ett paket verifieras för att vara kompatibla med varandra, vilket hjälper dig att undvika konflikter mellan paket. Med tilläggs paket kan du undvika att behöva publicera .NET-projekt kod med ett non-.NET Functions-projekt. Du aktiverar tilläggs paket i appens host.jsfil.  

Du kan använda tilläggs paket med version 2. x och senare versioner av Functions-körningen. 

Använd tilläggs paket för lokal utveckling med hjälp av Azure Functions Core Tools, Visual Studio Code och när du skapar en fjärr anslutning. När du utvecklar lokalt kontrollerar du att du använder den senaste versionen av [Azure Functions Core tools](functions-run-local.md#v2). Tilläggs paket används också när du utvecklar funktioner i Azure Portal. 

Om du inte använder tilläggs paket måste du installera .NET Core 2. x SDK på den lokala datorn innan du [installerar eventuella bindnings tillägg](#explicitly-install-extensions). En Extensions. CSPROJ-fil, som uttryckligen definierar de nödvändiga tilläggen, läggs till i projektet. Paket för tillägg tar bort de här kraven för lokal utveckling. 

Om du vill använda tilläggs paket uppdaterar du *host.jspå* fil för att inkludera följande post för `extensionBundle` :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>Installera tillägg uttryckligen

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>NuGet-paket

För ett C#-projekt med klass biblioteks funktioner bör du installera tillägg direkt. Tilläggs paket är särskilt utformade för projekt som inte är C#-klass biblioteks-baserade.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> C- \# klass bibliotek med Visual Studio

I **Visual Studio**kan du installera paket från Package Manager-konsolen med kommandot [install-Package](/nuget/tools/ps-ref-install-package) , som du ser i följande exempel:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Namnet på det paket som används för en angiven bindning anges i referens artikeln för den bindningen. Ett exempel finns i [avsnittet paket i referens artikeln Service Bus bindning](functions-bindings-service-bus.md#functions-1x).

Ersätt `<TARGET_VERSION>` i exemplet med en angiven version av paketet, till exempel `3.0.0-beta5` . Giltiga versioner visas på de enskilda paket sidorna på [NuGet.org](https://nuget.org). De huvud versioner som motsvarar Functions Runtime 1. x eller 2. x anges i referens artikeln för bindningen.

Om du använder `Install-Package` för att referera till en bindning behöver du inte använda [tilläggs paket](#extension-bundles). Den här metoden är speciell för klass bibliotek som skapats i Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> C#-klass bibliotek med Visual Studio Code

I **Visual Studio Code**installerar du paket för ett C#-klass biblioteks projekt från kommando tolken med kommandot [dotNet Lägg till paket](/dotnet/core/tools/dotnet-add-package) i .net Core cli. Följande exempel visar hur du lägger till en bindning:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI kan bara användas för Azure Functions 2. x-utveckling.

Ersätt `<BINDING_TYPE_NAME>` med namnet på det paket som innehåller den bindning du behöver. Du hittar den önskade bindnings referens artikeln i [listan över bindningar som stöds](./functions-triggers-bindings.md#supported-bindings).

Ersätt `<TARGET_VERSION>` i exemplet med en angiven version av paketet, till exempel `3.0.0-beta5` . Giltiga versioner visas på de enskilda paket sidorna på [NuGet.org](https://nuget.org). De huvud versioner som motsvarar Functions Runtime 1. x eller 2. x anges i referens artikeln för bindningen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Azure Function-utlösare och bindnings exempel](./functions-bindings-example.md)
