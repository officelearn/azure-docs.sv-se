---
title: Ansluta ett Azure Arc-aktiverat Kubernetes-kluster (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Anslut ett Azure Arc-aktiverat Kubernetes-kluster med Azure Arc
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.custom: references_regions
ms.openlocfilehash: 868964361e6089eb3417b0f2e2681d82d4aa0b75
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299651"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Ansluta ett Azure Arc-aktiverat Kubernetes-kluster (för hands version)

Anslut ett Kubernetes-kluster till Azure-bågen.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har följande krav:

* Ett Kubernetes-kluster som är igång
* Du behöver åtkomst med kubeconfig och kluster administratörs åtkomst.
* Användaren eller tjänstens huvud namn som används med `az login` och- `az connectedk8s connect` kommandon måste ha behörigheterna Läs och skriv för resurs typen Microsoft. Kubernetes/connectedclusters. Rollen "Azure-båge för Kubernetes onboarding" som har dessa behörigheter kan användas för roll tilldelningar för användaren eller tjänstens huvud namn som används med Azure CLI för onboarding.
* Senaste versionen av *connectedk8s* och *k8sconfiguration* -tillägg

## <a name="supported-regions"></a>Regioner som stöds

* USA, östra
* Europa, västra

## <a name="network-requirements"></a>Nätverkskrav

Azure Arc-agenter kräver att följande protokoll/portar/utgående URL: er fungerar.

* TCP på port 443-->`https://:443`
* TCP på port 9418-->`git://:9418`

| Slut punkt (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Krävs för att agenten ska kunna ansluta till Azure och registrera klustret                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Data planens slut punkt för agenten för att push-överföra status och hämta konfigurations information                                      |
| `https://docker.io`                                                                                            | Krävs för att hämta behållar avbildningar                                                                                         |
| `https://github.com`, git://github.com                                                                         | Exempel på GitOps-databaser finns på GitHub. Konfigurations agenten kräver anslutning till den git-slutpunkt som du anger. |
| `https://login.microsoftonline.com`                                                                            | Krävs för att hämta och uppdatera Azure Resource Manager tokens                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Krävs för att hämta behållar avbildningar för Azure Arc-agenter                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrera de två providers för Azure Arc-aktiverade Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

Registreringen är en asynkron process. Registreringen kan ta ungefär 10 minuter. Du kan övervaka registrerings processen med följande kommandon:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-and-arc-enabled-kubernetes-extensions"></a>Installera Azure CLI-och Arc-aktiverade Kubernetes-tillägg
Azure CLI version 2.3 + krävs för att installera Azure Arc-aktiverade Kubernetes CLI-tillägg. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) eller uppdatera till den senaste versionen för att säkerställa att du har Azure CLI version 2.3 +.

Installera `connectedk8s` tillägget, som hjälper dig att ansluta Kubernetes-kluster till Azure:

```console
az extension add --name connectedk8s
```

Installera `k8sconfiguration` tillägget:

```console
az extension add --name k8sconfiguration
```

Kör följande kommandon för att uppdatera tilläggen till de senaste versionerna.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="install-helm"></a>Installera Helm
Helm 3 krävs för onboarding av klustret med connectedk8s-tillägget. [Installera den senaste versionen av Helm 3](https://helm.sh/docs/intro/install) för att uppfylla det här kravet.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Använd en resurs grupp för att lagra metadata för klustret.

Skapa först en resurs grupp som ska innehålla den anslutna kluster resursen.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Utdataparametrar**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Anslut ett kluster

Nu ska vi ansluta vårt Kubernetes-kluster till Azure. Arbets flödet för `az connectedk8s connect` ser ut så här:

1. Kontrol lera anslutningen till ditt Kubernetes-kluster: via `KUBECONFIG` , `~/.kube/config` eller`--kube-config`
1. Distribuera Azure Arc-agenter för Kubernetes med Helm 3, i `azure-arc` namn området

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Utdataparametrar**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verifiera anslutet kluster

Lista de anslutna klustren:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Utdataparametrar**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Du kan också visa den här resursen på [Azure Preview Portal](https://preview.portal.azure.com/). När du har öppnat portalen i webbläsaren navigerar du till resurs gruppen och den Azure Arc-aktiverade Kubernetes-resursen baserat på de resurs namn och resurs grupp namn som användes tidigare i `az connectedk8s connect` kommandot.

Azure Arc-aktiverade Kubernetes distribuerar några operatörer till `azure-arc` namn området. Du kan visa dessa distributioner och poddar här:

```console
kubectl -n azure-arc get deployments,pods
```

**Utdataparametrar**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Azure Arc-agenter för Kubernetes

Azure Arc-aktiverade Kubernetes består av några agenter (operatörer) som körs i ditt kluster och som har distribuerats till `azure-arc` namn området.

* `deployment.apps/config-agent`: bevakar det anslutna klustret för käll kontroll konfigurations resurser som tillämpas på klustret och uppdaterar kompatibilitetstillstånd
* `deployment.apps/controller-manager`: är en operatör av operatörer och dirigerar interaktioner mellan Azure båg-komponenter
* `deployment.apps/metrics-agent`: samlar in Mät värden för andra Arc-agenter för att säkerställa att dessa agenter visar optimala prestanda
* `deployment.apps/cluster-metadata-operator`: samlar in kluster-metadata-kluster version, antal noder och Arc-agentens version
* `deployment.apps/resource-sync-agent`: synkroniserar ovanstående klustrade metadata till Azure
* `deployment.apps/clusteridentityoperator`: upprätthåller det hanterade tjänst identitets certifikatet (MSI) som används av andra agenter för kommunikation med Azure
* `deployment.apps/flux-logs-agent`: samlar in loggar från flödes operatörer som distribuerats som en del av käll kontroll konfigurationen

## <a name="delete-a-connected-cluster"></a>Ta bort ett anslutet kluster

Du kan ta bort en `Microsoft.Kubernetes/connectedcluster` resurs med hjälp av Azure CLI eller Azure Portal.


* **Ta bort med Azure CLI**: följande Azure CLI-kommando kan användas för att initiera borttagning av den Azure Arc-aktiverade Kubernetes-resursen.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Detta tar bort `Microsoft.Kubernetes/connectedCluster` resursen och eventuella associerade `sourcecontrolconfiguration` resurser i Azure. Azure CLI använder Helm Uninstall för att ta bort de agenter som körs i klustret också.

* **Borttagning på Azure Portal**: om du tar bort den Azure Arc-aktiverade Kubernetes-resursen på Azure Portal tas `Microsoft.Kubernetes/connectedcluster` resursen och eventuella associerade `sourcecontrolconfiguration` resurser i Azure bort, men de agenter som körs i klustret tas inte bort. Kör följande kommando för att ta bort agenterna som körs i klustret.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Nästa steg

* [Använd GitOps i ett anslutet kluster](./use-gitops-connected-cluster.md)
* [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)