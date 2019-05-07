---
title: De resurser som stöds för Azure Red Hat OpenShift | Microsoft Docs
description: Förstå vilka Azure-regioner och storlekar för virtuella datorer som stöds av Microsoft Azure Red Hat OpenShift.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 22cc195b7808ad4a9111aafcf883a68c51f3709f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076112"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift-resurser

Det här avsnittet visas de Azure-regioner och storlekar för virtuella datorer som stöds av Microsoft Azure Red Hat OpenShift-tjänsten.

## <a name="azure-regions"></a>Azure-regioner

Du kan distribuera Azure Red Hat OpenShift-kluster till följande Azure-regioner i hela världen:

|Region|CLI-kod|
|-|-|
|🇦🇺 Australien, östra|`australiaeast`|
|🇨🇦 Kanada, centrala|`canadacentral`|
|Kanada, Östra Kanada 🇨🇦|`canadaeast`|
|🇺🇸 Östra USA|`eastus`|
|🇺🇸 Västra USA|`westus`|
|🇪🇺 Västeuropa|`westeurope`|
|🇪🇺 Nordeuropa|`northeurope`|

## <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

Här följer stöds VM-storlekar som du kan ange för compute-noder i klustret Azure Red Hat OpenShift.

> [!Important]
> Varje virtuell dator har olika antal enheter som kan kopplas. Det kanske inte som omedelbart Rensa som minne eller CPU-storlek.
> Inte alla VM-storlekar är tillgängliga i alla regioner. Även om API: et har stöd för storleken som du anger, kan du få ett felmeddelande om storleken inte är tillgänglig i den region som du anger.
> Se [aktuell lista över stöds Virtuella datorer per region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) för mer information.

## <a name="compute-node-sizes"></a>Beräkna storleken på

I följande storleken på beräkningsnoder stöds av Azure Red Hat OpenShift REST-API:

|Storlek|Virtuell processor|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Huvudnoden storlekar

Följande master / infrastruktur nodstorlekar som stöds av Azure Red Hat OpenShift REST-API:

|Storlek|Virtuell processor|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Nästa steg

Prova den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.