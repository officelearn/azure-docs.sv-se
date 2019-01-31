---
title: Store tillgång till autentiseringsuppgifterna på den virtuella datorn för datavetenskap på ett säkert sätt – Azure | Microsoft Docs
description: Lär dig mer om att på ett säkert sätt lagra autentiseringsuppgifter på den virtuella datorn för datavetenskap. Du kommer lära dig hur du använder hanterade tjänstidentiteter och Azure Key Vault för att lagra autentiseringsuppgifter.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys, tdsp
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 79dba586a5f7102d0012c381593551a951f1b38e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451300"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Store åtkomst autentiseringsuppgifter på den virtuella datorn för datavetenskap på ett säkert sätt

En gemensam utmaning att skapa molnprogram är så här hanterar du de autentiseringsuppgifter som måste finnas i din kod för att autentisera till molntjänster. Att skydda dessa autentiseringsuppgifter är en viktig uppgift. Vi rekommenderar de aldrig visas på arbetsstationer eller hämta checkats in till källkontroll. 

[Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) gör lösa problemet enklare genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan några autentiseringsuppgifter i din kod. 

Ett sätt att skydda autentiseringsuppgifter är att använda MSI i kombination med [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), en hanterad Azure-tjänsten för att lagra hemligheter och kryptografiska nycklar på ett säkert sätt. Du kan komma åt ett nyckelvalv med hjälp av den hanterade identitet och hämta auktoriserade hemligheter och kryptografiska nycklar i key Vault. 

Hanterade identiteter för Azure-resurser och dokumentation om Key Vault är en heltäckande resurs för detaljerad information om dessa tjänster. Resten av den här artikeln beskriver grundläggande användning av MSI och Key Vault på Data Science Virtual Machine (DSVM) att komma åt Azure-resurser. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Skapa en hanterad identitet på DSVM 


```
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="assign-key-vault-access-permission-to-a-vm-principal"></a>Ge Key Vault-behörighet för ett huvudnamn för virtuell dator
```
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI. 

# Assign only get and set permission but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Åtkomst till en hemlighet i nyckelvalvet från DSVM

```
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Åtkomstnycklar för lagring från DSVM

```
# Prerequisite: You have granted your VM's MSI access to use storage account access keys based on instructions from the article at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```
## <a name="access-the-key-vault-from-python"></a>Åtkomst till nyckelvalvet från Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your key vault URL.
"SQLPasswd",       # The name of your secret that already exists in the key vault.
""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Åtkomst till nyckelvalvet från Azure CLI

```
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. Here are commands to access the key vault from Azure CLI without having to log in to an Azure account. 
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permission, like accessing storage account keys, reading specific secrets, and writing new secrets, is provided to the MSI. 

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
