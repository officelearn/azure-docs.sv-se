---
title: Självstudie – Använda Azure Key Vault med Azure-webbapp i .NET – Azure Key Vault | Microsoft Docs
description: Självstudie – Konfigurera ett ASP.NET Core-program för att läsa en hemlighet från Key Vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: b6dbae0f721983920c2073927fff74100528678e
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998803"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Självstudie: Använda Azure Key Vault med en Azure-webbapp i .NET

Med Azure Key Vault kan du skydda hemligheter, till exempel API-nycklar och databasanslutningssträngar. Det ger dig tillgång till dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du skapar ett Azure-webbprogram som kan läsa information från ett Azure-nyckelvalv. Processen använder hanterade identiteter för Azure-resurser. Mer information om Azure-webbprogram finns i [Azure App Service](../app-service/overview.md).

Artikeln visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lagra en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa ett Azure-webbprogram.
> * Aktivera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för webbappen.
> * Bevilja de behörigheter som krävs för att webbprogrammet ska kunna läsa data från nyckelvalvet.
> * Kör webbprogrammet i Azure.

Innan du fortsätter bör du läsa [grundläggande begrepp om Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Nödvändiga komponenter

* I Windows:
  * [.NET Core 2.1 SDK eller senare](https://www.microsoft.com/net/download/windows)

* På Mac:
  * [Visual Studio för Mac](https://visualstudio.microsoft.com/vs/mac/)

* Alla plattformar:
  * [Git](https://git-scm.com/downloads)
  * En Azure-prenumeration <br />(Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.)
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 eller senare, tillgänglig för Windows, Mac och Linux
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>Så här fungerar hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt så att de inte ingår i din kod. Men måste du autentisera till Azure Key Vault för att hämta dina nycklar. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Hanterad tjänstidentitet (MSI) löser problemet genom att tillhandahålla en _bootstrapidentitet_ som förenklar processen.

När du aktiverar MSI för en Azure-tjänst (till exempel: Virtual Machines, App Service, or Functions) skapar Azure ett [tjänsthuvudnamn](key-vault-whatis.md#basic-concepts). MSI gör detta för instansen av tjänsten i Azure Active Directory (Azure AD) och lägger in autentiseringsuppgifterna för tjänstens huvudnamn i den instansen.

![MSI-diagram](media/MSI.png)

Koden anropar sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen för att hämta en åtkomsttoken. Koden använder den åtkomsttoken som den får från den lokala MSI_ENDPOINT för att autentisera till en Azure Key Vault-tjänst.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create).
1. Välj ett resursgruppnamn och fyll i platshållaren. I följande exempel skapas en resursgrupp i regionen USA, västra:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Du kan använda den här resursgruppen i den här självstudien.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Ange följande information för att skapa ett nyckelvalv i resursgruppen:

* Namn på nyckelvalv: en sträng med 3 till 24 tecken som endast får innehålla siffror, bokstäver och bindestreck (till exempel: 0-9, a–z, A–Z och -)
* Namn på resursgrupp
* Plats: **USA, västra**

Ange följande kommando i Azure CLI:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Nu kan du lägga till en hemlighet. Det kan vara en SQL-anslutningssträng eller annan information som du behöver hålla både skyddad och tillgänglig för ditt program.

Skriv följande kommando för att skapa en hemlighet i nyckelvalvet som kallas **AppSecret**. Den här hemligheten lagrar värdet **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Om du vill visa värdet som finns i hemligheten som oformaterad text anger du följande kommando:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Det här kommandot visar den hemliga informationen, inklusive URI:n. När du har slutfört de här stegen bör du ha en URI till en hemlighet i nyckelvalvet. Anteckna den här informationen. Du behöver den senare.

## <a name="create-a-net-core-web-app"></a>Skapa en .NET Core-webbapp

Följ den här [självstudien](../app-service/app-service-web-get-started-dotnet.md) för att skapa en .NET Core-webbapp och **publicera** den till Azure. Du kan också titta på nedanstående video:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Öppna och redigera lösningen

1. Bläddra till **Sidor** > **About.cshtml.cs-filen**.
2. Installera dessa Nuget-paket:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importera följande i kod i About.cshtml.cs-filen:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. Koden i klassen AboutModel bör se ut så här:

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
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>Kör appen

1. På huvudmenyn i Visual Studio 2017 väljer du **Felsök** > **Starta** med eller utan felsökning. 
1. När webbläsaren visas går du till sidan **Om**.
1. Värdet för **AppSecret** visas.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Aktivera en hanterad identitet för webbappen

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. [Hanterade identiteter för Azure-resurser (översikt)](../active-directory/managed-identities-azure-resources/overview.md) löser detta problem genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

1. Kör kommandot assign-identity i Azure CLI för att skapa identiteten för det här programmet:

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >Du måste ersätta \<YourAppName\> med namnet på den publicerade appen på Azure. Om namnet på din publicerade app var till exempel **MyAwesomeapp.azurewebsites.net** ersätter du \<YourAppName\> med **MyAwesomeapp**.

1. Anteckna `PrincipalId` när du publicerar programmet till Azure. Kommandot i steg 1 ger utdata i följande format:

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>Kommandot i den här proceduren fungerar på samma sätt som när du går till [portalen](https://portal.azure.com) och växlar inställningen **Identitet/Systemtilldelad** till **På** i egenskaperna för webbapp.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Tilldela behörigheter till ditt program att läsa hemligheter från Key Vault

Ersätt \<YourKeyVaultName\> med namnet på ditt nyckelvalv och \<PrincipalId\> med värdet för **PrincipalId** i följande kommando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Det här kommandot ger identiteten (MSI) för App Service behörighet att utföra åtgärderna **hämta** och **lista** i ditt nyckelvalv.

## <a name="publish-the-web-application-to-azure"></a>Publicera webbappen till Azure

Publicera din webbapp till Azure igen för att se om din livewebbapp kan hämta det hemliga värdet.

1. I Visual Studio väljer du projektet **key-vault-dotnet-core-quickstart**.
2. Välj **Publicera** > **Starta**.
3. Välj **Skapa**.

När du kör programmet bör se att det knan hämta ditt hemliga värde.

Du har nu skapat en webbapp i .NET som lagrar och hämtar sina hemligheter från Key Vault.

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
>[Utvecklarguide för Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
