---
title: Självstudie – Skapa ett geo-replikerat Docker-register i Azure
description: Skapa ett Azure-containerregister, konfigurera geo-replikering, förbereda en Docker-avbildning och distribuera den till registret. Del ett av en serie i tre delar.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 07e328c022e8e81782902445fd8fc6e320625a51
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978957"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Självstudie: Förbereda ett georeplikerat Azure Container Registry

Ett Azure-containerregister är ett privat Docker-register som distribueras i Azure så att du kan lagra containerregistren nära distributionerna i nätverket. I den här serien med tre självstudieartiklar får du lära dig att använda geo-replikering för att distribuera ett ASP.NET Core-webbprogram som körs i en Linux-behållare eller två [Web Apps for Containers](../app-service/containers/index.yml)-instanser. Du ser hur Azure automatiskt distribuerar avbildningen till varje webbappinstans från den närmaste geo-replikerade lagringsplatsen.

I den här självstudien ingår i en serie med tre delar:

> [!div class="checklist"]
> * Skapa ett georeplikerat Azure-containerregister
> * Klona programmets källkod från GitHub
> * Skapa en Docker-containeravbildning från programkällkoden
> * Överföra containeravbildningen till registret

I efterföljande självstudier distribuerar du containern från ditt privata register till en webbapp som körs i två Azure-regioner. Du uppdaterar sedan koden i programmet, och uppdaterar båda Web App-instanserna med en enda `docker push` till registret.

## <a name="before-you-begin"></a>Innan du börjar

För den här självstudien krävs en lokal installation av Azure CLI (version 2.0.31 eller senare). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

Du bör känna till viktiga Docker-begrepp som containrar, containeravbildningar och grundläggande Docker CLI-kommandon. Läs mer om grunderna för containrar i [Kom igång med Docker]( https://docs.docker.com/get-started/).

I den här självstudien behöver du en lokal Docker-installation. I Docker finns installationsanvisningar för [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i den här självstudien. Vi rekommenderar därför en lokal installation av Azure CLI och Docker-utvecklingsmiljön.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Logga in på [Azure-portalen](http://portal.azure.com).

Välj **Skapa en resurs** > **Behållare** > **Azure Container Registry**.

![Skapa ett containerregister i Azure-portalen][tut-portal-01]

Konfigurera ditt nya register med följande inställningar:

* **Registernamn**: Skapa ett registernamn som är globalt unikt i Azure och som innehåller 5–50 alfanumeriska tecken
* **Resursgrupp**: **Skapa ny** > `myResourceGroup`
* **Plats**: `West US`
* **Adminstratörsanvändare**: `Enable` (krävs för Web App for Containers för att hämta avbildningar)
* **SKU**: `Premium` (krävs för geo-replikering)

Välj **Skapa** för att distribuera ACR-instansen.

![Skapa ett containerregister i Azure-portalen][tut-portal-02]

I resten av den här självstudien använder vi `<acrName>` som platshållare för det **containerregisternamn** du väljer.

> [!TIP]
> Eftersom Azure-containerregister vanligtvis är långlivade resurser som används av flera containervärdar rekommenderar vi att du skapar ditt register i en egen resursgrupp. När du konfigurerar georeplikerade register och webhooks placeras de här ytterligare resurserna i samma resursgrupp.
>

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering

Nu när du har ett Premium-register kan du konfigurera geo-replikering. Din webbapp, som du konfigurerar i nästa självstudie för att köras i två regioner, kan då hämta sina containeravbildningar från det närmaste registret.

Gå till ditt nya containerregister i Azure-portalen och välj **Replikeringar** under **TJÄNSTER**:

![Replikeringar i användargränssnittet i containerregister i Azure-portalen][tut-portal-03]

En karta visas som visar gröna sexhörningar som representerar Azure-regioner som är tillgängliga för geo-replikering:

 ![Regionskarta i Azure Portal][tut-map-01]

Replikera ditt register till regionen USA, östra genom att välja dess gröna sexhörning. Välj sedan **Skapa** under **Skapa replikering**:

 ![Skapa replikerings-UI i Azure-portalen][tut-portal-04]

När replikeringen är slutförd visar portalen *Klar* för båda regionerna. Använd knappen **Uppdatera** för att uppdatera replikeringens status. Det kan ta en minut eller så innan replikerna har skapats och synkroniserats.

![Status för replikerings-UI i Azure-portalen][tut-portal-05]

## <a name="container-registry-login"></a>Logga in på containerregistret

Nu när du har konfigurerat geo-replikering ska du skapa en containeravbildning och push-överföra den till registret. Logga in på din ACR-instans innan du push-överför avbildningar till den.

Använd kommandot [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) för att autentisera och cachelagra autentiseringsuppgifterna för registret. Ersätt `<acrName>` med namnet på registret som du skapade tidigare.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar `Login Succeeded` när det har slutförts.

## <a name="get-application-code"></a>Hämta programkod

Exemplet i den här självstudien innehåller en liten webbapp som är skapad med [ASP.NET Core][aspnet-core]. Appen använder en HTML-sida som visar regionen som avbildningen distribuerades från av Azure Container Registry.

![Självstudieappen visas i webbläsare][tut-app-01]

Använd git för att ladda ned exemplet till en lokal katalog, och `cd` till katalogen:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Om du inte har `git` installerat, kan du [hämta ZIP-arkivet][acr-helloworld-zip] direkt från GitHub.

## <a name="update-dockerfile"></a>Uppdatera Dockerfile

Den Dockerfile som finns i exemplet visar hur containern är byggd. Den startar från en officiell [aspnetcore][dockerhub-aspnetcore]-avbildning, kopierar programfilerna till containern, installerar beroenden, kompilerar utdatan med den officiella [aspnetcore-build][dockerhub-aspnetcore-build]-avbildningen och skapar slutligen en optimerad aspnetcore-avbildning.

[Dockerfile][dockerfile] finns på `./AcrHelloworld/Dockerfile` i den klonade källan.

```Dockerfile
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

Programmet i avbildningen *acr-helloworld* försöker bestämma region som dess container distribuerades från genom att fråga DNS om information om registrets inloggningsserver. Du måste ange det fullständiga domännamnet (FQDN) för registrets inloggningsserver i miljövariabeln `DOCKER_REGISTRY` i Dockerfile.

Hämta först registrets inloggningsserver med kommandot `az acr show`. Ersätt `<acrName>` med namnet på registret som du skapade i föregående steg.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Resultat:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Därefter uppdaterar du raden `ENV DOCKER_REGISTRY` med FQDN för registrets inloggningsserver. Det här exemplet visar exempelregistrets namn *uniqueregistryname*:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Skapa containeravbildning

Nu när du har uppdaterat Dockerfile med FQDN för registrets inloggningsserver, kan du använda `docker build` för att skapa containeravbildningen. Kör följande kommando för att skapa avbildningen och tagga den med URL-adressen till ditt privata register, där du återigen ersätter `<acrName>` med namnet på ditt register:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Flera rader med utdata visas när Docker-avbildningen har skapats (visas trunkerat här):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Använd `docker images` för att se den skapade och taggade avbildningen:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Använd därefter kommandot `docker push` för att skicka avbildningen *acr-helloworld* till ditt register. Ersätt `<acrName>` med namnet på registret.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Eftersom du har konfigurerat registret för georeplikering replikeras avbildningen automatiskt till både regionerna *USA, västra* och *USA, östra* med kommandot `docker push`.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
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

I den här självstudien har du skapat ett privat, geo-replikerat containerregister, skapat en containeravbildning och sedan push-överfört avbildningen till ditt register.

Fortsätt till nästa självstudie för att distribuera behållaren till flera Web Apps for Containers-instanser, samt använd geo-replikering för att hantera avbildningarna lokalt.

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

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: http://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile