---
title: Konfigurera Azure Functions för Azure anpassade Providers
description: Den här självstudien får du gå igenom hur du skapar en Azure-funktion och konfigurera den för att fungera med Azure anpassade Providers
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799996"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Konfigurera Azure Functions för Azure anpassade Providers

Anpassade providers kan du anpassa arbetsflöden på Azure. En anpassad provider är ett avtal mellan Azure och en `endpoint`. Den här självstudien går igenom processen för att skapa en Azure-funktion för att fungera som en anpassad provider `endpoint`.

Den här självstudien är uppdelad i följande steg:

- Skapa Azure-funktion
- Installera Azure Table-bindningar
- Uppdatera RESTful HTTP-metoder
- Lägg till Azure Resource Manager-NuGet-paket

Den här självstudiekursen skapar följande självstudiekurser:

- [Skapa din första Azure-funktion via Azure portal](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Skapa Azure-funktion

> [!NOTE]
> I de här självstudierna kommer vi skapa en enkel slutpunkt med hjälp av en Azure-funktion, men en anpassad provider kan använda valfri offentligt tillgänglig `endpoint`. Azure Logic Apps, Azure API Management och Azure Web Apps har du några bra alternativ.

Om du vill starta den här självstudien bör du följa självstudien [skapa din första Azure-funktion i Azure portal](../azure-functions/functions-create-first-azure-function.md). I självstudiekursen skapar en .NET core webhook-funktion som kan ändras i Azure-portalen.

## <a name="install-azure-table-bindings"></a>Installera Azure Table-bindningar

Det här avsnittet går igenom snabbsteg för att installera Azure Table storage-bindningar.

1. Navigera till den `Integrate` fliken för HttpTrigger.
2. Klicka på den `+ New Input`.
3. Välj `Azure Table Storage`.
4. Installera den `Microsoft.Azure.WebJobs.Extensions.Storage` om den inte redan är installerad.
5. Uppdatera den `Table parameter name` till ”tableStorage” och `Table name` till ”myCustomResources”.
6. Spara den uppdaterade Indataparametern.

![Översikt över anpassad provider](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Uppdatera RESTful HTTP-metoder

Det här avsnittet går igenom snabbsteg för att konfigurera Azure Function för att inkludera de anpassade providern RESTful metodbegäranden.

1. Navigera till den `Integrate` fliken för HttpTrigger.
2. Uppdatera den `Selected HTTP methods` till: Hämta POST, ta bort och PUT.

![Översikt över anpassad provider](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Ändra csproj

> [!NOTE]
> Om csproj saknas från katalogen, den kan läggas till manuellt eller kommer att visas när den `Microsoft.Azure.WebJobs.Extensions.Storage` tillägget finns installerat på funktionen.

Vi kan sedan uppdatera csproj-filen om du vill inkludera användbara NuGet-bibliotek som gör det enklare att tolka inkommande begäranden från anpassade providers. Följ stegen i [lägga till tillägg från portalen](../azure-functions/install-update-binding-extensions-manual.md) och uppdatera csproj för att inkludera följande paketet refererar till:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Exempelfilen för csproj:

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

I den här artikeln ska vi konfigurera en Azure-funktion för att fungera som en anpassad Provider för Azure `endpoint`. Gå till nästa artikel om hur du skapar en anpassad provider för RESTful `endpoint`.

- [Självstudier: Redigera en anpassad provider för RESTful-slutpunkt](./tutorial-custom-providers-function-authoring.md)
