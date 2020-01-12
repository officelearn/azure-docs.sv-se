---
title: Använd en kundhanterad nyckel för att kryptera Azure-diskar i Azure Kubernetes service (AKS)
description: Ta med dina egna nycklar (BYOK) för att kryptera AKS OS-och data diskar.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/09/2020
ms.author: mlearned
ms.openlocfilehash: 3efb7a6005d862b15beec0f979b67a701a26ba74
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903969"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ta med dina egna nycklar (BYOK) med Azure-diskar i Azure Kubernetes service (AKS)

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha mer kontroll över krypterings nycklar kan du ange [Kundhanterade nycklar][customer-managed-keys] som ska användas för kryptering av både operativ system och data diskar för dina AKS-kluster.

> [!NOTE]
> Linux-och Windows-baserade AKS-kluster stöds båda.

## <a name="before-you-begin"></a>Innan du börjar

* Den här artikeln förutsätter att du skapar ett *nytt AKS-kluster*.  Du måste också använda eller skapa en instans av Azure Key Vault för att lagra dina krypterings nycklar.

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

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>Skapa en Azure Key Vault-instans för att lagra dina nycklar

Du kan också använda Azure Portal för att [Konfigurera Kundhanterade nycklar med Azure Key Vault][byok-azure-portal]

Skapa en ny *resurs grupp*och skapa sedan en ny *Key Vault* -instans och aktivera mjuk borttagning och tömning av skydd.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup-l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Skapa en instans av en DiskEncryptionSet

Du behöver en *nyckel* som är lagrad i Azure Key Vault för att utföra följande steg.  Lagra din befintliga nyckel i Key Vault du skapade eller [Generera en nyckel][key-vault-generate]
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup--source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>Bevilja DiskEncryptionSet resurs åtkomst till nyckel valvet

Använd de DiskEncryptionSet och resurs grupper som du skapade i föregående steg och ge DiskEncryptionSet resurs åtkomst till Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup--query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup--object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>Skapa ett nytt AKS-kluster och kryptera OS-disken med en kund-hanterade-nyckel

Skapa en ny resurs grupp och ett AKS-kluster och Använd sedan din nyckel för att kryptera operativ system disken.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId
```

## <a name="add-a-node-pool-to-an-existing-aks-cluster-and-encrypt-the-os-disk-with-a-customer-managed-key"></a>Lägg till en Node-pool i ett befintligt AKS-kluster och kryptera OS-disken med en kundhanterad nyckel

Nya nodepools använder inte krypterade diskar som standard.  Du kan lägga till en ny Node-pool i ett befintligt kluster och kryptera operativ system disken med din egen nyckel genom att använda följande kommando.

```azurecli-interactive
# Add a nodepool to an existing cluster with BYOK encryption
nodepool add –-cluster-name myAKSCluster -n myNodePoolName -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId  
```

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>Kryptera din AKS-kluster data disk med en kundhanterad nyckel

Du kan också kryptera AKS data diskar med dina egna nycklar.  Ersätt myResourceGroup och myDiskEncryptionSetName med dina verkliga värden och Använd yaml.

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Distribuera exempel bilden från ACR till AKS

Se till att du har rätt AKS-autentiseringsuppgifter

Skapa en fil med namnet **BYOK-Azure-disk. yaml** som innehåller följande information.  Ersätt myResourceGroup och myDiskEncrptionSetName med dina värden.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Kör sedan den här distributionen i ditt AKS-kluster:
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Begränsningar

* Disk kryptering för operativ system som stöds med Kubernetes version 1,17 och senare   
* Endast tillgängligt i regioner där BYOK stöds
* Det här är för närvarande endast för nya AKS-kluster. det går inte att uppgradera befintliga kluster
* AKS-kluster som använder Virtual Machine Scale Sets krävs, inget stöd för tillgänglighets uppsättningar för virtuella datorer


## <a name="next-steps"></a>Nästa steg

Granska [metod tips för AKS kluster säkerhet][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
