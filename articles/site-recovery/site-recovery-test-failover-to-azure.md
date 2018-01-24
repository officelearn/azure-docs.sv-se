---
title: Testa redundans till Azure i Site Recovery | Microsoft Docs
description: "Lär dig mer om att köra ett redundanstest från lokal till Azure"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/16/2017
ms.author: pratshar
ms.openlocfilehash: a4555b1cc758e2d4bdd11a16776dc3bb209adee8
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="test--failover-to-azure-in-site-recovery"></a>Testa redundans till Azure i Site Recovery



Den här artikeln beskriver hur du kör en disaster recovery-gå till Azure, med ett redundanstest för Site Recovery.  

Du kan köra ett redundanstest för att verifiera dina replikering och strategi för katastrofåterställning utan avbrott eller dataförlust. Testa redundans påverkar inte pågående replikering eller din produktionsmiljö. Du kan köra ett redundanstest på en specifik virtuell dator (VM), eller på en [återställningsplanen](site-recovery-create-recovery-plans.md) som innehåller flera virtuella datorer. 


## <a name="run-a-test-failover"></a>Köra ett redundanstest
Den här proceduren beskriver hur du kör ett redundanstest för en återställningsplan. 

![Testa redundans](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. I Site Recovery på Azure-portalen klickar du på **Återställningsplaner** > *recoveryplan_name* > **Redundanstest**.
2. Välj en **återställningspunkt** som ska växlas. Du kan använda något av följande alternativ:
    - **Senaste bearbetas**: det här alternativet flyttas över alla virtuella datorer i planen för att den senaste återställningspunkten som bearbetas av Site Recovery. Information om den senaste återställningspunkten för en specifik VM, **senaste återställningspunkter** i VM-inställningarna. Det här alternativet ger en låga RTO (mål), eftersom ingen tid läggs obearbetade data bearbetas.
    - **Senaste programkonsekventa**: det här alternativet flyttas över alla virtuella datorer i planen till den senaste programkonsekventa återställningspunkten bearbetas av Site Recovery. Information om den senaste återställningspunkten för en specifik VM, **senaste återställningspunkter** i VM-inställningarna. 
    - **Senaste**: det här alternativet först bearbetar alla data som har skickats till Site Recovery-tjänsten för att skapa en återställningspunkt för varje virtuell dator innan åtgärden misslyckas över till den. Det här alternativet ger den lägsta RPO (mål för återställningspunkt), eftersom den virtuella datorn skapas efter växling vid fel har alla data som replikeras till Site Recovery när redundans utlöstes.
    - **Senaste multi-VM bearbetas**: det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har aktiverats för flera Virtuella datorer. Virtuella datorer med inställningen aktiverad växlas över till den senaste vanliga multi-VM konsekventa återställningspunkten. Andra virtuella datorer som växlar över till den senaste bearbetade återställningspunkten.  
    - **Senaste multi-VM programkonsekventa**: det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har aktiverats för flera Virtuella datorer. Virtuella datorer som tillhör en replikeringsgrupp växlas över till den senaste vanliga multi-VM programkonsekventa återställningspunkten. Andra virtuella datorer som växlar över till sina senaste programkonsekventa återställningspunkten. 
    - **Anpassad**: Använd det här alternativet för att växla över en specifik VM till en viss återställningspunkt.
3. Välj ett virtuellt Azure-nätverk test virtuella datorer kommer att skapas.

    - Site Recovery-försök att skapa testa virtuella datorer i ett undernät med samma namn och samma IP-adress som angetts i den **beräknings- och nätverksinställningar** inställningarna för den virtuella datorn.
    - Om ett undernät med samma namn är inte tillgänglig i virtuella Azure-nätverket används för att testa redundans testet VM skapas i det första undernätet i alfabetisk ordning.
    - Om samma IP-adress inte finns i undernätet, får den virtuella datorn en annan tillgänglig IP-adress i undernätet. [Läs mer](#creating-a-network-for-test-failover).
4. Om du växlar över till Azure och datakryptering är aktiverat i **krypteringsnyckeln**, Välj det certifikat som utfärdades när du har aktiverat kryptering under installationen av providern. Du kan ignorera det här steget kryptering är inte aktiverad.
5. Följa redundansförloppet på den **jobb** fliken. Du ska kunna se testdatorn för replikering i Azure-portalen.
6. Om du vill initiera en RDP-anslutning till Azure VM, behöver du [lägga till en offentlig IP-adress](site-recovery-monitoring-and-troubleshooting.md) för gränssnitt för den virtuella datorn. 
7. När allt fungerar som förväntat, klickar du på **Rensa redundanstestet**. Detta tar bort de virtuella datorerna som skapades under testning av redundans.
8. I **anteckningar**, registrera och spara observationer från redundanstestningen. 


![Testa redundans](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

När ett redundanstest utlöses inträffar följande:

1. **Krav för**: en kravkontroll körs ska du kontrollera att alla krav som gäller för växling vid fel är uppfyllda.
2. **Redundans**: redundans bearbetar och förbereda data, så att en virtuell dator i Azure kan skapas från den.
3. **Senaste**: Om du har valt den senaste återställningspunkten, skapa en återställningspunkt från data som skickas till tjänsten.
4. **Starta**: det här steget skapar en virtuell Azure-dator med hjälp av data som bearbetas i föregående steg.

### <a name="failover-timing"></a>Tidsinställning för växling vid fel

Redundanskluster kräver ett extra steg som tar vanligtvis cirka 8 till 10 minuter för att slutföra i följande scenarier:

* Virtuella VMware-datorer med en version av mobilitetstjänsten som är äldre än 9.8
* Fysiska servrar
* Virtuella VMware-Linux-datorer
* Hyper-V-dator skyddas som fysiska servrar
* VMware VM där följande drivrutiner inte startdrivrutiner:
    * storvsc
    * vmbus
    * storflt
    * Intelide
    * ATAPI
* VMware VM som inte har aktiverat DHCP rrespective av om de använder DHCP eller statiska IP-adresser.

Inga mellanliggande steg behövs inte i alla andra fall, och tar avsevärt mindre tid att växling vid fel.


## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för att testa redundans

Vi rekommenderar att för att testa redundans du väljer ett nätverk som är isolerat från återställning plats produktionsnätverket specifika i den **beräknings- och nätverksinställningar** inställningar för varje virtuell dator. Som standard när du skapar en Azure-nätverk, är det isolerade från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernät ska ha samma namn.
- Testnätverket ska använda samma IP-adress rangek.
- Uppdatera DNS i testnätverket med IP-adress som angetts för DNS VM i **beräknings- och nätverksinställningar** inställningar. Läs [redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations) för mer information.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testa redundans för ett produktionsnätverk i återställningsplatsen

Även om vi rekommenderar att du använder ett testnätverk som är separat från produktionsnätverket, om du vill testa en disaster recovery detaljerat produktionsnätverket, Tänk på följande: 

- Kontrollera att den primära virtuella datorn är avstängd när du kör redundanstestet. Otherewise det kommer att två virtuella datorer med samma identitet som körs i samma nätverk på samma gång. Detta kan leda till oväntade konsekvenser.
- Alla ändringar av virtuella datorer som skapades för redundanstestningen försvinner när du rensar bort växling vid fel. Dessa ändringar replikeras inte till den primära virtuella datorn.
- Testa i produktionsmiljön leder till ett driftstopp för dina produktionsprogram. Användare får inte använda appar som körs på virtuella datorer när du testar redundansen pågår.  



## <a name="prepare-active-directory-and-dns"></a>Förbered Active Directory och DNS

Om du vill köra ett redundanstest för program, testa, måste en kopia av din produktionsmiljö för Active Directory i din testmiljö. Läs [redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations) vill veta mer.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Om du vill ansluta till virtuella Azure-datorer med RDP efter en växling vid fel, följa kraven sammanfattas i tabellen.

**Växling vid fel** | **Plats** | **Åtgärder**
--- | --- | ---
**Azure virtuell dator som kör Windows** | Lokala datorer före redundans | För att komma åt den virtuella Azure-datorn via internet aktiverar du RDP och kontrollera att TCP och UDP-regler har lagts till för **offentliga**, och att RDP tillåts för alla profiler i **Windows-brandväggen**  >  **Tillåtna appar**.<br/><br/> För att komma åt den virtuella Azure-datorn via en plats-till-plats-anslutning, aktiverar du RDP på datorn och se till att RDP tillåts i den **Windows-brandväggen** -> **tillåtna appar och funktioner**, för **Domän och privat** nätverk.<br/><br/>  Kontrollera att operativsystemet SAN-principen anges till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Kontrollera att det finns ingen Windows-uppdateringar som väntar på den virtuella datorn när du utlösa redundansväxling. Windows update kan börja när du växlar över och du inte kan logga in på den virtuella datorn förrän uppdateringen är klar. 
**Azure virtuell dator som kör Windows** | Azure VM efter växling vid fel |  [Lägg till en offentlig IP-adress](site-recovery-monitoring-and-troubleshooting.md) för den virtuella datorn.<br/><br/> Reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn (och Azure-undernätet som den är ansluten) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrollera **starta diagnostik** att verifiera en skärmbild av den virtuella datorn.<br/><br/> Om du inte kan ansluta kontrollerar du att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure VM som kör Linux** | Lokala datorer före redundans | Se till att Secure Shell-tjänsten på den virtuella datorn är inställd att starta automatiskt på systemstart.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.
**Azure VM som kör Linux** | Azure VM efter växling vid fel | Reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn (och Azure-undernätet som den är ansluten) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](site-recovery-monitoring-and-troubleshooting.md) för den virtuella datorn.<br/><br/> Kontrollera **starta diagnostik** en skärmbild av den virtuella datorn.<br/><br/>



## <a name="next-steps"></a>Nästa steg
När du har slutfört disaster recovery visa mer information om andra typer av [redundans](site-recovery-failover.md).
