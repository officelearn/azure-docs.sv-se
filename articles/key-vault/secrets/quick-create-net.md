---
title: Snabb start – Azure Key Vault klient bibliotek för .NET (v4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av .NET-klient biblioteket (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 84f89b9def3fc5d98ebb669b9f5b86523854f7a8
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185731"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Snabb start: Azure Key Vault klient bibliotek för .NET (SDK v4)

Kom igång med Azure Key Vault klient biblioteket för .NET. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault klient bibliotek för .NET för att:

- Öka säkerheten och kontrollen över nycklar och lösen ord.
- Skapa och importera krypterings nycklar på några minuter.
- Minska svars tiden med moln skalning och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 nivå 2-verifierade HSM: er.

[API-referens dokumentation](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 3,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/)

Den här snabb starten förutsätter att du kör `dotnet` , [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)och Windows-kommandon i en Windows Terminal (till exempel [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)eller [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Konfigurera

### <a name="create-new-net-console-app"></a>Skapa ny .NET-konsol program

I ett konsol fönster använder du `dotnet new` kommandot för att skapa en ny .net-konsol program med namnet `key-vault-console-app` .

```console
dotnet new console -n key-vault-console-app
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Installera paketet

I konsol fönstret installerar du Azure Key Vault klient biblioteket för .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

I den här snabb starten behöver du även installera följande paket:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Ange miljövariabler

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Objekt modell

Med Azure Key Vault klient biblioteket för .NET kan du hantera nycklar och relaterade till gångar som certifikat och hemligheter. I kod exemplen nedan visas hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsol programmet finns på https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i koden:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Autentisering till ditt nyckel valv och att skapa en Key Vault-klient beror på miljövariablerna i steget [Ange miljövariabler](#set-environmental-variables) ovan. Namnet på nyckel valvet expanderas till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". Koden nedan använder ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) för autentisering till Key Vault, som läser miljövariabler för att hämta åtkomsttoken. 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du ställa in en hemlighet i ditt nyckel valv med hjälp av- [klienten. SetSecret-metod](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) detta kräver ett namn för hemligheten – vi använder "hemligheter" i det här exemplet.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Du kan kontrol lera att hemligheten har angetts med kommandot [AZ-valvets hemliga show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare angivna värdet med- [klienten. GetSecret-metod](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Din hemlighet sparas nu som `secret.Value` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen tar vi bort hemligheten från nyckel valvet med- [klienten. DeleteSecret-metod](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Du kan kontrol lera att hemligheten är borta med kommandot [AZ-valv för hemligt show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckel valvet och motsvarande resurs grupp.

### <a name="delete-a-key-vault"></a>Ta bort en Key Vault
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Rensa en Key Vault
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Ta bort en resursgrupp

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

I den här snabb starten skapade du ett nyckel valv, lagrat en hemlighet och hämtat hemligheten. Se [hela konsol programmet i GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)
