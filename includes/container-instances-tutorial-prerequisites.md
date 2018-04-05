---
title: ta med fil
description: ta med fil
services: container-instances
author: mmacy
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 95a8cf4eca7969631d069a31c9d08ba52c8b02b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
Du måste uppfylla följande krav för att kunna slutföra den här självstudiekursen:

**Azure CLI**: Du måste ha Azure CLI version 2.0.29 eller senare installerat på den lokala datorn. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0][azure-cli-install].

**Docker**: Den här självstudiekursen förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som behållare, behållaravbildningar och grundläggande `docker`-kommandon. En introduktion till grunderna för Docker och behållare finns i [Docker-översikt][docker-get-started].

**Docker-motorn**: Om du ska kunna genomföra den här kursen måste Docker-motorn ha installerats lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS][docker-mac], [Windows][docker-windows] och [Linux] [docker-linux].

> [!IMPORTANT]
> Eftersom Azure Cloud Shell inte innehåller Docker-daemon *måste* du installera både Azure CLI och Docker-motorn på din *lokala dator* för att kunna fullfölja den här självstudiekursen. Du kan använda Azure Cloud Shell för den här självstudiekursen.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli