---
title: Snabbstart - Azure Key Vault-klientbibliotek för .NET (SDK v3)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure-nyckelvalv med .NET-klientbiblioteket (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: dc9c55c7caf2f78d66d8873e2da8ed7efdbdcec9
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411639"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Snabbstart: Azure Key Vault-klientbibliotek för .NET (SDK v3)

Kom igång med Azure Key Vault-klientbiblioteket för .NET. Följ stegen nedan för att installera paketet och prova exempelkod för grundläggande uppgifter.

> [!NOTE]
> Den här snabbstarten använder v3.0.4-versionen av microsoft.Azure.KeyVault-klientbiblioteket. Information om hur du använder den senaste versionen av Key Vault-klientbiblioteket finns i [Azure Key Vault-klientbiblioteket för .NET (SDK v4)](quick-create-net.md). 

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd klientbiblioteket Key Vault för .NET för att:

- Öka säkerheten och kontrollen över nycklar och lösenord.
- Skapa och importera krypteringsnycklar på några minuter.
- Minska svarstiden med molnskala och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 Nivå 2 validerade HSM.Use FIPS 140-2 Level 2 validated HSMs.

[API-referensdokumentation](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Varje nyckelvalv måste ha ett unikt namn. Ersätt <ditt unika nyckelval-namn> med namnet på nyckelvalvet i följande exempel.


## <a name="prerequisites"></a>Krav

* En Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 2.1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabbstarten `dotnet`förutsätter att du kör Kommandona [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)och Windows i en Windows-terminal (till exempel [PowerShell Core,](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6) [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)eller [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Inrätta

### <a name="create-new-net-console-app"></a>Skapa en ny .NET-konsolapp

I ett konsolfönster `dotnet new` använder du kommandot för att skapa `akv-dotnet`en ny .NET-konsolapp med namnet .


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

För den här snabbstarten måste du också installera följande paket:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och nyckelvalv

Den här snabbstarten använder ett förskapade Azure-nyckelvalv. Du kan skapa ett nyckelvalv genom att följa stegen i [snabbstarten Azure CLI,](quick-create-cli.md) [Snabbstarten i Azure PowerShell](quick-create-powershell.md)eller [Snabbstarten för Azure-portalen](quick-create-portal.md). Alternativt kan du helt enkelt köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckelvalv måste ha ett unikt namn. Ersätt <ditt unika nyckelval-namn> med namnet på nyckelvalvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
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

Notera clientId och clientSecret, eftersom vi kommer att använda dem i [Authenticate till din nyckel valv](#authenticate-to-your-key-vault) steg nedan.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvudnamn åtkomst till nyckelvalvet

Skapa en åtkomstprincip för nyckelvalvet som ger behörighet till tjänstens huvudnamn genom att skicka clientId till kommandot [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Ge tjänstens huvudnamn behörigheter, lista och ange behörigheter för både nycklar och hemligheter.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objektmodell

Med Azure Key Vault-klientbiblioteket för .NET kan du hantera nycklar och relaterade resurser som certifikat och hemligheter. Kodexemplen nedan visar hur du ställer in en hemlighet och hämta en hemlighet.

Hela konsolappen finns https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnetpå .

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande direktiv överst i koden:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Autentisera till ditt nyckelvalv

Den här snabbstarten FÖR .NET är beroende av miljövariabler för att lagra autentiseringsuppgifter som inte ska placeras i kod. 

Innan du skapar och kör `setx` appen använder `akvClientId`du `akvClientSecret` `akvTenantId`kommandot `akvSubscriptionId` för att ställa in variablerna , , och miljö till de värden som du noterade ovan.

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

**Macos**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Tilldela dessa miljövariabler till strängar i koden och autentisera sedan programmet genom att skicka dem till [klassen KeyVaultClient:](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du placera en hemlighet i din keyvault med [metoden SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Detta kräver URL:en för nyckelvalvet, som finns i formuläret `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Det kräver också ett namn för hemligheten - vi använder "mySecret". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Du kan kontrollera att hemligheten har angetts med kommandot [az keyvault secret show:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare inställda värdet med [metoden GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Din hemlighet är `keyvaultSecret.Value;`nu sparad som .

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckelvalvet och motsvarande resursgrupp.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv, lagrade en hemlighet och hämtade den hemligheten. Se [hela konsolappen i GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Implementera [autentisering från tjänst till Azure Key Vault med .NET](service-to-service-authentication.md)
- Läs en [översikt över Azure Key Vault](key-vault-overview.md)
- Se [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md)
- Läs mer om [nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- Granska [metodtips för Azure Key Vault](key-vault-best-practices.md)
