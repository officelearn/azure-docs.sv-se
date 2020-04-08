---
title: Skapa Azure-filresurs manuellt
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar en volym manuellt med Azure-filer för användning med flera samtidiga poddar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 412b7158ea366eefb1c3e9c1d2586d54c316aa6c
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803457"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Skapa och använda en volym med Azure Files share manuellt i Azure Kubernetes Service (AKS)

Behållarbaserade program behöver ofta komma åt och bevara data i en extern datavolym. Om flera poddar behöver samtidig åtkomst till samma lagringsvolym kan du använda Azure Files för att ansluta med [SMB-protokollet (Server Message Block).][smb-overview] Den här artikeln visar hur du skapar en Azure-filresurs manuellt och bifogar den till en pod i AKS.

Mer information om Kubernetes volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda Azure-filer som en Kubernetes-volym måste du skapa ett Azure Storage-konto och filresursen. Följande kommandon skapar en resursgrupp med namnet *myAKSShare*, ett lagringskonto och en filresurs med namnet *aksshare:*

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
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Anteckna namnet och nyckeln för lagringskontot som visas i slutet av skriptutdata. Dessa värden behövs när du skapar Kubernetes-volymen i något av följande steg.

## <a name="create-a-kubernetes-secret"></a>Skapa en Kubernetes-hemlighet

Kubernetes behöver autentiseringsuppgifter för att komma åt filresursen som skapades i föregående steg. Dessa autentiseringsuppgifter lagras i en [Kubernetes-hemlighet][kubernetes-secret], som refereras när du skapar en Kubernetes pod.

Använd `kubectl create secret` kommandot för att skapa hemligheten. I följande exempel skapas en delad med namnet *azure-secret* och fyller *i azurestorageaccountname* och *azurestorageaccountkey* från föregående steg. Om du vill använda ett befintligt Azure-lagringskonto anger du kontonamnet och nyckeln.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Montera filresursen som en volym

Om du vill montera Azure Files-resursen i din pod konfigurerar `azure-files-pod.yaml` du volymen i behållarspecifikationen. Om du har ändrat namnet på filresursen eller det hemliga namnet uppdaterar du *shareName* och *secretName*. Om du vill `mountPath`kan du uppdatera den sökväg där filresursen är monterad i podden. För Windows Server-behållare (för närvarande i förhandsversionen i AKS) anger du en *mountPath* med hjälp av Windows-sökvägskonventionen, till exempel *"D:".*

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

Använd `kubectl` kommandot för att skapa podden.

```console
kubectl apply -f azure-files-pod.yaml
```

Du har nu en pod som körs med en Azure Files-resurs monterad på */mnt/azure*. Du kan `kubectl describe pod mypod` använda för att kontrollera att resursen har monterats. Följande kondenserade exempelutgång visar volymen som är monterad i behållaren:

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

Standardvärdet för *fileMode* och *dirMode* är *0755* för Kubernetes version 1.9.1 och senare. Om du använder ett kluster med Kubernetes version 1.8.5 eller mer och statiskt skapar det beständiga volymobjektet måste monteringsalternativ anges på *PersistentVolume-objektet.* Följande exempel anger *0777:*

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Om du använder ett kluster av version 1.8.0 - 1.8.4 kan en säkerhetskontext anges med värdet *runAsUser* inställt på *0*. Mer information om säkerhetskontexten för pod finns i [Konfigurera en säkerhetskontext][kubernetes-security-context].

Om du vill uppdatera monteringsalternativen skapar du en *azurefile-mount-options-pv.yaml-fil* med en *PersistentVolume*. Ett exempel:

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Skapa en *azurefile-mount-options-pvc.yaml-fil* med en *PersistentVolumeClaim* som använder *PersistentVolume*. Ett exempel:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Använd `kubectl` kommandona för att skapa *PersistentVolume* och *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Kontrollera att *PersistentVolumeClaim* skapas och är bunden till *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Uppdatera din behållarspecifikation för att referera till din *PersistentVolumeClaim* och uppdatera din pod. Ett exempel:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Nästa steg

Information om associerade metodtips finns [i Metodtips för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

Mer information om AKS-kluster interagerar med Azure-filer finns i [kubernetes plugin för Azure Files][kubernetes-files].

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
