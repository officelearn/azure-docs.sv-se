---
title: Azure Functions på Kubernetes med KEDA
description: Lär dig hur du kör Azure Functions i Kubernetes i molnet eller lokalt med hjälp av KEDA, Kubernetes-baserad händelse driven autoskalning.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 9978bd567b1b07e8dd0e22e1f02834626281a5dd
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920668"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions på Kubernetes med KEDA

Azure Functions runtime ger flexibilitet i värd och hur du vill.  [KEDA](https://keda.sh) (Kubernetes Event drived autoskalning) är sömlöst med Azure Functions Runtime och verktyg för att tillhandahålla händelse driven Scale i Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Hur Kubernetes-baserade funktioner fungerar

Azure Functionss tjänsten består av två viktiga komponenter: en körnings miljö och en skalnings styrenhet.  Functions-körningen körs och kör koden.  Körningen innehåller logik för att utlösa, logga och hantera funktions körningar.  Azure Functions körningen kan köras *var som helst*.  Den andra komponenten är en skalnings styrenhet.  Skalnings styrenheten övervakar händelse frekvensen som är riktad mot din funktion och skalar i proaktivt antalet instanser som kör din app.  Läs mer i [Azure Functions skala och vara värd](functions-scale.md).

Kubernetes-baserade funktioner tillhandahåller Functions-körning i en [Docker-behållare](functions-create-function-linux-custom-image.md) med händelse driven skalning via KEDA.  KEDA kan skala ned till 0 instanser (när inga händelser inträffar) och upp till *n* instanser. Detta sker genom att de anpassade måtten exponeras för Kubernetes autoskalning (horisontell Pod autoskalning).  Med hjälp av Functions-behållare med KEDA kan du replikera Server lös funktions funktioner i ett Kubernetes-kluster.  Dessa funktioner kan också distribueras med funktionen [virtuella AKS-noder (Azure Kubernetes Services)](../aks/virtual-nodes-cli.md) för Server lös infrastruktur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Hantera KEDA och funktioner i Kubernetes

Om du vill köra funktioner i ditt Kubernetes-kluster måste du installera KEDA-komponenten. Du kan installera den här komponenten med hjälp av [Azure Functions Core tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installera med Azure Functions Core Tools

Som standard installerar kärn verktyg både KEDA-och Osiris-komponenter, som stöder händelse driven respektive HTTP-skalning.  Installationen använder `kubectl` som körs i den aktuella kontexten.

Installera KEDA i klustret genom att köra följande installations kommando:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuera en Function-app till Kubernetes

Du kan distribuera valfri Function-app till ett Kubernetes-kluster som kör KEDA.  Eftersom dina funktioner körs i en Docker-behållare behöver projektet en `Dockerfile`.  Om den inte redan har en, kan du lägga till en Dockerfile genom att köra följande kommando i roten för ditt Functions-projekt:

```cli
func init --docker-only
```

Om du vill skapa en avbildning och distribuera dina funktioner till Kubernetes kör du följande kommando:

> [!NOTE]
> Kärn verktygen använder Docker CLI för att bygga och publicera avbildningen. Se till att Docker redan är installerat och är anslutna till ditt konto med `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Ersätt `<name-of-function-deployment>` med namnet på din Function-app.

Detta skapar en Kubernetes `Deployment` resurs, en `ScaledObject` resurs och en `Secrets`, som innehåller miljövariabler som importeras från `local.settings.json`s filen.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Distribuera en Function-app från ett privat register

Ovanstående flöde fungerar även för privata register.  Om du hämtar behållar avbildningen från ett privat register ska du ta med den `--pull-secret` flagga som refererar till den Kubernetes hemlighet som innehåller de privata autentiseringsuppgifterna för registret när du kör `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Ta bort en Function-app från Kubernetes

När du har distribuerat kan du ta bort en funktion genom att ta bort den associerade `Deployment``ScaledObject`en `Secrets` som skapats.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Avinstallerar KEDA från Kubernetes

Du kan köra följande Core tools-kommando för att ta bort KEDA från ett Kubernetes-kluster:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Utlösare som stöds i KEDA

KEDA har stöd för följande Azure Function-utlösare:

* [Azure Storage-köer](functions-bindings-storage-queue.md)
* [Azure Service Bus köer](functions-bindings-service-bus.md)
* [Azure Event/IoT-hubbar](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-kö](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Stöd för HTTP-utlösare

Du kan använda Azure Functions som exponerar HTTP-utlösare, men KEDA inte direkt hanterar dem.  Azure Functions Core Tools installerar ett relaterat projekt, Osiris, som möjliggör skalning av HTTP-slutpunkter från 0 till 1.  Skalning från 1 till *n* förlitar sig på traditionella skalnings principer för Kubernetes.

## <a name="next-steps"></a>Efterföljande moment
Mer information finns i följande resurser:

* [Skapa en funktion med en anpassad avbildning](functions-create-function-linux-custom-image.md)
* [Koda och testa Azure Functions lokalt](functions-develop-local.md)
* [Så här fungerar Azure Function förbrukning-planen](functions-scale.md)