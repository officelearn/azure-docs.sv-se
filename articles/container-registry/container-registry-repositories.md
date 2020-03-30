---
title: Visa databaser i portalen
description: Använd Azure-portalen för att visa Azure Container Registry-databaser, som är värd för Docker-behållaravbildningar och andra artefakter som stöds.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456291"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Visa behållarregisterdatabaser i Azure-portalen

Azure Container Registry kan du lagra Docker-behållaravbildningar i databaser. Genom att lagra bilder i databaser kan du lagra grupper av bilder (eller versioner av bilder) i isolerade miljöer. Du kan ange dessa databaser när du skickar avbildningar till registret och visar deras innehåll i Azure-portalen.

## <a name="prerequisites"></a>Krav

* **Behållarregistret**: Skapa ett behållarregister i din Azure-prenumeration. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI:** Installera [Docker][docker-install] på din lokala dator, som ger dig Docker kommandoradsgränssnitt.
* **Container avbildning:** Skicka en avbildning till behållarregistret. Vägledning om hur du trycker och hämtar bilder finns i [Tryck och dra en bild](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Visa databaser i Azure-portalen

Du kan se en lista över de databaser som är värdar för dina avbildningar, samt avbildningstaggarna, i Azure-portalen.

Om du följde stegen i [Push and pull an image](container-registry-get-started-docker-cli.md) (och inte tog bort avbildningen senare) bör du ha en Nginx-avbildning i behållarregistret. I instruktionerna i den artikeln angavs att du taggar bilden med `/samples/nginx`ett namnområde, "samples" i . Som en repetitionsåtgärd var [docker push-kommandot][docker-push] som angavs i den artikeln:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Eftersom Azure Container Registry stöder sådana namnområden för flera nivåer kan du begränsa samlingar av avbildningar som är relaterade till en viss app, eller en samling appar, till olika utvecklings- eller driftteam. Mer information om databaser i behållarregister finns i [Privata Docker-behållarregister i Azure](container-registry-intro.md).

Så här visar du en databas:

1. Logga in på [Azure-portalen][portal]
1. Välj **det Azure-behållarregister** som du tryckte Nginx-avbildningen till
1. Välj **Databaser om** du vill visa en lista över de databaser som innehåller avbildningarna i registret
1. Markera en databas om du vill visa bildtaggarna i databasen

Om du till exempel tryckte på Nginx-bilden enligt instruktionerna i [Tryck och dra en bild](container-registry-get-started-docker-cli.md)bör du se något som liknar:

![Databaser i portalen](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Nästa steg

Nu när du vet grunderna i att visa och arbeta med databaser i portalen kan du prova att använda Azure Container Registry med aks-klustret [(Azure Kubernetes Service).](../aks/tutorial-kubernetes-prepare-app.md)

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
