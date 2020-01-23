---
title: Använd en kundhanterad nyckel för att kryptera Azure-diskar i Azure Kubernetes service (AKS)
description: Ta med dina egna nycklar (BYOK) för att kryptera AKS OS-och data diskar.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 23a5dbf2333ca86c2d51d54bf983b00a71936eec
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547956"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ta med dina egna nycklar (BYOK) med Azure-diskar i Azure Kubernetes service (AKS)

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha mer kontroll över krypterings nycklar kan du ange [Kundhanterade nycklar][customer-managed-keys] som ska användas för kryptering av både operativ system och data diskar för dina AKS-kluster.

> [!NOTE]
> BYOK Linux-och Windows-baserade AKS-kluster är tillgängliga i [Azure-regioner][supported-regions] som stöder kryptering på Server sidan av Azure Managed disks.

## <a name="before-you-begin"></a>Innan du börjar

* Den här artikeln förutsätter att du skapar ett *nytt AKS-kluster*.

* Du måste aktivera mjuk borttagning och tömning av skydd för *Azure Key Vault* när du använder Key Vault för att kryptera hanterade diskar.

* Du behöver Azure CLI-version 2.0.79 eller senare och 0.4.26-tillägget AKS-Preview

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS](support-policies.md)
> * [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Installera det senaste AKS CLI Preview-tillägget

Om du vill använda Kundhanterade nycklar behöver du *AKS-Preview CLI-* tillägg version 0.4.26 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Skapa en Azure Key Vault-instans

Använd en Azure Key Vault-instans för att lagra dina nycklar.  Du kan också använda Azure Portal för att [Konfigurera Kundhanterade nycklar med Azure Key Vault][byok-azure-portal]

Skapa en ny *resurs grupp*och skapa sedan en ny *Key Vault* -instans och aktivera mjuk borttagning och tömning av skydd.  Se till att du använder samma region och resurs grupp namn för varje kommando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Skapa en instans av en DiskEncryptionSet

Ersätt *myKeyVaultName* med namnet på ditt nyckel valv.  Du behöver också en *nyckel* som lagras i Azure Key Vault för att utföra följande steg.  Lagra antingen den befintliga nyckeln i Key Vault du skapade i föregående steg, eller [Generera en ny nyckel][key-vault-generate] och Ersätt *myKeyName* nedan med namnet på din nyckel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Bevilja DiskEncryptionSet åtkomst till nyckel valvet

Använd de DiskEncryptionSet och resurs grupper som du skapade i föregående steg och ge DiskEncryptionSet resurs åtkomst till Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Skapa ett nytt AKS-kluster och kryptera OS-disken

Skapa en **Ny resurs grupp** och ett AKS-kluster och Använd sedan din nyckel för att kryptera operativ system disken. Kundhanterade nycklar stöds bara i Kubernetes-versioner som är större än 1,17. 

> [!IMPORTANT]
> Se till att du skapar en ny resurs-grupp för ditt AKS-kluster

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

När nya noder läggs till i klustret som skapats ovan används den Kundhanterade nyckeln som angavs under skapandet för att kryptera operativ system disken.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Kryptera din AKS-kluster data disk

Du kan också kryptera AKS data diskar med dina egna nycklar.

> [!IMPORTANT]
> Se till att du har rätt AKS-autentiseringsuppgifter. Tjänstens huvud namn måste ha deltagar åtkomst till den resurs grupp där diskencryptionset har distribuerats. Annars får du ett fel meddelande som anger att tjänstens huvud namn inte har behörighet.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Skapa en fil med namnet **BYOK-Azure-disk. yaml** som innehåller följande information.  Ersätt myAzureSubscriptionId, myResourceGroup och myDiskEncrptionSetName med värdena och Använd yaml.  Se till att använda resurs gruppen där din DiskEncryptionSet har distribuerats.  Om du använder Azure Cloud Shell kan filen skapas med hjälp av vi eller nano som om du arbetar med ett virtuellt eller fysiskt system:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Kör sedan den här distributionen i ditt AKS-kluster:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Begränsningar

* BYOK är för närvarande endast tillgängligt i GA och för hands versionen i vissa [Azure-regioner][supported-regions]
* Disk kryptering för operativ system som stöds med Kubernetes version 1,17 och senare   
* Endast tillgängligt i regioner där BYOK stöds
* Kryptering med Kundhanterade nycklar för närvarande endast för nya AKS-kluster, går det inte att uppgradera befintliga kluster
* AKS-kluster som använder Virtual Machine Scale Sets krävs, inget stöd för tillgänglighets uppsättningar för virtuella datorer


## <a name="next-steps"></a>Nästa steg

Granska [metod tips för AKS kluster säkerhet][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
