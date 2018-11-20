---
title: Självstudie – Så använder du Azure Key Vault med Azure Web App i .NET | Microsoft Docs
description: 'Självstudie: Konfigurera ett ASP.NET Core-program att läsa en hemlighet från Key Vault'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 6e30a181bc69678051e6d6469a2f1c533de811df
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686219"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Självstudie: Använda Azure Key Vault med Azure Web App i .NET

Azure Key Vault hjälper dig att skydda hemligheter, till exempel API-nycklar, databasanslutningssträngar som behövs för att komma åt dina program, tjänster samt IT-resurser.

I den här självstudien följer du de steg som behövs för att få ett Azure-webbprogram att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Den här självstudien baseras på [Azure Web Apps](../app-service/app-service-web-overview.md). I följande avsnitt lär du dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lagra en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa ett Azure-webbprogram.
> * Aktivera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för webbappen.
> * Bevilja de behörigheter som krävs för att webbprogrammet ska kunna läsa data från nyckelvalvet.
> * Köra webbprogrammet i Azure

Innan du fortsätter rekommenderar vi att du läser avsnittet om [grundbegreppen](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Nödvändiga komponenter

* I Windows:
  * [.NET Core 2.1 SDK eller senare](https://www.microsoft.com/net/download/windows)

* På Mac:
  * Visa [nyheter i Visual Studio för Mac](https://visualstudio.microsoft.com/vs/mac/).

* Alla plattformar:
  * Git ([ladda ned](https://git-scm.com/downloads)).
  * En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 och senare. Det här är tillgängligt för Windows, Mac och Linux.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Vad är hanterad tjänstidentitet (MSI) och hur fungerar det?
Innan vi går vidare tar vi en titt på MSI. Azure Key Vault kan lagra autentiseringsuppgifter på ett säkert sätt så att de inte ingår i din kod, men för att hämta dem behöver du autentisera till Azure Key Vault. För att autentisera till Key Vault behöver du autentiseringsuppgifter! Det är ett klassiskt bootstrap-problem. Via Azure och Azure AD tillhandahåller MSI en ”bootstrap-identitet” som gör det mycket enklare att komma igång.

Så här fungerar det. När du aktiverar MSI för en Azure-tjänst, till exempel virtuella datorer, App Service eller Functions, skapar Azure ett [tjänsthuvudnamn](key-vault-whatis.md#basic-concepts) för tjänstens instans i Azure Active Directory, och matar in autentiseringsuppgifterna för tjänsthuvudnamnet till tjänstens instans. 

![MSI](media/MSI.png)

Koden anropar sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen för att hämta en åtkomsttoken.
Koden använder den åtkomsttoken som den får från den lokala MSI_ENDPOINT för att autentisera till en Azure Key Vault-tjänst. 

Nu börjar vi med självstudien.

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Välj ett resursgruppnamn och fyll i platshållaren.
I följande exempel skapas en resursgrupp i regionen USA, västra:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Den resursgrupp du nyss skapade används i hela den här artikeln.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Nu ska du skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg. Ange följande information:

* Nyckelvalvsnamn: namnet måste vara en sträng på 3 till 24 tecken och får endast innehålla (0–9, a–z, A–Z och -).
* Namn på resursgrupp.
* Plats: **USA, västra**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som du behöver skydda men göra tillgänglig för ditt program.

Skriv följande kommandon för att skapa en hemlighet i nyckelvalvet som kallas **AppSecret**. Den här hemligheten lagrar värdet **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Det här kommandot visar den hemliga informationen, inklusive URI:n. När du har slutfört de här stegen bör du ha en URI till en hemlighet i nyckelvalvet. Anteckna den här informationen. Du behöver den senare.

## <a name="create-a-net-core-web-app"></a>Skapa en .NET Core-webbapp

Följ den här [självstudien](../app-service/app-service-web-get-started-dotnet.md) för att skapa en .NET Core-webbapp och **publicera** den till Azure **ELLER** titta på videon nedan
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Öppna och redigera lösningen

1. Navigera till Pages (Sidor) > filen About.cshtml.cs.
2. Installera dessa 2 Nuget-paket
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importera följande i filen About.cshtml.cs

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. Koden i klassen AboutModel bör se ut som nedan
    ```
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

På huvudmenyn i Visual Studio 2017 väljer du **Felsök** > **Starta** med/utan felsökning. När webbläsaren visas går du till sidan **Om**. Värdet för **AppSecret** visas.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Aktivera en hanterad identitet för webbappen

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. [Hanterade identiteter för Azure-resurser (översikt)](../active-directory/managed-identities-azure-resources/overview.md) löser detta problem på ett enklare sätt genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

1. Gå tillbaka till Azure CLI.
2. Kör kommandot assign-identity för att skapa identiteten för det här programmet: 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   Kom ihåg att du måste ersätta <YourAppName> med namnet på den publicerade appen på Azure. Om namnet på din publicerade app var MyAwesomeapp.azurewebsites.net ersätter du alltså <YourAppName> med MyAwesomeapp
 
 Utdata för kommandot ovan ser ut som följande. Anteckna PrincipalId när du publicerar programmet till Azure. Det ska vara i formatet:
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>Kommandot i den här proceduren fungerar på samma sätt som när du går till [portalen](https://portal.azure.com) och växlar inställningen **Identitet/Systemtilldelad** till **På** i egenskaperna för webbapp.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Tilldela behörigheter till ditt program att läsa hemligheter från Key Vault
        
Kör sedan det här kommandot med namnet på ditt nyckelvalv och värdet för **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Publicera webbappen till Azure

Publicera den här appen till Azure igen för att se den live som webbapp och för att se att du kan hämta det hemliga värdet.

1. I Visual Studio väljer du projektet **key-vault-dotnet-core-quickstart**.
2. Välj **Publicera** > **Starta**.
3. Välj **Skapa**.

I kommandot ovan ger du identiteten (MSI) för App Service behörighet att utföra åtgärderna **hämta** och **lista** i ditt nyckelvalv. <br />
Nu när du kör programmet bör ditt hemliga värde hämtas. 

Då var det klart. Du har nu skapat en webbapp i .NET som lagrar och hämtar sina hemligheter från nyckelvalvet.
