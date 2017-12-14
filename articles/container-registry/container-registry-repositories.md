---
title: "Azure-behållaren registret databaser"
description: "Hur du använder Azure-behållare registret databaser för Docker bilder"
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: 3321dd1d8bbd1b8232c26491edd8c374df16b813
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-repositories"></a>Azure-behållaren registret databaser

Azure-behållaren registret kan du lagra avbildningar för behållare i databaser. Du kan ha grupper med bilder (eller version av avbildningar) i isolerade miljöer genom att lagra avbildningar i databaser. Du kan ange dessa databaser när du trycker på bilder i registret.


## <a name="prerequisites"></a>Krav
* **Azure-behållarregister** – Skapa ett behållarregister i din Azure-prenumeration. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI 2.0](container-registry-get-started-azure-cli.md).
* **Docker CLI** – Om du vill konfigurera den lokala datorn som en Docker-värd och komma åt Docker CLI-kommandona installerar du [Docker-motorn](https://docs.docker.com/engine/installation/).
* **Hämtar en bild** - dra en bild från offentliga Docker hubb registret tagga den och push i registret. Anvisningar om hur push och pull-avbildningar, se [Push Docker avbildningen till Azure privata registret](container-registry-get-started-docker-cli.md).


## <a name="viewing-repositories-in-the-portal"></a>Visa databaser i portalen

När du har pushas avbildningar till behållaren registret, kan du se en lista över databaser som är värd för avbildningar i Azure-portalen.

Om du har följt stegen i den [Push Docker avbildningen till Azure privata registret](container-registry-get-started-docker-cli.md) artikeln du bör nu ha en Nginx-avbildning i behållaren-registret. Som en del av instruktionerna, bör du har angett ett namnområde för avbildningen. I exemplet nedan skickar kommandot NGinx-avbildningen till databasen ”exempel”:

```
docker push myregistry.azurecr.io/samples/nginx
```
 Azure Container Registry har stöd för namnområden för lagringsplatser på flera nivåer. Den här funktionen gör att du kan gruppera samlingar med avbildningar relaterade till en viss app, eller en samling appar för specifika utvecklingsgrupper eller operativa team. Du kan läsa mer om databaserna i behållaren register finns [privata Docker behållare register i Azure](container-registry-intro.md).

Visa behållaren registret databaser:

1. Logga in på Azure Portal
2. På den **Azure Container registret** bladet Välj registret som du vill granska
3. I registret-bladet klickar du på **databaser** att se en lista över alla databaser och deras bilder
4. (Valfritt) Välj en viss bild att se taggar

![Databaserna i portalen](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>Nästa steg
Nu när du känner till grunderna är du redo att börja använda registret! Du kan till exempel börja distribuera behållaravbildningar till ett [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/)-kluster.
