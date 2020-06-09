---
title: Snabb start – Azure Key Vault klient bibliotek för python
description: Lär dig hur du skapar, hämtar och tar bort nycklar från ett Azure Key Vault med hjälp av python-klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: 830866880c1fc86bbc7510290a50df7fc86b9d1e
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84555957"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Snabb start: Azure Key Vault klient bibliotek för python

Kom igång med Azure Key Vault klient biblioteket för python. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault klient bibliotek för python för att:

- Öka säkerheten och kontrollen över nycklar och lösen ord.
- Skapa och importera krypterings nycklar på några minuter.
- Minska svars tiden med moln skalning och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 nivå 2-verifierade HSM: er.

[API-referens dokumentation](/python/api/overview/azure/key-vault?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault)  |  [Paket (python-paket index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 eller senare
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview)

Den här snabb starten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) i ett Linux-terminalfönster.

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-package"></a>Installera paketet

I konsol fönstret installerar du biblioteket Azure Key Vault nycklar för python.

```console
pip install azure-keyvault-keys
```

I den här snabb starten måste du även installera Azure. Identity-paketet:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

I den här snabb starten används ett i förväg skapade Azure Key Vault. Du kan skapa ett nyckel valv genom att följa stegen i snabb starten för [Azure CLI](quick-create-cli.md), [Azure PowerShell snabb start](quick-create-powershell.md)eller [Azure Portal snabb start](quick-create-portal.md). Du kan också köra Azure CLI-kommandona nedan.

> [!Important]
> Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Det enklaste sättet att autentisera ett molnbaserad .NET-program är med en hanterad identitet. Mer information finns i [använda en app service hanterad identitet för att få åtkomst till Azure Key Vault](../general/managed-identity.md) . 

För enkelhetens skull skapar den här snabb starten ett Skriv bords program som kräver användning av ett huvud namn för tjänsten och en princip för åtkomst kontroll. Din tjänst princip kräver ett unikt namn i formatet "http:// &lt; My-Unique-service-name &gt; ".

Skapa en tjänst princip med Azure CLI [-AZ AD SP Create-for-RBAC-](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommando:

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principle-name&gt;" --sdk-auth
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

Anteckna clientId och clientSecret, eftersom vi kommer att använda dem i steget [Ange miljö variabel](#set-environmental-variables) .

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som ger behörighet till tjänstens huvud namn genom att skicka clientId till [AZ-kommandot Set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Ge tjänstens huvud namn get-, list-och Create-behörigheter för nycklar.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Ange miljövariabler

DefaultAzureCredential-metoden i programmet är beroende av tre miljövariabler: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` och `AZURE_TENANT_ID` . Ange dessa variabler som clientId-, clientSecret-och tenantId-värden som du antecknade i steget [skapa ett huvud namn för tjänsten](#create-a-service-principal) med hjälp av `export VARNAME=VALUE` formatet. (Den här metoden anger bara variablerna för ditt aktuella gränssnitt och processer som skapats från gränssnittet. om du vill lägga till dessa variabler permanent i miljön redigerar du `/etc/environment ` filen.) 

Du måste också spara nyckel valvets namn som en miljö variabel som kallas `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objekt modell

Med Azure Key Vault klient biblioteket för python kan du hantera nycklar och relaterade till gångar som certifikat och hemligheter. I kod exemplen nedan visas hur du skapar en-klient, skapar en nyckel, hämtar en nyckel och tar bort en nyckel.

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i koden:

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Autentisering till ditt nyckel valv och att skapa en Key Vault-klient beror på miljövariablerna i steget [Ange miljövariabler](#set-environmental-variables) ovan. Namnet på nyckel valvet expanderas till Key Vault-URI: n i formatet "https://<Your-Key-Valve-Name>. vault.azure.net".

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Spara en nyckel

Nu när ditt program är autentiserat kan du ställa in en nyckel i ditt nyckel valv 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

Du kan kontrol lera att nyckeln har angetts med kommandot [AZ Key Vault Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Hämta en nyckel

Nu kan du hämta den tidigare skapade nyckeln

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

Din nyckel sparas nu som `retrieved_key` .

### <a name="delete-a-key"></a>Ta bort en nyckel

Slutligen tar vi bort nyckeln från nyckel valvet

```python
client.delete_key(keyName)
```

Du kan kontrol lera att nyckeln är borta med kommandot [AZ Key Vault Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckel valvet och motsvarande resurs grupp.

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

I den här snabb starten skapade du ett nyckel valv, lagrat en nyckel och hämtat nyckeln. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)
