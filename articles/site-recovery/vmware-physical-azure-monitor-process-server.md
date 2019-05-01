---
title: Övervaka Azure Site Recovery processervern
description: Den här artikeln beskriver hur du övervakar processervern för Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: rayne
ms.openlocfilehash: 5fac369f15edb3ef0be31d3dc7d7434104c18dfe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928174"
---
# <a name="monitor-the-process-server"></a>Övervaka processerver

Den här artikeln beskrivs hur du övervakar den [Site Recovery](site-recovery-overview.md) processervern.

- Processervern används när du konfigurerar haveriberedskap för lokala virtuella VMware-datorer och fysiska servrar till Azure.
- Som standard körs processervern på konfigurationsservern. Den installeras som standard när du distribuerar konfigurationsservern.
- Du kan också distribuera ytterligare, skala ut processervrar att skala och hantera större antal replikerade datorer och större mängder replikeringstrafik.

[Läs mer](vmware-physical-azure-config-process-server-overview.md) roll och distribution av processervrar.

## <a name="monitoring-overview"></a>Övervakningsöversikt

Eftersom processervern har så många roller, särskilt i replikerade datacachelagring, komprimering och överföring till Azure, är det viktigt att övervaka hälsa för processen servern med jämna mellanrum.

Det finns ett antal situationer som ofta påverkar processen serverprestanda. Problem som påverkar prestanda har en sammanhängande effekt på VM-hälsa, så småningom skicka både processervern och dess replikerade datorer till ett kritiskt tillstånd. Situationer är:

- Väldigt många virtuella datorer använder en processerver närmar sig eller överstiger rekommenderade begränsningar.
- Virtuella datorer med hjälp av processervern har en hög omsättningsfrekvensen.
- Dataflöde i nätverket mellan virtuella datorer och processervern räcker inte att ladda upp replikeringsdata till processervern.
- Dataflöde i nätverket mellan processervern och Azure är inte tillräckliga för att ladda upp replikeringsdata från processervern till Azure.

Alla dessa problem kan påverka återställningspunkt (RPO) av virtuella datorer. 

**Varför?** Eftersom du skapar en återställningspunkt för en virtuell dator kräver att alla diskar på den virtuella datorn ha en gemensam tidpunkt. Om en disk har en hög omsättning frekvens, replikeringen går långsamt eller processervern inte optimalt, påverkar hur effektivt återställningspunkter skapas.

## <a name="monitor-proactively"></a>Övervaka proaktivt

För att undvika problem med processervern, är det viktigt att:

- Förstå särskilda krav för processervrar med [kapacitet och ändra storlek på vägledning](site-recovery-plan-capacity-vmware.md#capacity-considerations), och kontrollera att processervrar distribueras och körs enligt rekommendationerna.
- Övervaka aviseringar och felsöka problem när de inträffar, att hålla processervrar kör effektivt.


## <a name="process-server-alerts"></a>Bearbeta serveraviseringar

Processervern genererar ett antal hälsovarningar sammanfattas i tabellen nedan.

**Typ av avisering** | **Detaljer**
--- | ---
![Felfri][green] | Processervern är ansluten och felfri.
![Varning][yellow] | CPU-användning > 80% för de senaste 15 minuterna
![Varning][yellow] | Minne användning > 80% för de senaste 15 minuterna
![Varning][yellow] | Cachemapp ledigt utrymme < 30% under de senaste 15 minuterna
![Varning][yellow] | Processervertjänsterna körs inte under de senaste 15 minuterna
![Kritisk][red] | CPU-användning > 95% under de senaste 15 minuterna
![Kritisk][red] | Minne användning > 95% under de senaste 15 minuterna
![Kritisk][red] | Cachemapp ledigt utrymme < 25% under de senaste 15 minuterna
![Kritisk][red] | Inget pulsslag från processervern under 15 minuter.

![tabellnyckel](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Övergripande hälsostatus processervern baseras på det sämsta avisering som genererats.



## <a name="monitor-process-server-health"></a>Övervakare för hälsa för processen-server

Du kan övervaka hälsotillståndet för din processervrar på följande sätt: 

1. Övervaka replikering hälsotillstånd och status på en replikerad dator och dess processerver i valvet > **replikerade objekt**, klicka på den dator som du vill övervaka.
2. I **Replikeringshälsa**, du kan övervaka hälsotillståndet för virtuella datorer. Klicka på status och öka detaljnivån för felinformation.

    ![Hälsa för processen server i VM-instrumentpanelen](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. I **hälsa för processen Server**, du kan övervaka status för processervern. Granska nedåt för mer information.

    ![Information om processervern i VM-instrumentpanelen](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Hälsa kan övervakas med hjälp av en grafisk representation på sidan virtuell dator.
    - En skalbar processerver är markerade i orange om det finns varningar som är kopplade till den och röd om den har alla kritiska problem. 
    - Om processervern körs i standard-distributionen på konfigurationsservern, kommer sedan configuration server att markeras.
    - Om du vill granska nedåt, klickar du på konfigurationsservern eller processervern. Observera att eventuella problem, och alla rekommenderade åtgärder.

Du kan också övervaka bearbeta servrar i valvet under **Site Recovery-infrastruktur**. I **hantera infrastrukturen för Site Recovery**, klickar du på **Konfigurationsservrar**. Välj configuration server som är associerade med processervern och gå ned till information om processervern.


## <a name="next-steps"></a>Nästa steg

- Om du har några bearbeta servrar problem, följ vår [felsökningsanvisningar](vmware-physical-azure-troubleshoot-process-server.md)
- Om du behöver mer hjälp kan du ställa din fråga i den [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
