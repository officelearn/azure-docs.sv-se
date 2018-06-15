---
title: OpenShift i översikt över Azure | Microsoft Docs
description: En översikt över OpenShift i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: c8e740a66271c88b3abb036867d1760cc9e77607
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944509"
---
# <a name="openshift-in-azure"></a>OpenShift i Azure

OpenShift är en öppen och utökningsbar behållaren programmet plattform som ger Docker och Kubernetes till företaget.  

OpenShift innehåller Kubernetes för behållaren orchestration och hantering. Den innehåller developer - och operations-elementbaserade som möjliggör:

- Snabb utveckling.
- Enkel distribution och skalning.
- Livscykel på lång sikt för grupper och program.

Det finns flera versioner av OpenShift:

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift dedikerad

Av de fyra versionerna som beskrivs i den här artikeln endast två är tillgängliga för kunder att distribuera i Azure: OpenShift ursprung och OpenShift behållare plattform.

## <a name="openshift-origin"></a>OpenShift Origin

Ursprung är en [öppen källkod](https://www.openshift.org/) överordnade projektet för OpenShift är community stöds. Ursprung kan installeras på CentOS eller Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Behållaren är en enterprise-redo [kommersiella version](https://www.openshift.com) från och stöds i Red Hat. Med den här versionen kunder köpa nödvändiga rättigheterna för OpenShift behållare plattform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunder ”äger” hela plattformen kan installera de den i sina lokala datacenter eller i ett offentligt moln (till exempel Azure, AWS eller Google).

## <a name="openshift-online"></a>OpenShift Online

Red Hat-hanterad online är *flera innehavare* OpenShift som använder behållaren plattform. Red Hat hanterar alla underliggande infrastruktur (t.ex virtuella datorer, OpenShift kluster, nätverk och lagring). 

Med den här versionen kunden distribuerar behållare, men har ingen kontroll över vilka värdar som behållarna körs. Eftersom Online är flera innehavare, kan behållare finnas på samma VM-värdar som behållare från andra kunder. Det är kostnaden per behållare.

## <a name="openshift-dedicated"></a>OpenShift dedikerad

Dedikerad är Red Hat-hanterad *stöd för en innehavare* OpenShift som använder behållaren plattform. Red Hat hanterar alla underliggande infrastruktur (virtuella datorer, OpenShift kluster, nätverk, lagring, etc.). Klustret är specifik för en kund och körs i ett offentligt moln (till exempel AWS eller Google, med Azure som kommer in tidigt 2018). Ett första kluster innehåller fyra programmet noder för $48,000 per år (betald direkt).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera gemensamma krav för OpenShift i Azure](./openshift-prerequisites.md)
- [Distribuera OpenShift ursprung i Azure](./openshift-origin.md)
- [Distribuera OpenShift behållare plattform i Azure](./openshift-container-platform.md)
- [Uppgifter efter distributionen](./openshift-post-deployment.md)
- [Felsöka OpenShift distribution](./openshift-troubleshooting.md)
