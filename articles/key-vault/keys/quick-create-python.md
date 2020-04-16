---
title: Snabbstart - Azure Key Vault-klientbibliotek för Python
description: Lär dig hur du skapar, hämtar och tar bort nycklar från ett Azure-nyckelvalv med Python-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: e6120d5961dc31845c1322d052d46b52f4d2be6c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424183"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Snabbstart: Azure Key Vault-klientbibliotek för Python

Kom igång med Azure Key Vault-klientbiblioteket för Python. Följ stegen nedan för att installera paketet och prova exempelkod för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault-klientbiblioteket för Python för att:

- Öka säkerheten och kontrollen över nycklar och lösenord.
- Skapa och importera krypteringsnycklar på några minuter.
- Minska svarstiden med molnskala och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 Nivå 2 validerade HSM.Use FIPS 140-2 Level 2 validated HSMs.

[API-referensdokumentation](/python/api/overview/azure/key-vault?view=azure-python) | [Bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [(Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, 3.5.3 eller senare
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabbstarten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) i ett Linux-terminalfönster.

## <a name="setting-up"></a>Inrätta

### <a name="install-the-package"></a>Installera paketet

Installera azure key vault-nyckelbiblioteket för Python i konsolfönstret.

```console
pip install azure-keyvault-keys
```

För den här snabbstarten måste du också installera azure.identity-paketet:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och nyckelvalv

Den här snabbstarten använder ett förskapade Azure-nyckelvalv. Du kan skapa ett nyckelvalv genom att följa stegen i [snabbstarten Azure CLI,](quick-create-cli.md) [Snabbstarten i Azure PowerShell](quick-create-powershell.md)eller [Snabbstarten för Azure-portalen](quick-create-portal.md). Alternativt kan du köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckelvalv måste ha ett unikt namn. Ersätt <ditt unika nyckelval-namn> med namnet på nyckelvalvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserat .NET-program är med en hanterad identitet. Se [Använda en hanterad apptjänstidentitet för att komma åt Azure Key Vault](../general/managed-identity.md) för mer information. För enkelhetens skull skapar dock den här snabbstarten ett .NET-konsolprogram. Autentisering av ett skrivbordsprogram med Azure kräver användning av ett tjänsthuvudnamn och en åtkomstkontrollprincip.

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

Notera clientId och clientSecret, eftersom vi kommer att använda dem i [steget Ange miljövariabel](#set-environmental-variables) nedan.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvudnamn åtkomst till nyckelvalvet

Skapa en åtkomstprincip för nyckelvalvet som ger behörighet till tjänstens huvudnamn genom att skicka clientId till kommandot [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Ge tjänstens huvudnamn hämta, lista och skapa behörigheter för nycklar.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Ange miljövariabler

Metoden DefaultAzureCredential i vår applikation bygger på `AZURE_CLIENT_ID`tre `AZURE_CLIENT_SECRET`miljövariabler: , och `AZURE_TENANT_ID`. Ange dessa variabler till klientId-, clientSecret- och tenantId-värden som `export VARNAME=VALUE` du noterade i steget Skapa ett [tjänsthuvudnamn](#create-a-service-principal) med formatet. (Den här metoden anger bara variablerna för det aktuella skalet och processer som skapats `/etc/environment ` från skalet. 

Du måste också spara nyckelvalvets namn som `KEY_VAULT_NAME`en miljövariabel som kallas .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektmodell

Med Azure Key Vault-klientbiblioteket för Python kan du hantera nycklar och relaterade resurser som certifikat och hemligheter. Kodexemplen nedan visar hur du skapar en klient, skapar en nyckel, hämtar en nyckel och tar bort en nyckel.

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande direktiv överst i koden:

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Att autentisera till ditt nyckelvalv och skapa en nyckelvalvklient beror på miljövariablerna i steget [Ange miljövariabler](#set-environmental-variables) ovan. Namnet på nyckelvalvet expanderas till nyckelvalvet URI, i formatet "https://<your-key-vault-name>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Spara en nyckel

Nu när din ansökan är autentiserat kan du placera en nyckel i din keyvault 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

Du kan kontrollera att nyckeln har ställts in med kommandot [az keyvault key show:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Hämta en nyckel

Du kan nu hämta den tidigare skapade nyckeln

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

Nyckeln sparas nu `retrieved_key`som .

### <a name="delete-a-key"></a>Ta bort en nyckel

Slutligen, låt oss ta bort nyckeln från din nyckel valv

```python
client.delete_key(keyName)
```

Du kan kontrollera att nyckeln är borta med kommandot [az keyvault key show:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
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

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv, lagrade en nyckel och hämtade nyckeln. Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se [utvecklarguiden för Azure Key Vault](../general/developers-guide.md)
- Granska [metodtips för Azure Key Vault](../general/best-practices.md)
