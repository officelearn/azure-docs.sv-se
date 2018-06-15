---
title: Azure Container registret databaser i Azure-portalen
description: Hur du visar Azure Container registret databaser i Azure-portalen.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 171593483fc94c1c67013ab520b0085ca98f3a82
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33768336"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Visa behållaren registret databaser i Azure-portalen

Azure Container registret kan du lagra Docker behållare bilder i databaser. Du kan lagra grupper med bilder (eller versioner av avbildningar) i isolerade miljöer genom att lagra avbildningar i databaser. Du kan ange dessa databaser när du push-avbildningar till registret och visa innehållet i Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

* **Behållaren registret**: skapa en behållare registret i din Azure-prenumeration. Till exempel använda den [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Installera [Docker] [ docker-install] på den lokala datorn, vilket ger dig Docker-kommandoradsgränssnittet.
* **Behållaren image**: Push en bild i registret för behållaren. Anvisningar om hur du push och pull-avbildningar finns [Push och pull en bild](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Visa databaser i Azure-portalen

Du kan se en lista över databaser som är värd för dina bilder, samt taggarna avbildning i Azure-portalen.

Om du har följt stegen i [Push och pull en bild](container-registry-get-started-docker-cli.md) (och inte därefter ta bort bilden) bör du ha en Nginx-avbildning i behållaren-registret. Anvisningarna i artikeln anges du tagga avbildningen med ett namnområde, ”exempel” i `/samples/nginx`. Som en uppdaterare av [docker push] [ docker-push] kommandot som anges i den artikeln var:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Eftersom Azure Container registret har stöd för sådana flera nivåer databasen namnområden, kan du definiera samlingar bilder i samband med en viss app eller en mängd olika utveckling eller operativa team-appar. Du kan läsa mer om databaserna i behållaren register finns [privata Docker behållare register i Azure](container-registry-intro.md).

Visa en databas:

1. Logga in på den [Azure-portalen][portal]
1. Välj den **Azure Container registret** som du pushas Nginx-bild
1. Välj **databaser** att se en lista över databaser som innehåller bilder i registret
1. Välj en databas för att se Bildtaggar i databasen

Till exempel om du pushas Nginx-avbildning som beskrivet i [Push och pull en bild](container-registry-get-started-docker-cli.md), bör du se något som liknar:

![Databaserna i portalen](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Nästa steg

Nu när du känner till grunderna för att visa och arbeta med databaser i portalen, försök med Azure Container registret med en [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) klustret.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
