---
title: Azure Container Registry-databaser i Azure portal
description: Så här visar Azure Container Registry databaser i Azure-portalen.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 685c978ff206e75d770918f2528a826ad522b706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427113"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Visa container registry lagringsplatser i Azure portal

Azure Container Registry kan du lagra Docker-behållaravbildningar i databaser. Du kan lagra grupper av bilder (eller versioner av avbildningar) i isolerade miljöer genom att lagra bilder i databaser. Du kan ange dessa databaser när du överför avbildningar till registret och visa innehållet i Azure-portalen.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Behållarregister**: Skapa ett behållarregister i Azure-prenumerationen. Till exempel använda den [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Installera [Docker] [ docker-install] på den lokala datorn, vilket ger dig kommandoradsgränssnittet för Docker.
* **Behållaravbildning**: Push-överför avbildningen till behållarregistret. Anvisningar för hur du push och pull-avbildningar finns i [Push och pull en avbildning](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Visa lagringsplatser i Azure-portalen

Du kan se en lista över databaser som är värd för dina avbildningar, samt taggarna avbildning i Azure-portalen.

Om du har följt stegen i [Push och pull en avbildning](container-registry-get-started-docker-cli.md) (och inte senare att ta bort avbildningen), bör du ha en Nginx-avbildning i ditt behållarregister. Anvisningarna i den här artikeln anges du tagga avbildningen med ett namnområde, ”exempel” i `/samples/nginx`. Som en påminnelse kan du besöka den [docker push] [ docker-push] kommandot som angetts i den här artikeln var:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Eftersom Azure Container Registry har stöd för sådana namnområden för lagringsplatser på flera nivåer, kan du begränsa samlingar med avbildningar relaterade till en viss app eller en samling appar för olika utvecklingsgrupper eller operativa team. Mer information om databaserna i behållarregister finns i [privata Docker-behållarregister i Azure](container-registry-intro.md).

Visa en lagringsplats:

1. Logga in på den [Azure-portalen][portal]
1. Välj den **Azure Container Registry** som du push-överfört Nginx-avbildningen
1. Välj **databaser** att se en lista över databaser som innehåller bilder i registret
1. Välj en databas för att se taggarna i databasen

Till exempel om du push-överfört Nginx-avbildningen som finns beskrivet i [Push och pull en avbildning](container-registry-get-started-docker-cli.md), bör du se något som liknar:

![Databaser i portalen](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Nästa steg

Nu när du känner till grunderna för att visa och arbeta med databaser i portalen kan du prova att använda Azure Container Registry med ett [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) kluster.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
