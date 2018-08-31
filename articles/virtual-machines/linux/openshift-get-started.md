---
title: OpenShift i Azure – översikt | Microsoft Docs
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
ms.openlocfilehash: e3ab060c1cea28f83c18dc89aeea7716ec86572a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190351"
---
# <a name="openshift-in-azure"></a>OpenShift i Azure

OpenShift är en öppen och utökningsbar behållarplattform som ger Docker och Kubernetes för företaget.  

OpenShift innehåller Kubernetes för orkestrering av behållare och hantering. Den innehåller och operations-utvecklare som möjliggör:

- Snabb programutveckling.
- Enkel distribution och skalning.
- Livscykel på lång sikt för grupper och program.

Det finns flera versioner av OpenShift:

- OKD (tidigare OpenShift Origin)
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

Av fyra versioner som beskrivs i den här artikeln har bara två tillgängliga för kunder att distribuera i Azure: OpenShift Origin och OpenShift Container Platform.

## <a name="okd-formerly-openshift-origin"></a>OKD (tidigare OpenShift Origin)

OKD är en [öppen källkod](https://www.okd.io/) överordnade projektet för OpenShift som är community stöds. OKD kan installeras på CentOS eller Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Behållarplattform är en företagsklar [kommersiella versioner](https://www.openshift.com) från och stöds av Red Hat. Med den här versionen kan kunder köper nödvändiga rättigheterna för OpenShift Container Platform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunder ”äger” hela plattformen, kan de installera den i sina lokala datacenter eller i ett offentligt moln (till exempel Azure, AWS och Google).

## <a name="openshift-online"></a>OpenShift Online

Online är en Red Hat-hanterade *flera innehavare* OpenShift som använder Behållarplattform. Red Hat hanterar den underliggande infrastrukturen (till exempel virtuella datorer, OpenShift kluster, nätverk och lagring). 

Med den här versionen kan kunden distribuerar behållare, men har ingen kontroll över vilka värdar som behållarna körs. Eftersom Online är flera innehavare, kan behållare finnas på samma VM-värdarna som behållare från andra kunder. Kostnaden är per behållare.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedikerad är en Red Hat-hanterad *enda klient* OpenShift som använder Behållarplattform. Red Hat hanterar den underliggande infrastrukturen (virtuella datorer, OpenShift kluster, nätverk, lagring osv.). Klustret är specifik för en kund och körs i ett offentligt moln (till exempel AWS eller Google med Azure som kommer med tidigt 2018). Ett från kluster innehåller fyra noder för programmet för $48,000 per år (betalas direkt).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera vanliga krav för OpenShift i Azure](./openshift-prerequisites.md)
- [Distribuera OpenShift Origin i Azure](./openshift-origin.md)
- [Distribuera OpenShift Container Platform i Azure](./openshift-container-platform.md)
- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka distribution för OpenShift](./openshift-troubleshooting.md)
