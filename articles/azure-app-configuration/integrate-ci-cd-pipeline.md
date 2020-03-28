---
title: Integrera Azure App-konfiguration med hjälp av en kontinuerlig integrerings- och leveranspipeline
description: Lär dig att implementera kontinuerlig integrering och leverans med Azure App-konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: c744557471a9b37bd620bb9195bdb709c24649ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047293"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrera med en CI/CD-pipeline

I den här artikeln beskrivs hur du använder data från Azure App-konfiguration i ett system för kontinuerlig integrering och kontinuerlig distribution.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Använda appkonfiguration i din Azure DevOps Pipeline

Om du har en Azure DevOps Pipeline kan du hämta nyckelvärden från appkonfiguration och ange dem som uppgiftsvariabler. [Azure App Configuration DevOps-tillägget](https://go.microsoft.com/fwlink/?linkid=2091063) är en tilläggsmodul som tillhandahåller den här funktionen. Följ instruktionerna för att använda tillägget i en versions- eller versionsaktivitetssekvens.

## <a name="deploy-app-configuration-data-with-your-application"></a>Distribuera appkonfigurationsdata med ditt program

Ditt program kan misslyckas med att köras om det är beroende av Azure App-konfiguration och kan inte nå den. Förbättra programmets återhämtning genom att paketera konfigurationsdata i en fil som distribueras med programmet och läsas in lokalt under programstart. Den här metoden garanterar att ditt program har standardinställningsvärden vid start. Dessa värden skrivs över av alla nyare ändringar i ett App Configuration Store när det är tillgängligt.

Med hjälp av [funktionen Exportera](./howto-import-export-data.md#export-data) i Azure App Configuration kan du automatisera processen med att hämta aktuella konfigurationsdata som en enda fil. Du kan sedan bädda in den här filen i ett bygg- eller distributionssteg i din pipeline för kontinuerlig integrering och kontinuerlig distribution (CI/CD).

I följande exempel visas hur du inkluderar appkonfigurationsdata som ett byggsteg för webbappen som introducerades i snabbstarterna. Innan du fortsätter slutför du [Skapa en ASP.NET Core-app med appkonfiguration](./quickstart-aspnet-core-app.md) först.

Du kan använda vilken kodredigerare som helst för att göra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som finns på Windows-, macOS- och Linux-plattformarna.

### <a name="prerequisites"></a>Krav

Om du bygger lokalt kan du hämta och installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du inte redan har gjort det.

För att göra en molnversion, med Azure DevOps till exempel, se till att [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) är installerat i ditt byggsystem.

### <a name="export-an-app-configuration-store"></a>Exportera ett appkonfigurationsarkiv

1. Öppna *CSproj-filen* och lägg till följande skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Öppna *Program.cs*och uppdatera `CreateWebHostBuilder` metoden för att använda den exporterade `config.AddJsonFile()` JSON-filen genom att anropa metoden.  Lägg `System.Reflection` också till namnområdet.

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

### <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljövariabel med namnet **ConnectionString**och ange den till åtkomstnyckeln till appkonfigurationsarkivet. 
    Om du använder kommandotolken i Windows kör du följande kommando och startar om kommandotolken så att ändringen kan börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommandoskalet:

        dotnet build

3. När versionen har slutförts kör du följande kommando för att köra webbappen lokalt:

        dotnet run

4. Öppna ett webbläsarfönster `http://localhost:5000`och gå till , som är standard-URL för webbappen lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien exporterade du Azure App Configuration-data som ska användas i en distributionspipeline. Om du vill veta mer om hur du använder Appkonfiguration fortsätter du till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./howto-integrate-azure-managed-service-identity.md)
