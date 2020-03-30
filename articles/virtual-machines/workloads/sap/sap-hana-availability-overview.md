---
title: SAP HANA-tillgänglighet på virtuella Azure-datorer – Översikt | Microsoft-dokument
description: Beskriver SAP HANA-åtgärder på inbyggda virtuella Azure-datorer.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccaf45cf617bd31a584b6c73f3dd08877bc8587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266059"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA hög tillgänglighet för virtuella Azure-datorer

Du kan använda många Azure-funktioner för att distribuera verksamhetskritiska databaser som SAP HANA på virtuella Azure-datorer. Den här artikeln innehåller vägledning om hur du uppnår tillgänglighet för SAP HANA-instanser som finns i virtuella Azure-datorer. Artikeln beskriver flera scenarier som du kan implementera med hjälp av Azure-infrastrukturen för att öka tillgängligheten för SAP HANA i Azure. 

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med infrastruktur som en tjänst (IaaS) grunderna i Azure, inklusive: 

- Så här distribuerar du virtuella datorer eller virtuella nätverk via Azure-portalen eller PowerShell.
- Använda Azure-gränssnittet för kommandoradsgränserna för Azure (Azure CLI), inklusive alternativet att använda JSON-mallar (JavaScript Object Notation).

Den här artikeln förutsätter också att du är bekant med att installera SAP HANA-instanser och administrera och driva SAP HANA-instanser. Det är särskilt viktigt att känna till installationen och driften av HANA-systemreplikering. Detta inkluderar uppgifter som säkerhetskopiering och återställning för SAP HANA-databaser.

De här artiklarna ger en bra översikt över hur du använder SAP HANA i Azure:

- [Manuell installation av SAP HANA med en instans på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Konfigurera SAP HANA-systemreplikering i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Säkerhetskopiera SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Det är också en bra idé att vara bekant med dessa artiklar om SAP HANA:

- [Hög tillgänglighet för SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Vanliga frågor: Hög tillgänglighet för SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Utför systemreplikering för SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 Nyheter: Hög tillgänglighet](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Nätverksrekommendationer för SAP HANA-systemreplikering](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA-tjänsten startar om automatiskt](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurera SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Förutom att du är bekant med att distribuera virtuella datorer i Azure, innan du definierar din tillgänglighetsarkitektur i Azure, rekommenderar vi att du läser [Hantera tillgängligheten för Virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Servicenivåavtal för Azure-komponenter

Azure har olika tillgänglighetsslamor för olika komponenter, till exempel nätverk, lagring och virtuella datorer. Alla SLA:er är dokumenterade. Mer information finns i [Microsoft Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/). 

[SLA för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) beskriver tre olika SLA: er, för tre olika konfigurationer:

- En enda virtuell dator som använder [Azure premium SSD:er](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) för OS-disken och alla datadiskar. Det här alternativet ger en månatlig drifttid på 99,9 procent.
- Flera (minst två) virtuella datorer som är ordnade i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Det här alternativet ger en månatlig drifttid på 99,95 procent.
- Flera (minst två) virtuella datorer som är ordnade i en [Availablity-zon](https://docs.microsoft.com/azure/availability-zones/az-overview). Det här alternativet gav en månatlig drifttid på 99,99 procent.

Mät ditt tillgänglighetskrav mot de SLA som Azure-komponenter kan tillhandahålla. Välj sedan dina scenarier för SAP HANA för att uppnå önskad tillgänglighetsnivå.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [SAP HANA-tillgänglighet inom en Azure-region](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Lär dig mer om [SAP HANA-tillgänglighet i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


