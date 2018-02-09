---
title: "Självstudie för Azure Container Service – Uppdatera ett program"
description: "Självstudie för Azure Container Service – Uppdatera ett program"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 34b08111863df99dc05a7b269464ce65a916a171
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="update-an-application-in-kubernetes"></a>Uppdatera ett program i Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

När ett program har distribuerats i Kubernetes kan du uppdatera det genom att ange en ny behållaravbildning eller avbildningsversion. När du gör det mellanlagras uppdateringen så att endast en del av distributionen uppdateras samtidigt. Den här mellanlagrade uppdateringen gör att programmet kan fortsätta att köras under uppdateringen. Det ger också en mekanism för återställning om ett distributionsfel inträffar. 

I den här självstudien, som är del sex av sju, uppdateras Azure Vote-exempelappen. Här är några av uppgifterna:

> [!div class="checklist"]
> * Uppdatera klientdelens programkod
> * Skapa en uppdaterad behållaravbildning
> * Push-överföra behållaravbildningen till Azure Container Registry
> * Distribuera den uppdaterade behållaravbildningen

I senare självstudier konfigureras Operations Management Suite för att övervaka Kubernetes-klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades. Programmet kördes därefter i Kubernetes-klustret. 

En programlagringsplats klonades också, med programmets källkod och en färdig Docker Compose-fil som används i den här självstudien. Verifiera att du har skapat en klon av lagringsplatsen och att du har ändrat kataloger i den klonade katalogen. Inuti finns en katalog med namnet `azure-vote` och en fil med namnet `docker-compose.yml`.

Om du inte har slutfört dessa steg och vill följa med återgår du till [Självstudie 1 – Skapa behållaravbildningar](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Uppdatera programmet

För den här självstudien görs en ändring av programmet, och det uppdaterade programmet distribueras till Kubernetes-klustret. 

Programmets källkod finns i katalogen `azure-vote`. Öppna filen `config_file.cfg` i valfritt redigeringsprogram. I det här exemplet används `vi`.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Ändra värdena för `VOTE1VALUE` och `VOTE2VALUE` och spara filen.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Spara och stäng filen.

## <a name="update-container-image"></a>Uppdatera behållaravbildningen

Använd [docker-compose](https://docs.docker.com/compose/) till att skapa kilentdelsavbildningen igen och kör det uppdaterade programmet. Argumentet `--build` används till att instruera Docker Compose att skapa programavbildningen på nytt.

```bash
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

Använd [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) till att tagga avbildningen. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för Azure Container Registry eller värdnamnet för ett offentligt register. Lägg även märke till att avbildningsversionen har uppdaterats till `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Använd [docker push](https://docs.docker.com/engine/reference/commandline/push/) och ladda upp avbildningen till registret. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för Azure Container Registry.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Distribuera det uppdaterade programmet

Du får minimala störningar om flera instanser av programpodden körs. Verifiera konfigurationen med kommandot [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pod
```

Resultat:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Om du inte har flera poddar som kör avbildningen azure-vote-front skalar du ut `azure-vote-front`-distributionen.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

När du ska uppdatera programmet använder du kommandot [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set). Uppdatera `<acrLoginServer>` med inloggningsservern eller värdnamnet för ditt behållarregister.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Du övervakar distributionen med kommandot [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). Eftersom det uppdaterade programmet är distribuerat avslutas dina poddar och återskapas med den nya behållaravbildningen.

```azurecli-interactive
kubectl get pod
```

Resultat:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testa det uppdaterade programmet

Hämta den externa IP-adressen för tjänsten `azure-vote-front`.

```azurecli-interactive
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

Gå vidare till nästa självstudie om du vill lära dig hur du övervakar Kubernetes med Operations Management Suite.

> [!div class="nextstepaction"]
> [Övervaka Kubernetes med OMS (Monitor Kubernetes with OMS)](./container-service-tutorial-kubernetes-monitor.md)