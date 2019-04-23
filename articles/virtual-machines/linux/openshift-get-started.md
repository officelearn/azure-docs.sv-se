---
title: OpenShift i Azure – översikt | Microsoft Docs
description: En översikt över OpenShift i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006972"
---
# <a name="openshift-in-azure"></a>OpenShift i Azure

OpenShift är en öppen och utökningsbar behållarplattform som ger Docker och Kubernetes för företaget.  

OpenShift innehåller Kubernetes för orkestrering av behållare och hantering. Den lägger till utvecklare och operations-centric verktyg för att aktivera:

- Snabb programutveckling.
- Enkel distribution och skalning.
- Livscykel på lång sikt för grupper och program.

Det finns flera versioner av OpenShift.  Av dessa versioner är endast två tillgängliga idag för kunder att distribuera i Azure: OpenShift Container Platform och OKD (tidigare OpenShift Origin).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Behållarplattform är en företagsklar [kommersiella versioner](https://www.openshift.com) från och stöds av Red Hat. Med den här versionen kan kunder köper nödvändiga rättigheterna för OpenShift Container Platform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunder ”äger” hela plattformen, kan de installera den i sina lokala datacenter eller i ett offentligt moln (till exempel Azure).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Azure Red Hat OpenShift är en fullständigt hanterad tjänst för OpenShift som körs i Azure. Den här tjänsten är gemensamt hanteras och stöds av Microsoft och Red Hat. Klustret kommer att distribuera till kundens Azure-prenumeration. Tjänsten kommer att vara allmänt tillgänglig runt maj 2019. Separata dokumentationen för hanterade tjänsten blir tillgänglig när tjänsten är GA.

## <a name="okd"></a>OKD

OKD är en [öppen källkod](https://www.okd.io/) överordnade projektet för OpenShift som är community stöds. OKD kan installeras på CentOS eller Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera vanliga krav för OpenShift i Azure](./openshift-prerequisites.md)
- [Distribuera OpenShift Container Platform i Azure](./openshift-container-platform.md)
- [Distribuera OpenShift Container Platform självhanterade Marketplace-erbjudandet](./openshift-marketplace-self-managed.md)
- [Distribuera OpenShift i Azure Stack](./openshift-azure-stack.md)
- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka distribution för OpenShift](./openshift-troubleshooting.md)
