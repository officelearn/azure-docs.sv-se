---
title: Snabb start – Azure Key Vault python-klient bibliotek – hantera certifikat
description: Lär dig hur du skapar, hämtar och tar bort certifikat från ett Azure Key Vault med hjälp av python-klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488634"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Snabb start: klient bibliotek för Azure Key Vault certifikat för python

Kom igång med Azure Key Vault klient biblioteket för python. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter. Genom att använda Key Vault för att lagra certifikat undviker du att lagra certifikat i din kod, vilket ökar säkerheten för din app.

[API-referens dokumentation](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Paket (python-paket index)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Konfigurera din lokala miljö

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installera biblioteket för Key Vault certifikat:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Ge tjänstens huvud namn åtkomst till ditt nyckel valv

Kör följande [AZ-nyckel valv set-princip](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) kommando för att auktorisera tjänstens huvud namn för Get-, list-och Create-åtgärder på certifikat.

# <a name="cmd"></a>[kommandot](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Detta kommando förlitar `KEY_VAULT_NAME` sig på `AZURE_CLIENT_ID` variablerna och för miljön som skapats i föregående steg.

Mer information finns i [tilldela en åtkomst princip – CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Skapa exempel koden

Med Azure Key Vault klient biblioteket för python kan du hantera certifikat och relaterade till gångar, till exempel hemligheter och kryptografiska nycklar. Följande kod exempel visar hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Skapa en fil med namnet *kv_certificates. py* som innehåller den här koden.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kör koden

Kontrol lera att koden i föregående avsnitt finns i en fil med namnet *kv_certificates. py*. Kör sedan koden med följande kommando:

```terminal
python kv_certificates.py
```

- Om du får problem med behörigheten kontrollerar du att du körde [ `az keyvault set-policy` kommandot](#give-the-service-principal-access-to-your-key-vault).
- Att köra koden igen med samma nyckel namn kan orsaka felet, "(konflikt) certifikatet <name> är för närvarande i ett borttaget, men går inte att återställa." Använd ett annat nyckel namn.

## <a name="code-details"></a>Kod information

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I föregående kod [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) använder objektet de miljövariabler som du skapade för tjänstens huvud namn. Du anger den här autentiseringsuppgiften när du skapar ett klient objekt från ett Azure-bibliotek, till exempel [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python) , tillsammans med URI: n för den resurs som du vill arbeta med via klienten:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Spara ett certifikat

När du har fått klient objekt för nyckel valvet kan du skapa ett certifikat med hjälp av metoden [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) : 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Här kräver certifikatet en princip som erhållits med metoden [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--) .

Anropa en `begin_create_certificate` metod genererar ett asynkront anrop till Azure-REST API för nyckel valvet. Det asynkrona anropet returnerar ett avsöknings objekt. Anropa avsöknings metoden för att vänta på resultatet av åtgärden `result` .

Vid hantering av begäran autentiserar Azure anroparens identitet (tjänstens huvud namn) med hjälp av det Credential-objekt som du angav för klienten.

Det kontrollerar också att anroparen har behörighet att utföra den begärda åtgärden. Du har beviljat den här behörigheten till tjänstens huvud namn tidigare med hjälp av [ `az keyvault set-policy` kommandot](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-certificate"></a>Hämta ett certifikat

Om du vill läsa ett certifikat från Key Vault använder du metoden [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-) :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Du kan också kontrol lera att certifikatet har angetts med Azure CLI-kommandot [AZ för certifikat visning](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Ta bort ett certifikat

Om du vill ta bort ett certifikat använder du metoden [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-) :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate`Metoden är asynkron och returnerar ett avsöknings objekt. Att anropa avsöknings `result` metoden väntar på att slutföras.

Du kan kontrol lera att certifikatet har tagits bort med Azure CLI-kommandot [AZ för certifikat visning](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

När du har tagit bort ett certifikat finns det kvar i ett borttaget, men ett återställnings Bart tillstånd för en tid. Om du kör koden igen ska du använda ett annat certifikat namn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du också vill experimentera med [hemligheter](../secrets/quick-create-python.md) och [nycklar](../keys/quick-create-python.md)kan du återanvända Key Vault som skapats i den här artikeln.

Annars, när du är klar med resurserna som du skapade i den här artikeln, använder du följande kommando för att ta bort resurs gruppen och alla resurser som finns i den:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Guide för Azure Key Vault utvecklare](../general/developers-guide.md)
- [Metod tips för Azure Key Vault](../general/best-practices.md)
- [Autentisera med Key Vault](../general/authentication.md)
