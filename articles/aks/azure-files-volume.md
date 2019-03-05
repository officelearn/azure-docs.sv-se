---
title: Skapa en statisk volym för flera poddar i Azure Kubernetes Service (AKS)
description: Lär dig hur du manuellt skapa en volym med Azure Files för användning med flera samtidiga poddar i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: f8558529df24c0aaede0c58744e17829ec0b5669
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337540"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Manuellt skapa och använda en volym med Azure Files-resurs i Azure Kubernetes Service (AKS)

Behållarbaserade program behöver ofta åtkomst till och bevara data i en extern datavolym. Om flera poddar behöver samtidig åtkomst till samma lagringsvolymen kan du använda Azure Files för att ansluta med den [Server Message Block (SMB) protokollet][smb-overview]. Den här artikeln visar hur du manuellt skapa en Azure Files-resurs och koppla den till en pod i AKS.

Mer information om Kubernetes volymer finns i [lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.59 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda Azure Files som en Kubernetes-volym, måste du skapa ett Azure Storage-konto och filresursen. Följande kommandon för skapar en resursgrupp med namnet *myAKSShare*, ett lagringskonto och en resurs för filer som heter *aksshare*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Anteckna lagringskontonamn och nyckel visas i slutet av utdata från skriptet. Dessa värden krävs när du skapar Kubernetes-volym i något av följande steg.

## <a name="create-a-kubernetes-secret"></a>Skapa ett Kubernetes-hemlighet

Kubernetes behöver autentiseringsuppgifter för att få åtkomst till filresursen som skapades i föregående steg. Dessa autentiseringsuppgifter lagras i en [Kubernetes-hemlighet][kubernetes-secret], som refereras till när du skapar en Kubernetes-pod.

Använd den `kubectl create secret` kommando för att skapa hemligheten. I följande exempel skapas en delad med namnet *azure-secret* och fyller den *azurestorageaccountname* och *azurestorageaccountkey* från föregående steg. Ange kontonamnet och nyckeln för att använda ett befintligt Azure storage-konto.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Montera filresursen som en volym

Om du vill montera Azure Files-resurs i din pod, konfigurerar du volymen i container-specifikationen. Skapa en ny fil med namnet `azure-files-pod.yaml` med följande innehåll. Om du har ändrat namnet på resursen filer eller hemligt namn måste du uppdatera den *shareName* och *secretName*. Om du vill kan du uppdatera den `mountPath`, vilket är den sökväg där filerna delar är monterad i en pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Använd den `kubectl` kommando för att skapa en pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Nu har du en aktiv pod med en Azure Files-resurs som är monterad på */mnt/azure*. Du kan använda `kubectl describe pod mypod` att verifiera resursen är monterad har. Följande komprimerade exempel på utdata visar volym monteras i behållaren:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>Monteringsalternativ

Standard *fileMode* och *dirMode* värden skiljer sig åt mellan Kubernetes-versioner som beskrivs i följande tabell.

| version | värde |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 eller senare | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 eller senare | 0755 |

Om du använder ett kluster av version 1.8.5 återställning eller större och statiskt skapar objektet permanent volym, monteringsalternativ måste anges på den *PersistentVolume* objekt.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
```

Om du använder ett kluster av version 1.8.0 - 1.8.4, en säkerhetskontext kan bara anges med den *användare* värdet *0*. Läs mer på Pod säkerhetskontext [konfigurera en säkerhetskontext][kubernetes-security-context].

## <a name="next-steps"></a>Nästa steg

Associerade metodtips finns [bästa praxis för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

Mer information om AKS-kluster interagerar med Azure Files, finns i den [Kubernetes-plugin-programmet för Azure Files][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
