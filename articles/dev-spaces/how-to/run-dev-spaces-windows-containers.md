---
title: Använd Azure dev Spaces för att interagera med Windows-behållare
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/25/2019
ms.topic: conceptual
description: Lär dig hur du kör Azure dev Spaces i ett befintligt kluster med Windows-behållare
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes-tjänsten, behållare, Windows-behållare
ms.openlocfilehash: 2110636b331f0cf4e74c77f41726ead5bf80a64f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501525"
---
# <a name="use-azure-dev-spaces-to-interact-with-windows-containers"></a>Använd Azure dev Spaces för att interagera med Windows-behållare

Du kan aktivera Azure dev Spaces på både nya och befintliga Kubernetes-namnområden. Azure dev Spaces kommer att köra och instrumentera tjänster som körs på Linux-behållare. Dessa tjänster kan också samverka med program som körs på Windows-behållare i samma namnrymd. Den här artikeln visar hur du använder dev Spaces för att köra tjänster i ett namn område med befintliga Windows-behållare.

## <a name="set-up-your-cluster"></a>Konfigurera ditt kluster

Den här artikeln förutsätter att du redan har ett kluster med både Linux-och Windows-adresspooler. Om du behöver skapa ett kluster med Linux-och Windows-adresspooler kan du följa instruktionerna [här][windows-container-cli].

Anslut till klustret med [kubectl][kubectl], Kubernetes kommando rads klient. För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempel på utdata visar ett kluster med både en Windows-och Linux-nod. Kontrol lera att statusen är *klar* för varje nod innan du fortsätter.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

Använd en- [smak][using-taints] på dina Windows-noder. Bismaken på dina Windows-noder förhindrar att dev-rymder schemalägger att köra på dina Windows-noder. Följande kommando exempel använder en-utsmak till Windows-noden *aksnpwin987654* från föregående exempel.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

## <a name="run-your-windows-service"></a>Kör din Windows-tjänst

Kör Windows-tjänsten på ditt AKS-kluster och kontrol lera att den är i ett *Kör* tillstånd. I den här artikeln används ett [exempel program][sample-application] för att demonstrera en Windows-och Linux-tjänst som körs i klustret.

Klona exempel programmet från GitHub och navigera till `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` katalogen:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Exempel programmet använder [Helm][helm-installed] för att köra Windows-tjänsten på klustret. Installera Helm på klustret och ge det rätt behörigheter:

```console
helm init --wait
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
``` 

Navigera till `charts` katalogen och kör Windows-tjänsten:

```console
cd charts/
helm install . --namespace dev
```

Kommandot ovan använder Helm för att köra Windows-tjänsten i *dev* -namnområdet. Om du inte har ett namn område med namnet *dev*kommer det att skapas.

`kubectl get pods` Använd kommandot för att kontrol lera att Windows-tjänsten körs i klustret. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Aktivera Azure dev Spaces

Aktivera dev-utrymmen i samma namnrymd som du använde för att köra Windows-tjänsten. Följande kommando aktiverar dev-rymder i namn området för *utveckling* :

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Uppdatera din Windows-tjänst för dev Spaces

När du aktiverar dev Spaces i ett befintligt namn område med behållare som redan körs, kommer som standard att försöka utföra och instrumentera nya behållare som körs i namn området. Dev-utrymmen kommer också att prova och instrumentera nya behållare som skapats för tjänsten som redan körs i namn området. Om du vill förhindra att dev-Spaces instrumenterar en behållare som körs i namn området lägger du till huvudet `deployment.yaml` *no-proxy* i.

Lägg `azds.io/no-proxy: "true"` till`existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` i filen:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Använd `helm list` för att visa en lista över distributionen för din Windows-tjänst:

```cmd
$ helm list
NAME            REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
gilded-jackal   1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

I ovanstående exempel är namnet på din distribution *Gilded-Jackal*. Uppdatera din Windows-tjänst med den nya konfigurationen `helm upgrade`med hjälp av:

```cmd
$ helm upgrade gilded-jackal . --namespace dev
Release "gilded-jackal" has been upgraded.
```

Eftersom du `deployment.yaml`har uppdaterat kommer inte dev Spaces att försöka utföra och instrumentera din tjänst.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Köra Linux-programmet med Azure dev Spaces

Navigera till `webfrontend` katalogen och `azds prep` Använd-och `azds up` -kommandona för att köra Linux-programmet i klustret.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

`azds prep --public` Kommandot genererar Helm-diagrammet och Dockerfiles för ditt program. `azds up` Kommandot kör tjänsten i namn området.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Du kan se den tjänst som körs genom att öppna den offentliga URL: en, som visas i utdata från kommandot azds up. I det här exemplet är `http://dev.webfrontend.abcdef0123.eus.azds.io/`den offentliga URL: en. Navigera till tjänsten i en webbläsare och klicka på överst  . Kontrol lera att du ser ett meddelande från *mywebapi* -tjänsten som innehåller den version av Windows som behållaren använder.

![Exempel program som visar Windows-version från mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[team-development-qs]: ../quickstart-team-development.md

[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
