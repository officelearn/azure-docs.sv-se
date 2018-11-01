---
title: Använda grafikprocessorer på Azure Kubernetes Service (AKS)
description: 'Lär dig hur du använder GPU: er för databehandling med höga prestanda eller grafikintensiva arbetsbelastningar på Azure Kubernetes Service (AKS)'
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/25/2018
ms.author: laevenso
ms.openlocfilehash: 683abd9bad93bff51bea84c8081d2b8f9d300cd4
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419258"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Använd GPU: er för beräkningsintensiva arbetsbelastningar på Azure Kubernetes Service (AKS)

Grafisk bearbetningsenheter (GPU) används ofta för beräkningsintensiva arbetsbelastningar som grafik och visualisering arbetsbelastningar. AKS stöder skapandet av GPU-aktiverade nodpooler att köra dessa beräkningsintensiva arbetsbelastningar i Kubernetes. Mer information om tillgängliga GPU-aktiverade virtuella datorer finns i [GPU-optimerad VM-storlekar i Azure][gpu-skus]. För AKS-noder, rekommenderar vi en minsta storlek på *Standard_NC6*.

> [!NOTE]
> GPU-aktiverade virtuella datorer innehåller specialiserad maskinvara som omfattas av högre priser och regional tillgänglighet. Mer information finns i den [priser] [ azure-pricing] verktyget och [regiontillgänglighet][azure-availability].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster med noder som har stöd för GPU: er. AKS-klustret måste köra Kubernetes 1.10 eller senare. Om du behöver ett AKS-kluster som uppfyller dessa krav finns i den första delen av den här artikeln att [skapa ett AKS-kluster](#create-an-aks-cluster).

Du också ha Azure CLI version 2.0.49 eller senare installerat och konfigurerat. Kör `az --version` att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Om du behöver ett AKS-kluster som uppfyller minimikraven (GPU-aktiverade noden och Kubernetes version 1.10 eller senare), utför följande steg. Om du redan har ett AKS-kluster som uppfyller dessa krav kan du gå vidare till nästa avsnitt.

Skapa först en resursgrupp för klustret med den [az gruppen skapa] [ az-group-create] kommando. I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den *eastus* region:

```azurecli
az group create --name myResourceGroup --location eastus
```

Nu skapa ett AKS-kluster med den [az aks skapa] [ az-aks-create] kommando. I följande exempel skapas ett kluster med en enda nod med storleken `Standard_NC6`, och kör Kubernetes version 1.10.8:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.10.8
```

Hämta autentiseringsuppgifterna för AKS-kluster med den [aaz aks get-credentials] [ az-aks-get-credentials] kommando:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Bekräfta att GPU: er är avtalad

Med AKS-klustret skapas, bekräfta att GPU: er är avtalad i Kubernetes. Först, listar noderna i ditt kluster med hjälp av den [kubectl få noder] [ kubectl-get] kommando:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-18821093-0   Ready    agent   6m    v1.10.8
```

Nu använda den [kubectl beskriver noden] [ kubectl-describe] kommando för att bekräfta att de GPU: er är avtalad. Under den *kapacitet* avsnittet GPU bör lista som `nvidia.com/gpu:  1`. Om du inte ser de GPU: er finns i den [felsöka GPU tillgänglighet](#troubleshoot-gpu-availability) avsnittet.

Följande komprimerade exempel som visar att en GPU är tillgängliga i noden med namnet *aks-nodepool1-18821093-0*:

```
$ kubectl describe node aks-nodepool1-18821093-0

Name:               aks-nodepool1-18821093-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713824Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5940m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             53417120Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 688e083d19554d4a9563bd138f4ca98b
 System UUID:                08162568-B987-A84D-8865-98D6EFC64B32
 Boot ID:                    7b440249-8a96-42eb-950f-08c9a3c530b7
 Kernel Version:             4.15.0-1023-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.10.8
 Kube-Proxy Version:         v1.10.8
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myGPUCluster_myGPUCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-18821093-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                    CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                  ----                                    ------------  ----------  ---------------  -------------
  gpu-resources              nvidia-device-plugin-9cfcf              0 (0%)        0 (0%)      0 (0%)           0 (0%)

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Köra en GPU-aktiverade arbetsbelastning

Schemalägga en GPU-aktiverade arbetsbelastning med lämpliga resursbegäran om du vill se GPU fungerar i praktiken. I det här exemplet ska vi köra en [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) jobb mot den [MNIST datauppsättning](http://yann.lecun.com/exdb/mnist/).

Skapa en fil med namnet *exempel-tf-mnist-demo.yaml* och klistra in följande YAML-manifestet. Följande jobbet Manifestet innehåller en resursgräns av `nvidia.com/gpu: 1`:

> [!NOTE]
> Om du får ett versionsmatchningsfel vid anrop till drivrutiner, t.ex., CUDA drivrutinsversion inte räcker för CUDA körningsversion, granska efterlevnadsdiagram för nVidia-drivrutin matris- [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Använd den [kubectl gäller] [ kubectl-apply] kommando för att köra jobbet. Det här kommandot Parsar manifestfilen och skapar de definierade Kubernetes-objekten:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Visa status och utdata av arbetsbelastningen i GPU-aktiverade

Övervaka förloppet för jobbet med hjälp av den [kubectl hämta jobb] [ kubectl-get] med den `--watch` argumentet. Det kan ta några minuter att första pull avbildningen och bearbeta datauppsättningen. När den *SLUTFÖRANDEN* kolumnen visar *1/1*, jobbet har slutförts:

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Om du vill titta på resultatet av arbetsbelastningen i GPU-aktiverade, först hämta namnet på poddarna med den [kubectl hämta poddar] [ kubectl-get] kommando:

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Nu använda den [kubectl loggar] [ kubectl-logs] kommando för att visa pod-loggar. Följande exempel pod loggar bekräfta att lämpliga GPU-enheten har hittats `Tesla K80`. Ange din egen pod-namn:

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de associerade Kubernetes-objekten som skapats i den här artikeln med den [kubectl ta bort jobbet] [ kubectl delete] -kommandot enligt följande:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>Felsöka GPU-tillgänglighet

Om du inte ser GPU: er som kan finnas på noderna, kan du behöva distribuera en DaemonSet för plugin-programmet för nVidia-enhet. Den här DaemonSet körs en pod på varje nod för att tillhandahålla nödvändiga drivrutiner för de GPU: er.

Börja med att skapa ett namnområde med hjälp av den [kubectl skapa namnområde] [ kubectl-create] kommandot, till exempel *gpu-resurser*:

```console
kubectl create namespace gpu-resources
```

Skapa en fil med namnet *nvidia-enhet-plugin-programmet-ds.yaml* och klistra in följande YAML-manifestet. Uppdatera den `image: nvidia/k8s-device-plugin:1.10` halvvägs ner manifestet så att den matchar ditt Kubernetes-version. Till exempel om AKS-klustret kör Kubernetes version 1.11, uppdatera taggen att `image: nvidia/k8s-device-plugin:1.11`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      containers:
      - image: nvidia/k8s-device-plugin:1.10 # Update this tag to match your Kubernetes version
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Nu använda den [kubectl gäller] [ kubectl-apply] kommando för att skapa ett DaemonSet:

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Kör den [kubectl beskriver noden] [ kubectl-describe] kommando igen för att kontrollera att GPU nu är tillgängliga på noden.

## <a name="next-steps"></a>Nästa steg

Om du vill köra Apache Spark-jobb, se [kör Apache Spark-jobb på AKS][aks-spark].

Läs mer om att köra machine learning (ML) arbetsbelastningar på Kubernetes [Kubeflow Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
