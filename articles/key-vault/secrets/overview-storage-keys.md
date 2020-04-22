---
title: Hantera lagringskontonycklar med Azure Key Vault och Azure CLI
description: Lagringskontonycklar ger sömlös integrering mellan Azure Key Vault och nyckelbaserad åtkomst till ett Azure-lagringskonto.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 1125bafa43ce1752c58d1cce0bba66a6bbd32c32
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685420"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Hantera lagringskontonycklar med Key Vault och Azure CLI

Ett Azure-lagringskonto använder autentiseringsuppgifter som består av ett kontonamn och en nyckel. Nyckeln genereras automatiskt och fungerar som ett lösenord, snarare än en kryptografisk nyckel. Key Vault hanterar lagringskontonycklar genom att lagra dem som Key Vault-hemligheter. 

Du kan använda nyckelnyckelfunktionen för hanterade lagringskontonycklar för att lista (synkronisera) nycklar med ett Azure-lagringskonto och återskapa (rotera) nycklarna med jämna mellanrum. Du kan hantera nycklar för både lagringskonton och klassiska lagringskonton.

När du använder nyckelfunktionen för hanterade lagringskonton bör du tänka på följande:

- Nyckelvärden returneras aldrig som svar på en uppringare.
- Endast Key Vault ska hantera dina lagringskontonycklar. Hantera inte nycklarna själv och undvik att störa Key Vault-processer.
- Endast ett key vault-objekt ska hantera lagringskontonycklar. Tillåt inte nyckelhantering från flera objekt.
- Du kan begära att Key Vault hanterar ditt lagringskonto med ett användarobjekt, men inte med ett huvudnamn för tjänsten.
- Återskapa nycklar med endast Key Vault. Återskapa inte dina lagringskontonycklar manuellt.

Vi rekommenderar att du använder Azure Storage-integrering med Azure Active Directory (Azure AD), Microsofts molnbaserade identitets- och åtkomsthanteringstjänst. Azure AD-integrering är tillgänglig för [Azure-blobbar och köer](../../storage/common/storage-auth-aad.md)och ger OAuth2 tokenbaserad åtkomst till Azure Storage (precis som Azure Key Vault).

Med Azure AD kan du autentisera klientprogrammet med hjälp av ett program eller en användaridentitet i stället för autentiseringsuppgifter för lagringskonto. Du kan använda en [Azure AD-hanterad identitet](/azure/active-directory/managed-identities-azure-resources/) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering och lagring av autentiseringsuppgifter i eller med ditt program.

Azure AD använder rollbaserad åtkomstkontroll (RBAC) för att hantera auktorisering, som också stöds av Key Vault.

## <a name="service-principal-application-id"></a>Tjänsthuvudnamns-ID

En Azure AD-klient ger varje registrerat program ett [huvudnamn](/azure/active-directory/develop/developer-glossary#service-principal-object)för tjänsten . Tjänstens huvudnamn fungerar som program-ID, som används under auktoriseringsinställningar för åtkomst till andra Azure-resurser via RBAC.

Key Vault är ett Microsoft-program som är förregistrerat i alla Azure AD-klienter. Key Vault registreras under samma program-ID i varje Azure-moln.

| Klientorganisationer | Molnet | Program-ID:t |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, offentlig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Annat  | Alla | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här guiden måste du först göra följande:

- [Installera Azure CLI](/cli/azure/install-azure-cli).
- [Skapa ett nyckelvalv](quick-create-cli.md)
- [Skapa ett Azure-lagringskonto](../../storage/common/storage-account-create.md?tabs=azure-cli). Lagringskontonamnet får endast använda gemener och siffror. Namnets längd måste vara mellan 3 och 24 tecken.
      
## <a name="manage-storage-account-keys"></a>Hantera lagringskontonycklar

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Autentisera din Azure CLI-session med [az-inloggningskommandona.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Ge Key Vault åtkomst till ditt lagringskonto

Använd kommandot Skapa Azure CLI [az-rolltilldelning](/cli/azure/role/assignment?view=azure-cli-latest) för att ge Key Vault åtkomst till ditt lagringskonto. Ange följande parametervärden för kommandot:

- `--role`: Skicka RBAC-rollen "Key Operator Service Role" för lagringskonto nyckeloperatör. Den här rollen begränsar åtkomstomfånget till ditt lagringskonto. För ett klassiskt lagringskonto skickar du i stället "Klassisk lagringskontonyckeloperatörstjänstroll" i stället.
- `--assignee`: Skicka värdethttps://vault.azure.net" ", som är url:en för Key Vault i det offentliga Azure-molnet. (För Azure Goverment-moln använd '-asingee-object-id' i stället, se [Tjänsthuvudnamnstillämbetningsprogram-ID](#service-principal-application-id).)
- `--scope`: Skicka ditt lagringskontoresurs-ID, `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`som finns i formuläret . Om du vill hitta ditt prenumerations-ID använder du kommandot Azure CLI [az account list;](/cli/azure/account?view=azure-cli-latest#az-account-list) Om du vill hitta ditt lagringskontonamn och resursgrupp för lagringskonto använder du kommandot Azure CLI [az storage account list.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge ditt användarkonto behörighet till hanterade lagringskonton

Använd cmdleten Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) för att uppdatera åtkomstprincipen för Key Vault och bevilja lagringskontobehörigheter till ditt användarkonto.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Observera att behörigheter för lagringskonton inte är tillgängliga på sidan "Åtkomstprinciper" för lagringskontot i Azure-portalen.
### <a name="create-a-key-vault-managed-storage-account"></a>Skapa ett key vault-hanterat lagringskonto

 Skapa ett Key Vault-hanterat lagringskonto med azure CLI [az keyvault-lagringskommandot.](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) Ställ in en regenereringsperiod på 90 dagar. Efter 90 dagar återskapas `key1` och byts `key2` den `key1`aktiva nyckeln från till . `key1`markeras sedan som den aktiva nyckeln. Ange följande parametervärden för kommandot:

- `--vault-name`: Skicka namnet på nyckelvalvet. Om du vill hitta namnet på nyckelvalvet använder du kommandot Azure CLI [az keyvault list.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)
- `-n`: Skicka namnet på ditt lagringskonto. Om du vill hitta namnet på ditt lagringskonto använder du kommandot Azure CLI [az storage account list.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
- `--resource-id`: Skicka ditt lagringskontoresurs-ID, `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`som finns i formuläret . Om du vill hitta ditt prenumerations-ID använder du kommandot Azure CLI [az account list;](/cli/azure/account?view=azure-cli-latest#az-account-list) Om du vill hitta ditt lagringskontonamn och resursgrupp för lagringskonto använder du kommandot Azure CLI [az storage account list.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Signaturtoken för delad åtkomst

Du kan också be Key Vault att generera signaturtoken för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan ge klienter åtkomst till resurser i ditt lagringskonto utan att dela dina kontonycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagringsresurser utan att kompromissa med dina kontonycklar.

Kommandona i det här avsnittet utför följande åtgärder:

- Ange en signaturdefinition `<YourSASDefinitionName>`för delad åtkomst för konto . Definitionen anges på ett Key Vault-hanterat `<YourStorageAccountName>` `<YourKeyVaultName>`lagringskonto i nyckelvalvet .
- Skapa en signaturtoken för delad åtkomst för ett konto för Blob-, File-, Table- och Queue-tjänster. Token skapas för resurstyper Tjänst, Behållare och Objekt. Token skapas med alla behörigheter, över https och med de angivna start- och slutdatumen.
- Ange en signaturdefinition för delad åtkomst för Key Vault-hanterad åtkomst i valvet. Definitionen har mallen URI för signaturtoken för delad åtkomst som skapades. Definitionen har signaturtypen `account` delad åtkomst och är giltig i N dagar.
- Kontrollera att signaturen för delad åtkomst har sparats i nyckelvalvet som en hemlighet.

### <a name="create-a-shared-access-signature-token"></a>Skapa en signaturtoken för delad åtkomst

Skapa en signaturdefinition för delad åtkomst med kommandot Azure CLI [az storage account generate-sas.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Den här `storage` åtgärden `setsas` kräver behörigheter och behörigheter.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
När åtgärden har körts kopierar du utdata.

```console
"se=2020-01-01&sp=***"
```

Den här utdata kommer `--template-id` att skickas till parametern i nästa steg.

### <a name="generate-a-shared-access-signature-definition"></a>Generera en signaturdefinition för delad åtkomst

Använd kommandot Azure CLI [az keyvault storage sas-definition create,](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) som `--template-id` skickar utdata från föregående steg till parametern, för att skapa en signaturdefinition för delad åtkomst.  Du kan ange namnet på `-n` valfrit till parametern.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifiera signaturdefinitionen för delad åtkomst

Du kan kontrollera att signaturdefinitionen för delad åtkomst har lagrats i nyckelvalvet med hjälp av den hemliga listan Azure CLI [az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) och [az keyvault secret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) show-kommandon.

Leta först reda på signaturdefinitionen för delad åtkomst i nyckelvalvet med kommandot [az keyvault secret list.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Hemligheten som motsvarar SAS-definitionen har följande egenskaper:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Du kan nu använda kommandot [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) och egenskapen `id` för att visa innehållet i hemligheten.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Utdata för det här kommandot visar`value`sas-definitionssträngen som .


## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/).
- Granska artiklar på [Azure Key Vault-teamets blogg](https://blogs.technet.microsoft.com/kv/).
- Se referensdokumentationen [för az keyvault-lagring.](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)
