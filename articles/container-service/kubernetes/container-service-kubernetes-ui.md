---
title: Hantera Azure Kubernetes kluster med webbgränssnittet
description: Med hjälp av Kubernetes webbgränssnittet i Azure Container Service
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0680551d3a87c942574a4eac70fa380cc1e9b5d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163256"
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Med Azure Container Service Kubernetes webbgränssnittet

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här genomgången förutsätter att du har [skapas ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).


Det förutsätts även att du har Azure CLI 2.0 och `kubectl` verktygen som installeras.

Du kan testa om du har den `az` installerat genom att köra verktyget:

```console
$ az --version
```

Om du inte har den `az` verktyget är installerat, det finns instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om du har den `kubectl` installerat genom att köra verktyget:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Översikt

### <a name="connect-to-the-web-ui"></a>Ansluta till webbgränssnittet
Du kan starta Kubernetes webbgränssnittet genom att köra:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Detta ska öppna en webbläsare som konfigurerats för att kommunicera med en säker proxy ansluta din lokala dator till Kubernetes webbgränssnittet.

### <a name="create-and-expose-a-service"></a>Skapa och visa en tjänst
1. Klicka på Kubernetes webbgränssnittet **skapa** knappen i det övre högra fönstret.

    ![Kubernetes skapa gränssnitt](./media/container-service-kubernetes-ui/create.png)

    En öppnas dialogrutan där du kan börja skapa ditt program.

2. Ge den namnet `hello-nginx`. Använd den [ `nginx` behållare från Docker](https://hub.docker.com/_/nginx/) och distribuera tre repliker av den här webbtjänsten.

    ![Dialogrutan Skapa i Kubernetes baljor](./media/container-service-kubernetes-ui/nginx.png)

3. Under **Service**väljer **externa** och ange port 80.

    Den här inställningen belastningsutjämnas trafik till tre replikerna.

    ![Dialogrutan Skapa i Kubernetes Service](./media/container-service-kubernetes-ui/service.png)

4. Klicka på **distribuera** att distribuera dessa behållare och tjänster.

    ![Distribuera Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Visa behållarna
När du klickar på **distribuera**, Gränssnittet visas en vy över din tjänst som distribueras:

![Kubernetes Status](./media/container-service-kubernetes-ui/status.png)

Du kan se status för varje Kubernetes objekt i cirkeln på vänster sida av Användargränssnittet under **skida**. Om det är en delvis fylld cirkel distribuera objektet fortfarande. När ett objekt har distribuerats helt, visas en grön bock:

![Kubernetes distribueras](./media/container-service-kubernetes-ui/deployed.png)

Klicka på en av dina skida att se information om webbtjänsten körs när allt körs.

![Kubernetes skida](./media/container-service-kubernetes-ui/pods.png)

I den **skida** kan du se information om behållare i baljor samt resurserna CPU och minne som används av dessa behållare:

![Kubernetes resurser](./media/container-service-kubernetes-ui/resources.png)

Om du inte ser resurserna, kan du behöva vänta några minuter att sprida övervakningsdata.

Klicka för att visa loggar för din behållaren **visa loggar**.

![Kubernetes loggar](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visa din tjänst
Förutom att köra behållarna Kubernetes UI har skapat en extern `Service` som etablerar en belastningsutjämnare för att göra trafik behållare i klustret.

I det vänstra navigeringsfönstret klickar du på **Services** att visa alla tjänster (det ska bara finnas ett).

![Kubernetes tjänster](./media/container-service-kubernetes-ui/service-deployed.png)

I vyn, bör du se en extern slutpunkt (IP-adress) som har allokerats till din tjänst.
Om du klickar på att IP-adress, bör du se din Nginx-behållaren som körs bakom belastningsutjämnaren.

![nginx-vyn](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Ändra storlek på tjänsten
Förutom att visa objekten i Användargränssnittet, kan du redigera och uppdatera Kubernetes API-objekt.

Klicka först på **distributioner** i det vänstra navigeringsfönstret för att se distributionen för tjänsten.

När du är i vyn klickar du på uppsättningen och klicka sedan på **redigera** i det övre navigeringsfältet:

![Redigera Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Redigera den `spec.replicas` fältet ska vara `2`, och klicka på **uppdatering**.

Detta gör antalet repliker till två genom att ta bort en av dina skida.

 

