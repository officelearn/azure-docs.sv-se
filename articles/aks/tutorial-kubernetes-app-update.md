---
title: Självstudie om Kubernetes i Azure – Uppdatera program
description: Självstudie om AKS – Uppdatera program
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 622cd17a93bf1b9fa9d3c138d385ca1d29426f3b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Självstudie: Uppdatera ett program i Azure Kubernetes Service (AKS)

När ett program har distribuerats i Kubernetes kan du uppdatera det genom att ange en ny behållaravbildning eller avbildningsversion. När du gör det mellanlagras uppdateringen så att endast en del av distributionen uppdateras samtidigt. Den här mellanlagrade uppdateringen gör att programmet kan fortsätta att köras under uppdateringen. Det ger också en mekanism för återställning om ett distributionsfel inträffar.

I den här självstudien, del sex av åtta, uppdateras Azure Vote-exempelappen. Uppgifter som du kan slutföra inkluderar:

> [!div class="checklist"]
> * Uppdatera klientdelens programkod
> * Skapa en uppdaterad behållaravbildning
> * Push-överföra behållaravbildningen till Azure Container Registry
> * Distribuera den uppdaterade behållaravbildningen

I senare självstudier konfigureras Log Analytics för att övervaka Kubernetes-klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades. Programmet kördes därefter i Kubernetes-klustret.

En programlagringsplats klonades också, med programmets källkod och en färdig Docker Compose-fil som används i den här självstudien. Verifiera att du har skapat en klon av lagringsplatsen och att du har ändrat kataloger i den klonade katalogen. Inuti finns en katalog som heter `azure-vote` och en fil med namnet `docker-compose.yaml`.

Om du inte har slutfört dessa steg och vill hänga med återgår du till [Självstudie 1 – Skapa behållaravbildningar][aks-tutorial-prepare-app].

## <a name="update-application"></a>Uppdatera program

För den här självstudien görs en ändring av programmet, och det uppdaterade programmet distribueras till Kubernetes-klustret.

Programmets källkod finns i katalogen `azure-vote`. Öppna filen `config_file.cfg` i valfritt redigeringsprogram. I det här exemplet används `vi`.

```console
vi azure-vote/azure-vote/config_file.cfg
```

Ändra värdena för `VOTE1VALUE` och `VOTE2VALUE` och spara filen.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Spara och stäng filen.

## <a name="update-container-image"></a>Uppdatera behållaravbildningen

Använd [docker-compose][docker-compose] för att skapa klientdelsavbildningen igen och kör det uppdaterade programmet. Argumentet `--build` används för att instruera Docker Compose att skapa programavbildningen på nytt.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testa programmet lokalt

Gå till http://localhost:8080 om du vill se det uppdaterade programmet.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Tagga och push-överföra avbildningar

Tagga avbildningen `azure-vote-front` med namnet på inloggningsservern för behållarregistret.

Hämta inloggningsserverns namn med kommandot [az acr list](/cli/azure/acr#az_acr_list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd [dockertagg][docker-tag] för att tagga avbildningen. Ersätt `<acrLoginServer>` med ditt Azure Container Registry-inloggningsservernamn eller offentliga registervärdnamn. Lägg även märke till att avbildningsversionen har uppdaterats till `v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Använd [docker push][docker-push] och ladda upp avbildningen till registret. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för Azure Container Registry. Om du får problem med push-överföring till ACR-registret, så kontrollera att du har kört kommandot [az acr login][az-acr-login].

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-application"></a>Distribuera det uppdaterade programmet

Du får minimala störningar om flera instanser av programpodden körs. Verifiera konfigurationen med kommandot [kubectl get pod][kubectl-get].

```
kubectl get pod
```

Resultat:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Om du inte har flera poddar som kör avbildningen azure-vote-front skalar du ut `azure-vote-front`-distributionen.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

När du ska uppdatera programmet använder du kommandot [kubectl set][kubectl-set]. Uppdatera `<acrLoginServer>` med inloggningsservern eller värdnamnet på ditt behållarregister.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Du övervakar distributionen av kommandot [kubectl get pod][kubectl-get]. Eftersom det uppdaterade programmet är distribuerat avslutas dina poddar och återskapas med den nya behållaravbildningen.

```azurecli
kubectl get pod
```

Resultat:

```
NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-updated-application"></a>Testa det uppdaterade programmet

Hämta den externa IP-adressen för tjänsten `azure-vote-front`.

```azurecli
kubectl get service azure-vote-front
```

Gå till IP-adressen om du vill se det uppdaterade programmet.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du uppdaterat ett program och distribuerat uppdateringen till ett Kubernetes-kluster. Följande uppgifter har slutförts:

> [!div class="checklist"]
> * Uppdaterade klientdelens programkod
> * Skapade en uppdaterad behållaravbildning
> * Push-överförde behållaravbildningen till Azure Container Registry
> * Distribuerade det uppdaterade programmet

Gå vidare till nästa självstudie om du vill lära dig om att uppgradera Kubernetes till en ny version.

> [!div class="nextstepaction"]
> [Uppgradera Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login