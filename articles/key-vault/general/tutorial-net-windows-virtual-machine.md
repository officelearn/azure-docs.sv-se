---
title: Självstudiekurs - Använd Azure Key Vault med en virtuell Windows-dator i .NET | Microsoft-dokument
description: I den här självstudien konfigurerar du ett ASP.NET kärnprogram för att läsa en hemlighet från nyckelvalvet.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 6ba78a44af7beb9b5b79aa1a87e08f5a82589cce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422867"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Självstudiekurs: Använd Azure Key Vault med en virtuell Windows-dator i .NET

Azure Key Vault hjälper dig att skydda hemligheter som API-nycklar, databasanslutningssträngar du behöver för att komma åt dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du får ett konsolprogram för att läsa information från Azure Key Vault. För att göra det använder du hanterade identiteter för Azure-resurser. 

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa en resursgrupp.
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn.
> * Tilldela behörigheter till vm-identiteten.

Innan du börjar läser du [grundläggande begrepp för Nyckelvalv](basic-concepts.md). 

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Krav

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI version 2.0.4 eller senare installerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Om Hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt, så att de inte visas i din kod. Men måste du autentisera till Azure Key Vault för att hämta dina nycklar. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Hanterad tjänstidentitet (MSI) löser problemet genom att tillhandahålla en _bootstrapidentitet_ som förenklar processen.

När du aktiverar MSI för en Azure-tjänst, till exempel Virtuella Azure-datorer, Azure App Service eller Azure Functions, skapar Azure ett [tjänsthuvudnamn](basic-concepts.md). MSI gör detta för instansen av tjänsten i Azure Active Directory (Azure AD) och injicerar tjänstens huvudreferenser i den instansen. 

![MSI](../media/MSI.png)

För att få en åtkomsttoken anropar koden sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen. För att autentisera till en Azure Key Vault-tjänst använder din kod åtkomsttoken som den får från den lokala MSI-slutpunkten. 

## <a name="create-resources-and-assign-permissions"></a>Skapa resurser och tilldela behörigheter

Innan du börjar koda måste du skapa vissa resurser, placera en hemlighet i nyckelvalvet och tilldela behörigheter.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). 

I det här exemplet skapas en resursgrupp på platsen i västra USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Din nyskapade resursgrupp kommer att användas i hela den här självstudien.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Skapa ett nyckelvalv och fyll det med en hemlighet

Skapa ett nyckelvalv i resursgruppen genom att ge kommandot [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) med följande information:

* Nyckelvalvsnamn: en sträng med 3 till 24 tecken som bara kan innehålla siffror (0–9), bokstäver (a-z, A-Ö) och bindestreck (-)
* Namn på resursgrupp
* Plats: **Västra USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nu är ditt Azure-konto det enda som har behörighet att utföra åtgärder på det här nya nyckelvalvet.

Lägg nu till en hemlighet i ditt nyckelvalv med kommandot [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Om du vill skapa en hemlighet i nyckelvalvet **appsecret**anger du följande kommando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Den här hemligheten lagrar värdet **MySecret**.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator med någon av följande metoder:

* [The Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
Skapa en systemtilldelad identitet för den virtuella datorn med kommandot [az vm identity assign:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera den systemtilldelade identitet som visas i följande kod. Resultatet av föregående kommando skulle vara: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till vm-identiteten
Tilldela de tidigare skapade identitetsbehörigheterna till nyckelvalvet med kommandot [az keyvault-princip:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn följer du instruktionerna i [Anslut och loggar in på en virtuell Azure-dator som kör Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Konfigurera konsolappen

Skapa en konsolapp och installera de `dotnet` paket som krävs med kommandot.

### <a name="install-net-core"></a>Installera .NET Core

Om du vill installera .NET Core går du till sidan [.NET downloads.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Skapa och kör ett exempel på .NET-app

Öppna en kommandotolk.

Du kan skriva ut "Hello World" till konsolen genom att köra följande kommandon:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Installera paketen

Installera de .NET-paket som krävs för den här snabbstarten i konsolfönstret:

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Redigera konsolappen

Öppna *filen Program.cs* och lägg till följande paket:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Redigera klassfilen så att den innehåller koden i följande trestegsprocess:

1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn. Om du gör det hämtas också en token från Azure AD.
2. Skicka token till ditt nyckelvalv och hämta sedan din hemlighet. 
3. Lägg till valvets namn och hemligt namn i begäran.

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }
        
        static string FetchSecretValueFromKeyVault(string token)
        {
            //Step 3: Add the vault name and secret name to the request.
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

Följande kod visar hur du utför åtgärder med Azure Key Vault på en virtuell Windows-dator.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort den virtuella datorn och nyckelvalvet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
