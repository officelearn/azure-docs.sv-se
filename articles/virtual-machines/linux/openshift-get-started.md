---
title: OpenShift i Azure – översikt | Microsoft Docs
description: En översikt över OpenShift i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: haroldw
ms.openlocfilehash: 826085df8d928cab0a05527be8c464af5f4e9180
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002512"
---
# <a name="openshift-in-azure"></a>OpenShift i Azure

OpenShift är en öppen och utökningsbar behållarplattform som ger Docker och Kubernetes för företaget.  

OpenShift innehåller Kubernetes för orkestrering av behållare och hantering. Den lägger till utvecklare och operations-centric verktyg för att aktivera:

- Snabb programutveckling.
- Enkel distribution och skalning.
- Livscykel på lång sikt för grupper och program.

Det finns flera versioner av OpenShift:

- OpenShift Container Platform
- OpenShift på Azure (fullständigt hanterad OpenShift kommer runt slutet av Q1 CY2019)
- OKD (tidigare OpenShift Origin)
- OpenShift Dedicated
- OpenShift Online

Av fem versioner som beskrivs i den här artikeln är bara två tillgängliga idag för kunder att distribuera i Azure: OpenShift Container Platform och OKD.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Behållarplattform är en företagsklar [kommersiella versioner](https://www.openshift.com) från och stöds av Red Hat. Med den här versionen kan kunder köper nödvändiga rättigheterna för OpenShift Container Platform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunder ”äger” hela plattformen, kan de installera den i sina lokala datacenter eller i ett offentligt moln (till exempel Azure).

## <a name="openshift-on-azure"></a>OpenShift på Azure

OpenShift på Azure är en fullständigt hanterad tjänst för OpenShift som körs i Azure. Den här tjänsten är gemensamt hanteras och stöds av Microsoft och Red Hat. Klustret kommer att distribuera till kundens Azure-prenumeration. Tjänsten är för tillfället i privat förhandsversion och kommer att vara GA runt Q1 CY2019 är slut. För kunder som vill delta i den privata förhandsgranskningen, Fyll i [Nomineringsformuläret](https://aka.ms/openshiftazureinterest).  Mer information kommer att finnas när erbjudandet blir närmare till GA.

## <a name="okd-formerly-openshift-origin"></a>OKD (tidigare OpenShift Origin)

OKD är en [öppen källkod](https://www.okd.io/) överordnade projektet för OpenShift som är community stöds. OKD kan installeras på CentOS eller Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedikerad är en Red Hat-hanterad *enda klient* OpenShift som använder OpenShift Container Platform. Red Hat hanterar den underliggande infrastrukturen (virtuella datorer, OpenShift kluster, nätverk, lagring osv.). Klustret är specifik för en kund och körs i ett offentligt moln (till exempel Azure). Ett från kluster innehåller fyra programnoder och alla kostnader är årliga och betalda förskott.

## <a name="openshift-online"></a>OpenShift Online

Online är en Red Hat-hanterade *flera innehavare* OpenShift som använder Behållarplattform. Red Hat hanterar den underliggande infrastrukturen (till exempel virtuella datorer, OpenShift kluster, nätverk och lagring). 

Med den här versionen kan kunden distribuerar behållare, men har ingen kontroll över vilka värdar som behållarna körs. Eftersom Online är flera innehavare, kan behållare finnas på samma VM-värdarna som behållare från andra kunder. Kostnaden är per behållare.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera vanliga krav för OpenShift i Azure](./openshift-prerequisites.md)
- [Distribuera OpenShift Container Platform i Azure](./openshift-container-platform.md)
- [Distribuera OKD i Azure](./openshift-okd.md)
- [Distribuera OpenShift i Azure Stack](./openshift-azure-stack.md)
- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka distribution för OpenShift](./openshift-troubleshooting.md)
