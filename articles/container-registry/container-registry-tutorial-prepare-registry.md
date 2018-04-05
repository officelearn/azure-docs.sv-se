---
title: Självstudie om Azure Container Registry – förbereda ett georeplikerat Azure Container Registry
description: Skapa ett Azure-behållarregister, konfigurera geo-replikering, förbereda en Docker-avbildning och distribuera den till registret. Del ett av en serie i tre delar.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 3463acc3db3dae9633635aaf7410d876aacf9b38
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Självstudier: Förbereda ett geo-replikerat Azure Container Registry

Ett Azure-behållarregister är ett privat Docker-register som distribueras i Azure så att du kan lagra behållarregistren nära distributionerna i nätverket. I den här serien med tre självstudieartiklar får du lära dig att använda geo-replikering för att distribuera ett ASP.NET Core-webbprogram som körs i en Linux-behållare eller två [Web Apps for Containers](../app-service/containers/index.yml)-instanser. Du ser hur Azure automatiskt distribuerar avbildningen till varje webbappinstans från den närmaste geo-replikerade lagringsplatsen.

I den här självstudien ingår i en serie med tre delar:

> [!div class="checklist"]
> * Skapa ett georeplikerat Azure-behållarregister
> * Klona programmets källkod från GitHub
> * Skapa en Docker-behållaravbildning från programkällkoden
> * Överföra behållaravbildningen till registret

I efterföljande självstudier distribuerar du behållaren från ditt privata register till en webbapp som körs i två Azure-regioner. Du uppdaterar sedan koden i programmet, och uppdaterar båda Web App-instanserna med en enda `docker push` till registret.

## <a name="before-you-begin"></a>Innan du börjar

I den här självstudien krävs att du kör Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

Den här självstudien förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som behållare, behållaravbildningar och grundläggande docker-kommandon. Om det behövs kan du läsa [Get started with Docker]( https://docs.docker.com/get-started/) (Komma igång med Docker) för att få en genomgång av grunden för behållare.

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i den här självstudien. Vi rekommenderar därför en lokal installation av Azure CLI och Docker-utvecklingsmiljön.

> [!IMPORTANT]
> Funktionen för geo-replikering för Azure Container Registry är finns för närvarande i **förhandsversion**. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

Logga in på [Azure-portalen](http://portal.azure.com).

Välj **Skapa en resurs** > **Behållare** > **Azure Container Registry**.

![Skapa ett behållarregister i Azure-portalen][tut-portal-01]

Konfigurera ditt nya register med följande inställningar:

* **Registernamn**: Skapa ett registernamn som är globalt unikt i Azure och som innehåller 5–50 alfanumeriska tecken
* **Resursgrupp**: **Skapa ny** > `myResourceGroup`
* **Plats**: `West US`
* **Adminstratörsanvändare**: `Enable` (krävs för Web App for Containers för att hämta avbildningar)
* **SKU**: `Premium` (krävs för geo-replikering)

Välj **Skapa** för att distribuera ACR-instansen.

![Skapa ett behållarregister i Azure-portalen][tut-portal-02]

I resten av den här självstudien använder vi `<acrName>` som platshållare för det **behållarregisternamn** du väljer.

> [!TIP]
> Eftersom Azure-behållarregister vanligtvis är långlivade resurser som används av flera behållarvärdar rekommenderar vi att du skapar ditt register i en egen resursgrupp. När du konfigurerar georeplikerade register och webhooks placeras de här ytterligare resurserna i samma resursgrupp.
>

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering

Nu när du har ett Premium-register kan du konfigurera geo-replikering. Din webbapp, som du konfigurerar i nästa självstudie för att köras i två regioner, kan då hämta sina behållaravbildningar från det närmaste registret.

Gå till ditt nya behållarregister i Azure-portalen och välj **Replikeringar** under **TJÄNSTER**:

![Replikeringar i användargränssnittet i behållarregister i Azure-portalen][tut-portal-03]

En karta visas som visar gröna sexhörningar som representerar Azure-regioner som är tillgängliga för geo-replikering:

 ![Regionskarta i Azure Portal][tut-map-01]

Replikera ditt register till regionen USA, östra genom att välja dess gröna sexhörning. Välj sedan **Skapa** under **Skapa replikering**:

 ![Skapa replikerings-UI i Azure-portalen][tut-portal-04]

När replikeringen är slutförd visar portalen *Klar* för båda regionerna. Använd knappen **Uppdatera** för att uppdatera replikeringens status. Det kan ta en minut eller så innan replikerna har skapats och synkroniserats.

![Status för replikerings-UI i Azure-portalen][tut-portal-05]

## <a name="container-registry-login"></a>Logga in på behållarregistret

Nu när du har konfigurerat geo-replikering ska du skapa en behållaravbildning och push-överföra den till registret. Logga in på din ACR-instans innan du push-överför avbildningar till den. Med [SKU:er på nivån Basic, Standard och Premium](container-registry-skus.md) kan du autentisera med din Azure-identitet.

Använd kommandot [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) för att autentisera och cachelagra autentiseringsuppgifterna för registret. Ersätt `<acrName>` med namnet på registret som du skapade i föregående steg.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar `Login Succeeded` när det har slutförts.

## <a name="get-application-code"></a>Hämta programkod

Exemplet i den här självstudien innehåller en liten webbapp som är inbyggd i [ASP.NET Core](http://dot.net). Appen använder en HTML-sida som visar regionen som avbildningen distribuerades från av Azure Container Registry.

![Självstudieappen visas i webbläsare][tut-app-01]

Använd git för att ladda ned exemplet till en lokal katalog, och `cd` till katalogen:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Uppdatera Dockerfile

Den Dockerfile som finns i exemplet visar hur behållaren är byggd. Den startar från en officiell [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore)-avbildning, kopierar programfilerna till behållaren, installerar beroenden, kompilerar utdata med den officiella [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build)-avbildningen och skapar slutligen en optimerad aspnetcore-avbildning.

Dockerfile finns på `./AcrHelloworld/Dockerfile` i den klonade källan.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

Programmet i avbildningen *acr-helloworld* försöker bestämma region som dess behållare distribuerades från genom att fråga DNS om information om registrets inloggningsserver. Du måste ange URL-adressen till registrets inloggningsserver i miljövariabeln `DOCKER_REGISTRY` i Dockerfile.

Hämta först URL-adressen till registrets inloggningsserver med kommandot `az acr show`. Ersätt `<acrName>` med namnet på registret som du skapade i föregående steg.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Resultat:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Därefter uppdaterar du raden `DOCKER_REGISTRY` med registrets inloggningsserver-URL. I det här exemplet uppdaterar vi raden för att återspegla vårt exempelregisternamn, *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Skapa behållaravbildning

Nu när du har uppdaterat din Dockerfile med registrets URL kan du använda `docker build` för att skapa behållaravbildningen. Kör följande kommando för att skapa avbildningen och tagga den med URL-adressen till ditt privata register, där du återigen ersätter `<acrName>` med namnet på ditt register:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Flera rader med utdata visas när Docker-avbildningen har skapats (visas trunkerat här):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Använd kommandot `docker images` för att se den skapade avbildningen:

```bash
docker images
```

Resultat:

```bash
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Använd till slut kommandot `docker push` för att skicka avbildningen *acr-helloworld* till ditt register. Ersätt `<acrName>` med namnet på registret.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Eftersom du har konfigurerat registret för georeplikering replikeras avbildningen automatiskt till både regionerna *USA, västra* och *USA, östra* med kommandot `docker push`.

Resultat:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett privat, geo-replikerat behållarregister, skapat en behållaravbildning och sedan push-överfört avbildningen till ditt register. Genom att följa stegen i självstudien har du:

> [!div class="checklist"]
> * Skapat ett georeplikerat Azure-behållarregister
> * Klonat programmets källkod från GitHub
> * Skapat en Docker-behållaravbildning från programkällkoden
> * Överfört behållaravbildningen till registret

Fortsätt till nästa självstudie om du vill lära dig att distribuera behållaren till flera Web Apps for Containers-instanser och använda geo-replikering för att hantera avbildningarna lokalt.

> [!div class="nextstepaction"]
> [Distribuera webbapp från Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
