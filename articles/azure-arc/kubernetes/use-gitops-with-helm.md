---
title: Använda GitOps med Helm för en Azure Arc-aktiverad kluster konfiguration (förhands granskning)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använda GitOps med Helm för en Azure Arc-aktiverad kluster konfiguration (förhands granskning)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 9cd1169c7a622da0e4be3900f94dc31fc99e762d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664160"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Använda GitOps med Helm för en Azure Arc-aktiverad kluster konfiguration (förhands granskning)

Helm är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. Precis som Linux-paket hanterare som APT och yum, används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm med Azure Arc-aktiverade Kubernetes.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt Azure-båg aktiverat Kubernetes-kopplat kluster. Om du behöver ett anslutet kluster kan du läsa snabb starten för att [ansluta ett kluster](./connect-cluster.md).

Vi börjar med att ange miljövariabler som ska användas i den här självstudien. Du behöver resurs gruppens namn och kluster namnet för det anslutna klustret.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Verifiera att klustret är aktiverat med Arc

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Resultat:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Översikt över att använda Helm med Azure Arc Enabled Kubernetes

 Helm-operatören ger ett tillägg till flöde som automatiserar Helm-diagram versioner. En diagram version beskrivs via en Kubernetes-anpassad resurs med namnet HelmRelease. Flödet synkroniserar dessa resurser från git till klustret och Helm-operatorn kontrollerar att Helm-diagram släpps på det sätt som anges i resurserna.

 Nedan visas ett exempel på en git lagrings platsen-struktur som vi ska använda i den här självstudien:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── prod
        └── azure-vote-app.yaml
```

I git-lagrings platsen har vi två kataloger, en som innehåller ett Helm-diagram och en som innehåller de versioner som är konfigurerade. I `releases/prod` katalogen `azure-vote-app.yaml` innehåller HelmRelease-konfigurationen som visas nedan:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

Helm-versions konfigurationen innehåller följande fält:

- `metadata.name`är obligatoriskt och måste följa Kubernetes Naming Conventions
- `metadata.namespace`är valfritt och avgör var versionen skapas
- `spec.releaseName`är valfritt, och om det inte anges kommer versions namnet att $namespace-$name
- `spec.chart.path`är katalogen som innehåller diagrammet, angivet i förhållande till databas roten
- `spec.values`är användar anpassningar av standard parameter värden från själva diagrammet

Alternativen som anges i HelmRelease-spec. värdena åsidosätter de alternativ som anges i Values. yaml från diagram källan.

Du kan läsa mer om HelmRelease i den officiella [Helm operatörs dokumentationen](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html)

## <a name="create-a-configuration"></a>Skapa en konfiguration

Vi använder Azure CLI-tillägget för `k8sconfiguration` , vi länkar vårt anslutna kluster till exempel git-lagringsplatsen. Vi kommer att ge den här konfigurationen ett namn `azure-voting-app` och distribuera flödes operatorn i `prod` namn området.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Konfigurations parametrar

Om du vill anpassa skapandet av konfigurationen kan [du läsa mer om ytterligare parametrar som du kan använda](./use-gitops-connected-cluster.md#additional-parameters).


## <a name="validate-the-configuration"></a>Verifiera konfigurationen

Verifiera att du har skapat med hjälp av Azure CLI `sourceControlConfiguration` .

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Observera att `sourceControlConfiguration` resursen har uppdaterats med kompatibilitetsstatus, meddelanden och fel söknings information.

**Utdataparametrar**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Compliant",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Verifiera program

Nu ska vi kontrol lera att programmet är igång genom att hämta tjänstens IP-adress.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Utdataparametrar**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Hitta den externa IP-adressen från utdata ovan och öppna den i en webbläsare.

## <a name="next-steps"></a>Nästa steg

- [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
