---
title: Snabb start – Azure Key Vault klient bibliotek för .NET
description: Innehåller format och innehålls kriterier för att skriva snabb starter för klient bibliotek i Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e57b5a49ac0c99fa81e54134e74964bf38418e4d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934896"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Snabbstart: Azure Key Vault klient bibliotek för .NET

Kom igång med Azure Key Vault klient biblioteket för .NET. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault klient bibliotek för .NET för att:

- Öka säkerheten och kontrollen över nycklar och lösen ord.
- Skapa och importera krypterings nycklar på några minuter.
- Minska svars tiden med moln skalning och global redundans.
- Förenkla och automatisera uppgifter för SSL/TLS-certifikat.
- Använd FIPS 140-2 nivå 2-verifierade HSM: er.

[API Reference dokumentation](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | [paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)


## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 2,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabb starten förutsätter `dotnet`att du kör, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)och Windows-kommandon i en Windows Terminal (till exempel [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)eller [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Konfigurera

### <a name="create-new-net-console-app"></a>Skapa ny .NET-konsol program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE.

I ett konsol fönster använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet. `akv-dotnet`


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
> Varje Key Vault måste ha ett unikt namn. I följande exempel skapas en Key Vault med namnet *myKV*, men du måste namnge något annat och använda det namnet i den här snabb starten.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserad .NET-program är med en hanterad identitet. Mer information finns i [tjänst-till-tjänst-autentisering för Azure Key Vault med hjälp av .net](service-to-service-authentication.md) . För enkelhetens skull skapar den här snabb starten ett .NET-konsol program. Att autentisera ett Skriv bords program med Azure kräver att tjänstens huvud namn används.
Skapa en tjänst princip med Azure CLI [-AZ AD SP Create-for-RBAC-](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommando:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
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
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Anteckna clientId och clientSecret, eftersom vi kommer att använda dem i steget [autentisera till ditt nyckel valv](#authenticate-to-your-key-vault) nedan.

Du kommer också att behöva appID för tjänstens huvud namn. Du hittar den genom att köra [AZ AD SP-lista](https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) med `--show-mine` parametern:

```azurecli
az ad sp list --show-mine
```

`appID` Visas i den returnerade JSON-filen:

```json
    "appId": "2cf5aa18-0100-445a-9438-0b93e577a3ed",
```

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som ger behörighet till tjänstens huvud namn. Det gör du med kommandot [AZ-nyckel valv set-princip](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Vi ska ge tjänstens huvud namn get-, list-och set-behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <appid-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objekt modell

Med Azure Key Vault klient biblioteket för .NET kan du hantera nycklar och relaterade till gångar som certifikat och hemligheter. I kod exemplen nedan visas hur du ställer in en hemlighet och hämtar en hemlighet.

Hela konsol programmet finns på https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Kod exempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i koden:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Autentisera till ditt nyckel valv

Den här .NET-snabb starten använder miljövariabler för att lagra autentiseringsuppgifter som inte ska placeras i kod. 

Innan du skapar och kör din app använder `setx` du kommandot för att `akvClientId`ställa in variablerna `akvTenantId`, `akvClientSecret`, `akvSubscriptionId` , och för miljövariablerna i de värden som du antecknade ovan.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

Varje gång du ringer `setx`får du svaret "lyckades: Det angivna värdet sparades. "

Tilldela de här miljövariablerna till strängar i din kod och autentisera sedan ditt program genom att skicka dem till [klassen KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du ange en hemlighet i ditt nyckel valv med SetSecretAsync- [metoden](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) detta kräver URL: en för nyckel valvet, som är i formatet `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Det kräver också ett namn för hemligheten – vi använder "hemlig hemlighet".  Du kanske vill tilldela dessa strängar till en variabel för åter användning.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Du kan kontrol lera att hemligheten har angetts med kommandot [AZ-valvets hemliga show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare set-värdet med [metoden GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Din hemlighet sparas nu som `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckel valvet och motsvarande resurs grupp.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nyckel valv, lagrat en hemlighet och hämtat hemligheten. Se [hela konsol programmet i GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Implementera [tjänst-till-tjänst-autentisering för Azure Key Vault med .net](service-to-service-authentication.md)
- Läs en [Översikt över Azure Key Vault](key-vault-overview.md)
- Se [Azure Key Vault Developer ' s guide](key-vault-developers-guide.md)
- Lär dig mer om [nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- Granska [Azure Key Vault bästa praxis](key-vault-best-practices.md)
