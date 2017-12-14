---
title: "Kubernetes på Azure kursen - uppdateringsprogrammet"
description: AKS kursen - uppdateringsprogrammet
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5399fa40542fd9a1163654d5619cb94029bc3c6f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="update-an-application-in-azure-container-service-aks"></a>Uppdatera ett program i Azure Container Service (AKS)

När ett program har distribuerats i Kubernetes, kan den uppdateras genom att ange en ny behållare bild eller Bildversion. När du gör det mellanlagras uppdateringen så att endast en del av distributionen uppdateras samtidigt. Stegvis kan du använda programmet för att fortsätta att köras under uppdateringen. Det ger också en mekanism för återställning om ett distributionsfel inträffar. 

I den här självstudiekursen del sex åtta har Azure röst exempelappen uppdaterats. Uppgifterna som du har slutfört är:

> [!div class="checklist"]
> * Uppdatering av programkoden som frontend
> * Skapa en bild av uppdaterade behållare
> * Push-installation behållaren avbildningen till registret för Azure-behållare
> * Distribuera avbildningen uppdaterade behållare

I efterföljande självstudiekurser är Operations Management Suite konfigurerad för att övervaka Kubernetes klustret.

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudier, ett program som har paketerats i en behållare avbildning, bilden överförs till registret för Azure-behållaren och ett Kubernetes kluster skapas. Programmet körs sedan Kubernetes klustret. 

En program-databas har också klona som innehåller programmets källkod och en förskapad Docker Compose-fil som används i den här kursen. Kontrollera att du har skapat en klon av lagringsplatsen och att du har ändrat kataloger till katalogen klonade. I är en katalog med namnet `azure-vote` och en fil med namnet `docker-compose.yml`.

Om du inte har utfört stegen och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder][aks-tutorial-prepare-app]. 

## <a name="update-application"></a>Uppdatera program

En ändring görs till programmet och det uppdaterade programmet distribueras till Kubernetes klustret för den här självstudiekursen. 

Programmets källkod finns inuti den `azure-vote` directory. Öppna den `config_file.cfg` fil med någon kod eller textredigerare. I det här exemplet `vi` används.

```console
vi azure-vote/azure-vote/config_file.cfg
```

Ändra värdena för `VOTE1VALUE` och `VOTE2VALUE`, och sedan spara filen.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Spara och stäng filen.

## <a name="update-container-image"></a>Uppdatera behållaren avbildning

Använd [docker compose] [ docker-compose] att återskapa frontend avbildningen och köra programmet uppdaterade. Den `--build` argument används för att instruera Docker Compose för att återskapa programavbildning.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testa programmet lokalt

Bläddra till http://localhost: 8080 att se det uppdaterade programmet.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Taggen och push-avbildningar

Taggen i `azure-vote-front` avbildningen med loginServer av registret i behållaren. 

Hämta servernamn inloggningen med den [az acr lista](/cli/azure/acr#list) kommando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd [docker-taggen] [ docker-tag] att tagga avbildningen. Ersätt `<acrLoginServer>` med Azure Container registret inloggningsnamnet server eller offentliga registret värdnamn. Även Lägg märke till att Bildversion uppdateras till `redis-v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Använd [docker push] [ docker-push] att överföra avbildningen till registret. Ersätt `<acrLoginServer>` med server för Azure-behållare registret inloggningsnamn.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Distribuera program för uppdatering

För att säkerställa högsta drifttid, måste du köra flera instanser av programmet baljor. Kontrollera konfigurationen med den [kubectl hämta baljor] [ kubectl-get] kommando.

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

Om du inte har flera skida kör azure-röst-framför bilden skala den `azure-vote-front` distribution.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Uppdatera programmet med den [kubectl set] [ kubectl-set] kommando. Uppdatera `<acrLoginServer>` med inloggningsnamnet för server eller värd för behållaren registret.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Du övervakar distributionen av [kubectl hämta baljor] [ kubectl-get] kommando. Eftersom uppdaterade programmet distribueras avslutas din skida och återskapas med den nya behållare avbildningen.

```azurecli
kubectl get pod
```

Resultat:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testa uppdaterade program

Hämta externa IP-adressen för den `azure-vote-front` service.

```azurecli
kubectl get service azure-vote-front
```

Bläddra till IP-adressen finns det uppdaterade programmet.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen, uppdatera ett program och distribuerat den här uppdateringen till ett kluster som Kubernetes. Följande uppgifter har slutförts:

> [!div class="checklist"]
> * Uppdatera frontend programkoden
> * Skapa en bild av uppdaterade behållare
> * Pushas behållaren avbildningen till registret för Azure-behållare
> * Distribuerat uppdaterade program

Gå vidare till nästa kurs att lära dig hur du övervakar Kubernetes med Operations Management Suite.

> [!div class="nextstepaction"]
> [Övervakaren Kubernetes med logganalys][aks-tutorial-monitor]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-monitor]: ./tutorial-kubernetes-monitor.md