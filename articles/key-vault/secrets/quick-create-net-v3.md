---
title: Snabb start – Azure Key Vault klient bibliotek för .NET (SDK v3)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av .NET-klient biblioteket (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 0dba746379a6b5338c9e67fe312c392f37399012
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125795"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Snabb start: Azure Key Vault klient bibliotek för .NET (SDK v3)

Kom igång med Azure Key Vault klient biblioteket för .NET. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter.

> [!NOTE]
> Den här snabb starten använder v 3.0.4-versionen av klient biblioteket Microsoft. Azure. nyckel valv. Om du vill använda den senaste versionen av Key Vault klient biblioteket, se [Azure Key Vault klient bibliotek för .net (SDK v4)](quick-create-net.md). 

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault klient bibliotek för .NET för att:

- Öka säkerheten och kontrollen över nycklar och lösen ord.
- Skapa och importera krypterings nycklar på några minuter.
- Minska svars tiden med moln skalning och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 nivå 2-verifierade HSM: er.

[API-referens dokumentation](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.


## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 3,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabb starten förutsätter att du kör `dotnet` , [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)och Windows-kommandon i en Windows Terminal (till exempel [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)eller [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Konfigurera

### <a name="create-new-net-console-app"></a>Skapa ny .NET-konsol program

I ett konsol fönster använder du `dotnet new` kommandot för att skapa en ny .net-konsol program med namnet `akv-dotnet` .


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

I den här snabb starten behöver du även installera följande paket:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

I den här snabb starten används ett i förväg skapade Azure Key Vault. Du kan skapa ett nyckel valv genom att följa stegen i snabb starten för [Azure CLI](quick-create-cli.md), [Azure PowerShell snabb start](quick-create-powershell.md)eller [Azure Portal snabb start](quick-create-portal.md). Alternativt kan du bara köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserad .NET-program är med en hanterad identitet. Mer information finns i [använda en app service hanterad identitet för att få åtkomst till Azure Key Vault](../general/managed-identity.md) . 

För enkelhetens skull skapar den här snabb starten ett .NET-konsol program som kräver användning av ett huvud namn för tjänsten och en princip för åtkomst kontroll. Tjänstens huvud namn kräver ett unikt namn i formatet "http:// &lt; My-Unique-service-huvud namn &gt; ".

Skapa ett huvud namn för tjänsten med hjälp av Azure CLI [-AZ AD SP Create-for-RBAC-](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommandot:

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Den här åtgärden returnerar en serie med nyckel/värde-par. 

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

Anteckna clientId och clientSecret, eftersom vi kommer att använda dem i steget [autentisera till ditt nyckel valv](#authenticate-to-your-key-vault) nedan.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som ger behörighet till tjänstens huvud namn genom att skicka clientId till [AZ-kommandot Set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Ge tjänstens huvud namn get-, list-och set-behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objekt modell

Med Azure Key Vault klient biblioteket för .NET kan du hantera nycklar och relaterade till gångar som certifikat och hemligheter. I kod exemplen nedan visas hur du ställer in en hemlighet och hämtar en hemlighet.

Hela konsol programmet finns på https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet .

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i koden:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Autentisera till ditt nyckel valv

Den här .NET-snabb starten använder miljövariabler för att lagra autentiseringsuppgifter som inte ska placeras i kod. 

Innan du skapar och kör din app använder du `setx` kommandot för att ställa in `akvClientId` `akvClientSecret` `akvTenantId` variablerna,,, och `akvSubscriptionId` för miljövariablerna i de värden som du antecknade ovan.

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Tilldela de här miljövariablerna till strängar i din kod och autentisera sedan ditt program genom att skicka dem till [klassen KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du ange en hemlighet i ditt nyckel valv med SetSecretAsync- [metoden](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) detta kräver URL: en för nyckel valvet, som är i formatet `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Det kräver också ett namn för hemligheten – vi använder "hemlig hemlighet". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Du kan kontrol lera att hemligheten har angetts med kommandot [AZ-valvets hemliga show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare set-värdet med [metoden GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Din hemlighet sparas nu som `keyvaultSecret.Value;` .

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckel valvet och motsvarande resurs grupp.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nyckel valv, lagrat en hemlighet och hämtat hemligheten. Se [hela konsol programmet i GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Implementera [tjänst-till-tjänst-autentisering för Azure Key Vault med .net](../general/service-to-service-authentication.md)
- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)
