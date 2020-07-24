---
title: Snabb start – Azure Key Vault python-klient bibliotek – hantera hemligheter
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av python-klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: bb73df659e3910002378b8417596b7b957022b43
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074534"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Snabb start: klient bibliotek för Azure Key Vault hemligheter för python

Kom igång med Azure Key Vault klient biblioteket för python. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter.

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Använd Key Vault klient bibliotek för python för att:

- Öka säkerheten och kontrollen över nycklar och lösen ord.
- Skapa och importera krypterings nycklar på några minuter.
- Minska svars tiden med moln skalning och global redundans.
- Förenkla och automatisera uppgifter för TLS/SSL-certifikat.
- Använd FIPS 140-2 nivå 2-verifierade HSM: er.

[API-referens dokumentation](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault)  |  [Paket (python-paket index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 eller senare
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/)

Den här snabb starten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) i ett Linux-terminalfönster.

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-package"></a>Installera paketet

I konsol fönstret installerar du biblioteket Azure Key Vault hemligheter för python.

```console
pip install azure-keyvault-secrets
```

I den här snabb starten måste du även installera Azure. Identity-paketet:

```console
pip install azure.identity
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

Med Azure Key Vault klient biblioteket för python kan du hantera nycklar och relaterade till gångar som certifikat och hemligheter. I kod exemplen nedan visas hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsol programmet finns på https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i koden:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

Autentisering till ditt nyckel valv och att skapa en Key Vault-klient beror på miljövariablerna i steget [Ange miljövariabler](#set-environmental-variables) ovan. Namnet på nyckel valvet expanderas till Key Vault-URI: n i formatet "https://<Your-Key-Valve-Name>. vault.azure.net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du ställa in en hemlighet i ditt nyckel valv med hjälp av-klienten. SetSecret-metod] (/dotNet/API/Microsoft.Azure.keyvault.keyvaultclientextensions.setsecretasync) Detta kräver ett namn för hemligheten – vi använder "hemlig hemlighet" i det här exemplet.  

```python
client.set_secret(secretName, secretValue)
```

Du kan kontrol lera att hemligheten har angetts med kommandot [AZ-valvets hemliga show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare angivna värdet med- [klienten. GetSecret-metod](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Din hemlighet sparas nu som `retrieved_secret.value` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen tar vi bort hemligheten från nyckel valvet med- [klienten. DeleteSecret-metod](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName)
```

Du kan kontrol lera att hemligheten är borta med kommandot [AZ-valv för hemligt show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
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
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print("Your secret is '" + secretValue + "'.")

print("Retrieving your secret from " + keyVaultName + ".")

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.")
print("Deleting your secret from " + keyVaultName + " ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nyckel valv, lagrat en hemlighet och hämtat hemligheten. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)
