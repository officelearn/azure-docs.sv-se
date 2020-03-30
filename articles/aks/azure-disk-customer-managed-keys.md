---
title: Använda en kundhanterad nyckel för att kryptera Azure-diskar i Azure Kubernetes Service (AKS)
description: Ta med egna nycklar (BYOK) för att kryptera AKS OS- och datadiskar.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596512"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ta med egna nycklar (BYOK) med Azure-diskar i Azure Kubernetes Service (AKS)

Azure Storage krypterar alla data i ett lagringskonto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange [kundhanterade nycklar][customer-managed-keys] som ska användas för kryptering i vila för både operativsystem och datadiskar för AKS-kluster.

> [!NOTE]
> BYOK Linux- och Windows-baserade AKS-kluster är tillgängliga i [Azure-regioner][supported-regions] som stöder kryptering på serversidan av Azure-hanterade diskar.

## <a name="before-you-begin"></a>Innan du börjar

* Den här artikeln förutsätter att du skapar ett *nytt AKS-kluster*.

* Du måste aktivera skydd för mjuk borttagning och rensning för *Azure Key Vault* när du använder Key Vault för att kryptera hanterade diskar.

* Du behöver Azure CLI version 2.0.79 eller senare och aks-preview 0.4.26-tillägget

> [!IMPORTANT]
> AKS-förhandsgranskningsfunktioner är självbetjäningsanmälan. Förhandsvisningar tillhandahålls "i nu och "som tillgängligt" och är undantagna från servicenivåavtalen och den begränsade garantin. AKS-förhandsvisningar omfattas delvis av kundsupport efter bästa ansträngning. Därför är dessa funktioner inte avsedda för produktionsanvändning. För ytterligare infromation, se följande supportartiklar:
>
> * [Aks-supportpolicyer](support-policies.md)
> * [Vanliga frågor och svar om Azure-support](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Installera det senaste förhandstillägget för AKS CLI

Om du vill använda kundhanterade nycklar behöver du *CLI-tilläggsversionen* 0.4.26 eller senare för att kunna använda kundhanterade nycklar. Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add][az-extension-add] och sök sedan efter tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Skapa en Azure Key Vault-instans

Använd en Azure Key Vault-instans för att lagra dina nycklar.  Du kan också använda Azure-portalen för att [konfigurera kundhanterade nycklar med Azure Key Vault][byok-azure-portal]

Skapa en ny *resursgrupp*och skapa sedan en ny *Key Vault-instans* och aktivera skydd för mjuk borttagning och rensning.  Se till att du använder samma region- och resursgruppsnamn för varje kommando.

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

Ersätt *myKeyVaultName* med namnet på nyckelvalvet.  Du behöver också en *nyckel* som lagras i Azure Key Vault för att slutföra följande steg.  Antingen lagra din befintliga nyckel i Key Vault du skapade på föregående steg, eller [generera en ny nyckel][key-vault-generate] och ersätta *myKeyName* nedan med namnet på din nyckel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Bevilja DiskEncryptionSet-åtkomst till nyckelvalvet

Använd diskencryptionSet- och resursgrupper som du skapade i föregående steg och ge diskencryptionSet-resursen åtkomst till Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Skapa ett nytt AKS-kluster och kryptera OS-disken

Skapa en **ny resursgrupp** och AKS-kluster och använd sedan nyckeln för att kryptera OS-disken. Kundhanterade nycklar stöds bara i Kubernetes-versioner som är större än 1.17. 

> [!IMPORTANT]
> Se till att du skapar en ny resoruce-grupp för AKS-klustret

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

När nya nodpooler läggs till i klustret som skapats ovan används den kundhanterade nyckeln som tillhandahålls under skapa för att kryptera OS-disken.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Kryptera aks-klusterdatadisken

Du kan också kryptera AKS-datadiskarna med dina egna nycklar.

> [!IMPORTANT]
> Se till att du har rätt AKS-autentiseringsuppgifter. Huvudmannen Tjänsten måste ha deltagaren åtkomst till resursgruppen där diskenkrypteringsuppsättningen distribueras. Annars får du ett felmeddelande som tyder på att tjänstens huvudnamn inte har behörighet.

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

Skapa en fil som **anropas byok-azure-disk.yaml** som innehåller följande information.  Ersätt myAzureSubscriptionId, myResourceGroup och myDiskEncrptionSetName med dina värden och tillämpa yaml.  Se till att använda resursgruppen där DiskEncryptionSet distribueras.  Om du använder Azure Cloud Shell kan den här filen skapas med vi eller nano som om du arbetar med ett virtuellt eller fysiskt system:

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
Kör sedan den här distributionen i AKS-klustret:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Begränsningar

* BYOK är för närvarande endast tillgängligt i GA och Preview i vissa [Azure-regioner][supported-regions]
* Diskkryptering för OS stöds med Kubernetes version 1.17 och senare   
* Endast tillgängligt i regioner där BYOK stöds
* Kryptering med kundhanterade nycklar är för närvarande endast för nya AKS-kluster, befintliga kluster kan inte uppgraderas
* AKS-kluster med skalningsuppsättningar för virtuella datorer krävs, inget stöd för tillgänglighetsuppsättningar för virtuella datorer


## <a name="next-steps"></a>Nästa steg

Granska [metodtips för AKS-klustersäkerhet][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
