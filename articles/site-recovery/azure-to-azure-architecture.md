---
title: Arkitektur för Azure till Azure replikering i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du konfigurerar haveriberedskap mellan Azure-regioner för virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 797838b077993ddcb4120bcf48b026063abbe1ab
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105329"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure-datorer disaster recovery-arkitekturen


Den här artikeln beskriver arkitektur, komponenter och processer som används när du distribuerar haveriberedskap för virtuella Azure-datorer (VM) med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service. Med disaster recovery konfigurera replikera virtuella Azure-datorer kontinuerligt från till ett annat mål-region. Om ett avbrott uppstår kan du växla över virtuella datorer till den sekundära regionen och komma åt dem därifrån. När allt kommer normalt att körs igen kan du växla tillbaka och fortsätta arbeta på den primära platsen.



## <a name="architectural-components"></a>Arkitekturkomponenter

Komponenterna som ingår i haveriberedskap för virtuella Azure-datorer sammanfattas i tabellen nedan.

**Komponent** | **Krav**
--- | ---
**Virtuella datorer i källregionen** | En av flera virtuella Azure-datorer i en [stöds källregionen](azure-to-azure-support-matrix.md#region-support).<br/><br/> Virtuella datorer kan köras på valfritt [operativsystem som stöds](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Källa VM-lagring** | Virtuella Azure-datorer kan hanteras, eller ha icke-hanterade diskar som är fördelade på storage-konton.<br/><br/>[Lär dig mer om](azure-to-azure-support-matrix.md#replicated-machines---storage) stöds Azure storage.
**Virtuella datornätverk som källa** | Virtuella datorer kan finnas i ett eller flera undernät i ett virtuellt nätverk (VNet) i källregionen. [Läs mer](azure-to-azure-support-matrix.md#replicated-machines---networking) om nätverkskrav.
**Cachelagringskonto** | Du behöver ett cachelagringskonto i källnätverket. Under replikering lagras ändringar i virtuella datorer i cachen innan den skickas till mål-lagringskontot.<br/><br/> Använda en cache garanterar minimal inverkan på produktionsprogram som körs på en virtuell dator.<br/><br/> [Läs mer](azure-to-azure-support-matrix.md#cache-storage) om cache lagringskrav. 
**Målresurser** | Målresurser används vid replikering och redundans inträffar. Site Recovery kan ställa in målresurs som standard, eller du kan skapa/anpassa dem.<br/><br/> Kontrollera att det går att skapa virtuella datorer och att din prenumeration har tillräckligt med resurser som stöder VM-storlekar som krävs i målregionen i målregion. 

![Käll- och replikering](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Målresurser

När du aktiverar replikering för en virtuell dator, ger Site Recovery skapa målresurser automatiskt. 

**Målresurs** | **Standardinställningen**
--- | ---
**Målprenumerationen** | Samma som abonnemanget.
**Målresursgrupp** | Den resursgrupp som virtuella datorer tillhör efter en redundansväxling.<br/><br/> Det kan vara i alla Azure-regioner utom i källregionen.<br/><br/> Site Recovery skapar en ny resursgrupp i målregionen med suffixet ”asr”.<br/><br/>
**Mål-VNet** | Det virtuella nätverket (VNet) där replikerade virtuella datorer är placerade efter en redundansväxling. En nätverksmappning skapas mellan käll- och virtuella nätverk och vice versa.<br/><br/> Skapar site Recovery ett nytt virtuellt nätverk och undernät, med suffixet ”asr”.
**Mål-lagringskontot** |  Om den virtuella datorn inte använder en hanterad disk, är det lagringskonto som data ska replikeras.<br/><br/> Site Recovery skapar ett nytt lagringskonto i målregionen som speglar källagringskontot.
**Hanterade replikeringsdiskar** | Om den virtuella datorn använder en hanterad disk, är de hanterade diskarna som data ska replikeras.<br/><br/> Hanterade replikeringsdiskar skapar site Recovery i regionen lagring för spegling av källan.
**Tillgänglighetsuppsättningar för mål** |  Tillgänglighetsuppsättning i vilka replikerande virtuella datorer är placerade efter en redundansväxling.<br/><br/> Site Recovery skapar en tillgänglighetsuppsättning i målregionen med suffixet ”asr” för virtuella datorer som befinner sig i en tillgänglighetsuppsättning på källplatsen. Om en tillgänglighetsuppsättning finns det används och en ny skapas inte.
**Tillgänglighetszoner för mål** | Om målregionen stöder tillgänglighetszoner, tilldelar Webbplatsåterställning samma zon nummer som används i källregionen.

### <a name="managing-target-resources"></a>Hantera målresurser

Du kan hantera target-resurser på följande sätt:

- Du kan ändra inställningar för mål som du aktiverar replikering.
- Du kan ändra inställningarna för målet när replikering redan arbetar. Undantaget är tillgänglighet-typ (instans, uppsättning eller zon). Om du vill ändra den här inställningen måste du inaktivera replikering, ändra inställningen och sedan aktivera igen.



## <a name="replication-policy"></a>Replikeringsprincip 

När du aktiverar replikering av virtuella Azure-datorer kan som standard skapar Site Recovery en ny replikeringsprincip med standardinställningar som sammanfattas i tabellen.

**Principinställning** | **Detaljer** | **Standard**
--- | --- | ---
**Kvarhållning av återställningspunkt** | Anger hur länge behåller Site återställningspunkter | 24 timmar
**Appkompatibel ögonblicksbildsfrekvens** | Hur ofta tar Site Recovery en appkompatibel ögonblicksbild. | Var 60: e minut.

### <a name="managing-replication-policies"></a>Hantera replikeringsprinciper

Du kan hantera och ändra standard-principer replikeringsinställningarna enligt följande:
- Du kan ändra inställningarna när du aktiverar replikering.
- Du kan skapa en replikeringsprincip när som helst och använda den när du aktiverar replikering.

### <a name="multi-vm-consistency"></a>Multi-VM-konsekvens

Om du vill att virtuella datorer att replikera tillsammans och har delat kraschkonsekventa och appkonsekventa återställningspunkter vid en redundansväxling, kan du samla dem i en replikeringsgrupp. Konsekvens påverkar arbetsbelastningens prestanda och bör endast användas för virtuella datorer som kör arbetsbelastningar som behöver enhetlighet på alla datorer. 



## <a name="snapshots-and-recovery-points"></a>Ögonblicksbilder och återställningspunkter

Återställningspunkter skapas från ögonblicksbilder av Virtuella diskar som vidtas vid en viss tidpunkt. När du redundansväxlar en virtuell dator använder du en återställningspunkt för att återställa den virtuella datorn på målplatsen.

När redundansväxlingen, vill vi Allmänt se till att den virtuella datorn startar utan skador eller förlust av data och att virtuella datorns data är konsekvent för operativsystemet och för appar som körs på den virtuella datorn. Detta beror på vilken typ av ögonblicksbilder som tas.

Site Recovery tar ögonblicksbilder på följande sätt:

1. Site Recovery tar kraschkonsekventa ögonblicksbilder av data som standard och appkonsekventa ögonblicksbilder om du anger en frekvens för dessa.
2. Återställningspunkter skapas från ögonblicksbilder, och lagras i enlighet med inställningarna för datakvarhållning i replikeringsprincipen.

### <a name="consistency"></a>Konsekvens

I följande tabell beskrivs olika typer av konsekvens.

### <a name="crash-consistent"></a>Kraschkonsekvent

**Beskrivning** | **Detaljer** | **Rekommendationen**
--- | --- | ---
En konsekvent ögonblicksbild krascher samlar in data på disken när ögonblicksbilden togs. De omfattar inte något i minnet.<br/><br/> Den innehåller motsvarande data på disken som skulle vara närvarande om den virtuella datorn har kraschat eller kontakten hämtades från servern vid så snart som ögonblicksbilden togs.<br/><br/> En kraschkonsekvent garantera inte datakonsekvens för operativsystemet eller för appar på den virtuella datorn. | Skapar site Recovery kraschkonsekventa återställningspunkter var femte minut som standard. Den här inställningen kan inte ändras.<br/><br/>  | Idag är kan de flesta appar återställa från kraschkonsekventa återställningspunkter.<br/><br/> Kraschkonsekventa återställningspunkter är vanligen tillräckligt för replikering av operativsystem och program som till exempel DHCP-servrar och utskriftsservrar.

### <a name="app-consistent"></a>Appkonsekvent
**Beskrivning** | **Detaljer** | **Rekommendationen**
--- | --- | ---
Appkonsekventa återställningspunkter skapas från appkonsekventa ögonblicksbilder.<br/><br/> En appkompatibel ögonblicksbild innehålla all information på en kraschkonsekvent ögonblicksbild, plus alla data i minnet och pågående transaktioner. | Appkonsekventa ögonblicksbilder använda tjänsten Volume Shadow Copy (VSS):<br/><br/>   (1) när en ögonblicksbild initieras, utföra VSS en kopia vid skrivning (KO)-åtgärd på volymen.<br/><br/>   2) innan den utför KO, informerar VSS varje app på den dator som den behöver för att tömma minne kommande data till disk.<br/><br/>   3) VSS kan sedan appen backup/disaster recovery (i det här fallet Site Recovery) att läsa ögonblicksbilddata och fortsätta. | Appkonsekventa ögonblicksbilder tas i enlighet med vilken frekvens som du anger. Denna frekvens ska alltid vara mindre än vad som du anger för att bevara återställningspunkter. Om du behåller återställningspunkter genom att använda standardinställningen på 24 timmar, bör du till exempel ange frekvensen på mindre än 24 timmar.<br/><br/>De är mer komplexa och ta lite längre tid än kraschkonsekventa ögonblicksbilder.<br/><br/> De påverkar prestanda för appar som körs på en virtuell dator som aktiverats för replikering. | <br/><br/>Programkonsekventa återställningspunkter rekommenderas för databas-operativsystem och program, till exempel SQL.<br/><br/> Appkonsekventa ögonblicksbilder stöds bara för virtuella datorer som kör Windows.

## <a name="replication-process"></a>Replikeringsprocessen

När du aktiverar replikering för en Azure-dator händer följande:

1. Site Recovery mobilitetstjänsttillägget installeras automatiskt på den virtuella datorn.
2. Tillägget registreras den virtuella datorn med Site Recovery.
3. Det börjar kontinuerlig replikering för den virtuella datorn.  Diskskrivningar överförs direkt till cachelagringskontot på källplatsen.
4. Site Recovery bearbetar data i cacheminnet, och skickar det till mållagringskontot eller hanterade diskar på repliken.
5. När data har bearbetats skapas kraschkonsekventa återställningspunkter var femte minut. Appkonsekventa återställningspunkter skapas enligt inställningarna som anges i replikeringsprincipen.


   ![Aktivera replikeringsprocessen, steg 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Replikeringsprocessen**

## <a name="connectivity-requirements"></a>Krav för anslutning

 Virtuella Azure-datorer du replikera måste utgående anslutning. Site Recovery behöver aldrig inkommande anslutningar till den virtuella datorn. 

### <a name="outbound-connectivity-urls"></a>Utgående anslutning (URL: er)

Om utgående åtkomst för virtuella datorer styrs med URL: er, kan dessa URL: er.

| **URL** | **Detaljer** |
| ------- | ----------- |
| *.blob.core.windows.net | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| login.microsoftonline.com | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| *.hypervrecoverymanager.windowsazure.com | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| *.servicebus.windows.net | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

För att styra utgående anslutning för virtuella datorer med IP-adresser, kan dessa adresser.

#### <a name="source-region-rules"></a>Regionen källregler

**Regeln** |  **Detaljer** | **Tjänsttagg**
--- | --- | --- 
Tillåt HTTPS utgående: port 443 | Tillåt adressintervall som motsvarar storage-konton i källregionen | Lagring. < region-name >.
Tillåt HTTPS utgående: port 443 | Tillåt adressintervall som motsvarar Azure Active Directory (AD Azure).<br/><br/> Om Azure AD-adresser har lagts till i framtiden måste du skapa nya regler för Nätverkssäkerhetsgrupp (NSG).  | AzureActiveDirectory
Tillåt HTTPS utgående: port 443 | Tillåt åtkomst till [Site Recovery-slutpunkter](https://aka.ms/site-recovery-public-ips) som motsvarar målplatsen. 

#### <a name="target-region-rules"></a>Regler för mål-region

**Regeln** |  **Detaljer** | **Tjänsttagg**
--- | --- | --- 
Tillåt HTTPS utgående: port 443 | Tillåt adressintervall som motsvarar storage-konton i målregionen. | Lagring. < region-name >.
Tillåt HTTPS utgående: port 443 | Tillåt adressintervall som relaterar till Azure AD.<br/><br/> Om Azure AD-adresser har lagts till i framtiden måste du skapa nya NSG-regler.  | AzureActiveDirectory
Tillåt HTTPS utgående: port 443 | Tillåt åtkomst till [Site Recovery-slutpunkter](https://aka.ms/site-recovery-public-ips) som motsvarar källplatsen. 


#### <a name="control-access-with-nsg-rules"></a>Kontrollera åtkomst med NSG-regler

Om du vill styra VM-anslutning genom att filtrera nätverkstrafik till och från Azure-nätverk/undernät med [NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview), Observera följande krav:

- NSG-reglerna för den Azure källregion ska tillåta utgående åtkomst för replikeringstrafik.
- Vi rekommenderar att du skapar regler i en testmiljö innan du placerar dem i produktionen.
- Använd [tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) istället för att låta enskilda IP-adresser.
    - Tjänsttaggar representerar en grupp med IP-adressprefix som samlas in tillsammans för att minska komplexiteten när du skapar säkerhetsregler.
    - Microsoft uppdaterar automatiskt tjänsttaggar över tid. 
 
Läs mer om [utgående anslutning](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) för Site Recovery, och [Kontrollera anslutningen med NSG: er](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Anslutning för konsekvens

Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004.
- Se till att det inte finns någon brandvägg som blockerar den interna kommunikationen mellan de virtuella datorerna på port 20004.
- Om du vill att virtuella datorer med Linux ska vara med i en replikeringsgrupp måste du se till att du manuellt öppnar för utgående trafik på port 20004 enligt riktlinjerna för den specifika Linux-versionen.




## <a name="failover-process"></a>Redundans

När du har initierat en redundansväxling kan de virtuella datorerna skapas i målresursgruppen, målets virtuella nätverk, målundernätet, och i tillgänglighetsuppsättningen för målet. Du kan använda någon annan återställningspunkt under en redundansväxling.

![Redundans](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Nästa steg

[Replikera snabbt](azure-to-azure-quickstart.md) en Azure-dator till en sekundär region.
