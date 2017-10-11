---
title: "Granska kraven för Hyper-V-replikering till en sekundär plats för VMM med Azure Site Recovery | Microsoft Docs"
description: "Beskriver förutsättningarna för att replikera virtuella Hyper-V-datorer till en sekundär plats för VMM med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Steg 2: Granska förutsättningar och begränsningar för replikering av Hyper-V-dator till en sekundär plats för VMM


När du har granskat den [scenariots arkitektur](vmm-to-vmm-walkthrough-architecture.md), se till att du förstår distributionskraven, när replikera lokala Hyper-V virtuella datorer (VM) hanteras i System Center Virtual Machine Manager (VMM)-moln, till en sekundär plats med hjälp av den här artikeln [Azure Site Recovery](site-recovery-overview.md) i Azure-portalen.

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

**Krav** | **Detaljer**
--- | ---
**Azure** | En [Microsoft Azure](http://azure.microsoft.com/) prenumeration.<br/><br/> Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om priserna för Site Recovery.<br/><br/> Kontrollera regionerna som stöds för Site Recovery Under geografisk tillgänglighet i [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
**VMM-servrar** | Vi rekommenderar att du har två VMM-servrar, en på den primära platsen och en i sekundärt.<br/><br/> Replikera mellan moln på en enda VMM-servern stöds.<br/><br/> VMM-servrar måste köra minst System Center 2012 SP1 med de senaste uppdateringarna.<br/><br/> VMM-servrar måste internet-åtkomst.
**VMM-moln** | Varje VMM-servern måste ha ett eller flera moln och alla moln måste ha Hyper-V kapacitetsprofilen anges. <br/><br/>Molnen måste innehålla en eller flera VMM-värdgrupper.<br/><br/> Om du bara har en VMM-servern måste ha minst två moln fungerar som primär och sekundär.
**Hyper-V** | Hyper-V-servrar måste köra minst Windows Server 2012 med Hyper-V-rollen och har installerat de senaste uppdateringarna.<br/><br/> En Hyper-V-server måste innehålla en eller flera virtuella datorer.<br/><br/>  Hyper-V-värdservrar ska finnas i värdgrupper i de primära och sekundära VMM-molnen.<br/><br/> Om du kör Hyper-V i ett kluster på Windows Server 2012 R2, installera [uppdatera 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Om du kör Hyper-V i ett kluster på Windows Server 2012 skapas inte klusterutjämning automatiskt om du har en statisk IP-adress-kluster. Konfigurera klusterutjämningen manuellt. [Läs mer](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Hyper-V-servrar måste internet-åtkomst.




## <a name="next-steps"></a>Nästa steg

Gå till [steg3: Planera nätverk](vmm-to-vmm-walkthrough-network.md).
