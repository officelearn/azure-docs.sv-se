---
title: Självstudie för att använda Azure App konfiguration Key Vault referenser i en ASP.NET Core app | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure App konfigurations Key Vault referenser från en ASP.NET Core-app
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
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035860"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Självstudier: Använda Key Vault referenser i en ASP.NET Core-app

I den här självstudien får du lära dig hur du använder Azure App konfigurations tjänsten tillsammans med Azure Key Vault. Detta är kompletterande tjänster som kommer att användas sida vid sida i de flesta program distributioner. För att hjälpa dig att använda dem tillsammans kan du med app-konfigurationen skapa nycklar som refererar till värden som lagras i Key Vault. När du gör detta lagrar app-konfigurationen URI: n till Key Vault värde, i stället för själva värdet. Ditt program hämtar värdet för den här nyckeln med hjälp av klient leverantören för appens konfiguration, precis som alla andra nycklar som lagras i appens konfiguration. Klient leverantören känner igen den som en Key Vault referens och anropar Key Vault för att hämta värdet. Ditt program ansvarar för att autentisera korrekt för både app-konfigurationen och Key Vault. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du kan implementera Key Vault referenser i din kod. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en konfigurations nyckel för appen som refererar till ett värde som lagras i Key Vault
> * Få åtkomst till värdet för den här nyckeln från ett ASP.NET Core-webbprogram

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här själv studie kursen installerar du [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **Skapa en resurs** uppe till vänster i Azure Portal

    ![Utdata när nyckelvalvet har skapats](./media/quickstarts/search-services.png)
2. Skriv **Key Vault** i sökrutan.
3. Välj **Key Vault** i listan med resultat.
4. Välj **Skapa** i avsnittet Key Vault.
5. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:
    - **Namn på**: Ett unikt namn krävs. I den här snabb starten använder vi **contoso-vault2**. 
    - **Prenumeration**: Välj en prenumeration.
    - Under **resurs grupp**väljer du **Skapa ny** och anger ett resurs grupp namn.
    - Välj en plats i listrutan **Plats**.
    - Lämna standardvärdena för de andra alternativen.
6. När du har angett den här informationen väljer du **Skapa**.

I det här läget är ditt Azure-konto det enda som har behörighet att komma åt det här nya valvet.

![Utdata när nyckelvalvet har skapats](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

När du ska lägga till en hemlighet i valvet behöver du bara utföra några ytterligare steg. I det här fallet lägger vi till ett meddelande som vi kan använda för att testa Key Vault hämtning. Meddelandet kallas **meddelande** och vi lagrar värdet **Hello från Key Vault** i det.

1. På sidan Key Vault egenskaper väljer du **hemligheter**.
1. Klicka på **Generera/importera**.
1. Välj följande värden på skärmen **Skapa en hemlighet**:
    - **Uppladdningsalternativ**: Manuell.
    - **Namn på**: Message
    - **Värde**: Hej från Key Vault
    - Lämna standardvärdena för de andra alternativen. Klicka på **Skapa**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Lägg till en Key Vault referens till appens konfiguration

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **alla resurser**och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Klicka på **konfigurations Utforskaren**

1. Klicka på **+ skapa** > **Key Vault-referens** och välj följande värden:
    - **Nyckel**: TestApp: inställningar: KeyVaultMessage
    - **Etikett**: Lämna tomt
    - **Prenumeration**, **resurs grupp**, **nyckel valv**: Välj de alternativ som motsvarar de Key Vault som du skapade i föregående avsnitt.
    - **Hemlighet**: Välj hemligheten som kallas **meddelandet** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Anslut till Key Vault

1. I den här självstudien använder du ett huvud namn för tjänsten för autentisering till nyckel valv. Om du vill skapa tjänstens huvud namn använder du kommandot Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie med nyckel/värde-par. 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Kör följande kommando för att ge tjänstens huvud namn åtkomst till ditt nyckel valv:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Lägg till hemligheter för *clientId* och *ClientSecret* i hemligheter Manager. De här kommandona måste köras i samma katalog som *. CSPROJ* -filen.

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Dessa Key Vault autentiseringsuppgifter används endast i ditt program. Programmet autentiseras direkt till Key Vault med dessa autentiseringsuppgifter. De skickas aldrig till appens konfigurations tjänst.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera din kod för att använda en Key Vault referens

1. Öppna *program.cs*och Lägg till referenser till nödvändiga paket.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Uppdatera metoden `CreateWebHostBuilder` för att använda app-konfiguration genom att anropa metoden `config.AddAzureAppConfiguration()`. Inkludera alternativet `UseAzureKeyVault`, och skicka en ny `KeyVaultClient`-referens till din Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. När du har skickat *KeyVaultClient* -referensen till metoden `UseAzureKeyVault` när du initierat anslutningen till app config, kan du komma åt värdena i Key Vault referenser på samma sätt som du kommer åt värdena för vanliga konfigurations nycklar för appen. Om du vill se hur den här processen fungerar öppnar du *index. cshtml* i vyerna > Home Directory. Ersätt dess innehåll med följande kod:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Du kommer åt värdet för Key Vault Reference *TestApp: Settings: KeyVaultMessage* på samma sätt som konfiguration svärdet *TestApp: Settings: Message*

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

        dotnet build

2. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

        dotnet run

3. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL: en för webbappen som finns lokalt.

    ![Snabbstart av lokal app](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)
