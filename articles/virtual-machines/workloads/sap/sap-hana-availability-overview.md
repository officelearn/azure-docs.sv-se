---
title: "SAP HANA-tillgänglighet på virtuella Azure-datorer – översikt | Microsoft Docs"
description: "Åtgärder för SAP HANA på interna virtuella Azure-datorer"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Guiden hög tillgänglighet för SAP HANA för virtuella Azure-datorer
Azure tillhandahåller många funktioner som gör det möjligt för att distribuera uppdrag databaser som SAP HANA i virtuella Azure-datorer. Detta dokument ger vägledning om hur du få tillgänglighet för SAP HANA-instanser som finns i Azure Virtual Machines. I det här dokumentet beskrivs flera scenarier som kan implementeras på Azure-infrastruktur för att öka tillgängligheten för SAP HANA på Azure. 

## <a name="prerequisites"></a>Förutsättningar
Den här handboken förutsätts att du är bekant med denna infrastruktur som en tjänst (IaaS) grunderna i Azure som: 

- Hur du distribuerar virtuella datorer eller virtuella nätverk via Azure-portalen eller PowerShell.
- Azure plattformsoberoende kommandoradsgränssnittet (CLI), inklusive möjligheten att använda mallar för JavaScript Object Notation (JSON).

Den här handboken förutsätter också att du är bekant med installerar SAP HANA-instanser och administrerar och drift av SAP HANA-instanser. Ställa särskilt in och operations runt HANA System replikering eller åtgärder som säkerhetskopiering och återställning av databaser för SAP HANA.

Andra artiklar som ger en bra översikt för SAP HANA-avsnitt i Azure är:

- [Manuell installation av single instance SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Konfigurera SAP HANA System Replication i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Artikeln och innehåll som du bör känna om SAP HANA kan anges som:

- [Hög tillgänglighet för SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Vanliga frågor och svar: Hög tillgänglighet för SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Hur du utför replikering System för SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 Service Pack 01 Nyheter: hög tillgänglighet](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Nätverksrekommendationer för SAP HANA System replikering](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikering för SAP HANA-System](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA Service Auto-Restart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurerar replikering för SAP HANA-System](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


Utöver att bekanta dig med att distribuera virtuella datorer i Azure, vi rekommenderar även läsa artikeln [hantera tillgängligheten för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) innan du fortsätter med att definiera tillgänglighet arkitekturen i Azure.

## <a name="service-level-agreements-for-different-azure-components"></a>Service Level agreements för olika Azure-komponenter
Azure har olika tillgänglighets-SLA för olika komponenter som nätverk, lagring och virtuella datorer. Alla dessa SLA dokumenteras och finns från och med den [Microsoft Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/) sidan. Om du checkar ut den [SLA för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), Tänk på att Azure tillhandahåller två olika serviceavtal med två olika konfigurationer. SLA: er definieras som:

- En enskild virtuell dator med hjälp av [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) OS disk- och alla datadiskar ger en månatlig drifttid i procent för 99,9%
- Flera (minst två) virtuella datorer som är ordnade i en [Azure Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ger en månatlig drifttid i procent av 99,95%

Måttet dina behov av tillgänglighet mot Azure SLA-komponenter kan tillhandahålla och sedan bestämmer på olika scenarier som du behöver implementera med SAP HANA att få tillgänglighet kräver för att tillhandahålla.

## <a name="next-steps"></a>Nästa steg
Fortsätta att dokument:

- [SAP HANA tillgänglighet inom en Azure-region](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Tillgänglighet för SAP HANA över Azure-regioner](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


