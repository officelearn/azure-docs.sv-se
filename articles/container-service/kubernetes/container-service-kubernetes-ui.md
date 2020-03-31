---
title: (FÖRÅLDRAD) Hantera Azure Kubernetes-kluster med webbgränssnittet
description: Använda webbgränssnittet Kubernetes i Azure Container Service
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371145"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(FÖRÅLDRAD) Använda webbgränssnittet Kubernetes med Azure Container Service

> [!TIP]
> Den uppdaterade versionen som används i den här artikeln som använder Azure Kubernetes-tjänsten finns [i Komma åt Kubernetes webbinstrumentpanel i Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
Den här genomgången förutsätter att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).


Det förutsätter också att du `kubectl` har Azure CLI och verktyg installerade.

Du kan testa om `az` du har verktyget installerat genom att köra:

```azurecli
az --version
```

Om du inte har `az` verktyget installerat finns det instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om `kubectl` du har verktyget installerat genom att köra:

```console
kubectl version
```

Om du inte `kubectl` har installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Översikt

### <a name="connect-to-the-web-ui"></a>Ansluta till webbgränssnittet
Du kan starta webbgränssnittet Kubernetes genom att köra:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Detta bör öppna en webbläsare konfigurerad för att prata med en säker proxy som ansluter din lokala dator till Kubernetes webbgränssnitt.

### <a name="create-and-expose-a-service"></a>Skapa och exponera en tjänst
1. Klicka på **Knappen Skapa** i det övre högra fönstret i webbgränssnittet Kubernetes.

    ![Skapa användargränssnitt i Kubernetes](./media/container-service-kubernetes-ui/create.png)

    En dialogruta öppnas där du kan börja skapa programmet.

2. Ge det `hello-nginx`namnet . Använd [ `nginx` behållaren från Docker](https://hub.docker.com/_/nginx/) och distribuera tre repliker av den här webbtjänsten.

    ![Dialogrutan Skapa kubernetes pod](./media/container-service-kubernetes-ui/nginx.png)

3. Under **Tjänst**väljer du **Extern** och anger port 80.

    Den här inställningen belastningsutjämnar trafik till de tre replikerna.

    ![Dialogrutan Skapa kubernetes-tjänst](./media/container-service-kubernetes-ui/service.png)

4. Klicka på **Distribuera** om du vill distribuera dessa behållare och tjänster.

    ![Kubernetes-distribution](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Visa dina behållare
När du har **klickat**på Distribuera visas en vy av tjänsten när den distribuerar:

![Kubernetes-status](./media/container-service-kubernetes-ui/status.png)

Du kan se status för varje Kubernetes-objekt i cirkeln till vänster i användargränssnittet under **Poddar**. Om det är en delvis full cirkel distribueras objektet fortfarande. När ett objekt är helt distribuerat visas en grön bock:

![Kubernetes har distribuerats](./media/container-service-kubernetes-ui/deployed.png)

När allt är igång klickar du på en av dina poddar för att se information om den löpande webbtjänsten.

![Kubernetes skida](./media/container-service-kubernetes-ui/pods.png)

I vyn **Poddar** kan du se information om behållarna i podden samt de CPU- och minnesresurser som används av dessa behållare:

![Kubernetes-resurser](./media/container-service-kubernetes-ui/resources.png)

Om du inte ser resurserna kan du behöva vänta några minuter på att övervakningsdata ska spridas.

Klicka på **Visa loggar**om du vill visa loggarna för din behållare .

![Kubernetes loggar](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visa din tjänst
Förutom att köra dina behållare har kubernetes-användargränssnittet skapat en extern `Service` som avväger en belastningsutjämnare för att föra trafik till behållarna i klustret.

I det vänstra navigeringsfönstret klickar du på **Tjänster** för att visa alla tjänster (det bör bara finnas en).

![Kubernetes Services](./media/container-service-kubernetes-ui/service-deployed.png)

I den vyn bör du se en extern slutpunkt (IP-adress) som har allokerats till din tjänst.
Om du klickar på ip-adressen bör du se din Nginx-behållare som körs bakom belastningsutjämnaren.

![nginx-vy](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Ändra storlek på tjänsten
Förutom att visa dina objekt i användargränssnittet kan du redigera och uppdatera Kubernetes API-objekt.

Klicka först på Distributioner i det vänstra **navigeringsfönstret** för att se distributionen för tjänsten.

När du är i vyn klickar du på replikuppsättningen och klickar sedan på **Redigera** i det övre navigeringsfältet:

![Kubernetes Redigera](./media/container-service-kubernetes-ui/edit.png)

Redigera `spec.replicas` fältet så `2`att det är och klicka på **Uppdatera**.

Detta medför att antalet repliker att sjunka till två genom att ta bort en av dina poddar.

 

