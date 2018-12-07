---
title: (INAKTUELL) Hantera Azure Kubernetes-kluster med webbgränssnitt
description: Med Kubernetes webbgränssnitt i Azure Container Service
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c3a79b2e4fab807613a54d2792f5f5b97570293b
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996142"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(INAKTUELL) Med Azure Container Service Kubernetes webbgränssnitt

> [!TIP]
> Den uppdaterade versionen som den här artikeln som använder Azure Kubernetes Service, finns i [åt Kubernetes web-instrumentpanelen i Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här genomgången förutsätter att du har [skapade ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).


Den förutsätter också att du har Azure CLI och `kubectl` tools har installerats.

Du kan testa om du har den `az` verktyget installerat genom att köra:

```console
$ az --version
```

Om du inte har den `az` verktyget installerat, det finns anvisningar [här](https://github.com/azure/azure-cli#installation).

Du kan testa om du har den `kubectl` verktyget installerat genom att köra:

```console
$ kubectl version
```

Om du inte har `kubectl` installerat, kan du köra:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Översikt

### <a name="connect-to-the-web-ui"></a>Ansluta till webbgränssnittet
Du kan starta Kubernetes webbgränssnitt genom att köra:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Detta ska öppna en webbläsare som konfigurerats för att kommunicera med en säker proxy som ansluter din lokala dator till Kubernetes webbgränssnitt.

### <a name="create-and-expose-a-service"></a>Skapa och visa en tjänst
1. Klicka på i Kubernetes webbgränssnitt **skapa** knappen i det övre högra fönstret.

    ![Skapa Användargränssnittet för Kubernetes](./media/container-service-kubernetes-ui/create.png)

    En dialogruta öppnas där du kan börja skapa ditt program.

2. Ge den namnet `hello-nginx`. Använd den [ `nginx` behållare från Docker](https://hub.docker.com/_/nginx/) och distribuera tre repliker av den här webbtjänsten.

    ![Dialogrutan Skapa i Kubernetes-Pod](./media/container-service-kubernetes-ui/nginx.png)

3. Under **Service**väljer **externa** och ange port 80.

    Den här inställningen belastningsutjämnar trafik till de tre replikerna.

    ![Dialogrutan Skapa i Kubernetes-tjänst](./media/container-service-kubernetes-ui/service.png)

4. Klicka på **distribuera** att distribuera dessa behållare och tjänster.

    ![Distribuera Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Visa dina behållare
När du klickar på **distribuera**, Användargränssnittet visas en vy över din tjänst som distribueras:

![Kubernetes-Status](./media/container-service-kubernetes-ui/status.png)

Du kan se status för varje Kubernetes-objekt på cirkeln på vänster sida av Användargränssnittet under **Poddar**. Om det är en delvis fylld cirkel pågår objektet fortfarande. När ett objekt är fullständigt distribuerad, visas en grön bock:

![Kubernetes som distribuerats](./media/container-service-kubernetes-ui/deployed.png)

Klicka på en av dina poddar att se information om webbtjänsten som körs när allt körs.

![Kubernetes-Poddar](./media/container-service-kubernetes-ui/pods.png)

I den **Poddar** vy, visas information om behållare i en pod, samt processor- och resurser som används av de här behållarna:

![Kubernetes-resurser](./media/container-service-kubernetes-ui/resources.png)

Om du inte ser resurserna, kan du behöva vänta några minuter att sprida övervakningsdata.

Klicka för att visa loggarna för behållaren **visa loggar**.

![Kubernetes-loggar](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visa din tjänst
Förutom att köra dina behållare, Kubernetes UI har skapat en extern `Service` som etablerar en belastningsutjämnare för att ge trafik till behållarna i klustret.

I det vänstra navigeringsfönstret klickar du på **Services** att visa alla tjänster (det ska bara finnas en).

![Kubernetes-tjänster](./media/container-service-kubernetes-ui/service-deployed.png)

I vyn, bör du se en extern slutpunkt (IP-adress) som har allokerats till din tjänst.
Om du klickar på IP-adress, bör du se din Nginx-behållaren som körs bakom belastningsutjämnaren.

![Visa nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Ändra storlek på din tjänst
Förutom att visa objekten i Användargränssnittet, kan du redigera och uppdatera Kubernetes API-objekt.

Klicka först på **distributioner** i det vänstra navigeringsfönstret för att se distributionen för tjänsten.

När du är i vyn klickar du på uppsättningen och klickar sedan på **redigera** i det övre navigeringsfältet:

![Redigera Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Redigera den `spec.replicas` fältet ska vara `2`, och klicka på **uppdatering**.

Detta leder till antalet repliker för att lägga till två genom att ta bort en av dina poddar.

 

