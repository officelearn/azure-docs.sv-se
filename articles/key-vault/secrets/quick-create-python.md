---
title: Snabb start – Azure Key Vault python-klient bibliotek – hantera hemligheter
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av python-klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489212"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Snabb start: klient bibliotek för Azure Key Vault hemligheter för python

Kom igång med Azure Key Vault klient biblioteket för python. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter. Genom att använda Key Vault för att lagra hemligheter undviker du att lagra hemligheter i din kod, vilket ökar säkerheten för din app.

[API-referens dokumentation](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Paket (python-paket index)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Konfigurera din lokala miljö

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installera Key Vault hemligheter-biblioteket:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

Kör följande [AZ-nyckel valv set-princip](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) kommando för att auktorisera tjänstens huvud namn för Get-, list-och set-åtgärder på hemligheter. Detta kommando förlitar `KEY_VAULT_NAME` sig på `AZURE_CLIENT_ID` variablerna och för miljön som skapats i föregående steg.

# <a name="cmd"></a>[kommandot](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Detta kommando förlitar `KEY_VAULT_NAME` sig på `AZURE_CLIENT_ID` variablerna och för miljön som skapats i föregående steg.

Mer information finns i [tilldela en åtkomst princip – CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Skapa exempel koden

Med Azure Key Vault klient biblioteket för python kan du hantera hemligheter och relaterade till gångar som certifikat och kryptografiska nycklar. Följande kod exempel visar hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Skapa en fil med namnet *kv_secrets. py* som innehåller den här koden.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kör koden

Kontrol lera att koden i föregående avsnitt finns i en fil med namnet *kv_secrets. py*. Kör sedan koden med följande kommando:

```terminal
python kv_secrets.py
```

- Om du får problem med behörigheten kontrollerar du att du körde [ `az keyvault set-policy` kommandot](#give-the-service-principal-access-to-your-key-vault).
- Att köra koden igen med samma hemliga namn kan orsaka felet, "(konflikt) hemligheten <name> är för närvarande i ett borttaget men återställnings Bart tillstånd." Använd ett annat hemligt namn.

## <a name="code-details"></a>Kod information

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I föregående kod [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) använder objektet de miljövariabler som du skapade för tjänstens huvud namn. Du anger den här autentiseringsuppgiften när du skapar ett klient objekt från ett Azure-bibliotek, till exempel [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) , tillsammans med URI: n för den resurs som du vill arbeta med via klienten:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Spara en hemlighet

När du har fått klient objekt för nyckel valvet kan du lagra en hemlighet med hjälp av metoden [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) : 

```python
client.set_secret(secretName, secretValue)
```

Anrop `set_secret` genererar ett anrop till Azure-REST API för nyckel valvet.

Vid hantering av begäran autentiserar Azure anroparens identitet (tjänstens huvud namn) med hjälp av det Credential-objekt som du angav för klienten.

Det kontrollerar också att anroparen har behörighet att utföra den begärda åtgärden. Du har beviljat den här behörigheten till tjänstens huvud namn tidigare med hjälp av [ `az keyvault set-policy` kommandot](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Om du vill läsa en hemlighet från Key Vault använder du [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) -metoden:

```python
retrieved_secret = client.get_secret(secretName)
 ```

Det hemliga värdet finns i `retrieved_secret.value` .

Du kan också hämta en hemlighet med Azure CLI-kommandot [AZ-valv hemligt show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Om du vill ta bort en hemlighet använder du metoden [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret`Metoden är asynkron och returnerar ett avsöknings objekt. Att anropa avsöknings `result` metoden väntar på att slutföras.

Du kan kontrol lera att hemligheten har tagits bort med Azure CLI-kommandot [AZ-valv hemligt show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

När den har tagits bort kvarstår en hemlighet i ett borttaget, men ett återställnings Bart tillstånd för en tid. Om du kör koden igen ska du använda ett annat hemligt namn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du också vill experimentera med [certifikat](../certificates/quick-create-python.md) och [nycklar](../keys/quick-create-python.md)kan du återanvända Key Vault som skapats i den här artikeln.

Annars, när du är klar med resurserna som du skapade i den här artikeln, använder du följande kommando för att ta bort resurs gruppen och alla resurser som finns i den:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Guide för Azure Key Vault utvecklare](../general/developers-guide.md)
- [Metod tips för Azure Key Vault](../general/best-practices.md)
- [Autentisera med Key Vault](../general/authentication.md)
