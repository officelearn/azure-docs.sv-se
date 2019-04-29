---
title: ta med fil
description: ta med fil
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684216"
---
Du måste uppfylla följande krav för att kunna slutföra den här självstudiekursen:

**Azure CLI**: Du måste ha Azure CLI version 2.0.29 eller senare installerat på den lokala datorn. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI][azure-cli-install].

**Docker**: Den här självstudiekursen förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som behållare, behållaravbildningar och grundläggande `docker` kommandon. En introduktion till grunderna för Docker och containrar finns i [Docker-översikt][docker-get-started].

**Docker-motorn**: Den här kursen måste Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS][docker-mac], [Windows][docker-windows] och [Linux] [docker-linux].

> [!IMPORTANT]
> Eftersom Azure Cloud Shell inte innehåller Docker-daemon *måste* du installera både Azure CLI och Docker-motorn på din *lokala dator* för att kunna fullfölja den här självstudiekursen. Du kan använda Azure Cloud Shell för den här självstudiekursen.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
