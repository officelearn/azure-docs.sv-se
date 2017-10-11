---
title: "Krav för replikering till Azure med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln sammanfattar krav för att replikera virtuella datorer och fysiska datorer till Azure med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Krav för att replikera virtuella Azure-datorer till en annan region med hjälp av Azure Site Recovery

> [!div class="op_single_selector"]
> * [Replikera från Azure till Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replikera från lokal till Azure](site-recovery-prereq.md)

Azure Site Recovery-tjänsten bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi genom att samordna:
* Replikering av virtuella Azure-datorer till en annan Azure-region.
* Replikeringen av lokala fysiska servrar och virtuella datorer till Azure eller till ett sekundärt datacenter. 

Vid driftstopp på den primära platsen växlar du över till en sekundär plats för att hålla program och arbetsbelastningar som är tillgängliga. När den återgår till normal drift kan du växla tillbaka till den primära platsen. Mer information om Platsåterställning finns i [vad är Site Recovery?](site-recovery-overview.md).

Den här artikeln sammanfattar förutsättningar som krävs för att påbörja Site Recovery replikering från lokal till Azure.

Bokför eventuella kommentarer längst ned i artikeln eller tekniska frågor om den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Krav för Azure

**Krav** | **Detaljer**
--- | ---
**Azure-konto** | En [Microsoft Azure](http://azure.microsoft.com/) konto.<br/><br/> Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
**Site Recovery-tjänsten** | Mer information om priserna för Site Recovery finns [priserna för Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). Vi rekommenderar att du skapar ett Recovery Services-valv i mål-Azure-region som du vill använda som en återställningsplats för katastrofåterställning. Till exempel om dina virtuella källdatorer körs i östra USA och du vill replikera till centrala USA, rekommenderar vi att du skapar valvet i centrala USA.|
**Azure kapacitet** | Du måste ha en prenumeration med tillräckligt med kapacitet för virtuella datorer, lagringskonton och nätverkskomponenter för mål Azure-region som du vill använda som din disaster recovery-plats. Du kan kontakta supporten om du vill lägga till mer kapacitet.
**Vägledning för lagring** | Se till att du följer den [lagringsanvisningar](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) för källan Azure virtuella datorer för att undvika eventuella prestandaproblem. Om du följer standardinställningarna skapar Site Recovery krävs storage-konton som baseras på källserverns konfiguration. Om du anpassar och välja egna inställningar, se till att du följer den [skalbarhetsmål för virtuella diskar](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Vägledning för nätverk** | Du behöver godkända utgående anslutning från Azure-VM för specifika URL-adresser eller IP-adressintervall. Mer information finns i den [nätverk vägledning för att replikera virtuella datorer i Azure](site-recovery-azure-to-azure-networking-guidance.md) artikel.
**Virtuell Azure-dator** | Se till att de senaste rotcertifikat finns på Windows- eller Linux VM. Om de senaste rotcertifikat inte finns, kan inte den virtuella datorn registreras till Site Recovery på grund av säkerhetsbegränsningar.

>[!NOTE]
>Mer information om stöd för specifika konfigurationer finns i [supportmatrisen](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [nätverk vägledning för att replikera virtuella datorer i Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Börja skydda dina arbetsbelastningar av [replikering av Azure virtuella datorer](site-recovery-azure-to-azure.md).
