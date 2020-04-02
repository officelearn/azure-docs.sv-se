---
title: Rotera certifikat i Azure Kubernetes Service (AKS)
description: Lär dig hur du roterar dina certifikat i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549075"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Rotera certifikat i Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) använder certifikat för autentisering med många av dess komponenter. Med jämna mellanrum kan du behöva rotera dessa certifikat av säkerhets- eller principskäl. Du kan till exempel ha en princip för att rotera alla certifikat var 90:e dag.

I den här artikeln visas hur du roterar certifikaten i AKS-klustret.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.77 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-certifikat, certifikatutfärdare och tjänstkonton

AKS genererar och använder följande certifikat, certifikatutfärdare och tjänstkonton:

* AKS API-servern skapar en certifikatutfärdare som kallas klustercertifikatutfärdaren.
* API-servern har en klustercertifikatutfärdare som signerar certifikat för enkelriktad kommunikation från API-servern till kubelets.
* Varje kubelet skapar också en begäran om certifikatsignering (CSR), som är signerad av klustercertifikatutfärdaren, för kommunikation från kubelet till API-servern.
* Arkivet för etcd-nyckelvärde har ett certifikat signerat av klustercertifikatutfärdaren för kommunikation från etcd till API-servern.
* Värdearkivet med etcd skapar en certifikatutfärdare som signerar certifikat för att autentisera och auktorisera datareplikering mellan etcd-repliker i AKS-klustret.
* API-aggregatorn använder klustercertifikatutfärdaren för att utfärda certifikat för kommunikation med andra API:er. API-aggregatorn kan också ha en egen certifikatutfärdare för att utfärda dessa certifikat, men använder för närvarande klustercertifikatutfärdaren.
* Varje nod använder en SA-token (Service Account), som signeras av klustercauten.
* Klienten `kubectl` har ett certifikat för kommunikation med AKS-klustret.

> [!NOTE]
> AKS-kluster som skapats före mars 2019 har certifikat som upphör att gälla efter två år. Alla kluster som skapas efter mars 2019 eller ett kluster som har sina certifikat roterade har klustercertifikatutfärdare som upphör att gälla efter 30 år. Alla andra certifikat upphör att gälla efter två år. Om du vill kontrollera när `kubectl get nodes` klustret skapades använder du för att se *age-poolerna* för nodpoolerna.
> 
> Dessutom kan du kontrollera utgångsdatumet för klustrets certifikat. Följande kommando visar till exempel certifikatinformation för *myAKSCluster-klustret.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Rotera klustercertifikaten

> [!WARNING]
> Om du roterar `az aks rotate-certs` certifikaten med kan det leda till upp till 30 minuters driftstopp för AKS-klustret.

Använd [az aks get-credentials][az-aks-get-credentials] för att logga in på AKS-klustret. Det här kommandot hämtar och `kubectl` konfigurerar även klientcertifikatet på den lokala datorn.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Används `az aks rotate-certs` för att rotera alla certifikat, certifikatutfärdare och certifikatutfärdare i klustret.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Det kan ta upp till `az aks rotate-certs` 30 minuter för att slutföra. Om kommandot misslyckas innan `az aks show` det slutförs används för att kontrollera att klustrets status är *Certifikatroterande*. Om klustret är i ett `az aks rotate-certs` misslyckat tillstånd körs du igen för att rotera certifikaten igen.

Kontrollera att de gamla certifikaten inte `kubectl` längre är giltiga genom att köra ett kommando. Eftersom du inte har uppdaterat `kubectl`de certifikat som används av visas ett fel.  Ett exempel:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Uppdatera certifikatet `kubectl` som `az aks get-credentials`används av genom att köra .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Kontrollera att certifikaten har uppdaterats genom att köra ett `kubectl` kommando, som nu lyckas. Ett exempel:

```console
kubectl get no
```

> [!NOTE]
> Om du har några tjänster som körs ovanpå AKS, till exempel [Azure Dev Spaces,][dev-spaces]kan du behöva uppdatera certifikat som [är relaterade till dessa tjänster][dev-spaces-rotate] också.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du automatiskt roterar klustrets certifikat, certifikatutfärdare och SA:er automatiskt. Du kan se [metodtips för klustersäkerhet och uppgraderingar i Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] för mer information om metodtips för AKS-säkerhet.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
