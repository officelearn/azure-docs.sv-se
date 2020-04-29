---
title: Självstudie – Använd en virtuell Linux-dator och ett ASP.NET-konsol program för att lagra hemligheter i Azure Key Vault | Microsoft Docs
description: I den här självstudien får du lära dig hur du konfigurerar ett ASP.NET Core program för att läsa en hemlighet från Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 50810dd1fbf2d97989df47b537ef5c574be059d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81423154"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Självstudie: Använd en virtuell Linux-dator och en .NET-app för att lagra hemligheter i Azure Key Vault

Azure Key Vault hjälper dig att skydda hemligheter som API-nycklar och databas anslutnings strängar som behövs för att få åtkomst till dina program, tjänster och IT-resurser.

I den här självstudien skapar du ett .NET-konsol program för att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Lär dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv
> * Lagra en hemlighet i Key Vault
> * Skapa en virtuell Azure Linux-dator
> * Aktivera en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn
> * Ge nödvändiga behörigheter för konsol programmet för att läsa data från Key Vault
> * Hämta en hemlighet från Key Vault

Innan vi går vidare kan du läsa om [grundläggande koncept för Key Vault](../general/basic-concepts.md).

## <a name="prerequisites"></a>Krav

* [Git](https://git-scm.com/downloads).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
* [Azure CLI 2,0 eller senare](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) eller Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Förstå Hanterad tjänstidentitet

Azure Key Vault kan lagra autentiseringsuppgifter på ett säkert sätt så att de inte finns i din kod, men för att hämta dem måste du autentisera till Azure Key Vault. För att autentisera till Key Vault behöver du dock autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Med Azure och Azure Active Directory (Azure AD) kan Hanterad tjänstidentitet (MSI) tillhandahålla en start identitet som gör det mycket enklare att få saker igång.

När du aktiverar MSI för en Azure-tjänst som Virtual Machines, App Service eller functions, skapar Azure ett huvud namn för tjänsten för instansen av tjänsten i Azure Active Directory. Det lägger in autentiseringsuppgifterna för tjänstens huvudnamn i instansen av tjänsten.

![MSI](../media/MSI.png)

Koden anropar sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen för att hämta en åtkomsttoken.
Koden använder den åtkomsttoken som den får från den lokala MSI_ENDPOINT för att autentisera till en Azure Key Vault-tjänst.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp med hjälp av `az group create` kommandot. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp på platsen USA, västra. Välj ett namn för resurs gruppen och Ersätt `YourResourceGroupName` i följande exempel:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Du använder den här resursgruppen i hela självstudien.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Skapa sedan ett nyckel valv i resurs gruppen. Ange följande information:

* Nyckel valvets namn: en sträng på 3 till 24 tecken som bara får innehålla siffror, bokstäver och bindestreck (0-9, a-z, A-Z och \- ).
* Namn på resursgrupp
* Plats: **västra USA**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nu är det bara ditt Azure-konto som har behörighet att utföra åtgärder på det nya valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Nu ska du lägga till en hemlighet. I ett verkligt scenario kan du lagra en SQL-anslutningssträng eller annan information som du behöver för att skydda dig, men göra den tillgänglig för ditt program.

I den här självstudien skriver du följande kommandon för att skapa en hemlighet i nyckel valvet. Hemligheten kallas **AppSecret** och dess värde är **hemligt**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

Skapa en virtuell dator med `az vm create` kommandot.

Följande exempel skapar virtuell dator med namnet **myVM** och lägger till ett användarkonto med namnet **azureuser**. `--generate-ssh-keys` Parametern vi använde för att automatiskt generera en SSH-nyckel och ange den på standard platsen för nycklar (**~/.ssh**). Om du i stället vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Följande exempel på utdata visar att åtgärden för att skapa virtuella datorer lyckades.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna `publicIpAddress` i utdata från den virtuella datorn. Du använder den här adressen för att komma åt den virtuella datorn i senare steg.

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn

Skapa en systemtilldelad identitet till den virtuella datorn genom att köra följande kommando:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Resultatet av kommandot ska se ut ungefär så här:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anteckna `systemAssignedIdentity`. Du använder det i nästa steg.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Ge den virtuella datorns identitet behörighet till Key Vault

Nu kan du ge Key Vault-behörighet till den identitet som du skapade. Kör följande kommando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logga in på den virtuella datorn

Logga in på den virtuella datorn med hjälp av en terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Installera .NET Core på Linux

På din virtuella Linux-dator:

Registrera Microsofts produkt nyckel som betrodd genom att köra följande kommandon:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Ställ in det önskade paket flödet för versions värden baserat på operativ system:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Installera .NET och kontrol lera versionen:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Skapa och kör en exempel-.NET-app

Kör följande kommandon. Du bör se "Hello World" som skrivs ut till-konsolen.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Redigera-konsol programmet för att hämta din hemlighet

Öppna Program.cs-filen och Lägg till följande paket:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Det är en två stegs process att ändra klass filen för att tillåta att appen får åtkomst till hemligheten i nyckel valvet.

1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn som i sin tur hämtar en token från Azure Active Directory.
1. Skicka token till Key Vault och hämta din hemlighet.

   Redigera klass filen så att den innehåller följande kod:

   ```csharp
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

Nu har du lärt dig hur du utför åtgärder med Azure Key Vault i ett .NET-program som körs på en virtuell Azure Linux-dator.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när du inte längre behöver dem. Det gör du genom att markera resursgruppen för den virtuella datorn och välja **Ta bort**.

Ta bort nyckelvalvet med hjälp av kommandot `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
