---
title: Koncept – övervaka och reparera privata moln i Azure VMware-lösningen
description: Lär dig hur Azure VMware-lösningen övervakar och reparerar VMware ESXi-servrar i ett privat moln i Azure VMware-lösningar.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 11a3c53bff7ce7b67b677977eddb9829f336672d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684703"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Övervaka och reparera privata moln i Azure VMware-lösningar

Azure VMware-lösningen övervakar kontinuerligt VMware ESXi-servrarna i ett privat moln i Azure VMware-lösningen. 

## <a name="what-azure-vmware-solution-monitors"></a>Vad Azure VMware-lösningar övervakar

Azure VMware-lösningen övervakar följande för fel villkor på värden:  

- Processor status 
- Minnes status 
- Anslutning och energi tillstånd 
- Status för maskin varu fläkt 
- Förlust av nätverks anslutning 
- System korts status för maskin vara 
- Fel uppstod på diskarna för en virtuellt San-värd 
- Maskin varu spänning 
- Status för maskin varu temperatur 
- Maskin varu energi status 
- Lagrings status 
- Anslutningen misslyckades 

> [!NOTE]
> Klient administratörer för Azure VMware-lösningar får inte redigera eller ta bort ovanstående definierade VMware vCenter-larm, eftersom de hanteras av Azure VMware-lösningens kontroll plan på vCenter. Dessa larm används av Azure VMware Solution Monitoring för att utlösa Azure VMware-lösningens värd reparations process.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware-lösning värd reparation  

När Azure VMware-lösningen identifierar en försämring eller ett haveri på en Azure VMware-lösning på en innehavares privata moln, utlöses processen för värd reparation. Värd reparation innebär att ersätta den felaktiga noden med en ny felfria nod.  

Värd reparations processen startar genom att lägga till en ny felfria nod i klustret. När så är möjligt placeras den felande värden i VMware vSphere underhålls läge. VMware-vMotion används för att flytta de virtuella datorerna från den felaktiga värden till andra tillgängliga servrar i klustret, vilket möjliggör en direktmigrering av arbets belastningar utan avbrott. I scenarier där den felande värden inte kan placeras i underhålls läge tas värden bort från klustret.

## <a name="next-steps"></a>Nästa steg

Här följer några avsnitt som du kanske vill lära dig mer om:

- [Uppgraderingar av privata moln i Azure VMware-lösningar](concepts-upgrades.md)
- [Livs cykel hantering av virtuella Azure VMware-lösningar](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Skydda dina virtuella datorer med Azure VMware-lösningen med Azure Security Center-integrering](azure-security-integration.md)
- [Säkerhetskopiera virtuella datorer i Azure VMware-lösningen med Azure Backup Server](backup-azure-vmware-solution-virtual-machines.md)
- [Fullständig haveri beredskap för virtuella datorer med Azure VMware-lösning](disaster-recovery-for-virtual-machines.md)
