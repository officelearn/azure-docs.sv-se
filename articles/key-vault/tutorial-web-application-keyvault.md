---
title: 'Självstudier: Konfigurera ett Azure-webbprogram att läsa en hemlighet från Key Vault | Microsoft Docs'
description: 'Självstudier: Konfigurera ett ASP.Net Core-program att läsa en hemlighet från Key Vault'
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 521b6423550bf3e2d0bc90212b7e3fe0cbeddfc4
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167081"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Självstudier: Konfigurera ett Azure-webbprogram att läsa en hemlighet från Key Vault

Den här självstudiekursen beskriver steg för steg hur du konfigurerar ett Azure-webbprogram för att läsa information från Key Vault med hjälp av hanterade identiteter för Azure-resurser. Lär dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lagra en hemlighet i Key Vault.
> * Skapa ett Azure-webbprogram.
> * Aktivera en hanterad identitet för webbprogrammet.
> * Bevilja de behörigheter som krävs för att programmet ska kunna läsa data från Key Vault.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *ContosoResourceGroup* på platsen *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

Resursgruppen som du nyss skapade används i hela den här självstudiekursen.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

Nu ska du skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg. ”ContosoKeyVault” används som namn på nyckelvalvet i hela den här självstudiekursen, men det är viktigt att poängtera att du måste använda ett unikt namn. Ange följande information:

* Valvnamnet **ContosoKeyVault**.
* Resursgruppnamnet **ContosoResourceGroup**.
* Platsen är **Östra USA**.

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

Utdata från det här kommandot visar egenskaper för nyckelvalvet som du precis skapade. Anteckna de två egenskaperna som visas nedan:

* **Valvnamn**: I det här exemplet är namnet **ContosoKeyVault**. Du använder namnet på ditt nyckelvalv för alla Key Vault-kommandon.
* **Valvets URI**: I det här exemplet är det https://<YourKeyVaultName>.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

>[!IMPORTANT]
> Om du får felet Parameter 'vault_name' must conform to the following pattern: '^[a-zA-Z0-9-]{3,24}$' (Parametern "vault_name" måste ha följande mönster: '^[a-zA-Z0-9-]$') betyder det att -name-parametervärdet inte var unikt eller att det inte uppfyllde kravet på en alfanumerisk sträng med 3 till 24 tecken.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som du behöver skydda men göra tillgänglig för ditt program. I den här självstudiekursen kallas lösenordet **AppSecret** och lagrar värdet för **MySecret**.

Skriv kommandona nedan för att skapa en hemlighet i Key Vault med namnet **AppSecret** som ska lagra värdet **MySecret**:

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

Det här kommandot visar den hemliga informationen, inklusive URI:n. När du har slutfört de här stegen bör du ha en URI till en hemlighet i Azure Key Vault. Anteckna den här informationen. Du behöver den senare.

## <a name="create-a-web-app"></a>Skapa en webbapp

I det här avsnittet ska du skapa ett ASP.NET MVC-program och distribuera det i Azure som en webbapp. Mer information om Azure Web Apps finns i [Översikt över Web Apps](../app-service/app-service-web-overview.md).

1. Skapa ett nytt projekt i Visual Studio genom att välja **Arkiv > Nytt > Projekt**. 

2. I dialogrutan **Nytt projekt** väljer du **Visual C# > Webb > ASP.NET Core-webbtillämpningsprogram**.

3. Ge programmet namnet **WebKeyVault** och välj sedan **OK**.
   >[!IMPORTANT]
   > Du måste döpa appen till WebKeyVault så att koden som du kopierar och klistrar in matchar namnområdet. Om du döpte webbplatsen till något annat måste du ändra koden så att den matchar namnet på webbplatsen.

    ![Dialogrutan Nytt ASP.NET-projekt](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Du kan distribuera alla typer av ASP.NET Core-webbappar till Azure. I den här självstudiekursen väljer du mallen **Webbprogram** och ser till att autentiseringen är inställd på **Ingen autentisering**.

    ![ASPNET-dialogrutan Ingen autentisering](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Välj **OK**.

6. När ASP.NET Core-projektet har skapats visas välkomstsidan för ASP.NET Core med ett antal länkar till resurser som hjälper dig att komma igång.

7. På menyn väljer du **Felsöka > Starta utan felsökning** för att köra webbappen lokalt.

## <a name="modify-the-web-app"></a>Ändra webbappen

Du måste installera två NuGet-paket för webbprogrammet. Installera dem genom att följa stegen nedan:

1. Högerklicka på ditt webbplatsnamn i Solution Explorer.
2. Välj **Hantera NuGet-paket för lösning...**
3. Markera kryssrutan bredvid sökrutan. **Ta med förhandsversion**
4. Sök efter de tre NuGet-paket som anges nedan och godkänn att de läggs till i din lösning:

    * [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) – gör det enkelt att hämta åtkomsttoken för autentiseringsscenarier av typen ”tjänst till Azure-tjänst”. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) innehåller metoder för interaktion med Key Vault.
    * [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) – innehåller `IConfiguration`-tillägg för Azure Key Vault

5. Öppna `Program.cs` i Solution Explorer och ersätt innehållet i filen Program.cs med följande kod. Ersätt ```<YourKeyVaultName>``` med namnet på ditt nyckelvalv:

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
            ).UseStartup<Startup>()
             .Build();

           private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
         }
    }
    ```

6. Navigera till avsnittet **Sidor** med hjälp av Solution Explorer och öppna `About.cshtml`. Ersätt innehållet i **About.cshtml.cs** med koden nedan:

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

7. Från huvudmenyn väljer du **Felsöka** > **Starta utan felsökning**. När webbläsaren visas går du till sidan **Om**. Värdet för AppSecret visas.

>[!IMPORTANT]
> Om du får ett meddelande av typen HTTP Error 502.5 - Process Failure
> > ska du kontrollera namnet på nyckelvalvet som anges i `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Publicera webbappen till Azure

1. Välj **WebKeyVault** ovanför redigeraren.
2. Välj **Publicera** och sedan **Start**.
3. Skapa en ny **apptjänst** och välj **Publicera**.
4. Välj **Skapa**.

>[!IMPORTANT]
> Ett webbläsarfönster öppnas och ett meddelande av typen 502.5 – Processfel visas. Detta är normalt. Du måste bevilja programidentiteten behörighet att läsa hemligheter från Key Vault.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Aktivera en hanterad identitet för webbappen

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. [Hanterade identiteter för Azure-resurser (översikt)](../active-directory/managed-identities-azure-resources/overview.md) löser detta problem på ett enklare sätt genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

1. Gå tillbaka till Azure CLI
2. Kör kommandot assign-identity för att skapa identiteten för det här programmet:

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>Det här kommandot fungerar på samma sätt som när du går till portalen och väljer **På** för **Identitet/Systemtilldelad** i egenskaperna för webbprogrammet.

## <a name="grant-rights-to-the-application-identity"></a>Bevilja behörighet till programidentiteten

Gå till åtkomstprinciperna för Key Vault på Azure Portal och tilldela dig själv åtkomsten Hemlighetshantering för Key Vault. På så sätt kan du köra programmet på din lokala utvecklingsdator.

1. Sök efter ditt nyckelvalv i dialogrutan **Sök efter resurser** på Azure Portal.
2. Välj **Åtkomstprinciper**.
3. Välj **Lägg till ny** och välj **Hämta** och **Lista** i avsnittet **Hemliga behörigheter**.
4. Välj **Välj tjänstens huvudnamn** och lägg till programidentiteten. Den har samma namn som programmet.
5. Välj **OK**.

Nu har ditt konto i Azure och programidentiteten behörighet att läsa information från Key Vault. När du uppdaterar sidan bör du se webbplatsens landningssida. Om du väljer **Om** ser du värdet som du lagrade i Key Vault.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort en resursgrupp och alla dess resurser genom att köra kommandot **az group delete**.

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utvecklarguide för Azure Key Vault](key-vault-developers-guide.md)
