---
title: Självstudiekurs för att använda Azure App Configuration Key Vault-referenser i en ASP.NET Core-app | Microsoft-dokument
description: I den här självstudien får du lära dig hur du använder Azure App Configurations nyckelvalvsreferenser från en ASP.NET Core-app
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 4641c50f0579e2a8db514df58c0401eb2173d793
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309056"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Självstudiekurs: Använd Key Vault-referenser i en ASP.NET Core-app

I den här självstudien får du lära dig hur du använder Azure App Configuration-tjänsten tillsammans med Azure Key Vault. Appkonfiguration och Key Vault är kompletterande tjänster som används sida vid sida i de flesta programdistributioner.

Appkonfiguration hjälper dig att använda tjänsterna tillsammans genom att skapa nycklar som referensvärden som lagras i Key Vault. När appkonfiguration skapar sådana nycklar lagras URI:erna för key vault-värden i stället för själva värdena.

Ditt program använder appkonfigurationsklientleverantören för att hämta Key Vault-referenser, precis som för alla andra nycklar som lagras i appkonfiguration. I det här fallet är de värden som lagras i appkonfiguration URI:er som refererar till värdena i nyckelvalvet. De är inte key vault-värden eller autentiseringsuppgifter. Eftersom klientleverantören känner igen nycklarna som Key Vault-referenser används Key Vault för att hämta sina värden.

Ditt program ansvarar för att autentisera korrekt till både appkonfiguration och Nyckelvalv. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du implementerar Key Vault-referenser i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter slutför du [Skapa en ASP.NET Core-app med appkonfiguration](./quickstart-aspnet-core-app.md) först.

Du kan använda vilken kodredigerare som helst för att göra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är till exempel en plattformsoberoende kodredigerare som är tillgänglig för operativsystemen Windows, macOS och Linux.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en appkonfigurationsnyckel som refererar till ett värde som lagras i Key Vault.
> * Få tillgång till värdet för den här nyckeln från ett ASP.NET Core-webbprogram.

## <a name="prerequisites"></a>Krav

Innan du startar den här självstudien installerar du [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen:

    ![Utdata när skapandet av nyckelvalvet har slutförts](./media/quickstarts/search-services.png)
1. I sökrutan anger du **Nyckelvalv**.
1. Välj **Nyckelvalv** till vänster i resultatlistan.
1. Välj **Lägg till**i **Nyckelvalv.**
1. Till höger i **Skapa nyckelvalv**anger du följande information:
    - Välj **Prenumeration** om du vill välja en prenumeration.
    - Välj **Skapa nytt** i **Resursgrupp**och ange ett resursgruppnamn.
    - I **Key vault-namnet**krävs ett unikt namn. För den här självstudien anger du **Contoso-vault2**.
    - Välj en plats i listrutan **Region.**
1. Lämna de andra alternativen **för skapa nyckelvalv** med sina standardvärden.
1. Välj **Skapa**.

Nu är ditt Azure-konto det enda som har behörighet att komma åt det här nya valvet.

![Utdata när skapandet av nyckelvalvet har slutförts](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Om du vill lägga till en hemlighet i valvet måste du bara ta några ytterligare steg. I det här fallet lägger du till ett meddelande som du kan använda för att testa hämtning av Nyckelvalv. Meddelandet heter **Meddelande**och du lagrar värdet "Hej från Nyckelvalv" i det.

1. På egenskapssidorna För Nyckelvalv väljer du **Hemligheter**.
1. Välj **Generera/importera**.
1. Ange följande värden i fönstret **Skapa ett hemligt:**
    - **Ladda upp alternativ:** Ange **manuell**.
    - **Namn**: Ange **meddelande**.
    - **Värde**: Ange **Hej från Key Vault**.
1. Lämna den andra **Skapa en hemlig** egenskaper med sina standardvärden.
1. Välj **Skapa**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägga till en referens för Key Vault i appkonfiguration

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser**och välj sedan den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Konfigurationsutforskaren**.

1. Välj **+ Skapa** > **referens för nyckelvalv**och ange sedan följande värden:
    - **Nyckel:** Välj **TestApp:Inställningar:KeyVaultMessage**.
    - **Etikett**: Lämna det här värdet tomt.
    - **Prenumeration,** **resursgrupp**och **nyckelvalv:** Ange de värden som motsvarar värdena i nyckelvalvet som du skapade i föregående avsnitt.
    - **Hemlighet:** Välj den hemlighet som heter **Meddelande** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Anslut till Nyckelvalv

1. I den här självstudien använder du ett tjänsthuvudnamn för autentisering till Key Vault. Om du vill skapa det här tjänstens huvudnamn använder du kommandot Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie nyckel-/värdepar:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Kör följande kommando så att tjänstens huvudnamn kan komma åt nyckelvalvet:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Lägg till miljövariabler för att lagra värdena *för clientId,* *clientSecret*och *tenantId*.

    #### <a name="windows-command-prompt"></a>[Kommandotolken i Windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Dessa Key Vault-autentiseringsuppgifter används endast i ditt program. Ditt program autentiserar direkt till Key Vault med dessa autentiseringsuppgifter. De skickas aldrig till tjänsten App Configuration.

1. Starta om terminalen för att läsa in dessa nya miljövariabler.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera koden för att använda en Referens för Nyckelvalv

1. Lägg till en referens till de Nödvändiga NuGet-paketen genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Öppna *Program.cs*och lägg till referenser till följande paket som krävs:

    ```csharp
    using Azure.Identity;
    ```

1. Uppdatera `CreateWebHostBuilder` metoden för att använda `config.AddAzureAppConfiguration` appkonfiguration genom att anropa metoden. Inkludera `ConfigureKeyVault` alternativet och skicka rätt autentiseringsuppgifter till key vault.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. När du initierade anslutningen till appkonfigurationen konfigurerar du anslutningen `ConfigureKeyVault` till Key Vault genom att anropa metoden. Efter initieringen kan du komma åt värdena för Key Vault-referenser på samma sätt som du öppnar värdena för vanliga appkonfigurationsnycklar.

    Om du vill se den här processen i praktiken öppnar du *Index.cshtml* i**mappen** **Vyer.** >  Ersätt innehållet med följande kod:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Du kommer åt värdet för referenstestappen För **nyckelvalvet:Inställningar:KeyVaultMessage** på samma sätt som för konfigurationsvärdet för **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommandoskalet:

    ```dotnetcli
    dotnet build
    ```

1. När versionen är klar använder du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

1. Öppna ett webbläsarfönster och `http://localhost:5000`gå till , som är standard-URL:en för webbappen lokalt.

    ![Snabbstart lokal applansering](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en appkonfigurationsnyckel som refererar till ett värde som lagras i Key Vault. Om du vill veta hur du lägger till en Azure-hanterad tjänstidentitet som effektiviserar åtkomsten till Appkonfiguration och Nyckelvalv fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./howto-integrate-azure-managed-service-identity.md)
