---
title: Integrera med en pipeline för kontinuerlig integrering och leverans som har med Azure-Appkonfiguration | Microsoft Docs
description: Lär dig hur du skapar en konfigurationsfil med hjälp av data i Azure-Appkonfiguration vid kontinuerlig integrering och leverans
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957376"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrera med en CI/CD-pipeline

För att förbättra elasticiteten i ditt program mot remote risken att du inte att kunna nå Azure konfiguration, bör du paketera aktuella konfigurationsdata i en fil som har distribuerats med programmet och läsa in lokalt under Start . Den här metoden garanterar att ditt program har inställningen standardvärden minst. Dessa värden kommer att skrivas över av nya ändringar i en appbutik konfiguration när den är tillgänglig.

Med hjälp av den [ **exportera** ](./howto-import-export-data.md#export-data) funktionen för konfiguration av Azure, kan du automatisera processen för att hämta aktuella konfigurationsdata som en enda fil. Du kan bädda in den här filen i ett bygge eller distribution steg i din kontinuerlig integrering och kontinuerlig distributionspipeline.

I följande exempel visas hur du inkluderar konfiguration av data som en version som steg för webbappen som introducerades i snabbstarter. Fullständig [skapa en ASP.NET Core-app med Appkonfiguration](./quickstart-aspnet-core-app.md) först innan du fortsätter.

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

## <a name="prerequisites"></a>Förutsättningar

Om du bygger lokalt kan ladda ned och installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du inte redan har gjort.

Om du vill göra en cloud-version med Azure DevOps exempelvis kontrollera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installeras i ditt system.

## <a name="export-app-configuration-store"></a>Exportera konfiguration appbutik

1. Öppna din *.csproj* filen och Lägg till följande:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Den *ConnectionString* som är associerade med din appkonfiguration store bör läggas till som en miljövariabel.

2. Öppna *Program.cs* och uppdatera den `CreateWebHostBuilder` metod för att använda den exporterade json-fil genom att anropa den `config.AddJsonFile()` metoden.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel som heter **ConnectionString** och ange att snabbtangent i din appbutik för konfiguration. Om du använder Windows-Kommandotolken kör du följande kommando och starta om Kommandotolken så att ändringen ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux, kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. För att skapa en appversion med .NET Core CLI, kör du följande kommando i kommandogränssnittet:

        dotnet build

3. När versionen har slutförts, kör du följande kommando för att köra webbappen lokalt:

        dotnet run

4. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL för web-app som finns lokalt.

    ![Snabbstart för start av appen lokalt](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Nästa steg

* [Hanterade identiteter för integrering av Azure-resurser](./integrate-azure-managed-service-identity.md)
