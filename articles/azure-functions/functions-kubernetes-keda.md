---
title: Azure Functions på Kubernetes med KEDA
description: Lär dig hur du kör Azure Functions i Kubernetes i molnet eller lokalt med hjälp av KEDA, Kubernetes-baserad händelse driven autoskalning.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure Functions, functions, Event Processing, dynamisk beräkning, Server lös arkitektur, Kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096099"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions på Kubernetes med KEDA

Azure Functions runtime ger flexibilitet i värd och hur du vill.  [KEDA](https://github.com/kedacore/kore) (Kubernetes-baserade Event driven autoskalning) paras sömlöst med Azure Functions Runtime och verktyg för att tillhandahålla Event driven Scale i Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Hur Kubernetes-baserade funktioner fungerar

Azure Functionss tjänsten består av två viktiga komponenter: en körnings miljö och en skalnings styrenhet.  Functions-körningen körs och kör koden.  Körningen innehåller logik för att utlösa, logga och hantera funktions körningar.  Den andra komponenten är en skalnings styrenhet.  Skalnings styrenheten övervakar händelse frekvensen som är riktad mot din funktion och skalar i proaktivt antalet instanser som kör din app.  Läs mer i [Azure Functions skala och vara värd](functions-scale.md).

Kubernetes-baserade funktioner tillhandahåller Functions-körning i en Docker- [behållare](functions-create-function-linux-custom-image.md) med händelse driven skalning via KEDA.  KEDA kan skala ned till 0 instanser (när inga händelser inträffar) och upp till *n* instanser. Detta sker genom att de anpassade måtten exponeras för Kubernetes autoskalning (horisontell Pod autoskalning).  Med hjälp av Functions-behållare med KEDA kan du replikera Server lös funktions funktioner i ett Kubernetes-kluster.  Dessa funktioner kan också distribueras med funktionen [virtuella AKS-noder (Azure Kubernetes Services)](../aks/virtual-nodes-cli.md) för Server lös infrastruktur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Hantera KEDA och funktioner i Kubernetes

Om du vill köra funktioner i ditt Kubernetes-kluster måste du installera KEDA-komponenten. Du kan installera den här komponenten med hjälp av [Azure Functions Core tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installera med Azure Functions Core Tools

Som standard installerar kärn verktyg både KEDA-och Osiris-komponenter, som stöder händelse driven respektive HTTP-skalning.  Installationen `kubectl` körs i den aktuella kontexten.

Installera KEDA i klustret genom att köra följande installations kommando:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuera en Function-app till Kubernetes

Du kan distribuera valfri Function-app till ett Kubernetes-kluster som kör KEDA.  Eftersom dina funktioner körs i en Docker-behållare behöver ditt projekt en `Dockerfile`.  Om den inte redan har en, kan du lägga till en Dockerfile genom att köra följande kommando i roten för ditt Functions-projekt:

```cli
func init --docker-only
```

Om du vill skapa en avbildning och distribuera dina funktioner till Kubernetes kör du följande kommando:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Ersätt `<name-of-function-deployment>` med namnet på din Function-app.

Detta skapar en Kubernetes `Deployment` -resurs, `ScaledObject` en resurs och `Secrets` `local.settings.json` , som innehåller miljövariabler som importeras från filen.

## <a name="removing-a-function-app-from-kubernetes"></a>Ta bort en Function-app från Kubernetes

När du har distribuerat kan du ta bort en funktion `Deployment`genom `ScaledObject`att ta `Secrets` bort den associerade, en skapade.

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

KEDA finns för närvarande i beta version med stöd för följande Azure Function-utlösare:

* [Azure Storage köer](functions-bindings-storage-queue.md)
* [Azure Service Bus köer](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Skapa en funktion med en anpassad avbildning](functions-create-function-linux-custom-image.md)
* [Koda och testa Azure Functions lokalt](functions-develop-local.md)
* [Så här fungerar Azure Function förbrukning-planen](functions-scale.md)