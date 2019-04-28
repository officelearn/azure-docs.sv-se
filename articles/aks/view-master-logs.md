---
title: Visa Azure Kubernetes Service (AKS) domänkontrollant loggar
description: Lär dig hur du aktiverar och visa loggar för noden som Kubernetes i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: iainfou
ms.openlocfilehash: 77908e24a19a48bf9b84d5d5b664bf0443159118
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128710"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Aktivera och granska Kubernetes huvudnoden loggar i Azure Kubernetes Service (AKS)

Med Azure Kubernetes Service (AKS), huvudkomponenter som den *kube apiserver* och *kube-controller-manager* tillhandahålls som en hanterad tjänst. Du skapar och hanterar de noder som kör den *kubelet* och körning av behållare, och distribuera dina program via den hanterade Kubernetes API-servern. För att felsöka dina program och tjänster, kan du behöva visa loggar som genereras av komponenterna master. Den här artikeln visar hur du använder Azure Monitor-loggar att aktivera och fråga loggar från komponenten som Kubernetes.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver ett AKS-kluster som körs i ditt Azure-konto. Om du inte redan har ett AKS-kluster, skapar du en med den [Azure CLI] [ cli-quickstart] eller [Azure-portalen][portal-quickstart]. Azure Monitor-loggar fungerar med båda RBAC och icke-RBAC aktiverat AKS-kluster.

## <a name="enable-diagnostics-logs"></a>Aktivera diagnostikloggar

För att samla in och granska data från flera källor, ger Azure Monitor-loggar en frågespråk och en analytisk frågemotor som ger insikter i din miljö. En arbetsyta används för att sortera och analysera data och kan integreras med andra Azure-tjänster, till exempel Application Insights och Security Center. Om du vill använda en annan plattform för att analysera loggarna måste välja du i stället att skicka diagnostikloggar till en Azure storage-konto eller event hub. Mer information finns i [vad är Azure Monitor-loggar?] [log-analytics-overview].

Azure Monitor-loggar har aktiverats och hanteras i Azure-portalen. Öppna Azure portal i en webbläsare om du vill aktivera insamling av supportloggar för huvudkomponenter Kubernetes AKS-klustret och utför följande steg:

1. Välj resursgrupp för AKS-klustret, till exempel *myResourceGroup*. Välj den resursgrupp som innehåller din enskilda AKS-kluster-resurser, som inte *MC_myResourceGroup_myAKSCluster_eastus*.
1. Till vänster, Välj **diagnostikinställningar**.
1. Välj AKS-klustret, till exempel *myAKSCluster*, sedan välja att **slå på diagnostik**.
1. Ange ett namn, till exempel *myAKSClusterLogs*, Välj alternativet att **skicka till Log Analytics-arbetsyta**.
    * Välja att *konfigurera* Log Analytics-arbetsyta och sedan välja en befintlig arbetsyta eller **Skapa ny arbetsyta**.
    * Om du vill skapa en arbetsyta kan du ange ett namn, en resursgrupp och en plats.
1. I listan över tillgängliga loggar, väljer du de loggar som du vill aktivera. Som standard den *kube apiserver*, *kube-controller-manager*, och *kube-schemaläggare* loggar är aktiverade. Du kan aktivera ytterligare loggar som *kube-granskning* och *kluster autoskalningen*. Du kan gå tillbaka och ändra insamlade loggar när Log Analytics-arbetsytor är aktiverade.
1. När du är klar väljer **spara** att aktivera insamling av valda loggarna.

> [!NOTE]
> AKS samlar endast in granskningsloggar för kluster som skapas eller uppgraderas efter att en funktionsflagga har aktiverats på din prenumeration. Att registrera den *AKSAuditLog* funktion flaggan, använda den [az funktionen registrera] [ az-feature-register] kommandot som visas i följande exempel:
>
> `az feature register --name AKSAuditLog --namespace Microsoft.ContainerService`
>
> Vänta på status för att visa *registrerad*. Du kan kontrollera statusen registrering med den [az funktionslistan] [ az-feature-list] kommando:
>
> `az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAuditLog')].{Name:name,State:properties.state}"`
>
> När du är klar kan du uppdatera registreringen av resursprovidern AKS med hjälp av den [az provider register] [ az-provider-register] kommando:
>
> `az provider register --namespace Microsoft.ContainerService`

I följande exempel portal skärmbild visas den *diagnostikinställningar* fönstret och sedan på alternativet för att skapa en Log Analytics-arbetsyta:

![Aktivera Log Analytics-arbetsyta för Azure Monitor-loggarna för AKS-kluster](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Schemalägga en test-pod på AKS-kluster

Skapa en ny pod AKS-klustret för att generera vissa loggar. Följande exempel YAML Manifestet kan användas för att skapa en grundläggande NGINX-instans. Skapa en fil med namnet `nginx.yaml` i en textredigerare och klistra in följande innehåll:

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

Skapa en pod med den [kubectl skapa] [ kubectl-create] kommandot och ange din YAML-fil som du ser i följande exempel:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visa insamlade loggar

Det kan ta några minuter för diagnostikloggar till aktiveras och visas i Log Analytics-arbetsytan. I Azure-portalen väljer du resursgruppen för din Log Analytics-arbetsyta som *myResourceGroup*, väljer din log analytics-resurs, till exempel *myAKSLogs*.

![Välj Log Analytics-arbetsytan för AKS-kluster](media/view-master-logs/select-log-analytics-workspace.png)

Till vänster, Välj **loggar**. Visa den *kube-apiserver*, anger du följande fråga i textrutan:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Många loggar som returneras sannolikt för API-servern. Omfång av frågan för att visa loggarna om NGINX-pod som skapats i föregående steg genom att lägga till ytterligare *där* -uttrycket för att söka efter *poddar/nginx* som visas i följande exempelfråga:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Specifika loggar efter din pod för NGINX visas, enligt följande exempel skärmbild:

![Log analytics-frågeresultat för exemplet NGINX pod](media/view-master-logs/log-analytics-query-results.png)

Om du vill visa ytterligare loggar, du kan uppdatera frågan för den *kategori* namn till *kube-controller-manager* eller *kube-schemaläggare*, beroende på vad ytterligare loggar du Aktivera. Ytterligare *där* instruktioner kan sedan användas för att förfina de händelser som du letar efter.

Läs mer om hur du fråga och filtrera dina loggdata [visa eller analysera data som samlas in med log analytics-loggsökning][analyze-log-analytics].

## <a name="log-event-schema"></a>Händelseschema

I följande tabell beskrivs för att analysera loggdata, ett schema som används för varje händelse:

| Fältnamn               | Beskrivning |
|--------------------------|-------------|
| *Resurs-ID*             | Azure-resurs som skapas i loggen |
| *tid*                   | Tidsstämpel för när loggen har överförts |
| *Kategori*               | Namnet på behållaren/komponenten genererar loggen |
| *OperationName*          | Alltid *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Fulltext loggens från komponenten |
| *properties.stream*      | *stderr* eller *stdout* |
| *properties.pod*         | Podnamn loggen kommer ifrån |
| *properties.containerID* | ID för docker-behållaren som den här loggen kommer ifrån |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och granska loggarna för huvudkomponenter Kubernetes AKS-klustret. Om du vill övervaka och felsöka ytterligare, kan du också [visa Kubelet-loggar] [ kubelet-logs] och [aktivera SSH noden åtkomst][aks-ssh].

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
