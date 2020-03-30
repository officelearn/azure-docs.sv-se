---
title: Azure-funktioner på Kubernetes med KEDA
description: Förstå hur du kör Azure-funktioner i Kubernetes i molnet eller lokalt med KEDA, Kubernetes-baserad händelsedriven automatisk skalning.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301683"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure-funktioner på Kubernetes med KEDA

Azure Functions-körningen ger flexibilitet i värd var och hur du vill.  [KEDA](https://keda.sh) (Kubernetes-baserade Event Driven Autoscaling) parar sömlöst med Azure Functions runtime och verktyg för att tillhandahålla händelsedriven skala i Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Så här fungerar Kubernetes-baserade funktioner

Azure Functions-tjänsten består av två nyckelkomponenter: en körning och en skalningsstyrenhet.  Funktionerna kör och kör koden.  Körningen innehåller logik om hur du utlöser, loggar och hanterar funktionskörningar.  Azure Functions-körningen kan *köras var som helst*.  Den andra komponenten är en skalstyrenhet.  Skalkontrollanten övervakar antalet händelser som är inriktade på din funktion och skalar proaktivt antalet instanser som kör appen.  Mer information finns i [Azure Functions skala och vara värd .](functions-scale.md)

Kubernetes-baserade funktioner ger funktioner runtime i en [Docker-behållare](functions-create-function-linux-custom-image.md) med händelsedriven skalning genom KEDA.  KEDA kan skala in till 0 instanser (när inga *n* händelser inträffar) och ut till n-instanser. Detta gör detta genom att exponera anpassade mått för Kubernetes autoskalningsapparat (Horizontal Pod Autoscaler).  Genom att använda funktionsbehållare med KEDA kan du replikera serverlösa funktionsfunktioner i alla Kubernetes-kluster.  Dessa funktioner kan också distribueras med hjälp av [azure kubernetes services (AKS) virtuella noder](../aks/virtual-nodes-cli.md) funktion för serverlös infrastruktur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Hantera KEDA och funktioner i Kubernetes

Om du vill köra Funktioner i Kubernetes-klustret måste du installera KEDA-komponenten. Du kan installera den här komponenten med Hjälp av [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Installera med Helm

Det finns olika sätt att installera KEDA i alla Kubernetes kluster inklusive Helm.  Distributionsalternativen dokumenteras på [KEDA-platsen](https://keda.sh/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuera en funktionsapp till Kubernetes

Du kan distribuera alla funktionsappar till ett Kubernetes-kluster som kör KEDA.  Eftersom dina funktioner körs i en Docker-behållare behöver projektet en `Dockerfile`.  Om den inte redan har någon kan du lägga till en Dockerfile genom att köra följande kommando i roten för projektet Functions:

```cli
func init --docker-only
```

Om du vill skapa en avbildning och distribuera dina funktioner till Kubernetes kör du följande kommando:

> [!NOTE]
> Core Tools kommer att utnyttja docker CLI för att bygga och publicera avbildningen. Se till att docker redan är installerad och ansluten till ditt konto med `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Ersätt `<name-of-function-deployment>` med namnet på funktionsappen.

Detta skapar `Deployment` en Kubernetes-resurs, en `ScaledObject` resurs och `Secrets`, som `local.settings.json` innehåller miljövariabler som importerats från filen.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Distribuera en funktionsapp från ett privat register

Ovanstående flöde fungerar även för privata register.  Om du drar din behållaravbildning från `--pull-secret` ett privat register, inkludera flaggan som refererar till `func kubernetes deploy`Kubernetes hemlighet som innehar de privata registerautentiseringsuppgifterna när du kör .

## <a name="removing-a-function-app-from-kubernetes"></a>Ta bort en funktionsapp från Kubernetes

När du har distribuerat kan du `Deployment` `ScaledObject`ta `Secrets` bort en funktion genom att ta bort den associerade , , en skapad.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Avinstallera KEDA från Kubernetes

Steg för att avinstallera KEDA dokumenteras [på KEDA-webbplatsen](https://keda.sh/deploy/).

## <a name="supported-triggers-in-keda"></a>Triggers som stöds i KEDA

KEDA har stöd för följande Azure-funktionsutlösare:

* [Azure Storage-köer](functions-bindings-storage-queue.md)
* [Azure Service busköer](functions-bindings-service-bus.md)
* [Azure-händelse/ IoT-hubbar](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ Kö](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Stöd för HTTP-utlösare

Du kan använda Azure Functions som exponerar HTTP-utlösare, men KEDA hanterar dem inte direkt.  Du kan använda KEDA prometheus-utlösaren för att [skala HTTP Azure Functions från 1 till *n-instanser* ](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Efterföljande moment
Mer information finns i följande resurser:

* [Skapa en funktion med en anpassad bild](functions-create-function-linux-custom-image.md)
* [Koda och testa Azure Functions lokalt](functions-develop-local.md)
* [Så här fungerar Azure Function Consumption-planen](functions-scale.md)