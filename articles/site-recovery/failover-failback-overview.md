---
title: Om redundans och redundans i Azure Site Recovery
description: Lär dig mer om redundans och redundans i Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281813"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Om redundans-/redundans-/redundans för haveriberedskap på lokal katastrofåterställning

Den här artikeln innehåller en översikt över redundans och redundans vid haveriberedskap av lokala datorer till Azure med [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Återhämtningssteg

Redundans och redundans i Site Recovery har fyra steg:

- **Steg 1: Växla över från lokala:** När du har konfigurerat replikering till Azure för lokala datorer, när din lokala plats går ner, misslyckas du dessa datorer över till Azure. Efter redundans skapas virtuella Azure-datorer från replikerade data.
- **Steg 2: Reprotect Azure virtuella datorer:** I Azure, du reprotect Azure virtuella datorer så att de börjar replikera tillbaka till den lokala webbplatsen. Den lokala virtuella datorn (om sådan finns) är inaktiverad under återskydd, för att säkerställa datakonsekvens.
- **Steg 3: Växla över från Azure:** När din lokala webbplats körs som vanligt igen kör du en annan redundans, den här gången för att återställa virtuella Azure-datorer till din lokala webbplats. Du kan växla tillbaka till den ursprungliga platsen som du misslyckades med eller till en annan plats.
- **Steg 4: Rotera lokala datorer**på plats : Efter att ha misslyckats tillbaka kan replikering av lokala datorer till Azure åter igen aktivera replikering av lokala datorer till Azure.

## <a name="failover"></a>Redundans

Du utför en redundans som en del av din strategi för affärskontinuitet och haveriberedskap (BCDR).

- Som ett första steg i din BCDR-strategi replikerar du dina lokala datorer till Azure kontinuerligt. Användare får åtkomst till arbetsbelastningar och appar som körs på lokala källdatorer.
- Om behov uppstår, till exempel om det finns ett avbrott lokalt, misslyckas du replikerande datorer över till Azure. Virtuella Azure-datorer skapas med hjälp av replikerade data.
- För kontinuitet i verksamheten kan användare fortsätta att komma åt appar på virtuella Azure-datorer.

Redundans är en aktivitet i två faser:

- **Redundans:** Redundansen som skapar och tar upp en Virtuell Azure-dator med den valda återställningspunkten.
- **Commit**: Efter redundans verifierar du den virtuella datorn i Azure:
    - Du kan sedan genomföra redundansen till den valda återställningspunkten eller välja en annan punkt för commit.
    - När du har begått redundansen kan återställningspunkten inte ändras.


## <a name="connect-to-azure-after-failover"></a>Ansluta till Azure efter redundans

Om du vill ansluta till virtuella Azure-datorer som skapats efter redundans med RDP/SSH finns det ett antal krav.

**Redundans** | **Location** | **Åtgärder**
--- | --- | ---
**Azure VM (Windows(** | På den lokala maskinen före redundans | **Tillgång via internet**: Aktivera RDP. Kontrollera att TCP- och UDP-regler läggs till för **Offentliga**och att RDP är tillåtet för alla profiler i **Windows-brandväggen** > **Tillåtna appar**.<br/><br/> **Åtkomst via plats-till-plats-VPN:** Aktivera RDP på datorn. Kontrollera att RDP är tillåtet i **Windows-brandväggen** -> **Tillåts appar och funktioner**, för domän och **privata** nätverk.<br/><br/>  Kontrollera att operativsystemets SAN-princip är inställd på **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Kontrollera att det inte finns några Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Windows Update kan starta när du växlar över och du kan inte logga in på den virtuella datorn förrän uppdateringarna är klara.
**Azure VM som kör Windows** | På den virtuella Azure-datorn efter redundans |  [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Reglerna för nätverkssäkerhetsgruppen för den misslyckade över den virtuella datorn (och Det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrollera **startdiagnostik** för att verifiera en skärmbild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs och granskar [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure VM kör Linux** | På den lokala maskinen före redundans | Kontrollera att secure shell-tjänsten på den virtuella datorn är inställd på att starta automatiskt vid systemstart.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.
**Azure VM kör Linux** | På den virtuella Azure-datorn efter redundans | Reglerna för nätverkssäkerhetsgruppen för den misslyckade över den virtuella datorn (och Det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](https://aka.ms/addpublicip) för den virtuella datorn.<br/><br/> Kontrollera **Startdiagnostik** för en skärmbild av den virtuella datorn.<br/><br/>

## <a name="types-of-failover"></a>Typer av redundans

Site Recovery ger olika redundansalternativ.

**Redundans** | **Detaljer** | **Återställning** | **Arbetsflöde**
--- | --- | --- | ---
**Redundanstest** | Används för att köra en övning som validerar din BCDR-strategi, utan dataförlust eller driftstopp.| Skapar en kopia av den virtuella datorn i Azure, utan någon inverkan på pågående replikering eller på din produktionsmiljö. | 1. Kör en testundanställning på en enda virtuell dator eller på flera virtuella datorer i en återställningsplan.<br/><br/> 2. Välj en återställningspunkt som ska användas för test redundansen.<br/><br/> 3. Välj ett Azure-nätverk där Den virtuella Azure-datorn ska finnas när den skapas efter redundans. Nätverket används endast för test redundans.<br/><br/> 4. Kontrollera att borren fungerade som förväntat. Site Recovery rensar automatiskt upp virtuella datorer som skapats i Azure under övningen.
**Planerad redundans-Hyper-V**  | Används vanligtvis för planerade driftstopp.<br/><br/> Virtuella datorer med källa stängs av. De senaste data synkroniseras innan redundansen startas. | Noll dataförlust för det planerade arbetsflödet. | 1. Planera ett underhållsfönster för driftstopp och meddela användarna.<br/><br/> 2. Koppla från appar som är vända mot användaren.<br/><br/> 3. Initiera en planerad redundans med den senaste återställningspunkten. Redundansen körs inte om datorn inte stängs av eller om fel påträffas.<br/><br/> 4. Kontrollera efter redundansen att den virtuella repliken är aktiv i Azure.<br/><br/> 5. Begå redundans för att avsluta. Commit-åtgärden tar bort alla återställningspunkter.
**Redundans-Hyper-V** | Körs vanligtvis om det finns ett oplanerat avbrott eller om den primära platsen inte är tillgänglig.<br/><br/> Du kan också stänga av den virtuella datorn och synkronisera slutliga ändringar innan du initierar redundansen.  | Minimal dataförlust för appar. | 1. Initiera din BCDR-plan. <br/><br/> 2. Starta en redundans. Ange om site recovery ska stänga av den virtuella datorn och synkronisera/replikera de senaste ändringarna innan redundansen utlöses.<br/><br/> 3. Du kan växla över till ett antal återställningspunktsalternativ, sammanfattade i tabellen nedan.<br/><br/> Om du inte aktiverar alternativet för att stänga av den virtuella datorn, eller om site recovery inte kan stänga av den, används den senaste återställningspunkten.<br/>Redundansen körs även om maskinen inte kan stängas av.<br/><br/> 4. Efter redundans kontrollerar du att repliken Azure VM är aktiv i Azure.<br/> Om det behövs kan du välja en annan återställningspunkt från kvarhållningsfönstret på 24 timmar.<br/><br/> 5. Begå redundans för att avsluta. Commit-åtgärden tar bort alla tillgängliga återställningspunkter.
**Redundans-VMware** | Körs vanligtvis om det finns ett oplanerat avbrott eller om den primära platsen inte är tillgänglig.<br/><br/> Du kan också ange att site recovery ska försöka utlösa en avstängning av den virtuella datorn och synkronisera och replikera slutliga ändringar innan du initierar redundansen.  | Minimal dataförlust för appar. | 1. Initiera din BCDR-plan. <br/><br/> 2. Initiera en redundans från site recovery. Ange om Site Recovery ska försöka utlösa vm-avstängning och synkronisera innan du kör redundansen.<br/> Redundansen körs även om datorerna inte kan stängas av.<br/><br/> 3. Kontrollera efter redundansen att den virtuella repliken är aktiv i Azure. <br/>Om det behövs kan du välja en annan återställningspunkt från kvarhållningsfönstret på 72 timmar.<br/><br/> 5. Begå redundans för att avsluta. Commit-åtgärden tar bort alla återställningspunkter.<br/> För virtuella Windows-datorer inaktiverar Site Recovery VMware-verktygen under redundans.

## <a name="failover-processing"></a>Redundansbearbetning

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker längre test redundanstider för:

* Virtuella datorer med VMware som kör en mobilitetstjänstversion som är äldre än 9.8.
* Fysiska servrar.
* Virtuella Linux-datorer.
* Virtuella datorer med hyper-V skyddas som fysiska servrar.
* Virtuella datorer med VMware som inte har DHCP-tjänsten aktiverad.
* Virtuella datorer med VMware som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Alternativ för återställningspunkt

Under redundans kan du välja ett antal alternativ för återställningspunkt.

**Alternativet** | **Detaljer**
--- | ---
**Senaste (lägsta RPO)** | Det här alternativet ger det lägsta återställningspunktsmålet (RPO). Den bearbetar först alla data som har skickats till site recovery-tjänsten, för att skapa en återställningspunkt för varje virtuell dator, innan den inte över till den. Den här återställningspunkten har alla data replikerade till site recovery när redundansen utlöstes.
**Senast bearbetad**  | Det här alternativet växlar över virtuella datorer till den senaste återställningspunkten som bearbetas av Site Recovery. Om du vill se den senaste återställningspunkten för en viss virtuell dator kontrollerar du **de senaste återställningspunkterna** i vm-inställningarna. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
**Senaste appkonsekvent** |  Det här alternativet växlar över virtuella datorer till den senaste programkonsekventa återställningspunkten som bearbetas av Site Recovery, om appkonsekventa återställningspunkter är aktiverade. Kontrollera den senaste återställningspunkten i vm-inställningarna.
**Senaste flera virtuella datorer bearbetas** | Det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverat. Virtuella datorer med inställningen aktiverad växla över till den senaste gemensamma konsekventa återställningspunkten för flera virtuella datorer. Alla andra virtuella datorer i planen växlar över till den senaste bearbetade återställningspunkten.
**Senaste appkonsekvent för flera virtuella datorer** |  Det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverat. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga återställningspunkten för flera virtuella datorer. Andra virtuella datorer växlar över till sin senaste programkonsekventa återställningspunkt.
**Anpassad** | Använd det här alternativet om du vill växla över en viss virtuell dator till en viss återställningspunkt i tiden. Det här alternativet är inte tillgängligt för återställningsplaner.

> [!NOTE]
> Återställningspunkter kan inte migreras till ett annat Recovery Services-valv.

## <a name="reprotectionfailback"></a>Återbeskydda/återställning efter fel

Efter redundans till Azure är de replikerade virtuella Azure-datorerna i ett oskyddat tillstånd.

- Som ett första steg för att misslyckas tillbaka till din lokala plats måste du starta virtuella Azure-datorer som replikerar till lokala. Återskyddsprocessen beror på vilken typ av datorer du misslyckades med.
- När datorer replikeras från Azure till lokalt kan du köra en redundans från Azure till din lokala webbplats.
- När datorer körs lokalt igen kan du aktivera replikering så att de replikeras till Azure för haveriberedskap.

Failback fungerar på följande sätt:

- För att misslyckas tillbaka behöver en virtuell dator minst en återställningspunkt för att kunna återställas igen. I en återställningsplan behöver alla virtuella datorer i planen minst en återställningspunkt.
- Vi rekommenderar att du använder den **senaste återställningspunkten** för att växla tillbaka (detta är en kraschkonsekvent punkt).
    - Det finns ett alternativ för en appkonsekvent återställningspunkt. I det här fallet återställs en enda virtuell dator till sin senaste tillgängliga appkonsekventa återställningspunkt. För en återställningsplan med en replikeringsgrupp återställs varje replikeringsgrupp till sin gemensamma tillgängliga återställningspunkt.
    - Appkonsekventa återställningspunkter kan ligga efter i tid, och det kan finnas förlust i data.
- Under redundans från Azure till den lokala platsen stänger Site Recovery av azure-virtuella datorer. När du genomför redundansen tar Site Recovery bort de misslyckade azure-virtuella datorerna i Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fysiskt återskydd/återställning

För att återställa vmware-datorer och fysiska servrar från Azure till lokala behöver du en infrastruktur för återställning efter fel och det finns ett antal krav.

- **Tillfällig processserver i Azure:** För att växla tillbaka från Azure konfigurerar du en Virtuell Azure-dator för att fungera som en processserver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
- **VPN-anslutning:** För att växla tillbaka behöver du en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
- **Separat huvudmålserver:** Som standard hanterar huvudmålservern som installerades med konfigurationsservern på den lokala virtuella datorn för VMware återställning. Om du behöver växla tillbaka stora trafikvolymer ställer du in en separat lokal huvudmålserver för detta ändamål.
- **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapar en replikeringsprincip från lokalt till Azure.
    - Den här principen associeras automatiskt med konfigurationsservern.
    - Du kan inte redigera den här principen.
    - Principvärden: RPO-tröskel - 15 minuter; Kvarhållning av återställningspunkt - 24 timmar; Appkonsekvent ögonblicksbildfrekvens - 60 minuter.

Läs mer om VMware/fysiskt återbeskydd och återställning efter fel:
- [Granska](vmware-azure-reprotect.md#before-you-begin) ytterligare krav för återskydd och återställning.
- [Distribuera](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) en processserver i Azure.
- [Distribuera](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) en separat huvudmålserver.

När du reprotect Azure virtuella datorer till lokala, kan du ange att du vill växla tillbaka till den ursprungliga platsen, eller till en alternativ plats.

- **Ursprunglig platsåterställning:** Detta växlar tillbaka från Azure till samma källa lokalt om den finns. I det här fallet replikeras endast ändringar tillbaka till lokalt.
- **Alternativ platsåterställning:** Om den lokala datorn inte finns kan du växla tillbaka från Azure till en alternativ plats. När du roterar om Den virtuella Azure-datorn till lokalt skapas den lokala datorn. Fullständig datareplikering sker från Azure till lokalt. - - [Granska](concepts-types-of-failback.md) krav och begränsningar för plats failback.



## <a name="hyper-v-reprotectionfailback"></a>Hyper-V-återskydd/återställning

Så här återroterar och reder hyper-virtuella virtuella datorer från Azure till lokala:

- Du kan bara återställa Hyper-V-datorer som replikeras med ett lagringskonto. Återställning av virtuella hyper-virtuella datorer som replikeras med hanterade diskar stöds inte.
- Lokala Hyper-V-värdar (eller System Center VMM om de används) ska anslutas till Azure.
- Du kör en planerad återställning från Azure till lokalt.
- Inga specifika komponenter behöver ställas in för återställning av virtuella datorer med flera virtuella datorer.
- Under planerad redundans kan du välja alternativ för att synkronisera data före återställning efter fel:
    - **Synkronisera data före redundans:** Det här alternativet minimerar driftstopp för virtuella datorer när datorer synkroniseras utan att stänga av dem.
        - Fas 1: Tar en ögonblicksbild av Den virtuella Azure-datorn och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
        - Fas 2: Stänger av den virtuella azure-datorn så att inga nya ändringar inträffar där. Den slutliga uppsättningen deltaändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    - **Synkronisera data endast under redundans:** Det här alternativet är snabbare eftersom vi förväntar oss att de flesta av disken har ändrats och därmed inte utför kontrollsummens beräkningar. Den utför en nedladdning av disken. Vi rekommenderar att du använder det här alternativet om den virtuella datorn har körts i Azure ett tag (en månad eller mer) eller om den lokala virtuella datorn har tagits bort.

[Läs mer](hyper-v-azure-failback.md) om Hyper-V-återskydd och återställning efter fel.

När du reprotect Azure virtuella datorer till lokala, kan du ange att du vill växla tillbaka till den ursprungliga platsen, eller till en alternativ plats.

- **Ursprunglig platsåterställning:** Detta växlar tillbaka från Azure till samma källa lokalt om den finns. I det här fallet väljer du ett av de synkroniseringsalternativ som beskrivs i föregående procedur.
- **Alternativ platsåterställning:** Om den lokala datorn inte finns kan du växla tillbaka från Azure till en alternativ plats. När du roterar om Den virtuella Azure-datorn till lokalt skapas den lokala datorn. Med det här alternativet rekommenderar vi att du väljer alternativet att synkronisera data före redundans
- [Granska](hyper-v-azure-failback.md) kraven och begränsningarna för återställning av plats.


När du har misslyckats tillbaka till den lokala platsen aktiverar du **Omvänd replikera** för att starta replikera den virtuella datorn till Azure och slutföra cykeln.




## <a name="next-steps"></a>Nästa steg
- Växla över [specifika virtuella virtuella datorer med VMware](vmware-azure-tutorial-failover-failback.md)
- Växla över [specifika hyper-virtuella datorer](hyper-v-azure-failover-failback-tutorial.md).
- [Skapa](site-recovery-create-recovery-plans.md) en återställningsplan.
- Växla över [virtuella datorer i en återställningsplan](site-recovery-failover.md).
- [Förbered dig på](vmware-azure-failback.md) VMware reprotection och failback.
- Misslyckas tillbaka [hyper-virtuella datorer](hyper-v-azure-failback.md).

