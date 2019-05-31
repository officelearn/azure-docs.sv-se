---
title: Använda grafikprocessorer på Azure Kubernetes Service (AKS)
description: 'Lär dig hur du använder GPU: er för databehandling med höga prestanda eller grafikintensiva arbetsbelastningar på Azure Kubernetes Service (AKS)'
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/16/2019
ms.author: zarhoads
ms.openlocfilehash: 00d15b960afd2c3b39908fb359cf9898b5257abc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239364"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Använd GPU: er för beräkningsintensiva arbetsbelastningar på Azure Kubernetes Service (AKS)

Grafisk bearbetningsenheter (GPU) används ofta för beräkningsintensiva arbetsbelastningar som grafik och visualisering arbetsbelastningar. AKS stöder skapandet av GPU-aktiverade nodpooler att köra dessa beräkningsintensiva arbetsbelastningar i Kubernetes. Mer information om tillgängliga GPU-aktiverade virtuella datorer finns i [GPU-optimerad VM-storlekar i Azure][gpu-skus]. För AKS-noder, rekommenderar vi en minsta storlek på *Standard_NC6*.

> [!NOTE]
> GPU-aktiverade virtuella datorer innehåller specialiserad maskinvara som omfattas av högre priser och regional tillgänglighet. Mer information finns i den [priser] [ azure-pricing] verktyget och [regiontillgänglighet][azure-availability].

För närvarande är med GPU-aktiverade nodpooler endast tillgänglig för nodpooler för Linux.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster med noder som har stöd för GPU: er. AKS-klustret måste köra Kubernetes 1.10 eller senare. Om du behöver ett AKS-kluster som uppfyller dessa krav finns i den första delen av den här artikeln att [skapa ett AKS-kluster](#create-an-aks-cluster).

Du också ha Azure CLI version 2.0.64 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Om du behöver ett AKS-kluster som uppfyller minimikraven (GPU-aktiverade noden och Kubernetes version 1.10 eller senare), utför följande steg. Om du redan har ett AKS-kluster som uppfyller dessa krav [gå vidare till nästa avsnitt](#confirm-that-gpus-are-schedulable).

Skapa först en resursgrupp för klustret med den [az gruppen skapa] [ az-group-create] kommando. I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den *eastus* region:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nu skapa ett AKS-kluster med den [az aks skapa] [ az-aks-create] kommando. I följande exempel skapas ett kluster med en enda nod med storleken `Standard_NC6`:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Hämta autentiseringsuppgifterna för AKS-kluster med den [aaz aks get-credentials] [ az-aks-get-credentials] kommando:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-drivers"></a>Installera nVidia-drivrutiner

Innan GPU: er i noderna kan användas, måste du distribuera en DaemonSet för NVIDIA enheten plugin-programmet. Den här DaemonSet körs en pod på varje nod för att tillhandahålla nödvändiga drivrutiner för de GPU: er.

Börja med att skapa ett namnområde med hjälp av den [kubectl skapa namnområde] [ kubectl-create] kommandot, till exempel *gpu-resurser*:

```console
kubectl create namespace gpu-resources
```

Skapa en fil med namnet *nvidia-enhet-plugin-programmet-ds.yaml* och klistra in följande YAML-manifestet. Den här manifestet tillhandahålls som en del av den [NVIDIA enheten plugin-program för Kubernetes-projektet][nvidia-github].

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: kube-system
spec:
  updateStrategy:
    type: RollingUpdate
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
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: nvidia/k8s-device-plugin:1.0.0-beta
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
```

Nu använda den [kubectl gäller] [ kubectl-apply] kommando för att skapa ett DaemonSet och bekräfta nVidia enheten plugin-programmet har skapats, som visas i följande Exempelutdata:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Bekräfta att GPU: er är avtalad

Med AKS-klustret skapas, bekräfta att GPU: er är avtalad i Kubernetes. Först, listar noderna i ditt kluster med hjälp av den [kubectl få noder] [ kubectl-get] kommando:

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Nu använda den [kubectl beskriver noden] [ kubectl-describe] kommando för att bekräfta att de GPU: er är avtalad. Under den *kapacitet* avsnittet GPU bör lista som `nvidia.com/gpu:  1`.

Följande komprimerade exempel som visar att en GPU är tillgängliga i noden med namnet *aks-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

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

Övervaka förloppet för jobbet med hjälp av den [kubectl hämta jobb] [ kubectl-get] med den `--watch` argumentet. Det kan ta några minuter att första pull avbildningen och bearbeta datauppsättningen. När den *SLUTFÖRANDEN* kolumnen visar *1/1*, jobbet har slutförts. Avsluta den `kubetctl --watch` med *Ctrl-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Om du vill titta på resultatet av arbetsbelastningen i GPU-aktiverade, först hämta namnet på en pod med den [kubectl hämta poddar] [ kubectl-get] kommando:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Nu använda den [kubectl loggar] [ kubectl-logs] kommando för att visa pod-loggar. Följande exempel pod loggar bekräfta att lämpliga GPU-enheten har hittats `Tesla K80`. Ange din egen pod-namn:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de associerade Kubernetes-objekten som skapats i den här artikeln med den [kubectl ta bort jobbet] [ kubectl delete] -kommandot enligt följande:

```console
kubectl delete jobs samples-tf-mnist-demo
```

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
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
