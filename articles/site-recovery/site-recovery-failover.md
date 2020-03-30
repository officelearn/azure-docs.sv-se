---
title: Kör en redundans under haveriberedskap med Azure Site Recovery
description: Så här växlar du över virtuella datorer/fysiska servrar till Azure med Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471276"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Köra en redundans från lokalt till Azure

I den här artikeln beskrivs hur du växlar över lokala datorer till Azure i [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Innan du börjar

- [Lär dig mer](failover-failback-overview.md) om redundansprocessen vid haveriberedskap.
- Om du vill växla över flera datorer [kan du läsa om](recovery-plan-overview.md) hur du samlar datorer i en återställningsplan.
- Innan du gör en fullständig redundans, kör en [katastrofåterställningsövning](site-recovery-test-failover-to-azure.md) för att säkerställa att allt fungerar som förväntat.

## <a name="prepare-to-connect-after-failover"></a>Förbered anslutning efter redundans

För att vara säker på att du kan ansluta till de virtuella Azure-datorerna som skapas efter redundans, här är ett antal saker du behöver göra lokalt före redundans.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Förbereda lokalt för anslutning efter redundans

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter redundans, finns det ett antal saker du behöver göra lokalt före redundans.

**Efter redundans** | **Location** | **Åtgärder**
--- | --- | ---
**Azure VM som kör Windows** | Lokal maskin före redundans | Om du vill komma åt den virtuella Azure-datorn via internet aktiverar du RDP och kontrollerar att TCP- och UDP-regler läggs till för **offentliga**och att RDP tillåts för alla profiler i **Windows-brandväggen** > **som tillåts appar**.<br/><br/> Om du vill komma åt den virtuella Azure-datorn via en plats-till-plats-anslutning aktiverar du RDP på datorn och kontrollerar att RDP tillåts i **Windows-brandväggen** -> **Tillåtna appar och funktioner**för domän och **privata** nätverk.<br/><br/> <br/><br/> Ta bort alla statiska beständiga vägar och WinHTTP-proxy. Kontrollera att operativsystemets SAN-princip är inställd på **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Kontrollera att det inte finns några Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Windows Update kan starta när du växlar över och du kan inte logga in på den virtuella datorn förrän uppdateringen är klar.
**Azure VM kör Linux** | Lokal maskin före redundans | Kontrollera att secure shell-tjänsten på den virtuella datorn är inställd på att starta automatiskt vid systemstart.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.


## <a name="run-a-failover"></a>Köra en redundansväxling

Den här proceduren beskriver hur du kör en redundans för en [återställningsplan](site-recovery-create-recovery-plans.md). Om du vill köra en redundans för en enda virtuell dator följer du instruktionerna för en virtuell virtuell dator för [VMware](vmware-azure-tutorial-failover-failback.md), en [fysisk server](physical-to-azure-failover-failback.md)eller en [Virtuell Hyper-V.](hyper-v-azure-failover-failback-tutorial.md)


Kör redundansväxlingen för återställningsplanen enligt följande:

1. I valvet för platsåterställning väljer du **Återställningsplaner** > *recoveryplan_name*.
2. Klicka på **Redundans**.

    ![Redundans](./media/site-recovery-failover/Failover.png)

3. I **Redundans** > **redundansriktningsriktning**lämnar du standardinställningen om du replikerar till Azure.
4. I **Redundans**väljer du en **återställningspunkt** som du vill växla över.

    - **Senaste**: Använd den senaste punkten. Detta bearbetar alla data som har skickats till site recovery-tjänsten och skapar en återställningspunkt för varje dator. Det här alternativet ger den lägsta RPO (Recovery Point Objective) eftersom den virtuella datorn som skapats efter redundans har alla data som har replikerats till Site Recovery när redundansen utlöstes.
   - **Senast bearbetad:** Använd det här alternativet för att växla över virtuella datorer till den senaste återställningspunkten som redan bearbetats av Site Recovery. Du kan se den senaste bearbetade återställningspunkten i den **senaste vm-återställningspunkten**. Det här alternativet ger en låg RTO eftersom ingen tid går åt till att bearbeta obearbetade data
   - **Senaste app-konsekvent:** Använd det här alternativet för att misslyckas virtuella datorer över till den senaste programmet konsekvent återställningspunkt som har bearbetats av Site Recovery.
   - **Senaste flera virtuella datorer bearbetas:** Med det här alternativet virtuella datorer som ingår i en replikeringsgrupp redundans till den senaste gemensamma multi-VM konsekvent återställningspunkt. Andra virtuella datorer växlar över till sin senaste bearbetade återställningspunkt. Det här alternativet är endast för återställningsplaner som har minst en virtuell dator med konsekvens för flera virtuella datorer aktiverat.
   - **Senaste multi-VM app-konsekvent:** Med det här alternativet virtuella datorer som ingår i en replikeringsgrupp växla över till den senaste gemensamma multi-VM program-konsekvent återställningspunkt. Andra virtuella datorer redundans till sin senaste programkonsekvent återställningspunkt. Endast för återställningsplaner som har minst en virtuell dator med konsekvens för flera virtuella datorer aktiverat.
   - **Anpassad:** Inte tillgänglig för återställningsplaner. Det här alternativet är endast för redundans för enskilda virtuella datorer.

5. Välj **Stäng av datorn innan du påbörjar redundans** om du vill att virtuella datorer för webbplatsåterställning ska stängas av källan innan du startar redundansen. Redundansen fortsätter även om avstängningen misslyckas.  

    > [!NOTE]
    > Om du växlar över hyper-virtuella datorer försöker avstängningen synkronisera och replikera lokala data som ännu inte har skickats till tjänsten innan redundansen utlöses. 

6. Följ redundans förloppet på sidan **Jobb.** Även om fel inträffar körs återställningsplanen tills den är klar.
7. Efter redundansen loggar du in på den virtuella datorn för att validera den. 
8. Om du vill växla till en annan återställningspunkt som ska användas för redundansen använder du **Ändra återställningspunkt**.
9. När du är redo kan du genomföra redundansen. Åtgärden **Commit** tar bort alla återställningspunkter som är tillgängliga med tjänsten. Alternativet **Ändra återställningspunkt** är inte längre tillgängligt.

## <a name="run-a-planned-failover-hyper-v"></a>Kör en planerad redundans (Hyper-V)

Du kan köra en planerad redundans för virtuella hyper-virtuella datorer.

- En planerad redundans är ett nolldataförlustundanfallsalternativ.
- När en planerad redundans utlöses stängs först käll virtuella datorer av, de senaste data synkroniseras och sedan utlöses en redundans.
- Du kör en planerad redundans med alternativet **Planerad redundans.** Det körs på ett liknande sätt som en vanlig redundans.
 
## <a name="track-failovers"></a>Spåra redundans

Det finns ett antal jobb som är associerade med redundans.

![Redundans](./media/site-recovery-failover/FailoverJob.png)

- **Förutsättningskontroll**: Säkerställer att alla villkor som krävs för redundans är uppfyllda.
- **Redundans:** Bearbetar data så att en Azure-virtuell dator kan skapas från den. Om du har valt **Senaste** återställningspunkt skapas en återställningspunkt från de data som har skickats till tjänsten.
- **Start**: Skapar en virtuell Azure-dator med hjälp av de data som bearbetas i föregående steg.

> [!WARNING]
> **Avbryt inte en pågående redundans:** Innan redundansen startas stoppades replikeringssnad för den virtuella datorn. Om du avbryter ett pågående jobb stoppas redundansen, men den virtuella datorn startar inte replikeras. Replikeringen kan inte startas igen.


### <a name="extra-failover-time"></a>Extra redundanstid

I vissa fall kräver vm-redundans mellanliggande steg som vanligtvis tar cirka åtta till 10 minuter att slutföra. Dessa är de maskiner som påverkas av detta ytterligare steg / tid:

* Virtuella VMware-datorer som kör en mobilitetstjänstversion som är äldre än 9.8.
* Fysiska servrar och virtuella hyper-virtuella datorer som skyddas som fysiska servrar.
* Virtuella Linux-datorer.
* Virtuella datorer med VMware som dessa drivrutiner inte finns som startdrivrutiner:
    * storvsc (storvsc)
    * vmbus (vmbus)
    * storflt (storflt)
    * intelide (intelide)
    * Atapi
* Virtuella datorer med VMware som inte har DHCP aktiverat, oavsett om de använder DHCP eller statiska IP-adresser.


## <a name="automate-actions-during-failover"></a>Automatisera åtgärder under redundans

Du kanske vill automatisera åtgärder under redundans. För att göra detta kan du använda skript eller Azure automation runbooks i återställningsplaner.

- [Läs mer](site-recovery-create-recovery-plans.md) om hur du skapar och anpassar återställningsplaner, inklusive att lägga till skript.
- [Läs mer](site-recovery-runbook-automation.md) om hur du lägger till Azure Automation-runbooks i återställningsplaner.


## <a name="configure-settings-after-failover"></a>Konfigurera inställningar efter redundans

### <a name="retain-drive-letters-after-failover"></a>Behåll enhetsbeteckningar efter redundans

Site Recovery hanterar kvarhållning av enhetsbeteckningar. Om du utesluter diskar under VM-replikering [kan du granska ett exempel på](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) hur det fungerar.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Förbered i Azure för att ansluta efter redundans

Om du vill ansluta till virtuella Azure-datorer som skapas efter redundans med RDP eller SSH följer du kraven som sammanfattas i tabellen.

**Redundans** | **Location** | **Åtgärder**
--- | --- | ---
**Azure VM som kör Windows** | Azure VM efter redundans |  [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Reglerna för nätverkssäkerhetsgruppen för den misslyckade över den virtuella datorn (och Det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrollera **startdiagnostik** för att verifiera en skärmbild av den virtuella datorn.<br/><br/> Om du inte kan ansluta kontrollerar du att den virtuella datorn körs och läser de här [felsökningstipsen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure VM kör Linux** | Azure VM efter redundans | Reglerna för nätverkssäkerhetsgruppen för den misslyckade över den virtuella datorn (och Det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Kontrollera **Startdiagnostik** för en skärmbild av den virtuella datorn.<br/><br/>

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.

## <a name="set-up-ip-addressing"></a>Konfigurera IP-adressering

- **Interna IP-adresser:** Om du vill ange den interna IP-adressen för en virtuell Azure-dator efter redundans har du ett par alternativ:
    - Behåll samma IP-adress: Du kan använda samma IP-adress på den virtuella Azure-datorn som den som allokeras till den lokala datorn.
    - Använd olika IP-adress: Du kan använda en annan IP-adress för Den virtuella Azure-datorn.
    - [Läs mer](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) om hur du konfigurerar interna IP-adresser.
- **Externa IP-adresser:** Du kan behålla offentliga IP-adresser vid redundans. Virtuella Azure-datorer som skapats som en del av redundansprocessen måste tilldelas en offentlig Azure-IP-adress som är tillgänglig i Azure-regionen. Du kan tilldela en offentlig IP-adress antingen manuellt eller genom att automatisera processen med en återställningsplan. [Läs mer](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Nästa steg

När du har misslyckats över måste du reprotera för att börja replikera azure-virtuella datorer tillbaka till den lokala platsen. När replikeringen är igång kan du växla tillbaka lokalt när du är redo.

- [Läs mer](failover-failback-overview.md#reprotectionfailback) om återskydd och återställning.
- [Förbered dig](vmware-azure-reprotect.md) för VMware-återskydd och återställning.
- [Växla tillbaka](hyper-v-azure-failback.md) Virtuella datorer med hyper-V.
- [Lär dig mer om](physical-to-azure-failover-failback.md) redundans- och redundansprocessen för fysiska servrar.

