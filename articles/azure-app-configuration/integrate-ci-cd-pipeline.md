---
title: Självstudie för att integrera med en kontinuerlig integrering och leverans-pipeline med hjälp av Azure-Appkonfiguration | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en konfigurationsfil med hjälp av data i Azure-Appkonfiguration vid kontinuerlig integrering och leverans
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 26bd49af7245d6e6dde3162a2e1d95c54f13e35b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415955"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrera med en CI/CD-pipeline

Den här artikeln beskrivs olika sätt att använda data från Azure-Appkonfiguration i en kontinuerlig integrering och kontinuerlig distribution.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Om du Använd Appkonfiguration i din Azure DevOps-Pipeline

Om du har en Azure DevOps-Pipeline kan du hämta nyckel-värden från Appkonfiguration och ange dem som uppgiften variabler. Den [Azure App Configuration DevOps tillägget](https://go.microsoft.com/fwlink/?linkid=2091063) är ett tilläggsmodul som tillhandahåller den här funktionen. Följ bara dess instruktionerna för att använda tillägget i en version eller frigöra aktivitetssekvensen.

## <a name="deploy-app-configuration-data-with-your-application"></a>Distribuera App konfigurationsdata med ditt program

Programmet kanske inte körs om den är beroende av konfiguration av Azure och det går inte att nå den. Du kan förbättra återhämtning av ditt program behöver bry dig om sådan händelse, men inte troligt att inträffa. Du gör detta genom att paketera aktuella konfigurationsdata i en fil som har distribuerats med programmet och läsa in lokalt under start. Den här metoden garanterar att ditt program har inställningen standardvärden minst. De här värdena skrivs över av alla nyare ändringar i en appbutik konfiguration när den är tillgänglig.

Med hjälp av den [exportera](./howto-import-export-data.md#export-data) funktionen för konfiguration av Azure, kan du automatisera processen för att hämta aktuella konfigurationsdata som en enda fil. Bädda in den här filen i ett bygge eller distribution steg i din kontinuerlig integrering och en pipeline för kontinuerlig distribution (CI/CD).

I följande exempel visas hur du inkluderar konfiguration av data som en version som steg för webbappen som introducerades i snabbstarter. Innan du fortsätter Slutför [skapa en ASP.NET Core-app med Appkonfiguration](./quickstart-aspnet-core-app.md) första.

Du kan använda valfri Kodredigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ tillgängligt på Windows, macOS och Linux-plattformar.

### <a name="prerequisites"></a>Nödvändiga komponenter

Om du bygger lokalt kan ladda ned och installera den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du inte redan har gjort.

Om du vill göra en cloud-version med Azure DevOps till exempel kontrollera att den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installeras i ditt system.

### <a name="export-an-app-configuration-store"></a>Exportera konfiguration appbutik

1. Öppna din *.csproj* filen och Lägg till följande skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Lägg till den *ConnectionString* som är associerade med din app konfiguration av lagring som en miljövariabel.

2. Öppna *Program.cs*, och uppdatera den `CreateWebHostBuilder` metod för att använda den exporterade JSON-filen genom att anropa den `config.AddJsonFile()` metoden.

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

1. Ange en miljövariabel som heter **ConnectionString**, och ange att snabbtangent i din appbutik för konfiguration. Om du använder Windows-Kommandotolken kör du följande kommando och starta om Kommandotolken så att ändringen ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux, kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i Kommandotolken:

        dotnet build

3. När bygget har slutförts kör du följande kommando för att köra webbappen lokalt.

        dotnet run

4. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL för web-app som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien får exporterade du konfiguration av Azure data som ska användas i en pipeline för distribution. Om du vill veta mer om hur du använder Appkonfiguration, fortsätter du att Azure CLI-exempel.

> [!div class="nextstepaction"]
> [Hanterad identitet integration](./howto-integrate-azure-managed-service-identity.md)
