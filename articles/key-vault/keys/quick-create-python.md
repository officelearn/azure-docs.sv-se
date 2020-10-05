---
title: Snabb start – Azure Key Vault python-klient bibliotek – hantera nycklar
description: Lär dig hur du skapar, hämtar och tar bort nycklar från ett Azure Key Vault med hjälp av python-klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482151"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Snabb start: klient biblioteket för Azure Key Vaults nycklar för python

Kom igång med Azure Key Vault klient biblioteket för python. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter. Genom att använda Key Vault för att lagra kryptografiska nycklar undviker du att lagra sådana nycklar i din kod, vilket ökar säkerheten för din app.

[API-referens dokumentation](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Paket (python-paket index)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Konfigurera din lokala miljö

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installera biblioteket för Key Vault nycklar:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

Kör följande AZ-kommando för att [Ange princip](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) för att auktorisera tjänstens huvud namn för att ta bort, Hämta, lista och skapa åtgärder på nycklar. 

# <a name="cmd"></a>[kommandot](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Detta kommando förlitar `KEY_VAULT_NAME` sig på `AZURE_CLIENT_ID` variablerna och för miljön som skapats i föregående steg.

Mer information finns i [tilldela en åtkomst princip – CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Skapa exempel koden

Med Azure Key Vault klient biblioteket för python kan du hantera kryptografiska nycklar och relaterade till gångar som certifikat och hemligheter. Följande kod exempel visar hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Skapa en fil med namnet *kv_keys. py* som innehåller den här koden.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kör koden

Kontrol lera att koden i föregående avsnitt finns i en fil med namnet *kv_keys. py*. Kör sedan koden med följande kommando:

```terminal
python kv_keys.py
```

- Om du får problem med behörigheten kontrollerar du att du körde [ `az keyvault set-policy` kommandot](#give-the-service-principal-access-to-your-key-vault).
- Att köra koden igen med samma nyckel namn kan orsaka felet, "(konflikt)-nyckeln <name> är för närvarande i ett borttaget men återställnings Bart tillstånd." Använd ett annat nyckel namn.

## <a name="code-details"></a>Kod information

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I föregående kod [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) använder objektet de miljövariabler som du skapade för tjänstens huvud namn. Du anger den här autentiseringsuppgiften när du skapar ett klient objekt från ett Azure-bibliotek, till exempel [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python) , tillsammans med URI: n för den resurs som du vill arbeta med via klienten:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Spara en nyckel

När du har fått klient objekt för nyckel valvet kan du lagra en nyckel med hjälp av metoden [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) : 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Du kan också använda [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) eller [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

Anropa en `create` metod genererar ett anrop till Azure-REST API för nyckel valvet.

Vid hantering av begäran autentiserar Azure anroparens identitet (tjänstens huvud namn) med hjälp av det Credential-objekt som du angav för klienten.

Det kontrollerar också att anroparen har behörighet att utföra den begärda åtgärden. Du har beviljat den här behörigheten till tjänstens huvud namn tidigare med hjälp av [ `az keyvault set-policy` kommandot](#give-the-service-principal-access-to-your-key-vault).

## <a name="retrieve-a-key"></a>Hämta en nyckel

Om du vill läsa en nyckel från Key Vault använder du [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-) -metoden:

```python
retrieved_key = client.get_key(keyName)
 ```

Du kan också kontrol lera att nyckeln har angetts med Azure CLI-kommandot [AZ Key Vault Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

### <a name="delete-a-key"></a>Ta bort en nyckel

Om du vill ta bort en nyckel använder du metoden [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-) :

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key`Metoden är asynkron och returnerar ett avsöknings objekt. Att anropa avsöknings `result` metoden väntar på att slutföras.

Du kan kontrol lera att nyckeln har tagits bort med Azure CLI-kommandot [AZ Key Vault-nyckeln show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

När en nyckel har tagits bort finns den kvar i ett borttaget, men ett återställnings Bart tillstånd för en tid. Om du kör koden igen ska du använda ett annat nyckel namn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du också vill experimentera med [certifikat](../certificates/quick-create-python.md) och [hemligheter](../secrets/quick-create-python.md)kan du återanvända Key Vault som skapats i den här artikeln.

Annars, när du är klar med resurserna som du skapade i den här artikeln, använder du följande kommando för att ta bort resurs gruppen och alla resurser som finns i den:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Guide för Azure Key Vault utvecklare](../general/developers-guide.md)
- [Metod tips för Azure Key Vault](../general/best-practices.md)
- [Autentisera med Key Vault](../general/authentication.md)
