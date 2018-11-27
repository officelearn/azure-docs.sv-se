---
title: Självstudie – Använda Azure Key Vault med virtuell Azure Windows-dator i .NET | Microsoft Docs
description: 'Självstudie: Konfigurera ett ASP.NET Core-program att läsa en hemlighet från Key Vault'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: d1f24c8bebc8740f47dc0f02089db1091c22f597
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711335"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-windows-virtual-machine-in-net"></a>Självstudie: Använda Azure Key Vault med virtuell Azure Windows-dator i .NET

Azure Key Vault hjälper dig att skydda hemligheter, till exempel API-nycklar, databasanslutningssträngar som behövs för att komma åt dina program, tjänster samt IT-resurser.

I den här självstudien följer du de steg som behövs för att få ett konsolprogram att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Den här självstudien baseras på [Azure Web Apps](../app-service/app-service-web-overview.md). I följande avsnitt lär du dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lagra en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn.
> * Bevilja de behörigheter som krävs för att konsolprogrammet ska kunna läsa data från nyckelvalvet.
> * Hämta hemligheter från Key Vault

Innan du fortsätter rekommenderar vi att du läser avsnittet om [grundbegreppen](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Nödvändiga komponenter
* Alla plattformar:
  * Git ([ladda ned](https://git-scm.com/downloads)).
  * En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 och senare. Det här är tillgängligt för Windows, Mac och Linux.

I den här självstudien användas hanterad tjänstidentitet

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Vad är hanterad tjänstidentitet (MSI) och hur fungerar det?
Innan vi går vidare tar vi en titt på MSI. Azure Key Vault kan lagra autentiseringsuppgifter på ett säkert sätt så att de inte ingår i din kod, men för att hämta dem behöver du autentisera till Azure Key Vault. För att autentisera till Key Vault behöver du autentiseringsuppgifter! Det är ett klassiskt bootstrap-problem. Via Azure och Azure AD tillhandahåller MSI en ”bootstrap-identitet” som gör det mycket enklare att komma igång.

Så här fungerar det. När du aktiverar MSI för en Azure-tjänst, till exempel virtuella datorer, App Service eller Functions, skapar Azure ett [tjänsthuvudnamn](key-vault-whatis.md#basic-concepts) för tjänstens instans i Azure Active Directory, och matar in autentiseringsuppgifterna för tjänsthuvudnamnet till tjänstens instans. 

![MSI](media/MSI.png)

Koden anropar sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen för att hämta en åtkomsttoken.
Koden använder den åtkomsttoken som den får från den lokala MSI_ENDPOINT för att autentisera till en Azure Key Vault-tjänst. 

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

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Följ länkarna nedan för att skapa en virtuell Windows-dator

[Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-identity-to-virtual-machine"></a>Tilldela identitet till virtuell dator
I det här steget skapar vi en systemtilldelad identitet till den virtuella datorn genom att köra följande kommando i Azure CLI

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera systemAssignedIdentity som visas nedan. Utdata för kommandot ovan skulle vara 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>Ge Key Vault behörigheten Identitet för virtuell dator
Nu kan du ge ovanstående identitetsbehörighet till Key Vault genom att köra följande kommando

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Du kan följa den här [självstudien](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="install-net-core"></a>Installera .NET Core

Du kan installera .NET Core genom att följa stegen i den här [artikeln](https://www.microsoft.com/net/download)

## <a name="create-and-run-sample-dot-net-app"></a>Skapa och kör .NET-exempelappen

Öppna en kommandotolk.

Genom att köra nedanstående kommandon bör du se ”Hello World” utskrivet i konsolen

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>Redigera konsolappen
Öppna Program.cs-filen och lägg till dessa paket
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```
Ändra sedan klassfilen så att de innehåller koden nedan. Det är en tvåstegsprocess. 
1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn som i sin tur hämtar en token från Azure Active Directory
2. Skicka token till Key Vault och hämta din hemlighet 

```
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
            var token = GetToken();

            // Step 2: Fetch the secret value from Key Vault
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
            WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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


Koden ovan visar hur du utför åtgärder med Azure Key Vault på en virtuell Azure Linux-dator. 



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
