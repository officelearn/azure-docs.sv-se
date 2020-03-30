---
title: Visa AKS-styrenhetsloggar (Azure Kubernetes Service)
description: Lär dig hur du aktiverar och visar loggarna för Kubernetes-huvudnoden i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259388"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Aktivera och granska Kubernetes huvudnodloggar i Azure Kubernetes Service (AKS)

Med Azure Kubernetes Service (AKS) tillhandahålls huvudkomponenter som *kube-apiserver* och *kube-controller-manager* som en hanterad tjänst. Du skapar och hanterar noderna som kör *kubelet-* och containerkörningen och distribuerar dina program via den hanterade Kubernetes API-servern. Om du vill felsöka ditt program och dina tjänster kan du behöva visa loggarna som genereras av dessa huvudkomponenter. Den här artikeln visar hur du använder Azure Monitor-loggar för att aktivera och fråga loggarna från Kubernetes-huvudkomponenter.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver ett befintligt AKS-kluster som körs i ditt Azure-konto. Om du inte redan har ett AKS-kluster skapar du ett med [Azure CLI-][cli-quickstart] eller [Azure-portalen][portal-quickstart]. Azure Monitor-loggar fungerar med både RBAC och icke-RBAC-aktiverade AKS-kluster.

## <a name="enable-diagnostics-logs"></a>Aktivera diagnostikloggar

Azure Monitor-loggarna innehåller ett frågespråk och analysmotor som ger insikter i din miljö för att samla in och granska data från flera källor. En arbetsyta används för att samla in och analysera data och kan integreras med andra Azure-tjänster som Application Insights och Security Center. Om du vill använda en annan plattform för att analysera loggarna kan du i stället välja att skicka diagnostikloggar till ett Azure-lagringskonto eller händelsenav. Mer information finns i [Vad är Azure Monitor-loggar?][log-analytics-overview].

Azure Monitor-loggar är aktiverade och hanterade i Azure-portalen. Om du vill aktivera loggsamling för Kubernetes-huvudkomponenter i AKS-klustret öppnar du Azure-portalen i en webbläsare och utför följande steg:

1. Välj resursgruppen för AKS-klustret, till exempel *myResourceGroup*. Välj inte den resursgrupp som innehåller dina enskilda AKS-klusterresurser, till exempel *MC_myResourceGroup_myAKSCluster_eastus*.
1. Välj **Diagnostikinställningar**till vänster .
1. Välj AKS-kluster, till exempel *myAKSCluster,* och välj sedan **att lägga till diagnostikinställning**.
1. Ange ett namn, till exempel *myAKSClusterLogs*, välj sedan alternativet **Skicka till Logganalys**.
1. Välj en befintlig arbetsyta eller skapa en ny. Om du skapar en arbetsyta anger du ett arbetsytenamn, en resursgrupp och en plats.
1. I listan över tillgängliga loggar väljer du de loggar du vill aktivera. Vanliga loggar är *kube-apiserver,* *kube-controller-manager*och *kube-scheduler*. Du kan aktivera ytterligare loggar, till exempel *kube-audit* och *kluster-autoscaler*. Du kan returnera och ändra de insamlade loggarna när Log Analytics-arbetsytor är aktiverade.
1. När du är klar väljer du **Spara** för att aktivera insamling av de valda loggarna.

I följande exempelportal skärmdump visar *fönstret Diagnostikinställningar:*

![Aktivera log analytics-arbetsyta för Azure Monitor-loggar i AKS-kluster](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Schemalägga en testkapsel i AKS-klustret

Skapa en ny pod i AKS-klustret om du vill generera några loggar. Följande exempel YAML-manifest kan användas för att skapa en grundläggande NGINX-förekomst. Skapa en `nginx.yaml` fil som heter i en valfri redigerare och klistra in följande innehåll:

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

Skapa podden med kommandot [kubectl create][kubectl-create] och ange YAML-filen, som visas i följande exempel:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visa insamlade loggar

Det kan ta några minuter innan diagnostikloggarna aktiveras och visas på log analytics-arbetsytan. I Azure-portalen väljer du resursgruppen för din Log Analytics-arbetsyta, till exempel *myResourceGroup,* och väljer sedan din logganalysresurs, till exempel *myAKSLogs*.

![Välj arbetsytan Log Analytics för AKS-klustret](media/view-master-logs/select-log-analytics-workspace.png)

På vänster sida väljer du **Loggar**. Om du vill visa *kube-apiservern*anger du följande fråga i textrutan:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Många loggar returneras troligen för API-servern. Om du vill begränsa frågan för att visa loggarna om NGINX-podden som skapades i föregående steg lägger du till ytterligare en *där* sats för att söka efter *poddar/nginx* enligt följande exempelfråga:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

De specifika loggarna för din NGINX-pod visas, vilket visas i följande exempel skärmdump:

![Logganalysfrågeresultat för exempel på NGINX-pod](media/view-master-logs/log-analytics-query-results.png)

Om du vill visa ytterligare loggar kan du uppdatera frågan för *kategorinamnet* till *kube-controller-manager* eller *kube-scheduler*, beroende på vilka ytterligare loggar du aktiverar. Ytterligare *där* satser kan sedan användas för att förfina de händelser du letar efter.

Mer information om hur du frågar och filtrerar loggdata finns i [Visa eller analysera data som samlats in med logganalysloggsökning][analyze-log-analytics].

## <a name="log-event-schema"></a>Logga händelseschema

För att analysera loggdata, i följande tabell detaljer schemat som används för varje händelse:

| Fältnamn               | Beskrivning |
|--------------------------|-------------|
| *resourceId (resourceId)*             | Azure-resurs som producerade loggen |
| *Tid*                   | Tidsstämpel för när loggen laddades upp |
| *Kategori*               | Namn på behållaren/komponent som genererar loggen |
| *operationName*          | Alltid *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *egenskaper.log*         | Fulltext av loggen från komponenten |
| *egenskaper.stream*      | *stderr* eller *stdout* |
| *egenskaper.pod*         | Pod namn som loggen kom från |
| *egenskaper.containerID* | ID för dockerbehållaren kom den här loggen från |

## <a name="log-roles"></a>Loggroller

| Roll                     | Beskrivning |
|--------------------------|-------------|
| *aksService (aksService)*             | Visningsnamnet i granskningsloggen för kontrollplansdriften (från hcpService) |
| *masterclient (masterclient)*           | Visningsnamnet i granskningsloggen för MasterClientCertificate, certifikatet du får från az aks get-credentials |
| *nodeklient*             | Visningsnamnet för ClientCertificate, som används av agentnoder |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och granskar loggarna för Kubernetes-huvudkomponenterna i AKS-klustret. Om du vill övervaka och felsöka ytterligare kan du även [visa Kubelet-loggarna][kubelet-logs] och [aktivera SSH-nodåtkomst][aks-ssh].

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
