---
title: "Azure Container registret tutorial – förbereda en georeplikerad Azure-behållaren registret"
description: "Skapa en Azure-behållaren registret, konfigurera geo-replikering, förbereda en Docker-avbildning och distribuera den till registret. En del i en serie med tre delar."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker behållare, registret, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d751bf99cd081ea83928981db4c42bcacf4c27af
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>Förbereda ett geo-replikerade Azure-behållaren register

En Azure-behållaren är ett privat Docker-register som distribueras i Azure som du kan hålla nätverket Stäng dina distributioner. I den här uppsättningen med tre självstudiekursen artiklar du lära dig hur du använder geo-replikering för att distribuera ett ASP.NET Core webbprogram som körs i en Linux-behållare till två [Web Apps för behållare](../app-service/containers/index.yml) instanser. Du ser hur Azure automatiskt distribuerar avbildningen till varje förekomst av webbprogrammet från närmaste georeplikerad databasen.

I den här kursen ingår i en serie med tre delar:

> [!div class="checklist"]
> * Skapa ett geo-replikerade Azure-behållaren register
> * Klona programmets källkod från GitHub
> * Skapa en Docker behållaren image från programmet källa
> * Push-behållaren avbildningen till registret

I efterföljande självstudiekurser distribuera behållaren från din privata registret till ett webbprogram som körs i två Azure-regioner. Du sedan uppdatera koden i programmet och uppdatera både Web App-instanser med en enda `docker push` i registret.

## <a name="before-you-begin"></a>Innan du börjar

Den här kursen kräver att du använder Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

Den här självstudien förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som behållare, behållaravbildningar och grundläggande docker-kommandon. Om det behövs kan du läsa [Get started with Docker]( https://docs.docker.com/get-started/) (Komma igång med Docker) för att få en genomgång av grunden för behållare.

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

Azure Cloud Shell inkluderar inte Docker-komponenter som krävs för att slutföra varje steg den här kursen. Därför rekommenderar vi en lokal installation av Azure CLI och Docker-utvecklingsmiljö.

> [!IMPORTANT]
> Funktionen geo-replikering i Azure Container registret är för närvarande i **preview**. Förhandsgranskningar görs tillgängliga för dig under förutsättning att du godkänner den [kompletterande villkor för användning](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av den här funktionen kan ändras innan allmän tillgänglighet (GA).
>

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

Logga in på [Azure Portal](http://portal.azure.com).

Välj **nya** > **behållare** > **Azure-behållaren registret**.

![Skapa en behållare registret i Azure-portalen][tut-portal-01]

Konfigurera nya registret med följande inställningar:

* **Registrets**: skapa ett namn i registret som globalt unikt i Azure och innehåller 5 50 alfanumeriska tecken
* **Resursgruppen**: **Skapa nytt** > `myResourceGroup`
* **Plats**:`West US`
* **Administratörsanvändare**: `Enable` (krävs för webbprogrammet för behållare och hämtar bilder)
* **SKU**: `Premium` (krävs för geo-replikering)

Välj **skapa** att distribuera ACR-instans.

![Skapa en behållare registret i Azure-portalen][tut-portal-02]

I resten av den här kursen använder vi `<acrName>` som platshållare för behållaren **registrets** som du har valt.

> [!TIP]
> Eftersom Azure-behållaren register är vanligtvis långlivade resurser som används på flera värdar i behållare, rekommenderar vi att du skapar registret i sin egen resursgruppen. När du konfigurerar georeplikerad register och webhooks placeras dessa ytterligare resurser i samma resursgrupp.
>

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering

Nu när du har en Premium-registret kan du konfigurera geo-replikering. Ditt webbprogram som du konfigurerar i nästa kurs ska köras i två regioner, dra sedan dess behållare bilder från närmaste registret.

Navigera till din nya behållare registret i Azure portal och välj **replikeringar** under **SERVICES**:

![Replikeringar i Azure portal behållaren registret UI][tut-portal-03]

En karta visas med grön Sexhörningar som representerar Azure-regioner för geo-replikering:

 ![Region kartan i Azure-portalen][tut-map-01]

Replikera i registret till östra USA genom att välja den gröna Sexhörning och välj sedan **skapa** under **skapa replikering**:

 ![Skapa replikering Användargränssnittet i Azure-portalen][tut-portal-04]

När replikeringen är klar portalen visar *klar* för båda regioner. Använd den **uppdatera** knappen för att uppdatera status för replikering; det kan ta någon minut så för att replikerna skapas och synkroniseras.

![Replikeringsstatus Användargränssnittet i Azure-portalen][tut-portal-05]

## <a name="container-registry-login"></a>Behållaren registret inloggning

Nu när du har konfigurerat geo-replikering kan skapa en avbildning av behållaren och push i registret. Du måste först logga in till din ACR-instansen innan du skickar bilder till den. Med [Basic, Standard och Premium-SKU: er](container-registry-skus.md), du kan autentisera med hjälp av din Azure identitet.

Använd den [az acr inloggning](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) kommandot för att autentisera och cachelagrar autentiseringsuppgifter för registret. Ersätt `<acrName>` med namnet på registret som du skapade i föregående steg.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar `Login Succeeded` när du är klar.

## <a name="get-application-code"></a>Hämta programkod

Exemplet i den här självstudiekursen innehåller en liten webbprogram som skapats med [ASP.NET Core](http://dot.net). Appen fungerar en HTML-sida som visar den region som distribuerades avbildningen av registret för Azure-behållare.

![Självstudieappen visas i webbläsare][tut-app-01]

Använda git för att hämta exemplet i en lokal katalog och `cd` till katalogen:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Uppdatera Dockerfile

Dockerfile som ingår i exemplet visar hur behållaren har skapats. Den startar från ett officiellt [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore) bilden, kopierar programmet filer till behållare och installerar beroenden, kompilerar utdata med hjälp av officiellt [aspnetcore build](https://store.docker.com/community/images/microsoft/aspnetcore-build) bilden, och slutligen skapar en optimerad aspnetcore-bild.

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

Programmet i den *acr helloworld* bild försöker identifiera den region där dess behållare har distribuerats genom att fråga DNS om information om i registret inloggningsserver. Du måste ange i registret inloggnings server-URL i den `DOCKER_REGISTRY` miljövariabeln i Dockerfile.

Först hämta i registret inloggningen server URL: en med den `az acr show` kommando. Ersätt `<acrName>` med namnet på registret som du skapade i föregående steg.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Resultat:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Därefter uppdaterar du den `DOCKER_REGISTRY` raden med server i registret inloggnings-URL. I det här exemplet vi uppdaterar raden för att återspegla våra exempel registret namn, *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Skapa behållaren image

Nu när du har uppdaterat Dockerfile med URL-Adressen till registret, kan du använda `docker build` att skapa behållaren avbildningen. Kör följande kommando för att skapa avbildningen och tagga med URL-Adressen för din privata registret, Ersätt återigen `<acrName>` med namnet på registret:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Flera rader på utdata visas som Docker-avbildning är uppbyggd (visas här trunkerat):

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

Använd den `docker images` kommandot för att se den inbyggda avbildningen:

```bash
docker images
```

Resultat:

```bash
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Push-avbildningen till registret för Azure-behållare

Använd slutligen den `docker push` kommandot för att skicka den *acr helloworld* avbildningen till registret. Ersätt `<acrName>` med namnet på registret.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Eftersom du har konfigurerat i registret för geo-replikering replikeras automatiskt avbildningen till både den *västra USA* och *östra USA* regioner med den här enda `docker push` kommando.

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

I den här självstudiekursen skapas ett privat, georeplikerad behållare register som skapat en avbildning av behållare, och sedan pushas avbildningen till registret. Genom att följa stegen i kursen får du:

> [!div class="checklist"]
> * Skapa ett geo-replikerade Azure-behållaren register
> * Klonat programkällkod från GitHub
> * Bygger en Docker-behållare avbildning från programmet källa
> * Pushas behållaren avbildningen till registret

Gå vidare till nästa kurs mer information om hur du distribuerar din behållare till flera webbprogram för behållare instanser med geo-replikering för att hantera avbildningar lokalt.

> [!div class="nextstepaction"]
> [Distribuera webbapp från Azure-behållare registret](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
