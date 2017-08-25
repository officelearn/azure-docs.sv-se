---
title: "Granska kraven för replikering från Hyper-V till Azure (med System Center VMM) med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Beskriver kraven för att konfigurera replikering, redundans och återställning på lokala virtuella Hyper-V-datorer i VMM-moln till Azure med hjälp av Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.contentlocale: sv-se
ms.lasthandoff: 08/21/2017

---



# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>Steg 2: Granska kraven för replikering från Hyper-V (med VMM) till Azure

När du har gått igenom [scenariearkitekturen](vmm-to-azure-walkthrough-architecture.md) rekommenderar vi att du läser den här artikeln för att vara säker på att du förstår distributionskraven. 

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

**Krav** | **Detaljer**
--- | ---
**Azure-konto** | Du behöver ett [Microsoft Azure-konto](http://azure.microsoft.com/).
**Azure Storage** | Du behöver ett Azure-lagringskonto för att lagra replikerade data.<br/><br/> Lagringskontot måste finnas i samma region som Azure Recovery Services-valvet.<br/><br/>Du kan använda [geo-redundant lagring](../storage/common/storage-redundancy.md#geo-redundant-storage) eller lokalt redundant lagring. Vi rekommenderar geo-redundant lagring. Med geo-redundant lagring är dina data flexibla i händelse av ett regionalt avbrott, eller om den primära regionen inte kan återställas.<br/><br/> Du kan använda ett Azure-standardlagringskonto eller använda Azure [Premium Storage](../storage/common/storage-premium-storage.md). Premium Storage stöder I/O-intensiva arbetsbelastningar och används vanligtvis för virtuella datorer som behöver konstant höga I/O-prestanda och låg latens. Om du använder premiumlagring för replikerade data behöver du även ett standardlagringskonto. Ett standardlagringskonto lagrar replikeringsloggar som samlar in löpande ändringar av lokala data.
**Azure-nätverk** | Du behöver ett [Azure-nätverk](../virtual-network/virtual-network-get-started-vnet-subnet.md), som virtuella Azure-datorer ansluter till efter en redundansväxling. Azure-nätverket måste finnas i samma region som Recovery Services-valvet.
**Lokala VMM-servrar** | Du behöver en eller flera VMM-servrar som kör System Center 2012 R2 eller senare.<br/><br/> Varje VMM-server måste ha ett eller flera privata moln. Varje moln behöver en eller flera värdgrupper.<br/><br/> VMM-servern behöver Internetåtkomst.
**Lokal Hyper-V** | Hyper-V-värdservrar måste köra minst Windows Server 2012 R2 med Hyper-V-rollen aktiverad, eller Microsoft Hyper-V Server 2012 R2. De senaste uppdateringarna måste vara installerade.<br/><br/> Hyper-V-värden måste finnas i en VMM-värdgrupp (som finns i ett VMM-moln).<br/><br/> En värd måste ha en eller flera virtuella datorer som du vill replikera.<br/><br/> Hyper-V-värdar måste vara anslutna till Internet för replikering till Azure, direkt eller med en proxy. De korrigeringar som beskrivs i artikel [2961977](https://support.microsoft.com/kb/2961977) måste vara installerade på Hyper-V-värdar.
**Lokala virtuella Hyper-V-datorer** | Virtuella datorer du vill replikera bör köra ett[operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) och överensstämma med [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Namnen på virtuella datorer kan ändras efter att replikeringen har aktiverats. 




## <a name="next-steps"></a>Nästa steg

Gå till [Steg3: Planera kapacitet](vmm-to-azure-walkthrough-capacity.md)

