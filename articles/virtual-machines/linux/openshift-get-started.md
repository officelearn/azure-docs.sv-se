---
title: OpenShift i Azure översikt
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
ms.openlocfilehash: 021ebe010a27fa155de861121e1972466c800f4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035413"
---
# <a name="openshift-in-azure"></a>OpenShift i Azure

OpenShift är en öppen och utökningsbar containerapplikationsplattform som tar Docker och Kubernetes till företaget.  

OpenShift innehåller Kubernetes för containerorkestrering och hantering. Det lägger till utvecklarcentrerade och driftcentrerade verktyg som gör det möjligt:

- Snabb applikationsutveckling.
- Enkel distribution och skalning.
- Långsiktigt livscykelunderhåll för team och applikationer.

Det finns flera versioner av OpenShift tillgängliga.  Av dessa versioner är endast två tillgängliga idag för kunder att distribuera i Azure: OpenShift Container Platform och OKD (tidigare OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift är ett fullständigt hanterat erbjudande av OpenShift som körs i Azure. Den här tjänsten hanteras och stöds gemensamt av Microsoft och Red Hat. Mer information finns i Dokumentationen till [Azure Red Hat OpenShift Service.](https://docs.microsoft.com/azure/openshift/)

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform är en företagsklar [kommersiell version](https://www.openshift.com) från och stöds av Red Hat. Med den här versionen köper kunderna de nödvändiga berättigandena för OpenShift Container Platform och ansvarar för installation och hantering av hela infrastrukturen.

Eftersom kunderna "äger" hela plattformen kan de installera den i sina lokala datacenter eller i ett offentligt moln (till exempel Azure).

## <a name="okd"></a>OKD

OKD är ett [open-source upstream-projekt](https://www.okd.io/) av OpenShift som stöds av communityn. OKD kan installeras på CentOS eller Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera vanliga förutsättningar för OpenShift i Azure](./openshift-container-platform-3x-prerequisites.md)
- [Distribuera OpenShift-behållarplattform i Azure](./openshift-container-platform-3x.md)
- [Distribuera självhanterad marketplace-erbjudande för OpenShift-behållarplattform](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Distribuera OpenShift i Azure Stack](./openshift-azure-stack.md)
- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution](./openshift-container-platform-3x-troubleshooting.md)
