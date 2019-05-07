---
title: Azure Functions i Kubernetes med KEDA
description: Du ska kunna köra Azure Functions i Kubernetes i molnet eller lokalt med hjälp av KEDA, Kubernetes-baserade händelsedriven automatisk skalning.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077627"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions i Kubernetes med KEDA

Azure Functions-runtime ger flexibilitet i som är värd för var och hur du vill.  [KEDA](https://github.com/kedacore/kore) (Kubernetes-baserade Event Driven automatisk skalning) kan användas sömlöst med Azure Functions runtime och verktyg för att tillhandahålla händelsedriven skala i Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Hur Kubernetes-baserade fungerar

Azure Functions-tjänsten består av två viktiga komponenter: en runtime och en skala-styrenhet.  Functions-körningen körs och kör din kod.  Körningen innehåller logik att utlösa, logga in och hantera funktionskörningar.  Den andra komponenten körs på en skala.  Kontrollanten skala övervakar frekvensen för händelser som riktar in sig på din funktion och skalas proaktivt antalet instanser som kör appen.  Mer information finns i [Azure Functions skalning och värdtjänster](functions-scale.md).

Kubernetes-baserade Functions tillhandahåller Functions-körning i en [dockerbehållare](functions-create-function-linux-custom-image.md) med en händelsedriven skalning via KEDA.  KEDA kan skala ned till 0-instanser (när inga händelser inträffar) och upp till *n* instanser. Detta sker genom att exponera anpassade mått för Kubernetes autoskalningen (vågrät Pod Autoskalningen).  Med hjälp av funktioner behållare med KEDA gör det möjligt att replikera funktion funktioner i valfritt Kubernetes-kluster.  Dessa funktioner kan också distribueras med hjälp av [Azure Kubernetes Services (AKS) virtuella noder](../aks/virtual-nodes-cli.md) funktionen för serverfria infrastruktur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Hantera KEDA och funktioner i Kubernetes

Om du vill köra Functions på Kubernetes-kluster, måste du installera komponenten KEDA. Du kan installera den här komponenten med [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installera med Azure Functions Core Tools

Som standard installeras Core Tools både KEDA och Osiris komponenter som stöder en händelsedriven och skalning av HTTP-respektive.  Installationen använder `kubectl` som körs i den aktuella kontexten.

Installera KEDA i ditt kluster genom att köra följande installationskommando:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuera en funktionsapp till Kubernetes

Du kan distribuera alla funktionsappar till ett Kubernetes-kluster som kör KEDA.  Eftersom dina funktioner körs i en Docker-behållare, ditt projekt måste en `Dockerfile`.  Om det inte redan har en, kan du lägga till en Dockerfile genom att köra följande kommando i roten för din Functions-projekt:

```cli
func init --docker-only
```

Om du vill skapa en avbildning och distribuera dina funktioner till Kubernetes, kör du följande kommando:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Ersätt `<name-of-function-deployment>` med namnet på din funktionsapp.

Detta skapar en Kubernetes `Deployment` resurs, en `ScaledObject` resursen, och `Secrets`, vilket inkluderar miljövariabler som importerats från din `local.settings.json` fil.

## <a name="removing-a-function-app-from-kubernetes"></a>Ta bort en funktionsapp från Kubernetes

När du distribuerar du kan ta bort en funktion genom att ta bort den associerade `Deployment`, `ScaledObject`, en `Secrets` skapas.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Avinstallera KEDA på Kubernetes

Du kan köra kommandot core verktyg för att ta bort KEDA från ett Kubernetes-kluster:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Stöds utlösare i KEDA

KEDA är för närvarande i beta med stöd för följande Azure Function-utlösare:

* [Azure Storage Queues](functions-bindings-storage-queue.md)
* [Azure Service Bus-köer](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Skapa en funktion med en anpassad avbildning](functions-create-function-linux-custom-image.md)
* [Koda och testa Azure Functions lokalt](functions-develop-local.md)
* [Hur fungerar Azure Function-förbrukningsplan](functions-scale.md)