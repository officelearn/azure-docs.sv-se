---
title: "Överför Docker-avbildningen till det privata Azure-registret | Microsoft-dokument"
description: "Skicka och hämta Docker-avbildningar till ett privat behållarregister i Azure med hjälp av Docker CLI"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: b6c26f28aa1e574ba3aabe53eda359cb6bf2edcc
ms.lasthandoff: 03/27/2017

---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Skicka din första avbildning till ett privat Docker-behållarregister med hjälp av Docker CLI
Ett Azure-behållarregister lagrar och hanterar privata [Docker](http://hub.docker.com)-behållaravbildningar, på samma sätt som [Docker Hub](https://hub.docker.com/) lagrar offentliga Docker-avbildningar. Du använder [Docker-kommandoradsgränssnittet](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) för att [logga in](https://docs.docker.com/engine/reference/commandline/login/), [skicka](https://docs.docker.com/engine/reference/commandline/push/) och [hämta](https://docs.docker.com/engine/reference/commandline/pull/), samt för andra åtgärder för behållarregistret.

Mer bakgrund och koncept beskrivs i [översikten](container-registry-intro.md)



## <a name="prerequisites"></a>Krav
* **Azure-behållarregister** – Skapa ett behållarregister i din Azure-prenumeration. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI 2.0](container-registry-get-started-azure-cli.md).
* **Docker CLI** – Om du vill konfigurera den lokala datorn som en Docker-värd och komma åt Docker CLI-kommandona installerar du [Docker-motorn](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Logga in i ett register
Kör `docker login` för att logga in till behållarregistret med dina [autentiseringsuppgifter för registret](container-registry-authentication.md).

I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/active-directory-application-objects.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Se till att du anger det fullständigt kvalificerade registernamnet (endast gemener). I det här exemplet är det `myregistry.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Steg för att hämta och skicka en avbildning
I följande exempel ser du hur du hämtar Nginx-avbildningen från det offentliga Docker Hub-registret, taggar den för ditt privata Azure-behållarregister, skickar den till registret och sedan hämtar den igen.

**1. Hämta den officiella Docker-avbildningen för Nginx**

Börja med att hämta den offentliga Nginx-avbildningen till den lokala datorn.

```
docker pull nginx
```
**2. Starta Nginx-behållaren**

Följande kommando startar den lokala Nginx-behållaren interaktivt på port 8080, så att du kan visa utdata från Nginx. Behållaren som körs tas bort när den stoppats.

```
docker run -it --rm -p 8080:80 nginx
```

Bläddra till [http://localhost:8080](http://localhost:8080) för att visa behållaren som körs. En skärm som liknar följande visas.

![Nginx på lokal dator](./media/container-registry-get-started-docker-cli/nginx.png)

Du stoppar behållaren som körs genom att trycka på [CTRL] + [C].

**3. Skapa ett alias för avbildningen i registret**

Följande kommando skapar ett alias för avbildningen, med en fullständigt kvalificerad sökväg till registret. I det här exemplet anges `samples`-namnområdet för att undvika oreda i registrets rot.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4. Skicka avbildningen till registret**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5. Hämta avbildningen från registret**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6. Starta Nginx-behållaren från registret**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Bläddra till [http://localhost:8080](http://localhost:8080) för att visa behållaren som körs.

Du stoppar behållaren som körs genom att trycka på [CTRL] + [C].

**7. (Valfritt) Ta bort avbildningen**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>Gränser för samtidiga anrop
I vissa situationer kan anrop som körs samtidigt resultera i fel. Följande tabell innehåller gränserna för samtidiga anrop med ”push”- och ”pull”-åtgärder mot Azure-behållarregistret:

| Åtgärd  | Gräns                                  |
| ---------- | -------------------------------------- |
| PULL       | Upp till 10 samtidiga pull-åtgärder per register |
| PUSH       | Upp till 5 samtidiga push-åtgärder per register |

## <a name="next-steps"></a>Nästa steg
Nu när du känner till grunderna är du redo att börja använda registret! Du kan till exempel börja distribuera behållaravbildningar till ett [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/)-kluster.

