---
title: FÖRÅLDRAD Hantera Azure Kubernetes-kluster med webb gränssnittet
description: Använda Kubernetes-webbgränssnittet i Azure Container Service
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371145"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>FÖRÅLDRAD Använda Kubernetes-webbgränssnittet med Azure Container Service

> [!TIP]
> Den uppdaterade versionen av den här artikeln som använder Azure Kubernetes-tjänsten finns i [komma åt Kubernetes-webbinstrumentpanelen i Azure Kubernetes service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Krav
I den här genom gången förutsätter vi att du har [skapat ett Kubernetes-kluster med Azure Container Service](container-service-kubernetes-walkthrough.md).


Det förutsätter också att du har installerat Azure CLI och `kubectl` verktyg.

Du kan testa om du har `az` installerat verktyget genom att köra:

```azurecli
az --version
```

Om du inte har `az` installerat verktyget finns det instruktioner [här](https://github.com/azure/azure-cli#installation).

Du kan testa om du har `kubectl` installerat verktyget genom att köra:

```console
kubectl version
```

Om du inte har `kubectl` installerat kan du köra:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Översikt

### <a name="connect-to-the-web-ui"></a>Anslut till webb gränssnittet
Du kan starta Kubernetes-webbgränssnittet genom att köra:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Detta bör öppna en webbläsare som kon figurer ATS för att kommunicera med en säker proxy som ansluter din lokala dator till Kubernetes-webbgränssnittet.

### <a name="create-and-expose-a-service"></a>Skapa och exponera en tjänst
1. Klicka på knappen **skapa** i det övre högra fönstret i Kubernetes-webbgränssnittet.

    ![Skapa användar gränssnitt för Kubernetes](./media/container-service-kubernetes-ui/create.png)

    En dialog ruta öppnas där du kan börja skapa ditt program.

2. Ge den namnet `hello-nginx` . Använd [ `nginx` behållaren från Docker](https://hub.docker.com/_/nginx/) och distribuera tre repliker av webb tjänsten.

    ![Kubernetes Pod skapa dialog](./media/container-service-kubernetes-ui/nginx.png)

3. Under **tjänst**väljer du **extern** och anger port 80.

    Den här inställningen läser in belastnings Utjämnings trafik till de tre replikerna.

    ![Dialog rutan skapa Kubernetes-tjänst](./media/container-service-kubernetes-ui/service.png)

4. Klicka på **distribuera** för att distribuera de här behållarna och tjänsterna.

    ![Kubernetes-distribution](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Visa dina behållare
När du har klickat på **distribuera**visar användar gränssnittet en vy över tjänsten när den distribueras:

![Status för Kubernetes](./media/container-service-kubernetes-ui/status.png)

Du kan se status för varje Kubernetes-objekt i cirkeln till vänster i användar gränssnittet under **poddar**. Om det är en delvis fullständig cirkel, distribueras objektet fortfarande. När ett objekt är fullständigt distribuerat visas en grön bock markering:

![Kubernetes distribueras](./media/container-service-kubernetes-ui/deployed.png)

När allt är igång klickar du på en av poddar för att se information om den webb tjänst som körs.

![Kubernetes poddar](./media/container-service-kubernetes-ui/pods.png)

I vyn **poddar** kan du se information om behållare i pod samt de processor-och minnes resurser som används av dessa behållare:

![Kubernetes-resurser](./media/container-service-kubernetes-ui/resources.png)

Om du inte ser resurserna kan du behöva vänta några minuter på att övervaknings data ska spridas.

Klicka på **Visa loggar**om du vill se en behållares loggar.

![Kubernetes-loggar](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visa din tjänst
Förutom att köra dina behållare har Kubernetes-gränssnittet skapat en extern `Service` som etablerar en belastningsutjämnare för att överföra trafik till behållarna i klustret.

I det vänstra navigerings fönstret klickar du på **tjänster** för att visa alla tjänster (det får bara finnas en).

![Kubernetes Services](./media/container-service-kubernetes-ui/service-deployed.png)

I den vyn bör du se en extern slut punkt (IP-adress) som har allokerats till din tjänst.
Om du klickar på den här IP-adressen bör du se din nginx-behållare som körs bakom belastningsutjämnaren.

![nginx vy](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Ändra storlek på din tjänst
Förutom att visa dina objekt i användar gränssnittet kan du redigera och uppdatera Kubernetes API-objekt.

Klicka först på **distributioner** i det vänstra navigerings fönstret för att se distributionen av tjänsten.

När du är i vyn klickar du på replik uppsättningen och sedan på **Redigera** i det övre navigerings fältet:

![Redigera Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Redigera `spec.replicas` fältet som ska vara `2` och klicka på **Uppdatera**.

Detta gör att antalet repliker kan släppas till två genom att en av dina poddar tas bort.

 

