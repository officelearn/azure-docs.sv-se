---
title: "Självstudiekurs för Azure Container Service - uppdateringsprogrammet"
description: "Självstudiekurs för Azure Container Service - uppdateringsprogrammet"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5ecaa3a79270e29ac002e91065f7df4f7e8914e7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="update-an-application-in-kubernetes"></a>Uppdatera ett program i Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

När ett program har distribuerats i Kubernetes, kan den uppdateras genom att ange en ny behållare bild eller Bildversion. När du gör det mellanlagras uppdateringen så att endast en del av distributionen uppdateras samtidigt. Stegvis kan du använda programmet för att fortsätta att köras under uppdateringen. Det ger också en mekanism för återställning om ett distributionsfel inträffar. 

I den här självstudiekursen tillhör sex sju, har Azure röst exempelappen uppdaterats. Uppgifterna som du har slutfört är:

> [!div class="checklist"]
> * Uppdatering av programkoden som frontend
> * Skapa en bild av uppdaterade behållare
> * Push-installation behållaren avbildningen till registret för Azure-behållare
> * Distribuera avbildningen uppdaterade behållare

I efterföljande självstudiekurser är Operations Management Suite konfigurerad för att övervaka Kubernetes klustret.

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudier, ett program som har paketerats i en behållare avbildning, bilden överförs till registret för Azure-behållaren och ett Kubernetes kluster skapas. Programmet körs sedan Kubernetes klustret. 

En program-databas har också klona som innehåller programmets källkod och en förskapad Docker Compose-fil som används i den här kursen. Kontrollera att du har skapat en klon av lagringsplatsen och att du har ändrat kataloger till katalogen klonade. I är en katalog med namnet `azure-vote` och en fil med namnet `docker-compose.yml`.

Om du inte har utfört stegen och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Uppdatera program

En ändring görs till programmet och det uppdaterade programmet distribueras till Kubernetes klustret för den här självstudiekursen. 

Programmets källkod finns inuti den `azure-vote` directory. Öppna den `config_file.cfg` fil med någon kod eller textredigerare. I det här exemplet `vi` används.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Ändra värdena för `VOTE1VALUE` och `VOTE2VALUE`, och sedan spara filen.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Spara och stäng filen.

## <a name="update-container-image"></a>Uppdatera behållaren avbildning

Använd [docker compose](https://docs.docker.com/compose/) att återskapa frontend avbildningen och köra programmet uppdaterade. Den `--build` argument används för att instruera Docker Compose för att återskapa programavbildning.

```bash
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

Använd [docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) att tagga avbildningen. Ersätt `<acrLoginServer>` med Azure Container registret inloggningsnamnet server eller offentliga registret värdnamn. Även Lägg märke till att Bildversion uppdateras till `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Använd [docker push](https://docs.docker.com/engine/reference/commandline/push/) att överföra avbildningen till registret. Ersätt `<acrLoginServer>` med server för Azure-behållare registret inloggningsnamn.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Distribuera program för uppdatering

För att säkerställa högsta drifttid, måste du köra flera instanser av programmet baljor. Kontrollera konfigurationen med den [kubectl hämta baljor](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) kommando.

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

Om du inte har flera skida kör azure-röst-framför bilden skala den `azure-vote-front` distribution.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Uppdatera programmet med den [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) kommando. Uppdatera `<acrLoginServer>` med inloggningsnamnet för server eller värd för behållaren registret.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Du övervakar distributionen av [kubectl hämta baljor](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) kommando. Eftersom uppdaterade programmet distribueras avslutas din skida och återskapas med den nya behållare avbildningen.

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

## <a name="test-updated-application"></a>Testa uppdaterade program

Hämta externa IP-adressen för den `azure-vote-front` service.

```azurecli-interactive
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
> [Övervaka Kubernetes med OMS (Monitor Kubernetes with OMS)](./container-service-tutorial-kubernetes-monitor.md)