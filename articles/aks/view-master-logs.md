---
title: Visa Azure Kubernetes service (AKS)-styrenhets loggar
description: Lär dig hur du aktiverar och visar loggarna för Kubernetes-huvudnoden i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359561"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Aktivera och granska Kubernetes huvud Node-loggar i Azure Kubernetes service (AKS)

Med Azure Kubernetes service (AKS) tillhandahålls huvud komponenterna som *Kube-apiserver* och *Kube-Controller-Manager* som en hanterad tjänst. Du skapar och hanterar noderna som kör *kubelet* -och container Runtime och distribuerar dina program via den hanterade Kubernetes API-servern. Du kan behöva se de loggar som genereras av dessa huvud komponenter för att felsöka program och tjänster. Den här artikeln visar hur du använder Azure Monitor loggar för att aktivera och skicka frågor till loggarna från Kubernetes huvud komponenter.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att ett befintligt AKS-kluster körs i ditt Azure-konto. Om du inte redan har ett AKS-kluster kan du skapa ett med hjälp av [Azure CLI][cli-quickstart] eller [Azure Portal][portal-quickstart]. Azure Monitor loggar fungerar med både RBAC-och icke-RBAC-aktiverade AKS-kluster.

## <a name="enable-diagnostics-logs"></a>Aktivera diagnostikloggar

För att hjälpa till att samla in och granska data från flera källor, innehåller Azure Monitor loggar ett frågespråk och analys motor som ger insikter om din miljö. En arbets yta används för att sortera och analysera data och kan integreras med andra Azure-tjänster, till exempel Application Insights och Security Center. Om du vill använda en annan plattform för att analysera loggarna kan du istället välja att skicka diagnostikloggar till ett Azure Storage-konto eller händelsehubben. Mer information finns i [Vad är Azure Monitor loggar?][log-analytics-overview].

Azure Monitor loggar aktive ras och hanteras i Azure Portal. Om du vill aktivera logg insamling för Kubernetes huvud komponenter i ditt AKS-kluster öppnar du Azure Portal i en webbläsare och utför följande steg:

1. Välj resurs grupp för ditt AKS-kluster, till exempel *myResourceGroup*. Välj inte den resurs grupp som innehåller de enskilda AKS-kluster resurserna, t. ex. *MC_myResourceGroup_myAKSCluster_eastus*.
1. Välj **diagnostikinställningar**på vänster sida.
1. Välj ditt AKS-kluster, till exempel *myAKSCluster*, och välj sedan att **lägga till diagnostikinställningar**.
1. Ange ett namn, till exempel *myAKSClusterLogs*, och välj sedan alternativet att **Skicka till Log Analytics**.
1. Välj en befintlig arbets yta eller skapa en ny. Om du skapar en arbets yta anger du ett namn på arbets ytan, en resurs grupp och en plats.
1. I listan över tillgängliga loggar väljer du de loggar som du vill aktivera. Vanliga loggar omfattar *Kube-apiserver*, *Kube-Controller-Manager*och *Kube-Scheduler*. Du kan aktivera ytterligare loggar som *Kube-audit* och *cluster-autoscaler*. Du kan returnera och ändra de insamlade loggarna när Log Analytics arbets ytor har Aktiver ATS.
1. När du är klar väljer du **Spara** för att aktivera insamling av de valda loggarna.

Följande exempel på en portal visar fönstret *diagnostikinställningar* :

![Aktivera Log Analytics arbets yta för Azure Monitor loggar för AKS-kluster](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Schemalägg en test-Pod på AKS-klustret

Skapa en del loggar genom att skapa en ny Pod i ditt AKS-kluster. Följande exempel på YAML-manifest kan användas för att skapa en grundläggande NGINX-instans. Skapa en fil med namnet `nginx.yaml` i valfritt redigerings program och klistra in följande innehåll:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Skapa Pod med kommandot [kubectl Create][kubectl-create] och ange yaml-filen, som du ser i följande exempel:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visa insamlade loggar

Det kan ta några minuter innan diagnostikloggar är aktiverade och visas i arbets ytan Log Analytics. I Azure Portal väljer du resurs gruppen för arbets ytan Log Analytics, till exempel *myResourceGroup*, och väljer sedan din Log Analytics-resurs, till exempel *myAKSLogs*.

![Välj Log Analytics arbets yta för ditt AKS-kluster](media/view-master-logs/select-log-analytics-workspace.png)

På den vänstra sidan väljer du **loggar**. Om du vill visa *Kube-apiserver*anger du följande fråga i text rutan:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Många loggar returneras sannolikt för API-servern. Om du vill begränsa frågan till att visa loggarna om NGINX-Pod som skapades i föregående steg, lägger du till ytterligare en *WHERE* -instruktion för att söka efter *poddar/nginx* som visas i följande exempel fråga:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

De speciella loggarna för dina NGINX-Pod visas, som du ser i följande exempel skärm bild:

![Log Analytics-frågeresultat för exempel NGINX Pod](media/view-master-logs/log-analytics-query-results.png)

Om du vill visa fler loggar kan du uppdatera frågan för *kategori* namnet till *Kube-Controller-Manager* eller *Kube-Scheduler*, beroende på vilka ytterligare loggar du aktiverar. Ytterligare *WHERE* -instruktioner kan sedan användas för att begränsa de händelser som du söker efter.

Mer information om hur du frågar och filtrerar logg data finns i [Visa eller analysera data som samlas in med Log Analytics-loggs ökning][analyze-log-analytics].

## <a name="log-event-schema"></a>Logga händelse schema

Följande tabell beskriver det schema som används för varje händelse för att analysera loggdata:

| Fältnamn               | Beskrivning |
|--------------------------|-------------|
| *resourceId*             | Azure-resurs som skapade loggen |
| *tid*                   | Tidsstämpel för när loggen laddades upp |
| *kategori*               | Namn på container/komponent som genererar loggen |
| *operationName*          | Always *Microsoft. container service/managedClusters/diagnosticLogs/Read* |
| *egenskaper. log*         | Fullständig text för loggen från komponenten |
| *egenskaper. Stream*      | *stderr* eller *STDOUT* |
| *egenskaper. Pod*         | Pod namn som loggen kom från |
| *egenskaper. containerID* | ID för Docker-behållaren som loggen kom från |

## <a name="log-roles"></a>Logg roller

| Roll                     | Beskrivning |
|--------------------------|-------------|
| *aksService*             | Visnings namnet i gransknings loggen för åtgärden kontroll plan (från hcpService) |
| *masterclient*           | Visnings namnet i gransknings loggen för MasterClientCertificate, certifikatet du får från AZ AKS get-credentials |
| *nodeclient*             | Visnings namnet för ClientCertificate, som används av agent-noder |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och granskar loggarna för Kubernetes huvud komponenter i ditt AKS-kluster. För att övervaka och felsöka ytterligare kan du också [Visa Kubelet-loggarna][kubelet-logs] och [Aktivera åtkomst till SSH-noden][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
