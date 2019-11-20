---
title: Självstudie för integrering med kontinuerlig integrering och leverans pipelines med hjälp av Azure App konfiguration | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en konfigurations fil med hjälp av data i Azure App konfiguration under kontinuerlig integrering och leverans
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
ms.openlocfilehash: e9b81baed14b18c6db736bd94a2aba43a4e671ad
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185105"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrera med en CI/CD-pipeline

I den här artikeln beskrivs olika sätt att använda data från Azure App konfiguration i en kontinuerlig integrering och ett kontinuerligt distributions system.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Använd app-konfiguration i din Azure DevOps-pipeline

Om du har en Azure DevOps-pipeline kan du hämta nyckel värden från App-konfigurationen och ange dem som variabler för aktiviteter. [Tillägget Azure App Configuration DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) är en modul för tillägg som tillhandahåller den här funktionen. Följ bara anvisningarna för att använda tillägget i en aktivitetssekvens för att bygga eller släppa.

## <a name="deploy-app-configuration-data-with-your-application"></a>Distribuera konfigurations data för appar med ditt program

Programmet kanske inte kan köras om det är beroende av Azure App konfiguration och inte kan komma åt det. Du kan förbättra programmets återhämtning för att hantera en sådan händelse, men det är troligt att det inte sker. Det gör du genom att paketera de aktuella konfigurations data i en fil som distribueras med programmet och läsas in lokalt under starten. Den här metoden garanterar att ditt program har minst standardvärden. Dessa värden skrivs över av eventuella nyare ändringar i konfigurations arkivet för appar när det är tillgängligt.

Med hjälp av [export](./howto-import-export-data.md#export-data) funktionen i Azure App konfiguration kan du automatisera processen med att hämta aktuella konfigurations data som en enda fil. Bädda sedan in den här filen i ett build-eller distributions steg i en pipeline för kontinuerlig integrering och distribution (CI/CD).

I följande exempel visas hur du inkluderar konfigurations data för appar som ett build-steg för webbappen som introduceras i snabb starterna. Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

### <a name="prerequisites"></a>Krav

Om du skapar lokalt kan du hämta och installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om du inte redan gjort det.

Om du vill göra en moln version kan du till exempel se till att [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) är installerat i versions systemet med Azure DevOps.

### <a name="export-an-app-configuration-store"></a>Exportera ett konfigurations Arkiv för appen

1. Öppna din *. CSPROJ* -fil och Lägg till följande skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Lägg till *ConnectionString* som är associerad med appens konfigurations arkiv som en miljö variabel.

2. Öppna *program.cs*och uppdatera `CreateWebHostBuilder`-metoden för att använda den EXPORTERAde JSON-filen genom att anropa `config.AddJsonFile()`-metoden.

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

1. Ange en miljö variabel med namnet **ConnectionString**och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Om du använder macOS eller Linux kör du följande kommando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

        dotnet build

3. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

        dotnet run

4. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL: en för webbappen som finns lokalt.

    ![Snabbstart av lokal app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du exporterat Azure App konfigurations data som ska användas i en distributions pipeline. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
