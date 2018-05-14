---
title: Konfigurera ett Azure-webbprogram att läsa en hemlighet från nyckelvalvet | Microsoft Docs
description: Kursen konfigurera ett ASP.Net core-program att läsa en hemlighet från nyckelvalvet
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: b4e317a82b93513c6161d9da0c55883e99580cbb
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Självstudier: Konfigurera ett Azure-webbprogram att läsa en hemlighet från Nyckelvalvet

I den här självstudiekursen gå igenom nödvändiga steg för att hämta ett Azure-webbprogram att läsa information från nyckelvalvet med hanteringstjänster identiteter. Lär dig att:

> [!div class="checklist"]
> * Skapa en Nyckelvalvet.
> * Lagra en hemlighet i Nyckelvalvet.
> * Skapa ett Azure-webbprogram.
> * Aktivera hanteringstjänster identiteter
> * Bevilja behörigheterna som krävs för programmet att läsa data från nyckelvalvet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

Om du vill logga in på Azure med hjälp av CLI, skriver du:

```azurecli
az login
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

Resursgruppen som du nyss skapade används i hela den här kursen.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

Nu kan du skapa ett Nyckelvalv i resursgruppen som du skapade i föregående steg. Du måste ange en del information:

>[!NOTE]
> Även om ”ContosoKeyVault” används som namn på vår Key Vault under hela den här självstudiekursen, måste du använda ett unikt namn.

* Valvnamnet **ContosoKeyVault**.
* Resursgruppnamnet **ContosoResourceGroup**.
* Platsen är **Östra USA**.

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

Kommandots utdata visar egenskaperna för den nyligen skapade Nyckelvalvet. Anteckna de två egenskaperna som visas nedan:

* **Valvnamn**: I det här exemplet är namnet **ContosoKeyVault**. Namnet på ditt Nyckelvalv används för alla Key Vault-kommandon.
* **Valvet URI**: I det här exemplet är https://<YourKeyVaultName>.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

>[!IMPORTANT]
> Om det uppstår fel parametern 'vault_name' måste uppfylla följande mönster: ' ^ [en-öA-Z0 - 9-]{3,24}$”-namnet param värde är inte unikt eller överensstämde inte med en sträng som består av alfanumeriska tecken från 3 till 24 lång.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-key-vault"></a>Lägg till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur det fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som behövs för att hålla på ett säkert sätt men gör tillgängligt för ditt program. I den här självstudiekursen lösenordet anropas **AppSecret** och lagrar värdet för **MinHemlighet** i den.

Skriv kommandon nedan för att skapa en hemlighet i Nyckelvalvet kallas **AppSecret** som lagrar värdet **MinHemlighet**:

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

Det här kommandot visar den hemliga information, inklusive URI: N. När du har slutfört de här stegen bör du ha en URI för en hemlighet i en Azure Key Vault. Anteckna den här informationen. Du behöver den senare.

## <a name="create-a-web-app"></a>Skapa en webbapp

I det här avsnittet skapar du ett ASP.NET MVC-program och distribuerar den i Azure som en Webbapp. Läs mer om Azure Web Apps [översikt över Web Apps](../app-service/app-service-web-overview.md).

1. Skapa ett nytt projekt i Visual Studio genom att välja **Arkiv > Nytt > Projekt**. 

2. I dialogrutan **Nytt projekt** väljer du **Visual C# > Webb > ASP.NET Core-webbtillämpningsprogram**.

3. Ge programmet namnet **WebKeyVault**, och välj sedan **OK**.
   >[!IMPORTANT]
   > Du måste namnge appen WebKeyVault så du kopiera och klistra in koden matchar namnområdet. Om du namn på webbplats något annat behöver du ändra koden för att matcha namnet på webbplatsen.

    ![Dialogrutan Nytt ASP.NET-projekt](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Du kan distribuera alla typer av ASP.NET Core-webbappar till Azure. För den här kursen väljer du den **webbprogram** mall, och kontrollera att autentisering har angetts till **ingen autentisering**.

    ![ASPNET dialogrutan ingen autentisering](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Välj **OK**.

6. När ASP.NET Core-projektet har skapats visas välkomstsidan för ASP.NET Core med ett antal länkar till resurser som hjälper dig att komma igång.

7. På menyn väljer du **Felsöka > Starta utan felsökning** för att köra webbappen lokalt.

## <a name="modify-the-web-app"></a>Ändra webbappen

Det finns två NuGet-paket som ditt webbprogram måste ha installerats. Följ stegen nedan för att installera dem:

1. I solution explorer högerklickar du på webbplatsens namn.
2. Välj **hantera NuGet-paket för lösningen...**
3. Markera kryssrutan bredvid sökrutan. **Inkludera förhandsversion**
4. Sök efter två NuGet-paket som anges nedan och acceptera att läggas till i din lösning:

    * [Microsoft.Azure.Services.AppAuthentication (förhandsgranskning)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -gör det enkelt att hämta åtkomsttoken för Service to Azure Service autentiseringsscenarier. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) -innehåller metoder för att interagera med Key Vault.

5. Använd Solution Explorer för att öppna `Program.cs` och Ersätt innehållet i filen Program.cs med följande kod. Ersätt ```<YourKeyVaultName>``` med namnet på ditt nyckelvalv:

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
        namespace WebKeyVault
        {
        public class Program
        {
        public static void Main(string[] args)
        {
        BuildWebHost(args).Run();
        }
    
            public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                }
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Använd Solution Explorer för att navigera till den **sidor** avsnittet och öppna `About.cshtml`. Ersätt innehållet i **About.cshtml.cs** med koden nedan:

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. Huvudmenyn, välja **felsöka** > **starta utan Debugging**. När det visas i webbläsaren, navigera till den **om** sidan. Värdet för AppSecret visas.

>[!IMPORTANT]
> Om du får en HTTP-fel 502.5 - processfel message Kontrollera namnet på det Nyckelvalv som anges i `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Publicera webbappen till Azure

1. Ovan redigeraren Välj **WebKeyVault**.
2. Välj **publicera**.
3. Välj **publicera** igen.
4. Välj **skapa**.

>[!IMPORTANT]
> Ett fönster i webbläsaren öppnas och du ser ett 502.5 - fel i meddelande. Detta är förväntat. Du måste ge programmet identitet behörighet att läsa hemligheter från Nyckelvalvet.

## <a name="enable-managed-service-identity"></a>Aktivera hanterade tjänstidentiteten

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter och andra nycklar och hemligheter, men din kod måste autentisera till Key Vault de. Hanterad Service identitet (MSI) gör att lösa problemet enklare genom att ge en automatiskt hanterade identitet i Azure-tjänster i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault utan några autentiseringsuppgifter i koden.

1. Gå tillbaka till Azure CLI
2. Kör kommandot Tilldela identitet för att skapa identiteten för det här programmet:

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>Detta motsvarar gå till portalen och växla **hanterade tjänstidentiteten** till **på** i webbegenskaper.

## <a name="grant-rights-to-the-application-identity"></a>Bevilja behörighet till programidentiteten

Med hjälp av Azure portal, gå till den Key Vault åtkomstprinciper och bevilja dig själv hemlighet Management åtkomst till Nyckelvalvet. Detta kan du köra programmet på utvecklingsdatorn lokala.

1. Sök efter ditt Nyckelvalv i den **Sök efter resurser** dialogrutan i Azure-portalen.
2. Välj **åtkomstprinciper**.
3. Välj **Lägg till ny**i den **hemliga behörigheterna** avsnittet väljer **hämta** och **listan**.
4. Välj **Välj huvudnamn**, och Lägg till programidentiteten. Den har samma namn som programmet.
5. Välj **Ok**

Nu har ditt konto i Azure och programidentiteten behörighet att läsa information från Nyckelvalvet. Om du uppdaterar sidan bör du se Landningssida för platsen. Om du väljer **om**. Du kan se värdet i Nyckelvalvet.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort en resursgrupp och alla dess resurser genom att använda den **ta bort grupp az** kommando.

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Key Vault-Guide för utvecklare](key-vault-developers-guide.md)
