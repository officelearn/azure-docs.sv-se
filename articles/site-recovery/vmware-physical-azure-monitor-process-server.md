---
title: Övervaka processservern för Azure Site Recovery
description: I den hÃ¤r artikeln beskrivs hur du övervakar Azure Site Recovery process server used for VMware VM/physical server disaster recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257074"
---
# <a name="monitor-the-process-server"></a>Övervaka processservern

I den här artikeln beskrivs hur du övervakar servern för processen för återställning av [webbplats.](site-recovery-overview.md)

- Processservern används när du ställer in haveriberedskap för lokala virtuella datorer med VMware och fysiska servrar till Azure.
- Som standard körs processservern på konfigurationsservern. Den installeras som standard när du distribuerar konfigurationsservern.
- Om du vill skala och hantera ett större antal replikerade datorer och högre volymer replikeringstrafik kan du distribuera ytterligare utskalningsprocessservrar.

[Läs mer](vmware-physical-azure-config-process-server-overview.md) om roll och distribution av processservrar.

## <a name="monitoring-overview"></a>Övervakningsöversikt

Eftersom processservern har så många roller, särskilt i replikerad datacachening, komprimering och överföring till Azure, är det viktigt att övervaka processserverns hälsa kontinuerligt.

Det finns ett antal situationer som ofta påverkar processserverns prestanda. Problem som påverkar prestanda kommer att ha en kaskadeffekt på vm-hälsa, så småningom driver både processservern och dess replikerade datorer i ett kritiskt tillstånd. Situationer inkluderar:

- Ett stort antal virtuella datorer använder en processserver som närmar sig eller överskrider rekommenderade begränsningar.
- Virtuella datorer som använder processservern har en hög omsättningshastighet.
- Nätverksdataflöde mellan virtuella datorer och processservern räcker inte för att överföra replikeringsdata till processservern.
- Nätverksdataflöde mellan processservern och Azure är inte tillräckligt för att överföra replikeringsdata från processservern till Azure.

Alla dessa problem kan påverka återställningspunktsmålet (RPO) för virtuella datorer. 

**Varför det?** Eftersom generera en återställningspunkt för en virtuell dator kräver alla diskar på den virtuella datorn att ha en gemensam punkt. Om en disk har en hög omsättningshastighet är replikeringen långsam eller om processservern inte är optimal, påverkar den hur effektivt återställningspunkter skapas.

## <a name="monitor-proactively"></a>Övervaka proaktivt

För att undvika problem med processservern är det viktigt att:

- Förstå specifika krav för processservrar med hjälp av [kapacitet och storleksstyrning](site-recovery-plan-capacity-vmware.md#capacity-considerations)och se till att processservrar distribueras och körs enligt rekommendationer.
- Övervaka aviseringar och felsöka problem när de inträffar för att hålla processservrarna igång effektivt.


## <a name="process-server-alerts"></a>Bearbeta servervarningar

Processservern genererar ett antal hälsoaviseringar, som sammanfattas i följande tabell.

**Aviseringstyp** | **Detaljer**
--- | ---
![Felfri][green] | Processservern är ansluten och felfri.
![Varning][yellow] | CPU-användning > 80% under de senaste 15 minuterna
![Varning][yellow] | Minnesanvändning > 80% under de senaste 15 minuterna
![Varning][yellow] | Ledigt utrymme för cachemapp < 30 % under de senaste 15 minuterna
![Varning][yellow] | Site Recovery övervakar väntande/utgående data var femte minut och uppskattar att data i processservercachen inte kan överföras till Azure inom 30 minuter.
![Varning][yellow] | Processservertjänster körs inte under de senaste 15 minuterna
![Kritisk][red] | CPU-användning > 95% för de senaste 15 minuterna
![Kritisk][red] | Minnesanvändning > 95% under de senaste 15 minuterna
![Kritisk][red] | Ledigt utrymme för cachemapp < 25 % under de senaste 15 minuterna
![Kritisk][red] | Site Recovery övervakar väntande/utgående data var femte minut och uppskattar att data i processservercachen inte kan överföras till Azure inom 45 minuter.
![Kritisk][red] | Inga pulsslag från processservern på 15 minuter.

![Tabellnyckel](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Processserverns övergripande hälsostatus baseras på den värsta aviseringen som genereras.



## <a name="monitor-process-server-health"></a>Övervaka processserverns hälsa

Du kan övervaka hälsotillståndet för dina processservrar på följande sätt: 

1. Om du vill övervaka replikeringshälsan och statusen för en replikerad dator och dess processserver klickar du på den dator som du vill övervaka i valv > **replikerade objekt.**
2. I **Replikeringshälsa**kan du övervaka hälsotillståndet för den virtuella datorn. Klicka på statusen om du vill öka detaljnivån för felinformation.

    ![Processserverhälsa i instrumentpanelen för virtuella datorer](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. I **Process Server Health**kan du övervaka processserverns status. Öka detaljnivån för mer information.

    ![Bearbeta serverinformation i instrumentpanelen för virtuella datorer](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Hälsa kan också övervakas med hjälp av den grafiska representationen på vm-sidan.
    - En utskalningsprocessserver markeras i orange om det finns varningar som är associerade med den och röd om den har några kritiska problem. 
    - Om processservern körs i standarddistributionen på konfigurationsservern markeras konfigurationsservern i enlighet med detta.
    - Om du vill öka detaljnivån klickar du på konfigurationsservern eller processservern. Observera eventuella problem och eventuella reparationsrekommendationer.

Du kan också övervaka processservrar i valvet under **Infrastruktur för platsåterställning**. Klicka på **Konfigurationsservrar** **i Hantera infrastrukturen För webbplatsåterställning**. Välj den konfigurationsserver som är associerad med processservern och detaljgranska till processserverinformation.


## <a name="next-steps"></a>Nästa steg

- Om du har problem med processservrar följer du vår [felsökningsvägledning](vmware-physical-azure-troubleshoot-process-server.md)
- Om du behöver mer hjälp kan du ställa din fråga i [Azure Site Recovery-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
