---
title: "Push-Docker-avbildning till registret för privat Azure"
description: "Skicka och hämta Docker-avbildningar till ett privat behållarregister i Azure med hjälp av Docker CLI"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21d1abfbb49eaeae654a600d35ab350b96a12fd3
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Skicka din första avbildning till ett privat Docker-behållarregister med hjälp av Docker CLI

Ett Azure-behållarregister lagrar och hanterar privata [Docker](http://hub.docker.com)-behållaravbildningar, på samma sätt som [Docker Hub](https://hub.docker.com/) lagrar offentliga Docker-avbildningar. Du kan använda den [Docker-kommandoradsgränssnittet](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) för [inloggning](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/), och andra åtgärder på din behållare registret.

I följande steg ska du hämta ett officiellt [Nginx bild](https://store.docker.com/images/nginx) från offentliga Docker-hubb-registret tagga för privat Azure-behållaren registret push i registret och dra den från registret.

## <a name="prerequisites"></a>Krav

* **Azure-behållarregister** – Skapa ett behållarregister i din Azure-prenumeration. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI 2.0](container-registry-get-started-azure-cli.md).
* **Docker CLI** – om du vill konfigurera den lokala datorn som en Docker-värd och få åtkomst till Docker CLI-kommandon, installera [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Logga in i ett register

Det finns [flera olika sätt att autentisera](container-registry-authentication.md) privata behållare-registret. När du arbetar på en kommandorad rekommenderas med Azure CLI-kommandot [az acr inloggning](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Till exempel att logga in på ett register med namnet *myregistry*:

```azurecli
az acr login --name myregistry
```

Du kan också logga in med [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/). I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/active-directory-application-objects.md). Du kan till exempel ha [tilldelas ett huvudnamn för tjänsten](container-registry-authentication.md#service-principal) i registret för ett automation-scenario.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Båda kommandon returnera `Login Succeeded` när den har slutförts. Om du använder `docker login`, kan du också se en säkerhetsvarning rekommenderar användning av den `--password-stdin` parameter. När användningen är utanför omfånget för den här artikeln, rekommenderar vi följande denna bästa praxis. Mer information finns i [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot referens.

> [!TIP]
> Ange alltid registret fullständigt kvalificerade namnet (gemener) när du använder `docker login` och när du tagga avbildningar för push-installation till registret. I exemplen i den här artikeln är det fullständigt kvalificerade namnet *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Hämta den officiella Nginx-bilden

Först hämta den offentliga Nginx-avbildningen till den lokala datorn.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Kör behållaren lokalt

Kör följande [docker kör](https://docs.docker.com/engine/reference/run/) kommando för att starta en lokal instans av Nginx-behållaren interaktivt (`-it`) på port 8080. Den `--rm` argumentet anger att behållaren ska tas bort när du stoppar den.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Bläddra till [http://localhost: 8080](http://localhost:8080) att visa standardwebbsidan hanteras av Nginx-behållare körs. Du bör se en sida som liknar följande:

![Nginx på lokal dator](./media/container-registry-get-started-docker-cli/nginx.png)

Eftersom du startade behållaren interaktivt med `-it`, du kan se utdata från Nginx-servern på kommandoraden efter att navigera till den i webbläsaren.

Stoppa och ta bort behållaren genom att trycka på `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Skapa ett alias till bilden

Använd [docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) att skapa ett alias till bilden med den fullständiga sökvägen till registret. I det här exemplet anges `samples`-namnområdet för att undvika oreda i registrets rot.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Mer information om taggar med namnområden finns i [databasen namnområden](container-registry-best-practices.md#repository-namespaces) avsnitt i [bästa praxis för Azure-behållare registernyckeln](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Push-avbildningen till registret

Nu när du har lagt till taggar avbildningen med den fullständiga sökvägen till din privata registret, du kan pressa i registret med [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Hämta bilden från registret

Använd den [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) kommando för att hämta bilden från registret:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Starta Nginx-behållaren

Använd den [docker kör](https://docs.docker.com/engine/reference/run/) kommandot ska köras på bilden som du har hämtas från registret:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Bläddra till [http://localhost:8080](http://localhost:8080) för att visa behållaren som körs.

Stoppa och ta bort behållaren genom att trycka på `Control` + `C`.

## <a name="remove-the-image-optional"></a>Ta bort alla avbildningar (valfritt)

Om du behöver inte längre Nginx-avbildning, du kan ta bort det lokalt med den [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) kommando.

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Ta bort filer från Azure-behållaren registret, kan du använda kommandot Azure CLI [az acr databasen ta bort](/cli/azure/acr/repository#az_acr_repository_delete). Till exempel följande kommando tar bort manifestet refererar till en tagg och alla associerade lagerdata andra taggar som refererar till manifestet.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Nästa steg

Nu när du känner till grunderna är du redo att börja använda registret! Till exempel distribuera behållaren bilder från registret för att en [Azure Container Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) klustret.
