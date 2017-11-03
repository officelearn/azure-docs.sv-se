---
title: "OpenShift på Översikt över Azure | Microsoft Docs"
description: "OpenShift på Översikt över Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>Översikt över OpenShift

OpenShift är en öppen och utökningsbar behållaren programmet plattform som ger docker och Kubernetes till företaget.  

OpenShift innehåller Kubernetes för behållaren orchestration och hantering. Det lägger till åtgärder till Central verktyg som gör det möjligt för utvecklare och:

- Snabb utveckling
- Enkel distribution och skalning
- Livscykel på lång sikt för grupper och program

Det finns flera OpenShift som två är tillgänglig för körning i Azure-erbjudanden.

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift dedikerad

Dessa fyra erbjudanden som omfattas, två är tillgängliga för kunder att distribuera till Azure på egen hand - OpenShift ursprung och OpenShift behållare plattform.

## <a name="openshift-origin"></a>OpenShift Origin

[Öppen källkod](https://www.openshift.org/) överordnade projektet för OpenShift är community stöds. Ursprung kan installeras på CentOS eller RHEL.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Företagsförberedda ([kommersiella erbjudande](https://www.openshift.com)) version från Red Hat som stöds i Red Hat. Kund köper nödvändiga rättigheterna för OpenShift behållare plattform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunden ”äger” hela plattformen, kan installeras i sina lokala datacenter, offentligt moln (Azure, AWS, Google, etc.) och så vidare.

## <a name="openshift-online"></a>OpenShift Online

Red Hat hanteras **flera innehavare** OpenShift (med behållaren plattform). Red Hat hanterar alla underliggande infrastruktur (virtuella datorer, OpenShift kluster, nätverk, lagring, etc.). 

Kunden distribuerar behållare, men har ingen kontroll på vilka värdar som behållarna körs. Eftersom det finns flera innehavare, kan behållare samordnas på samma värdar för Virtuella datorer som behållare från andra kunder. Det är kostnaden per behållare.

## <a name="openshift-dedicated"></a>OpenShift dedikerad

Red Hat hanteras **stöd för en innehavare** OpenShift (med behållaren plattform). Red Hat hanterar alla underliggande infrastruktur (virtuella datorer, OpenShift kluster, nätverk, lagring, etc.). Klustret är specifik för en kund och körs i ett offentligt moln (AWS, Google, Azure - kommer tidig 2018). Första kluster innehåller fyra programmet noder för $48 kB per år (Summa betalning för ett helt år).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera gemensamma krav för OpenShift i Azure](./openshift-prerequisites.md)
- [Distribuera OpenShift ursprung](./openshift-origin.md)
- [Distribuera OpenShift behållare plattform](./openshift-container-platform.md)
- [Uppgifter för efter distribution](./openshift-post-deployment.md)
- [Felsökning av OpenShift distribution](./openshift-troubleshooting.md)
