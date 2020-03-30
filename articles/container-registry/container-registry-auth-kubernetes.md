---
title: Autentisera från Kubernetes-kluster
description: Lär dig hur du ger ett Kubernetes-kluster åtkomst till avbildningar i ditt Azure-behållarregister genom att skapa en pull-hemlighet med hjälp av ett tjänsthuvudnamn
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154899"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Hämta avbildningar från ett Azure-behållarregister till ett Kubernetes-kluster

Du kan använda ett Azure-behållarregister som en källa till behållaravbildningar med alla Kubernetes-kluster, inklusive "lokala" Kubernetes-kluster som [minikube](https://minikube.sigs.k8s.io/) och [typ](https://kind.sigs.k8s.io/). Den här artikeln visar hur du skapar en Kubernetes pull secret baserat på ett Huvudnamn för Azure Active Directory-tjänsten. Använd sedan hemligheten för att hämta avbildningar från ett Azure-behållarregister i en Kubernetes-distribution.

> [!TIP]
> Om du använder den hanterade [Azure Kubernetes-tjänsten](../aks/intro-kubernetes.md)kan du också [integrera klustret](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) med ett mål-Azure-behållarregister för avbildningshandtag. 

Den här artikeln förutsätter att du redan har skapat ett privat Azure-behållarregister. Du måste också ha ett Kubernetes-kluster `kubectl` igång och tillgängligt via kommandoradsverktyget.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Om du inte sparar eller kommer ihåg tjänstens huvudlösenord kan du återställa det med kommandot [az ad sp-återställning för autentiseringsuppgifter:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Det här kommandot returnerar ett nytt, giltigt lösenord för tjänstens huvudnamn.

## <a name="create-an-image-pull-secret"></a>Skapa en bild pull hemlighet

Kubernetes använder en *bild pull hemlighet* för att lagra information som behövs för att autentisera till registret. Om du vill skapa pull-hemligheten för ett Azure-behållarregister anger du tjänstens huvud-ID, lösenord och register-URL:en. 

Skapa en bild pull `kubectl` hemlighet med följande kommando:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
där:

| Värde | Beskrivning |
| :--- | :--- |
| `secret-name` | Namnet på bilden dra hemlighet, till exempel *acr-secret* |
| `namespace` | Kubernetes namnområde för att sätta hemligheten i <br/> Behövs bara om du vill placera hemligheten i ett annat namnområde än standardnamnområdet |
| `container-registry-name` | Namn på ditt Azure-behållarregister |
| `service-principal-ID` | ID för tjänstens huvudnamn som kommer att användas av Kubernetes för att komma åt ditt register |
| `service-principal-password` | Lösenord för tjänstens huvudnamn |

## <a name="use-the-image-pull-secret"></a>Använd bilden dra hemlighet

När du har skapat avbildningen pull secret, kan du använda den för att skapa Kubernetes poddar och distributioner. Ange namnet på hemligheten under `imagePullSecrets` i distributionsfilen. Ett exempel:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

I föregående exempel `your-awesome-app:v1` är namnet på avbildningen som ska hämtas `acr-secret` från Azure-behållarregistret och är namnet på pull-hemligheten som du skapade för att komma åt registret. När du distribuerar podden hämtar Kubernetes automatiskt avbildningen från registret, om den inte redan finns i klustret.


## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med tjänsthuvudnamn och Azure Container Registry finns i [Azure Container Registry authentication with service principals](container-registry-auth-service-principal.md)
* Läs mer om bildhandtagshemligheter i [Kubernetes-dokumentationen](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset