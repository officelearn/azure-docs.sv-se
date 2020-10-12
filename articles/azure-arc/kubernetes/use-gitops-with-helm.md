---
title: Distribuera Helm-diagram med GitOps on Arc Enabled Kubernetes Cluster (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använda GitOps med Helm för en Azure Arc-aktiverad kluster konfiguration (förhands granskning)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: eea81d458ac6631c4a023134b3198e4cdb04526e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541619"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Distribuera Helm-diagram med GitOps on Arc Enabled Kubernetes Cluster (för hands version)

Helm är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. Precis som Linux-paket hanterare som APT och yum, används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm med Azure Arc-aktiverade Kubernetes.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt Azure-båg aktiverat Kubernetes-kopplat kluster. Om du behöver ett anslutet kluster kan du läsa snabb starten för att [ansluta ett kluster](./connect-cluster.md).

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Översikt över användning av GitOps och Helm med Azure Arc Enabled Kubernetes

 Helm-operatören ger ett tillägg till flöde som automatiserar Helm-diagram versioner. En diagram version beskrivs via en Kubernetes-anpassad resurs med namnet HelmRelease. Flödet synkroniserar dessa resurser från git till klustret och Helm-operatorn kontrollerar att Helm-diagram släpps på det sätt som anges i resurserna.

 [Exempel lagrings platsen](https://github.com/Azure/arc-helm-demo) som används i det här dokumentet är strukturerad på följande sätt:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

I git-lagrings platsen har vi två kataloger, en som innehåller ett Helm-diagram och en som innehåller de versioner som är konfigurerade. I `releases` katalogen `app.yaml` innehåller HelmRelease-konfigurationen som visas nedan:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Helm-versions konfigurationen innehåller följande fält:

- `metadata.name` är obligatoriskt och måste följa Kubernetes Naming Conventions
- `metadata.namespace` är valfritt och avgör var versionen skapas
- `spec.releaseName` är valfritt, och om det inte anges kommer versions namnet att $namespace-$name
- `spec.chart.path` är katalogen som innehåller diagrammet, angivet i förhållande till databas roten
- `spec.values` är användar anpassningar av standard parameter värden från själva diagrammet

Alternativen som anges i HelmRelease-spec. värdena åsidosätter de alternativ som anges i Values. yaml från diagram källan.

Du kan läsa mer om HelmRelease i den officiella [Helm operatörs dokumentationen](https://docs.fluxcd.io/projects/helm-operator/en/stable/)

## <a name="create-a-configuration"></a>Skapa en konfiguration

Vi använder Azure CLI-tillägget för `k8sconfiguration` , vi länkar vårt anslutna kluster till exempel git-lagringsplatsen. Vi kommer att ge den här konfigurationen ett namn `azure-arc-sample` och distribuera flödes operatorn i `arc-k8s-demo` namn området.

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Konfigurations parametrar

Om du vill anpassa skapandet av konfigurationen kan [du läsa mer om ytterligare parametrar som du kan använda](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Verifiera konfigurationen

Verifiera att du har skapat med hjälp av Azure CLI `sourceControlConfiguration` .

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`Resursen har uppdaterats med kompatibilitetsstatus, meddelanden och fel söknings information.

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Verifiera program

Kör följande kommando och navigera till `localhost:8080` i webbläsaren för att kontrol lera att programmet körs.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Nästa steg

- [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
