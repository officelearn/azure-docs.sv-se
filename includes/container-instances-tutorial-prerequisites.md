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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67187901"
---
Du måste uppfylla följande krav för att kunna slutföra den här självstudiekursen:

**Azure CLI**: Du måste ha Azure CLI-version 2.0.29 eller senare installerad på den lokala datorn. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI][azure-cli-install].

**Docker**: Den här självstudien förutsätter grundläggande kunskaper om grundläggande Docker-koncept som behållare, behållar avbildningar och grundläggande `docker` kommandon. Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker][docker-get-started].

**Docker-motor**: Du måste ha Docker-motorn installerad lokalt för att kunna slutföra den här självstudien. Docker innehåller paket som konfigurerar Docker-miljön på [MacOS][docker-mac], [Windows][docker-windows]och [Linux][docker-linux].

> [!IMPORTANT]
> Eftersom Azure Cloud Shell inte innehåller Docker-daemon *måste* du installera både Azure CLI och Docker-motorn på din *lokala dator* för att kunna fullfölja den här självstudiekursen. Du kan använda Azure Cloud Shell för den här självstudiekursen.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
