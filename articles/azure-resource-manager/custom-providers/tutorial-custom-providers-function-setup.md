---
title: Konfigurera Azure Functions
description: Den här självstudien går igenom hur du skapar en Azure-funktionsapp och konfigurerar den så att den fungerar med Azure Custom Providers
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649990"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurera Azure-funktioner för Azure-anpassade leverantörer

En anpassad provider är ett kontrakt mellan Azure och en slutpunkt. Med anpassade leverantörer kan du ändra arbetsflöden i Azure. Den här självstudien visar hur du konfigurerar en Azure-funktionsapp för att fungera som en anpassad providerslutpunkt.

## <a name="create-the-azure-function-app"></a>Skapa Azure-funktionsappen

> [!NOTE]
> I den här självstudien skapar du en enkel tjänstslutpunkt som använder en Azure-funktionsapp. En anpassad provider kan dock använda vilken slutpunkt som helst tillgänglig för allmänheten. Alternativen inkluderar Azure Logic Apps, Azure API Management och webbappfunktionen i Azure App Service.

För att starta den här självstudien bör du först följa självstudien [Skapa din första Azure-funktionsapp i Azure-portalen](../../azure-functions/functions-create-first-azure-function.md). Den självstudien skapar en .NET core webhook-funktion som kan ändras i Azure-portalen. Det är också grunden för den nuvarande handledningen.

## <a name="install-azure-table-storage-bindings"></a>Installera Azure Table-lagringsbindningar

Så här installerar du Azure Table-lagringsbindningar:

1. Gå till fliken **Integrera** för HttpTrigger.
1. Välj **+ Ny inmatning**.
1. Välj **Azure Table Storage**.
1. Installera tillägget Microsoft.Azure.WebJobs.Extensions.Storage om det inte redan är installerat.
1. Ange **tabellförstoring**i rutan **Tabellparameternamn** .
1. Ange **myCustomResources**i rutan **Tabellnamn** .
1. Välj **Spara** om du vill spara den uppdaterade indataparametern.

![Översikt över anpassade leverantörer som visar tabellbindningar](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Uppdatera RESTful HTTP-metoder

Så här konfigurerar du Azure-funktionen så att den anpassade providern innehåller RESTful-begäransmetoder för den anpassade providern:

1. Gå till fliken **Integrera** för HttpTrigger.
1. Under **Valda HTTP-metoder**väljer du **HÄMTA**, **POST**, **TA BORT**och **LÄGG**.

![Översikt över anpassade provider som visar HTTP-metoder](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Lägg till Azure Resource Manager NuGet-paket

> [!NOTE]
> Om c#-projektfilen saknas i projektkatalogen kan du lägga till den manuellt. Eller så visas det när tillägget Microsoft.Azure.WebJobs.Extensions.Storage har installerats i funktionsappen.

Uppdatera sedan C#-projektfilen så att den innehåller användbara NuGet-bibliotek. Dessa bibliotek gör det enklare att tolka inkommande begäranden från anpassade leverantörer. Följ stegen för att lägga till [tillägg från portalen](../../azure-functions/install-update-binding-extensions-manual.md) och uppdatera C#-projektfilen så att den innehåller följande paketreferenser:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Följande XML-element är ett exempel på C#-projektfil:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du en Azure-funktionsapp för att fungera som en Azure-slutpunkt för anpassade provider.

Mer information om hur du skapar en SLUTPUNKT för en RESTful-anpassad provider finns i [Självstudiekurs: Skapa en SLUTPUNKT för en RESTful-anpassad provider](./tutorial-custom-providers-function-authoring.md).

