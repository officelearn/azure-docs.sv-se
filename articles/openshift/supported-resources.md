---
title: Resurser som stöds för Azure Red Hat OpenShift
description: Förstå vilka Azure-regioner och storlekar för virtuella datorer som stöds av Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243671"
---
# <a name="azure-red-hat-openshift-resources"></a>OpenShift-resurser för Azure Red Hat

I det här avsnittet visas azure-regioner och storlekar för virtuella datorer som stöds av Tjänsten Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Azure-regioner

Se [Produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) för en aktuell lista över regioner där du kan distribuera Azure Red Hat OpenShift-kluster.

## <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

Här är de storlekar som stöds för virtuella datorer som du kan ange för beräkningsnoderna i Azure Red Hat OpenShift-klustret.

> [!Important]
> Varje virtuell dator har olika antal enheter som kan anslutas. Detta kanske inte är lika omedelbart klart som minne eller CPU-storlek.
> Alla vm-storlekar är inte tillgängliga i alla regioner. Även om API:et stöder den storlek du anger kan du få ett felmeddelande om storleken inte är tillgänglig i den region som du anger.
> Se [Aktuell lista över vm-storlekar per region som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) för mer information.

## <a name="compute-node-sizes"></a>Beräkna nodstorlekar

Följande beräkningsnodstorlekar stöds av Azure Red Hat OpenShift REST API:

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

## <a name="master-node-sizes"></a>Storlek på huvudnod

Följande huvud-/infrastrukturnodstorlekar stöds av AZURE Red Hat OpenShift REST API:

|Storlek|Virtuell processor|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Nästa steg

Prova självstudien [Skapa en Azure Red Hat OpenShift-kluster.](tutorial-create-cluster.md)
