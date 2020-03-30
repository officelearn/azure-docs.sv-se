---
title: Interagera med Windows-containrar
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Lär dig hur du kör Azure Dev Spaces i ett befintligt kluster med Windows-behållare
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Windows-behållare
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240488"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interagera med Windows-behållare med Azure Dev Spaces

Du kan aktivera Azure Dev Spaces på både nya och befintliga Kubernetes-namnområden. Azure Dev Spaces körs och instrumenttjänster som körs på Linux-behållare. Dessa tjänster kan också interagera med program som körs på Windows-behållare i samma namnområde. Den här artikeln visar hur du använder Dev Spaces för att köra tjänster i ett namnområde med befintliga Windows-behållare. För närvarande kan du inte felsöka eller ansluta till Windows-behållare med Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Konfigurera klustret

Den här artikeln förutsätter att du redan har ett kluster med både Linux- och Windows-nodpooler. Om du behöver skapa ett kluster med Linux- och Windows-nodpooler kan du följa instruktionerna [här][windows-container-cli].

Anslut till klustret med [kubectl][kubectl], Kommandoradsklienten Kubernetes. För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempelutdata visar ett kluster med både en Windows- och Linux-nod. Kontrollera att statusen är *Klar* för varje nod innan du fortsätter.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Använd en [behäftning på][using-taints] dina Windows-noder. Den besudlade på dina Windows-noder hindrar Dev Spaces från att schemalägga Linux-behållare för att köras på dina Windows-noder. Följande kommandoexempel använder en befläcka på windows-noden *aksnpwin987654* från föregående exempel.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> När du använder en behäftning på en nod måste du konfigurera en matchande tolerans i tjänstens distributionsmall för att kunna köra tjänsten på den noden. Exempelprogrammet är redan konfigurerat med en matchande tolerans till den [befläckade][sample-application-toleration-example] du har konfigurerat i föregående kommando.

## <a name="run-your-windows-service"></a>Kör din Windows-tjänst

Kör din Windows-tjänst i AKS-klustret och kontrollera att den är i *körläge.* Den här artikeln använder ett [exempelprogram][sample-application] för att demonstrera en Windows- och Linux-tjänst som körs i klustret.

Klona exempelprogrammet från GitHub `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` och navigera till katalogen:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Exempelprogrammet använder [Helm 3][helm-installed] för att köra Windows-tjänsten i klustret. Navigera till `charts` katalogen och använd Helm kör Windows-tjänsten:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Kommandot Ovan använder Helm för att köra din Windows-tjänst i *dev-namnområdet.* Om du inte har ett namnområde med namnet *dev*skapas det.

Använd `kubectl get pods` kommandot för att kontrollera att Windows-tjänsten körs i klustret. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Aktivera Azure Dev Spaces

Aktivera Dev Spaces i samma namnområde som du använde för att köra din Windows-tjänst. Följande kommando aktiverar Dev Spaces i *dev-namnområdet:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Uppdatera din Windows-tjänst för Dev Spaces

När du aktiverar Dev Spaces på ett befintligt namnområde med behållare som redan körs försöker Dev Spaces som standard instrumentera alla nya behållare som körs i namnområdet. Dev Spaces kommer också att försöka instrumentera alla nya behållare som skapats för tjänsten som redan körs i namnområdet. Om du vill förhindra att Dev Spaces instrumenterar en behållare som `deployment.yaml`körs i namnområdet lägger du till *no-proxy-huvudet* i .

Lägg `azds.io/no-proxy: "true"` till `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` i filen:

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

Används `helm list` för att lista distributionen för din Windows-tjänst:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

I exemplet ovan är namnet på distributionen *windows-service*. Uppdatera Din Windows-tjänst med `helm upgrade`den nya konfigurationen med:

```cmd
helm upgrade windows-service . --namespace dev
```

Sedan du `deployment.yaml`har uppdaterat din kommer Dev Spaces inte att försöka instrumentera din tjänst.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Kör ditt Linux-program med Azure Dev Spaces

Navigera till `webfrontend` katalogen `azds prep` och `azds up` använd kommandona och för att köra ditt Linux-program i klustret.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Kommandot `azds prep --enable-ingress` genererar Helm-diagrammet och Dockerfiles för ditt program.

> [!TIP]
> [Dockerfile- och Helm-diagrammet](../how-dev-spaces-works-prep.md#prepare-your-code) för projektet används av Azure Dev Spaces för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet byggs och kördes.

Kommandot `azds up` kör tjänsten i namnområdet.

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

Du kan se tjänsten som körs genom att öppna den offentliga WEBBADRESSEN, som visas i utdata från azds up-kommandot. I det här exemplet `http://dev.webfrontend.abcdef0123.eus.azds.io/`är den offentliga WEBBADRESSEN . Navigera till tjänsten i en webbläsare och klicka på *Om* högst upp. Kontrollera att du ser ett meddelande från *mywebapi-tjänsten* som innehåller den version av Windows som behållaren använder.

![Exempelapp som visar Windows-versionen från mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Teamutveckling i Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
