---
title: Självstudie om Kubernetes i Azure – Förbereda ett program
description: I den här självstudien om Azure Kubernetes Service (AKS) lär du dig hur du förbereder och skapar en app med flera containrar med Docker Compose som du sedan kan distribuera till AKS.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b529af1db6d72d87abc25eb37f2f1c39216a0ba4
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724172"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Självstudier: Förbered ett program för Azure Kubernetes Service (AKS)

I den här självstudien, som är del ett av sju, förbereds ett program med flera behållare för användning i Kubernetes. Befintliga utvecklingsverktyg som till exempel Docker Compose används för att skapa och testa ett program lokalt. Lär dig att:

> [!div class="checklist"]
> * Klona en exempelprogramkälla från GitHub
> * Skapa en containeravbildning från exempelprogramkällan
> * Testa programmet med flera containrar i en lokal Docker-miljö

När det är klart körs följande program i din lokala utvecklingsmiljö:

![Bild av Kubernetes-kluster i Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

I ytterligare självstudier så överförs containeravbildningen till en Azure Container Registry och distribueras därefter till ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här självstudiekursen förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som containrar, containeravbildningar och `docker`-kommandon. Läs mer om grunderna för containrar i [Kom igång med Docker][docker-get-started].

För att slutföra den här självstudien behöver du en lokal Docker-utvecklingsmiljö som kör Linux-containrar. Docker tillhandahåller paket som konfigurerar Docker i ett [Mac][docker-for-mac]-, [Windows][docker-for-windows]- eller [Linux][docker-for-linux]-system.

Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i dessa självstudier. Därför rekommenderar vi att du använder en fullständig Docker-utvecklingsmiljö.

## <a name="get-application-code"></a>Hämta programkod

Exempelprogrammet som används i den här självstudien är en enkel röstningsapp. Programmet består av en webbkomponent på klientsidan och en Redis-instans på serversidan. Webbkomponenten paketeras i en anpassad containeravbildning. Redis-instansen använder en oförändrad avbildning från Docker Hub.

Använd [git][] för att klona exempelprogrammet till utvecklingsmiljön:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Ändra till den klonade katalogen.

```console
cd azure-voting-app-redis
```

Inuti katalogen finns programmets källkod, en förskapad Docker Compose-fil och en Kubernetes-manifestfil. De här filerna används i hela självstudien.

## <a name="create-container-images"></a>Skapa containeravbildningar

[Docker Compose][docker-compose] kan användas för att automatisera genereringen av containeravbildningar och distributionen av program med flera containrar.

Använd `docker-compose.yaml`-exempelfilen för att skapa containeravbildningen, ladda ned Redis-avbildningen och starta programmet:

```console
docker-compose up -d
```

När kommandot har körts kan du använda kommandot [docker images][docker-images] till att se de avbildningar som skapats. Tre avbildningar har hämtats eller skapats. *azure-vote-front*-avbildningen innehåller frontend-programmet och använder `nginx-flask`-avbildningen som bas. `redis`-avbildningen används för att starta en Redis-instans.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Kör kommandot [docker ps][docker-ps] för att visa de containrar som körs:

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testa programmet lokalt

Om du vill visa programmet som körs anger du http://localhost:8080 i en lokal webbläsare. Exempelprogrammet läses in, som du ser i följande exempel:

![Bild av Kubernetes-kluster i Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Rensa resurser

Nu när programfunktionen har verifierats, kan containrarna som körs stoppas och tas bort. Ta inte bort containeravbildningarna – i nästa självstudie ska du ladda upp *azure-vote-front*-avbildningen till en Azure Container Registry-instans.

Stoppa och ta bort containerinstanser och resurser med kommandot [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

När det lokala programmet har tagits bort, har du en Docker-avbildning som innehåller Azure Vote-programmet, *azure-front-front*, som används i nästa självstudie.

## <a name="next-steps"></a>Nästa steg

I den här självstudien testades ett program och containeravbildningar skapades för programmet. Du har lärt dig att:

> [!div class="checklist"]
> * Klona en exempelprogramkälla från GitHub
> * Skapa en containeravbildning från exempelprogramkällan
> * Testa programmet med flera containrar i en lokal Docker-miljö

Gå vidare till nästa självstudie och lär dig hur du lagrar containeravbildningar i Azure Container Registry.

> [!div class="nextstepaction"]
> [Push-överföra avbildningar till Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
