---
title: Kör en testväxling (haveriberedskapsövning) till Azure i Azure Site Recovery
description: Lär dig mer om hur du kör en testväxling från lokalt till Azure med hjälp av Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 26c734b7a2e9f5592ee6d51dfee4650a3998ab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257529"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Kör en testväxling (borrmaskin för haveriberedskap) till Azure 


I den här artikeln beskrivs hur du kör en borrmaskin för haveriberedskap till Azure med hjälp av en redundansväxling för site recovery-test.  

Du kör en testväxling för att validera replikerings- och haveriberedskapsstrategin, utan dataförlust eller driftstopp. En testväxling påverkar inte pågående replikering eller din produktionsmiljö. Du kan köra en testundanställning på en viss virtuell dator (VM) eller på en [återställningsplan](site-recovery-create-recovery-plans.md) som innehåller flera virtuella datorer.


## <a name="run-a-test-failover"></a>Köra ett redundanstest
Den här proceduren beskriver hur du kör en testväxling för en återställningsplan. Om du vill köra en testundanställning för en enda virtuell dator följer du stegen som beskrivs [här](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Testa redundans](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Klicka på **Återställningsplaner** > *recoveryplan_name* > **Test Redundans**i Site Recovery i Azure-portalen .
2. Välj en **återställningspunkt** som du vill växla över. Du kan välja något av följande alternativ:
    - **Senast bearbetad:** Det här alternativet misslyckas över alla virtuella datorer i planen till den senaste återställningspunkten som bearbetas av Site Recovery. Om du vill se den senaste återställningspunkten för en viss virtuell dator kontrollerar du **de senaste återställningspunkterna** i vm-inställningarna. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste app-konsekvent:** Det här alternativet misslyckas över alla virtuella datorer i planen till den senaste programkonsekvent återställningspunkt som bearbetas av Site Recovery. Om du vill se den senaste återställningspunkten för en viss virtuell dator kontrollerar du **de senaste återställningspunkterna** i vm-inställningarna.
    - **Senaste:** Det här alternativet bearbetar först alla data som har skickats till site recovery-tjänsten, för att skapa en återställningspunkt för varje virtuell dator innan den inte över till den. Det här alternativet ger den lägsta RPO (Recovery Point Objective), eftersom den virtuella datorn som skapats efter redundans kommer att ha alla data replikeras till Site Recovery när redundansen utlöstes.
    - **Senaste flera virtuella datorer som bearbetas:** Det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverat. Virtuella datorer med inställningen aktiverad växla över till den senaste gemensamma konsekventa återställningspunkten för flera virtuella datorer. Andra virtuella datorer växlar över till den senaste bearbetade återställningspunkten.  
    - **Senaste multi-VM app-konsekvent:** Det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har multi-VM konsekvens aktiverat. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga återställningspunkten för flera virtuella datorer. Andra virtuella datorer växlar över till sin senaste programkonsekventa återställningspunkt.
    - **Anpassad:** Använd det här alternativet om du vill växla över en viss virtuell dator till en viss återställningspunkt.
3. Välj ett virtuellt Azure-nätverk där test-virtuella datorer ska skapas.

    - Site Recovery försöker skapa test-virtuella datorer i ett undernät med samma namn och samma IP-adress som den som anges i **dators beräknings- och nätverksinställningar.**
    - Om ett undernät med samma namn inte är tillgängligt i det virtuella Azure-nätverket som används för test redundans, skapas testdatorn i det första undernätet i alfabetisk ordning.
    - Om samma IP-adress inte är tillgänglig i undernätet får den virtuella datorn en annan tillgänglig IP-adress i undernätet. [Läs mer](#create-a-network-for-test-failover).
4. Om du misslyckas med Azure och datakryptering är aktiverat väljer du det certifikat som utfärdades när du aktiverade kryptering under providerinstallationen i **Krypteringsnyckel.** Du kan ignorera det här steget om kryptering inte är aktiverad.
5. Spåra redundansstatus på fliken **Jobb.** Du bör kunna se testrepliken i Azure-portalen.
6. Om du vill initiera en RDP-anslutning till den virtuella Azure-datorn måste du lägga till [en offentlig IP-adress](https://aka.ms/addpublicip) i nätverksgränssnittet för den misslyckade över den virtuella datorn.
7. När allt fungerar som förväntat klickar du på **Rensa test redundans**. Detta tar bort de virtuella datorer som skapades under test redundans.
8. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.


![Testa redundans](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

När en testväxling utlöses inträffar följande:

1. **Förutsättningar**: En förutsättningskontroll körs för att säkerställa att alla villkor som krävs för redundans uppfylls.
2. **Redundans:** Redundansprocesserna och förberedde data, så att en Azure-virtuell dator kan skapas från den.
3. **Senaste**: Om du har valt den senaste återställningspunkten skapas en återställningspunkt från de data som har skickats till tjänsten.
4. **Start:** Det här steget skapar en virtuell Azure-dator med hjälp av de data som bearbetas i föregående steg.

### <a name="failover-timing"></a>Tid för redundans

I följande scenarier kräver redundans ett extra mellanliggande steg som vanligtvis tar cirka 8 till 10 minuter att slutföra:

* Virtuella datorer med VMware som kör en version av mobilitetstjänsten som är äldre än 9.8
* Fysiska servrar
* Virtuella datorer med VMware Linux
* Hyper-V VM skyddad som fysiska servrar
* Virtuell VMware där följande drivrutiner inte startas:
    * storvsc (storvsc)
    * vmbus (vmbus)
    * storflt (storflt)
    * intelide (intelide)
    * Atapi
* Virtuell VMware-dator som inte har DHCP aktiverat , oavsett om de använder DHCP eller statiska IP-adresser.

I alla andra fall krävs inget mellanliggande steg, och redundans tar betydligt kortare tid.


## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för redundanstest

Vi rekommenderar att du för redundanstest väljer ett nätverk som är isolerat från produktionsnätverkets återställningsplats specifik i inställningarna för **Beräkning och nätverk** för varje virtuella dator. Som standard, när du skapar ett virtuellt Azure-nätverk är det isolerat från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernäten ska ha samma namn.
- Textnätverket ska använda samma IP-adressintervall.
- Uppdatera DNS för testnätverket med IP-adressen som anges för DNS-VM i inställningarna i **Beräkning och nätverk**. Mer information finns i [Saker att tänka på vid redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testa redundans till ett produktionsnätverk på återställningsplatsen

Även om vi rekommenderar att du använder ett testnätverk som är skilt från produktionsnätverket, bör du tänka på följande om du vill testa en borrmaskin för haveriberedskap i produktionsnätverket:

- Kontrollera att den primära virtuella datorn stängs av när du kör testundansen. Annars kommer det att finnas två virtuella datorer med samma identitet, som körs i samma nätverk samtidigt. Detta kan leda till oväntade konsekvenser.
- Alla ändringar av virtuella datorer som skapats för test redundans går förlorade när du rensar redundansen. Dessa ändringar replikeras inte tillbaka till den primära virtuella datorn.
- Testning i din produktionsmiljö leder till driftstopp för ditt produktionsprogram. Användare bör inte använda appar som körs på virtuella datorer när test redundans pågår.  



## <a name="prepare-active-directory-and-dns"></a>Förbereda Active Directory och DNS

Om du vill köra en testväxling för programtestning behöver du en kopia av active directory-produktionsmiljön i testmiljön. Läs [test redundansöverväganden för Active Directory om](site-recovery-active-directory.md#test-failover-considerations) du vill veta mer.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter redundans följer du kraven som sammanfattas i tabellen.

**Redundans** | **Location** | **Åtgärder**
--- | --- | ---
**Azure VM som kör Windows** | Lokal maskin före redundans | Om du vill komma åt den virtuella Azure-datorn via internet aktiverar du RDP och kontrollerar att TCP- och UDP-regler läggs till för **offentliga**och att RDP tillåts för alla profiler i **Windows-brandväggen** > **som tillåts appar**.<br/><br/> Om du vill komma åt den virtuella Azure-datorn via en plats-till-plats-anslutning aktiverar du RDP på datorn och kontrollerar att RDP tillåts i **Windows-brandväggen** -> **Tillåtna appar och funktioner**för domän och **privata** nätverk.<br/><br/>  Kontrollera att operativsystemets SAN-princip är inställd på **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Kontrollera att det inte finns några Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Windows Update kan starta när du växlar över och du kan inte logga in på den virtuella datorn förrän uppdateringen är klar.
**Azure VM som kör Windows** | Azure VM efter redundans |  [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Reglerna för nätverkssäkerhetsgruppen för den misslyckade över den virtuella datorn (och Det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrollera **startdiagnostik** för att verifiera en skärmbild av den virtuella datorn.<br/><br/> Om du inte kan ansluta kontrollerar du att den virtuella datorn körs och läser de här [felsökningstipsen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure VM kör Linux** | Lokal maskin före redundans | Kontrollera att secure shell-tjänsten på den virtuella datorn är inställd på att starta automatiskt vid systemstart.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.
**Azure VM kör Linux** | Azure VM efter redundans | Reglerna för nätverkssäkerhetsgruppen för den misslyckade över den virtuella datorn (och Det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Kontrollera **Startdiagnostik** för en skärmbild av den virtuella datorn.<br/><br/>

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.

## <a name="next-steps"></a>Nästa steg
När du har slutfört en övning för haveriberedskap kan du läsa mer om andra typer av [redundans.](site-recovery-failover.md)
