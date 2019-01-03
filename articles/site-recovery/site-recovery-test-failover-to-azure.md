---
title: Kör ett programåterställningstest till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig mer om hur du kör ett programåterställningstest från lokala till Azure med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 19f41256866b42962be36bbb97f5f6d3c06d7fed
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976570"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Köra ett programåterställningstest till Azure 


Den här artikeln beskriver hur du kör ett programåterställningstest till Azure med ett redundanstest för Site Recovery.  

Du kör ett redundanstest för att verifiera dina replikering och strategi för haveriberedskap, utan någon dataförlust eller driftstopp. Ett redundanstest påverka inte pågående replikering eller din produktionsmiljö. Du kan köra ett redundanstest på en specifik virtuell dator (VM) eller på en [återställningsplanen](site-recovery-create-recovery-plans.md) som innehåller flera virtuella datorer.


## <a name="run-a-test-failover"></a>Köra ett redundanstest
Den här proceduren beskriver hur du kör ett redundanstest för en återställningsplan. Om du vill köra ett redundanstest för en enskild virtuell dator, följer du stegen som beskrivs [här](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Testa redundans](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. I Site Recovery i Azure portal, klickar du på **Återställningsplaner** > *recoveryplan_name* > **Redundanstest**.
2. Välj en **återställningspunkt** som att redundansväxla. Du kan välja något av följande alternativ:
    - **Senaste bearbetade**: Det här alternativet redundansväxlar alla virtuella datorer i planerna på att den senaste återställningspunkten som bearbetats av Site Recovery. Om du vill se den senaste återställningspunkten för en specifik virtuell dator, kontrollera **senaste återställningspunkter** i inställningarna för virtuella datorer. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer i planerna på att den senaste programkonsekventa återställningspunkten som bearbetats av Site Recovery. Om du vill se den senaste återställningspunkten för en specifik virtuell dator, kontrollera **senaste återställningspunkter** i inställningarna för virtuella datorer.
    - **Senaste**: Det här alternativet bearbetar först alla data som har skickats till Site Recovery-tjänsten för att skapa en återställningspunkt för varje virtuell dator innan växling till den. Det här alternativet ger den lägsta RPO (mål för återställningspunkt), eftersom den virtuella datorn skapas efter redundans har alla data som replikeras till Site Recovery när redundansen utlöstes.
    - **Senaste multi-VM bearbetas**: Det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har konsekvens aktiverat. Virtuella datorer med inställningen aktiverad växlar över till den senaste vanliga Konsekvens programkonsekvent återställningspunkten. Andra virtuella datorer som växlar över till den senaste bearbetade återställningspunkten.  
    - **Senaste multi-VM appkonsekvent**: Det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har konsekvens aktiverat. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga Konsekvens programkonsekventa återställningspunkten. Andra virtuella datorer som växlar över till sina senaste programkonsekventa återställningspunkten.
    - **Anpassad**: Använd det här alternativet om du vill redundansväxla en specifik virtuell dator till en specifik återställningspunkt.
3. Välj Azure-nätverk där test virtuella datorer kommer att skapas.

    - Site Recovery försöker skapa testa virtuella datorer i ett undernät med samma namn och samma IP-adress som anges i den **beräkning och nätverk** inställningarna för den virtuella datorn.
    - Om ett undernät med samma namn inte är tillgängligt i Azure-nätverk som används för att testa redundans, sedan testet VM skapas i det första undernätet i alfabetisk ordning.
    - Om samma IP-adress inte är tillgänglig i undernätet, får den virtuella datorn en annan tillgänglig IP-adress i undernätet. [Läs mer](#create-a-network-for-test-failover).
4. Om du växlar över till Azure och datakryptering är aktiverat i **krypteringsnyckeln**, Välj det certifikat som utfärdades när du har aktiverat kryptering under providerinstallationen. Du kan ignorera det här steget Ingen kryptering har aktiverats.
5. Följa redundansförloppet på den **jobb** fliken. Du bör kunna se replikdator för testning i Azure-portalen.
6. Om du vill initiera en RDP-anslutning till den virtuella Azure-datorn, måste du [lägga till en offentlig IP-adress](https://aka.ms/addpublicip) för gränssnitt på den redundansväxlade virtuella datorn.
7. När allt fungerar som förväntat, klickar du på **Rensa redundanstestning**. Detta tar bort de virtuella datorerna som skapades under redundanstestningen.
8. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.


![Testa redundans](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

När ett redundanstest utlöses, inträffar följande:

1. **Krav för**: En kravkontroll körs för att se till att alla villkor som krävs för redundans är uppfyllda.
2. **Redundans**: Redundansen bearbetar och förbereds data, så att en virtuell Azure-dator kan skapas från den.
3. **Senaste**: Om du har valt den senaste återställningspunkten skapas en återställningspunkt från data som skickas till tjänsten.
4. **Starta**: Det här steget skapar en Azure virtuell dator med hjälp av data som behandlas i föregående steg.

### <a name="failover-timing"></a>Tidsinställningen för redundans

I följande scenarier kräver ett extra steg som vanligtvis tar cirka 8 till 10 minuter för att slutföra redundans:

* Virtuella VMware-datorer som kör en version av mobilitetstjänstversion som är äldre än 9.8
* Fysiska servrar
* Virtuella VMware Linux-datorer
* Hyper-V-dator som skyddas som fysiska servrar
* VMware VM där följande drivrutiner inte startdrivrutiner:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware VM som inte har DHCP-aktiverat, oavsett om de använder DHCP eller statiska IP-adresser.

Inga mellanliggande steg krävs inte i alla andra fall, och redundans tar avsevärt kortare tid.


## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för redundanstest

Vi rekommenderar att du för redundanstest väljer ett nätverk som är isolerat från produktionsnätverkets återställningsplats specifik i inställningarna för **Beräkning och nätverk** för varje virtuella dator. Som standard, när du skapar ett virtuellt Azure-nätverk är det isolerat från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernäten ska ha samma namn.
- Textnätverket ska använda samma IP-adressintervall.
- Uppdatera DNS för testnätverket med IP-adressen som anges för DNS-VM i inställningarna i **Beräkning och nätverk**. Mer information finns i [Saker att tänka på vid redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testa redundans till ett produktionsnätverk i återställningsplatsen

Även om vi rekommenderar att du använder ett testnätverk som är separat från företagets nätverk, om du vill testa ett programåterställningstest till företagets nätverk, Tänk på följande:

- Kontrollera att den primära virtuella datorn stängs av när du kör redundanstestet. Annars kommer att två virtuella datorer med samma identitet som körs i samma nätverk på samma gång. Detta kan leda till oväntade konsekvenser.
- Ändringar av virtuella datorer som skapas för att testa redundans försvinner när du rensar växling vid fel. Dessa ändringar replikeras inte tillbaka till den primära virtuella datorn.
- Testa i din produktionsmiljö leder till ett driftstopp för dina produktionsprogram. Användare bör inte använda appar som körs på virtuella datorer när du testar redundansen pågår.  



## <a name="prepare-active-directory-and-dns"></a>Förbered Active Directory och DNS

Om du vill köra ett redundanstest för program som testar behöver du en kopia av produktionsmiljön för Active Directory i din testmiljö. Läs [testa tänka på vid för Active Directory](site-recovery-active-directory.md#test-failover-considerations) vill veta mer.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter en redundansväxling kan du följa kraven som sammanfattas i tabellen.

**Redundans** | **Plats** | **Åtgärder**
--- | --- | ---
**Azure virtuell dator som kör Windows** | Den lokala datorn före redundans | För att komma åt den virtuella Azure-datorn via internet aktiverar du RDP och se till att TCP och UDP-regler lagts till för **offentliga**, och att RDP tillåts för alla profiler i **Windows-brandväggen**  >  **Tillåtna appar**.<br/><br/> För att komma åt den virtuella Azure-datorn via en plats-till-plats-anslutning aktiverar du RDP på datorn och se till att RDP tillåts i den **Windows-brandväggen** -> **tillåtna appar och funktioner**, för **Domän och privat** nätverk.<br/><br/>  Kontrollera att operativsystemet SAN-princip har angetts **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Kontrollera att det finns inga Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Windows update kan börja när du växlar över, och du kan inte logga in på den virtuella datorn förrän uppdateringen är klar.
**Azure virtuell dator som kör Windows** | Azure virtuell dator efter redundans |  [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn (och Azure-undernätet som den är ansluten) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrollera **Startdiagnostik** att verifiera en skärmbild av den virtuella datorn.<br/><br/> Om du inte kan ansluta kontrollerar du att den virtuella datorn körs, och dessa [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure virtuell dator som kör Linux** | Den lokala datorn före redundans | Kontrollera att Secure Shell-tjänsten på den virtuella datorn är inställd att starta automatiskt vid systemstart.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.
**Azure virtuell dator som kör Linux** | Azure virtuell dator efter redundans | Reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn (och Azure-undernätet som den är ansluten) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Kontrollera **Startdiagnostik** för en skärmbild av den virtuella datorn.<br/><br/>

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.

## <a name="next-steps"></a>Nästa steg
När du har slutfört ett programåterställningstest, Läs mer om andra typer av [redundans](site-recovery-failover.md).
