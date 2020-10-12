---
title: Koncept – övervaka och reparera privata moln i Azure VMware-lösningen
description: Lär dig hur Azure VMware-lösningen övervakar och reparerar VMware ESXi-servrar i ett privat moln i Azure VMware-lösningar.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: bee08304cd14f2aeec6995203638b5a37d9a861c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91346232"
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

Lär dig om [uppgraderingar av privata moln i Azure VMware-lösningar](concepts-upgrades.md).  
