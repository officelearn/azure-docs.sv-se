---
title: Självstudiekurs – använda Azure Key Vault med en Windows-dator i .NET | Microsoft Docs
description: I den här självstudien konfigurerar du ett ASP.NET core-program att läsa en hemlighet från ditt nyckelvalv.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 4ae02a494949e92ad8e59cd35e46b6ce246ae7cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67115012"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Självstudier: Använda Azure Key Vault med en Windows-dator i .NET

Azure Key Vault hjälper dig att skydda hemligheter, till exempel API-nycklar, databasanslutningssträngar som du behöver komma åt dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du hämtar ett konsolprogram för att läsa information från Azure Key Vault. Om du vill göra det använder du hanterade identiteter för Azure-resurser. 

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa en resursgrupp.
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn.
> * Tilldela behörigheter till identitet för virtuell dator.

Innan du börjar läsa [grundläggande begrepp för Key Vault](key-vault-whatis.md#basic-concepts). 

Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Nödvändiga komponenter

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Om Hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt, så att de inte visas i din kod. Men måste du autentisera till Azure Key Vault för att hämta dina nycklar. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Hanterad tjänstidentitet (MSI) löser problemet genom att tillhandahålla en _bootstrapidentitet_ som förenklar processen.

När du aktiverar MSI för en Azure-tjänst, till exempel Azure Virtual Machines, Azure App Service eller Azure Functions, Azure skapar en [tjänstens huvudnamn](key-vault-whatis.md#basic-concepts). MSI gör detta för instansen av tjänsten i Azure Active Directory (Azure AD) och lägger in autentiseringsuppgifter för tjänstens huvudnamn i den instansen. 

![MSI](media/MSI.png)

Om du vill få en åtkomsttoken, anropar sedan koden en lokala metadata-tjänst som är tillgängliga på Azure-resursen. Koden använder åtkomsttoken som får den från den lokala MSI-slutpunkten för att autentisera till en Azure Key Vault-tjänsten. 

## <a name="create-resources-and-assign-permissions"></a>Skapa resurser och tilldela behörigheter

Innan du börjar skriva kod du behöver skapa några resurser, placerar en hemlighet i nyckelvalvet och tilldela behörigheter.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). 

Det här exemplet skapas en resursgrupp i USA, västra:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Nyligen skapade resursgruppen kommer att användas i den här självstudien.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Skapa ett nyckelvalv och fyller den med en hemlighet

Skapa ett nyckelvalv i resursgruppen genom att ange den [az keyvault skapa](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) kommandot med följande information:

* Namn på Key vault: en sträng på 3 till 24 tecken som får endast innehålla siffror (0-9), bokstäver (a – z, A-Z) och bindestreck (-)
* Namn på resursgrupp
* Plats: **USA, västra**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Ditt Azure-konto är nu den enda som har behörighet för att utföra åtgärder på den här nya key vault.

Lägg nu till en hemlighet till nyckelvalvet med den [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) kommando


Om du vill skapa en hemlighet i nyckelvalvet med namnet **AppSecret**, anger du följande kommando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Den här hemligheten lagrar värdet **MySecret**.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator genom att använda någon av följande metoder:

* [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

### <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
Skapa en systemtilldelad identitet för den virtuella datorn med den [az identitet för virtuell dator tilldelar](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) kommando:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera systemtilldelade identiteten som visas i följande kod. Utdata från föregående kommando skulle vara: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till identitet för virtuell dator
Tilldela behörigheterna som tidigare skapade identitet för nyckeln nyckelvalv med den [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) kommando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn, följer du anvisningarna i [Anslut och logga in på Azure-datorer som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="set-up-the-console-app"></a>Konfigurera konsolappen

Skapa en konsolapp och installera de nödvändiga paketen med det `dotnet` kommando.

### <a name="install-net-core"></a>Installera .NET Core

Om du vill installera .NET Core, går du till den [.NET hämtar](https://www.microsoft.com/net/download) sidan.

### <a name="create-and-run-a-sample-net-app"></a>Skapa och köra en .NET-exempelapp

Öppna en kommandotolk.

Du kan skriva ut ”Hello World” till konsolen genom att köra följande kommandon:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Installera paket

 Installera .NET-paket som krävs för den här snabbstarten från konsolfönstret:

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Redigera konsolappen

Öppna den *Program.cs* filen och Lägg till dessa paket:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Redigera filen klass så att den innehåller koden i följande två steg:

1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn. Gör det även hämtar en token från Azure AD.
1. Skicka token till ditt nyckelvalv och hämta din hemlighet. 

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

Ta bort den virtuella datorn och nyckelvalvet när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
