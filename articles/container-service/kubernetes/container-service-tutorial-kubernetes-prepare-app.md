---
title: (INAKTUELL) Självstudie för Azure Container Service – Förbereda programmet
description: Självstudie för Azure Container Service – Förbereda programmet
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 961f09d7581a26596b03e7a2f8e97d98fe83e6aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217101"
---
# <a name="deprecated-create-container-images-to-be-used-with-azure-container-service"></a>(INAKTUELL) Skapa containeravbildningar som ska användas med Azure Container Service

> [!TIP]
> För den uppdaterade versionen av den här självstudien som använder Azure Kubernetes Service läser du[Självstudie: Förbereda ett program för Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-prepare-app.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

I den här självstudien, som är del ett av sju, förbereds ett program med flera behållare för användning i Kubernetes. Det här är några av stegen:  

> [!div class="checklist"]
> * Klona programkällan från GitHub  
> * Skapa en containeravbildning från programkällan
> * Testa programmet i en lokal Docker-miljö

När den är slutförd är följande program tillgängligt i din lokala utvecklingsmiljö.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

I efterföljande självstudier har behållaravbildningen laddats upp till ett Azure Container Registry och körs sedan i ett Azure-värdbaserat Kubernetes-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här självstudien förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som containrar, containeravbildningar och grundläggande docker-kommandon. Om det behövs kan du läsa [Get started with Docker]( https://docs.docker.com/get-started/) (Komma igång med Docker) för att få en genomgång av grunden för containrar. 

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i den här självstudien. Därför rekommenderar vi att du använder en fullständig Docker-utvecklingsmiljö.

## <a name="get-application-code"></a>Hämta programkod

Exempelprogrammet som används i den här självstudien är en enkel röstningsapp. Programmet består av en webbkomponent på klientsidan och en Redis-instans på serversidan. Webbkomponenten paketeras i en anpassad containeravbildning. Redis-instansen använder en oförändrad avbildning från Docker Hub.  

Använd git och ladda ned en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Ändra katalogerna så att du arbetar från den klonade katalogen.

```
cd azure-voting-app-redis
```

Inuti katalogen finns programmets källkod, en förskapad Docker Compose-fil och en Kubernetes-manifestfil. De här filerna används i hela självstudien. 

## <a name="create-container-images"></a>Skapa containeravbildningar

[Docker Compose](https://docs.docker.com/compose/) kan användas för att automatisera kompilering från containeravbildningar och distribution av program med flera containrar.

Kör filen `docker-compose.yml` för att skapa containeravbildningen, ladda ned Redis-avbildningen och starta programmet.

```bash
docker-compose up -d
```

När kommandot har körts kan du använda kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/) till att se de avbildningar som skapats.

```bash
docker images
```

Observera att tre avbildningar har laddats ned eller skapats. `azure-vote-front`-avbildningen innehåller programmet och använder `nginx-flask`-avbildningen som bas. `redis`-avbildningen används för att starta en Redis-instans.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Kör kommandot [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) för att se de containrar som körs.

```bash
docker ps
```

Utdata:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testa programmet lokalt

Gå till `http://localhost:8080` om du vill se programmet som körs.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Rensa resurser

Nu när programfunktionen har verifierats, kan containrarna som körs stoppas och tas bort. Ta inte bort containeravbildningarna. `azure-vote-front`-avbildningen överförs till en Azure Container Registry-instans i nästa självstudie.

Kör följande för att stoppa de containrar som körs.

```bash
docker-compose stop
```

Ta bort stoppade containrar och resurser med följande kommando.

```bash
docker-compose down
```

När du är klar har du en containeravbildning som innehåller programmet Azure Vote.

## <a name="next-steps"></a>Nästa steg

I den här självstudien testades ett program och containeravbildningar skapades för programmet. Följande steg har slutförts:

> [!div class="checklist"]
> * Klona programkällan från GitHub  
> * Skapade en containeravbildning från programkällan
> * Testade programmet i en lokal Docker-miljö

Fortsätt till nästa självstudie och lär dig om att lagra behållaravbildningar i ett Azure Container Registry.

> [!div class="nextstepaction"]
> [Push-avbildningar med Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
