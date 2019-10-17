---
title: OpenShift i Azure-översikt | Microsoft Docs
description: En översikt över OpenShift i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 2293eac45da6f53b0cd29fda68b0ddc76aea1a6c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390638"
---
# <a name="openshift-in-azure"></a>OpenShift i Azure

OpenShift är en öppen och utöknings bar plattform för behållar program som ger Docker och Kubernetes till företaget.  

OpenShift innehåller Kubernetes för behållar dirigering och hantering. Det lägger till utvecklare-koncentriska och åtgärds drivna verktyg som möjliggör:

- Snabb program utveckling.
- Enkel distribution och skalning.
- Långsiktigt livs cykel underhåll för team och program.

Det finns flera versioner av OpenShift.  I dessa versioner är bara två tillgängliga idag för kunder som ska distribueras i Azure: OpenShift container Platform och OKD (tidigare OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift är ett fullständigt hanterat utbud av OpenShift som körs i Azure. Den här tjänsten hanteras gemensamt och stöds av Microsoft och Red Hat. Mer information finns i dokumentationen för [Azure Red Hat OpenShift-tjänsten](https://docs.microsoft.com/azure/openshift/) .

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform är en Enterprise-förberedd [kommersiell version](https://www.openshift.com) från och stöds av Red Hat. Med den här versionen köper kunderna de nödvändiga rättigheterna för OpenShift container Platform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunder "äger" hela plattformen kan de installera dem i sitt lokala data Center eller i ett offentligt moln (till exempel Azure).

## <a name="okd"></a>OKD

OKD är ett överordnat projekt med [öppen källkod](https://www.okd.io/) av OpenShift som stöds av gruppen. OKD kan installeras på CentOS eller Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera vanliga krav för OpenShift i Azure](./openshift-container-platform-3x-prerequisites.md)
- [Distribuera OpenShift container Platform i Azure](./openshift-container-platform-3x.md)
- [Distribuera OpenShift container Platform-erbjudandet för egen hantering](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Distribuera OpenShift i Azure Stack](./openshift-azure-stack.md)
- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution](./openshift-container-platform-3x-troubleshooting.md)
