---
title: Store komma åt autentiseringsuppgifter på den datavetenskap virtuella datorn på ett säkert sätt - Azure | Microsoft Docs
description: Lagra åtkomst autentiseringsuppgifter på den datavetenskap virtuella datorn på ett säkert sätt.
keywords: Djup learning AI datavetenskap verktyg, datavetenskap virtuell dator, geospatiala analytics, team av vetenskapliga data
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 4eb1d657adc37ef0d1e4055573b174d58baf2e0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Lagra åtkomst autentiseringsuppgifter på den datavetenskap virtuella datorn på ett säkert sätt

En gemensam utmaning när skapa molnprogram är hur du hanterar autentiseringsuppgifterna som måste vara i koden för att autentisera till molntjänster. Skydda autentiseringsuppgifterna är en viktig uppgift. Vi rekommenderar de aldrig visas på arbetsstationer eller checkas till källkontroll. 

[Hanterad Service identitet (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) gör lösa problemet enklare genom att ge Azure-tjänster en automatiskt hanterade identitet i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan några autentiseringsuppgifter i koden. En vanliga mönstret för att säkra autentiseringsuppgifter är att använda MSI i kombination med den [Azure Keyvault](https://docs.microsoft.com/azure/key-vault/)en hanterad Azure-tjänsten för att lagra hemligheter och krypteringsnycklar på ett säkert sätt. Du kan använda nyckelvalv med hjälp av hanterade tjänstidentiteten och hämta auktoriserade hemligheter och kryptografiska nycklar från Nyckelvalvet. 

Dokumentation för MSI och Key Vault är en heltäckande resurs för detaljerad information om dessa tjänster. Resten av den här artikeln är en grundläggande introduktion visar grundläggande användning av MSI och Key Vault på Data vetenskap virtuell dator (DSVM). 

## <a name="walkthrough-of-using-msi-to-securely-access-azure-resources"></a>Genomgång av MSI säker åtkomst till Azure-resurser

## <a name="1-create-msi-on-the-dsvm"></a>1. Skapa MSI på DSVM 


```
# Pre-requisites: You have already created a Data Science VM in the usual way

# Create identity principal for the VM
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal id of the DSVM
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="2-assign-keyvault-access-permission-to-vm-principal"></a>2. Tilldela Keyvault åtkomstbehörighet till VM säkerhetsobjekt
```
# Pre-requisites: You have already create an empty Key Vault resource on Azure using Portal or Azure CLI 

# Assign only get and set permission but not the capability to list the keys
az keyvault set-policy --object-id <PrincipalID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="3-access-a-secret-in-the-keyvault-from-the-dsvm"></a>3. Åtkomst till en hemlighet i Keyvault från DSVM

```
# Get the access token for VM
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access Keyvault using access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="4-access-storage-keys-from-the-dsvm"></a>4. Åtkomstnycklar för lagring från DSVM

```
# Pre-requisites: You have granted your VM's MSI access to use storage account access keys based on instruction from this article:https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage that describes this process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

#Now you can access the data in the storage account from the retrieved Storage account keys
```
## <a name="5-access-keyvault-from-python"></a>5. Åtkomst Keyvault från Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a KeyVault client
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your KeyVault URL
"SQLPasswd",       # Name of your secret that already exists in the Key Vault
""              # The version of the secret. Empty string for latest
)
print("My secret value is {}".format(secret.value))
```

## <a name="6-azure-cli-access-from-vm"></a>6. Azure CLI-åtkomst från virtuell dator

```
# With a Managed Service Identity setup on the DSVM, users on the DSVM can execute Azure CLI to perform the authorized functions. Here are commands to access Key Vault from a Azure CLI without having to login to an Azure account. 
# Pre-requisites: MSI is already setup on the DSVM as indicated earlier and specific permission like accessing storage account keys, reading specific secrets and writing new secrets is provided to the MSI . 

# Authenticate to Azure CLI without requiring an Azure Account. 
az login --msi

# Retrieve a secret from Key Vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new Secret in Key Vault
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List Storage Account Access Keys
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
