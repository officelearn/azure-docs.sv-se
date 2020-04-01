---
title: Snabbstart - Azure Key Vault-klientbibliotek för .NET (v4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure-nyckelvalv med .NET-klientbiblioteket (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: a94717c7bed3ba25a4682896053672fe100dc43a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398388"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Snabbstart: Azure Key Vault-klientbibliotek för .NET (SDK v4)

Kom igång med Azure Key Vault-klientbiblioteket för .NET. Följ stegen nedan för att installera paketet och prova exempelkod för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd klientbiblioteket Key Vault för .NET för att:

- Öka säkerheten och kontrollen över nycklar och lösenord.
- Skapa och importera krypteringsnycklar på några minuter.
- Minska svarstiden med molnskala och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 Nivå 2 validerade HSM.Use FIPS 140-2 Level 2 validated HSMs.

[API-referensdokumentation](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [Bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 2.1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabbstarten `dotnet`förutsätter att du kör Kommandona [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)och Windows i en Windows-terminal (till exempel [PowerShell Core,](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6) [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)eller [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Inrätta

### <a name="create-new-net-console-app"></a>Skapa en ny .NET-konsolapp

I ett konsolfönster `dotnet new` använder du kommandot för att skapa `key-vault-console-app`en ny .NET-konsolapp med namnet .

```console
dotnet new console -n key-vault-console-app
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```console
dotnet build
```

Byggutdata bör inte innehålla några varningar eller fel.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Installera paketet

Installera azure key vault-klientbiblioteket för .NET i konsolfönstret:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

För den här snabbstarten måste du också installera följande paket:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och nyckelvalv

Den här snabbstarten använder ett förskapade Azure-nyckelvalv. Du kan skapa ett nyckelvalv genom att följa stegen i [snabbstarten Azure CLI,](quick-create-cli.md) [Snabbstarten i Azure PowerShell](quick-create-powershell.md)eller [Snabbstarten för Azure-portalen](quick-create-portal.md). Alternativt kan du helt enkelt köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckelvalv måste ha ett unikt namn. Ersätt <ditt unika nyckelval-namn> med namnet på nyckelvalvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location EastUS
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserat .NET-program är med en hanterad identitet. Se [Använda en hanterad apptjänstidentitet för att komma åt Azure Key Vault](managed-identity.md) för mer information. För enkelhetens skull skapar dock den här snabbstarten ett .NET-konsolprogram. Autentisering av ett skrivbordsprogram med Azure kräver användning av ett tjänsthuvudnamn och en åtkomstkontrollprincip.

Skapa en tjänstprincip med kommandot Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Denna operation kommer att returnera en serie nyckel / värdepar. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Skapa ett tjänsthuvudnamn med kommandot Azure PowerShell [New-AzADServicePrincipal:](/powershell/module/az.resources/new-azadserviceprincipal)

```azurepowershell
# Create a new service principal
$spn = New-AzADServicePrincipal -DisplayName "http://mySP"

# Get the tenant ID and subscription ID of the service principal
$tenantId = (Get-AzContext).Tenant.Id
$subscriptionId = (Get-AzContext).Subscription.Id

# Get the client ID
$clientId = $spn.ApplicationId

# Get the client Secret
$bstr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$clientSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
```

Mer information om tjänstens huvudnamn med Azure PowerShell finns i [Skapa ett Azure-tjänsthuvudnamn med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Notera clientId, clientSecret och tenantId, eftersom vi kommer att använda dem i följande steg.


#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvudnamn åtkomst till nyckelvalvet

Skapa en åtkomstprincip för nyckelvalvet som ger behörighet till tjänstens huvudnamn genom att skicka clientId till kommandot [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Ge tjänstens huvudnamn behörigheter, lista och ange behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions list get set delete purge
```

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName <your-unique-keyvault-name> -ServicePrincipalName <clientId-of-your-service-principal> -PermissionsToSecrets list,get,set,delete,purge
```

#### <a name="set-environmental-variables"></a>Ange miljövariabler

Metoden DefaultAzureCredential i vår applikation bygger på `AZURE_CLIENT_ID`tre `AZURE_CLIENT_SECRET`miljövariabler: , och `AZURE_TENANT_ID`. Använd ange dessa variabler till klientId-, clientSecret- och tenantId-värden som du noterade i steget [Skapa en tjänsthuvudnamn](#create-a-service-principal) ovan.

Du måste också spara ditt nyckelvalvsnamn `KEY_VAULT_NAME`som en miljövariabel som kallas ;

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Varje gång `setx`du ringer bör du få ett svar på "FRAMGÅNG: Det angivna värdet sparades".

```shell
AZURE_CLIENT_ID=<your-clientID>

AZURE_CLIENT_SECRET=<your-clientSecret>

AZURE_TENANT_ID=<your-tenantId>

KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektmodell

Med Azure Key Vault-klientbiblioteket för .NET kan du hantera nycklar och relaterade resurser som certifikat och hemligheter. Kodexemplen nedan visar hur du skapar en klient, anger en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsolappen finns https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-apppå .

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande direktiv överst i koden:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Att autentisera till ditt nyckelvalv och skapa en nyckelvalvklient beror på miljövariablerna i steget [Ange miljövariabler](#set-environmental-variables) ovan. Namnet på nyckelvalvet expanderas till nyckelvalvet URI, i formatet\<"https:// din-key-vault-name\>.vault.azure.net".

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du lägga en hemlighet i ditt keyvault med hjälp av [klienten. SetSecret-metoden](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Detta kräver ett namn för hemligheten - vi använder "mySecret" i det här exemplet.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Du kan kontrollera att hemligheten har angetts med kommandot [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare inställda värdet med [klienten. Metoden GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Din hemlighet är `secret.Value`nu sparad som .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen, låt oss ta bort hemligheten från din nyckel valv med [klienten. Metoden DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Du kan kontrollera att hemligheten är borta med [az keyvault hemliga visa](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) kommandot:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckelvalvet och motsvarande resursgrupp.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exempelkod

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv, lagrade en hemlighet och hämtade den hemligheten. Se [hela konsolappen i GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Implementera [autentisering från tjänst till Azure Key Vault med .NET](service-to-service-authentication.md)
- Läs en [översikt över Azure Key Vault](key-vault-overview.md)
- Se [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md)
- Läs mer om [nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- Granska [metodtips för Azure Key Vault](key-vault-best-practices.md)
