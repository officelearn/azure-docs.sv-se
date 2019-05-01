---
title: Självstudie – Använda Azure Key Vault med Azure Web App i .NET | Microsoft Docs
description: I den här självstudien konfigurerar du ett ASP.NET core-program att läsa en hemlighet från ditt nyckelvalv.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 42c8d863a58c5f5d8f47f6686aa9a5b8f80277d2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710512"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Självstudier: Använda Azure Key Vault med en Azure-webbapp i .NET

Med Azure Key Vault kan du skydda hemligheter, till exempel API-nycklar och databasanslutningssträngar. Det ger dig tillgång till dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du skapar ett Azure-webbprogram som kan läsa information från ett Azure-nyckelvalv. Processen använder hanterade identiteter för Azure-resurser. Mer information om Azure-webbprogram finns i [Azure App Service](../app-service/overview.md).

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en Azure webbapp.
> * Aktivera en hanterad identitet för webbappen.
> * Tilldela behörigheter för webbappen.
> * Kör webbappen på Azure.

Innan du börjar läsa [grundläggande begrepp för Key Vault](key-vault-whatis.md#basic-concepts). 

Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Nödvändiga komponenter

* För Windows: [SDK för .NET Core 2.1 eller senare](https://www.microsoft.com/net/download/windows)
* För Mac: [Visual Studio för Mac](https://visualstudio.microsoft.com/vs/mac/)
* För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Om Hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt, så att de inte visas i din kod. Men måste du autentisera till Azure Key Vault för att hämta dina nycklar. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Hanterad tjänstidentitet (MSI) löser problemet genom att tillhandahålla en _bootstrapidentitet_ som förenklar processen.

När du aktiverar MSI för en Azure-tjänst, till exempel Azure Virtual Machines, Azure App Service eller Azure Functions, Azure skapar en [tjänstens huvudnamn](key-vault-whatis.md#basic-concepts). MSI gör detta för instansen av tjänsten i Azure Active Directory (Azure AD) och lägger in autentiseringsuppgifter för tjänstens huvudnamn i den instansen.

![MSI-diagram](media/MSI.png)

Om du vill få en åtkomsttoken, anropar sedan koden en lokala metadata-tjänst som är tillgängliga på Azure-resursen. Koden använder den åtkomsttoken som den får från den lokala MSI-slutpunkt för att autentisera till en Azure Key Vault-tjänst.

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create).

Sedan väljer ett resursgruppnamn och Fyll i platshållaren. I följande exempel skapas en resursgrupp i regionen USA, västra:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Du kan använda den här resursgruppen i den här självstudien.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Ange följande information för att skapa ett nyckelvalv i resursgruppen:

* Namn på Key vault: en sträng på 3 till 24 tecken som får endast innehålla siffror (0-9), bokstäver (a – z, A-Z) och bindestreck (-)
* Namn på resursgrupp
* Plats: **USA, västra**

Ange följande kommando i Azure-CLI:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Ditt Azure-konto är nu den enda som har behörighet för att utföra åtgärder i valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Nu kan du lägga till en hemlighet. Det kan vara en SQL-anslutningssträng eller annan information som du behöver hålla både skyddad och tillgänglig för ditt program.

Om du vill skapa en hemlighet i nyckelvalvet med namnet **AppSecret**, anger du följande kommando: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Den här hemligheten lagrar värdet **MySecret**.

Om du vill visa värdet som finns i hemligheten som oformaterad text, anger du följande kommando:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Detta kommando visar den hemliga information, inklusive URI: N. 

När du har slutfört de här stegen bör du ha en URI till en hemlighet i nyckelvalvet. Anteckna den här informationen för senare använda i den här självstudien. 

## <a name="create-a-net-core-web-app"></a>Skapa en .NET Core-webbapp

Om du vill skapa en .NET Core-webbapp och publicera den på Azure, följer du anvisningarna i [skapa en ASP.NET Core-webbapp i Azure](../app-service/app-service-web-get-started-dotnet.md). 

Du kan även se den här videon:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Öppna och redigera lösningen

1. Gå till den **sidor** > **About.cshtml.cs** fil.

1. Installera dessa Nuget-paket:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importera följande kod till den *About.cshtml.cs* fil:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Din kod i klassen AboutModel bör se ut så här:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Kör webbappen

1. På huvudmenyn för Visual Studio 2017, Välj **felsöka** > **starta**, med eller utan felsökning. 
1. I webbläsaren går du till den **om** sidan.  
    Värdet för **AppSecret** visas.

## <a name="enable-a-managed-identity"></a>Aktivera en hanterad identitet

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. [Hanterade identiteter för Azure-resurser (översikt)](../active-directory/managed-identities-azure-resources/overview.md) löser detta problem genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, inklusive Key Vault, utan att visa autentiseringsuppgifter i din kod.

Om du vill skapa identiteten för det här programmet i Azure-CLI kör du kommandot Tilldela-identity:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Ersätt \<Dittprogram > med namnet på den publicerade appen på Azure.  
    Om namnet på din publicerade app har till exempel **MyAwesomeapp.azurewebsites.net**, Ersätt \<Dittprogram > med **MyAwesomeapp**.

Anteckna `PrincipalId` när du publicerar programmet till Azure. Kommandot i steg 1 ger utdata i följande format:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Kommandot i den här proceduren är detsamma som att den [Azure-portalen](https://portal.azure.com) och växla den **identitet / System tilldelade** att ställa in **på** i webbprogrammet Egenskaper.

## <a name="assign-permissions-to-your-app"></a>Tilldela behörigheter till din app

Ersätt \<YourKeyVaultName > med namnet på ditt nyckelvalv och Ersätt \<PrincipalId > med värdet för den **PrincipalId** i följande kommando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Det här kommandot ger identity (MSI) för app service-tillstånd att göra **hämta** och **lista** åtgärder på nyckelvalvet.

## <a name="publish-the-web-app-to-azure"></a>Publicera webbappen i Azure

Publicera din webbapp till Azure igen för att verifiera att din live-webbapp kan hämta det hemliga värdet.

1. I Visual Studio väljer du projektet **key-vault-dotnet-core-quickstart**.
2. Välj **Publicera** > **Starta**.
3. Välj **Skapa**.

När du kör programmet bör se att det knan hämta ditt hemliga värde.

Du har nu skapat en webbapp i .NET som lagrar och hämtar dess hemligheter från nyckelvalvet.

## <a name="clean-up-resources"></a>Rensa resurser
När de inte längre behövs, kan du ta bort den virtuella datorn och ditt nyckelvalv.

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
>[Utvecklarguide för Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
