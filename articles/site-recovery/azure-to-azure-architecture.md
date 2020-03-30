---
title: Azure till Azure-arkitektur för haveriberedskap i Azure Site Recovery
description: Översikt över den arkitektur som används när du konfigurerar haveriberedskap mellan Azure-regioner för virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 94da1639b5398a03b36fba3ff88877468a97ec36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294120"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Haveriberedskapsarkitektur för Azure till Azure


I den här artikeln beskrivs arkitektur, komponenter och processer som används när du distribuerar haveriberedskap för virtuella Azure-datorer med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md) Med haveriberedskap inställda replikerar Virtuella Azure-datorer kontinuerligt från till en annan målregion. Om ett avbrott inträffar kan du växla över virtuella datorer till den sekundära regionen och komma åt dem därifrån. När allt körs normalt igen kan du växla tillbaka och fortsätta arbeta på den primära platsen.



## <a name="architectural-components"></a>Arkitekturkomponenter

Komponenterna som är involverade i haveriberedskap för virtuella Azure-datorer sammanfattas i följande tabell.

**Komponent** | **Krav**
--- | ---
**Virtuella datorer i källregionen** | En av fler virtuella Azure-datorer i en [källregion som stöds](azure-to-azure-support-matrix.md#region-support).<br/><br/> Virtuella datorer kan köra alla [operativsystem som stöds](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Lagring av virtuella källor** | Virtuella Azure-datorer kan hanteras eller ha icke-hanterade diskar spridda över lagringskonton.<br/><br/>[Läs mer om](azure-to-azure-support-matrix.md#replicated-machines---storage) Azure-lagring som stöds.
**Vm-nätverk för källa** | Virtuella datorer kan finnas i ett eller flera undernät i ett virtuellt nätverk (VNet) i källregionen. [Läs mer](azure-to-azure-support-matrix.md#replicated-machines---networking) om nätverkskrav.
**Cachelagringskonto** | Du behöver ett cachelagringskonto i källnätverket. Under replikering lagras vm-ändringar i cacheminnet innan de skickas till mållagring.  Cachelagringskonton måste vara Standard.<br/><br/> Med hjälp av en cache säkerställs minimal påverkan på produktionsprogram som körs på en virtuell dator.<br/><br/> [Läs mer](azure-to-azure-support-matrix.md#cache-storage) om lagringskrav för cachelagring. 
**Rikta resurser** | Målresurser används under replikering och när en redundans inträffar. Site Recovery kan ställa in målresurs som standard, eller så kan du skapa/anpassa dem.<br/><br/> I målregionen kontrollerar du att du kan skapa virtuella datorer och att prenumerationen har tillräckligt med resurser för att stödja vm-storlekar som behövs i målregionen. 

![Käll- och målreplikering](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Rikta resurser

När du aktiverar replikering för en virtuell dator ger site recovery dig möjlighet att skapa målresurser automatiskt. 

**Målresurs** | **Standardinställning**
--- | ---
**Målprenumeration** | Samma som källprenumerationen.
**Målgrupp för målresurs** | Den resursgrupp som virtuella datorer tillhör efter redundans.<br/><br/> Det kan vara i alla Azure-regioner utom källregionen.<br/><br/> Site Recovery skapar en ny resursgrupp i målregionen med ett suffix med "asr".<br/><br/>
**Mål-VNet** | Det virtuella nätverket (VNet) där replikerade virtuella datorer finns efter redundans. En nätverksmappning skapas mellan virtuella käll- och målnätverk och vice versa.<br/><br/> Site Recovery skapar ett nytt virtuella nätverk och undernät, med suffixet "asr".
**Mållagringskonto** |  Om den virtuella datorn inte använder en hanterad disk är detta det lagringskonto som data replikeras till.<br/><br/> Site Recovery skapar ett nytt lagringskonto i målregionen för att spegla källlagringskontot.
**Replikhanterade diskar** | Om den virtuella datorn använder en hanterad disk är detta de hanterade diskar som data replikeras till.<br/><br/> Site Recovery skapar replikhanterade diskar i lagringsregionen för att spegla källan.
**Uppsättningar för måltillgänglighet** |  Tillgänglighetsuppsättning där replikera virtuella datorer finns efter redundans.<br/><br/> Site Recovery skapar en tillgänglighet som anges i målregionen med suffixet "asr", för virtuella datorer som finns i en tillgänglighet som anges på källplatsen. Om det finns en tillgänglighetsuppsättning används den och en ny skapas inte.
**Måltillgänglighetszoner** | Om målregionen stöder tillgänglighetszoner tilldelar Site Recovery samma zonnummer som det som används i källregionen.

### <a name="managing-target-resources"></a>Hantera målresurser

Du kan hantera målresurser på följande sätt:

- Du kan ändra målinställningarna när du aktiverar replikering.
- Du kan ändra målinställningarna när replikeringen redan fungerar. Undantaget är tillgänglighetstypen (en instans, uppsättning eller zon). Om du vill ändra den här inställningen måste du inaktivera replikering, ändra inställningen och sedan återaktivera.



## <a name="replication-policy"></a>Replikeringsprincip 

När du aktiverar Azure VM-replikering skapar site recovery som standard en ny replikeringsprincip med standardinställningarna sammanfattade i tabellen.

**Principinställning** | **Detaljer** | **Default**
--- | --- | ---
**Kvarhållning av återställningspunkt** | Anger hur länge återställningspunkter för webbplats behåller återställningspunkter | 24 timmar
**Ögonblicksbildfrekvens för appen** | Hur ofta Site Recovery tar en app-konsekvent ögonblicksbild. | Var fjärde timme

### <a name="managing-replication-policies"></a>Hantera replikeringsprinciper

Du kan hantera och ändra standardinställningarna för replikeringsprinciper på följande sätt:
- Du kan ändra inställningarna när du aktiverar replikering.
- Du kan skapa en replikeringsprincip när som helst och sedan använda den när du aktiverar replikering.

### <a name="multi-vm-consistency"></a>Konsekvens för flera virtuella datorer

Om du vill att virtuella datorer ska replikeras tillsammans och har delade kraschkonsekventa och appkonsekventa återställningspunkter vid redundans kan du samla ihop dem till en replikeringsgrupp. Multi-VM konsekvens påverkar arbetsbelastningens prestanda, och bör endast användas för virtuella datorer som kör arbetsbelastningar som behöver konsekvens över alla datorer. 



## <a name="snapshots-and-recovery-points"></a>Ögonblicksbilder och återställningspunkter

Återställningspunkter skapas från ögonblicksbilder av VM-diskar som tas vid en viss tidpunkt. När du växlar över en virtuell dator använder du en återställningspunkt för att återställa den virtuella datorn på målplatsen.

När vi misslyckas över, vill vi i allmänhet se till att den virtuella datorn startar utan skador eller dataförlust, och att VM-data är konsekvent för operativsystemet och för appar som körs på den virtuella datorn. Detta beror på vilken typ av ögonblicksbilder som tas.

Site Recovery tar ögonblicksbilder på följande sätt:

1. Site Recovery tar kraschkonsekventa ögonblicksbilder av data som standard och appkonsekventa ögonblicksbilder om du anger en frekvens för dem.
2. Återställningspunkter skapas från ögonblicksbilderna och lagras i enlighet med kvarhållningsinställningar i replikeringsprincipen.

### <a name="consistency"></a>Konsekvens

I följande tabell beskrivs olika typer av konsekvens.

### <a name="crash-consistent"></a>Kraschkonsekvent

**Beskrivning** | **Detaljer** | **Rekommendation**
--- | --- | ---
En krasch konsekvent ögonblicksbild fångar data som fanns på disken när ögonblicksbilden togs. Det innehåller ingenting i minnet.<br/><br/> Den innehåller motsvarigheten till data på disk som skulle finnas om den virtuella datorn kraschade eller nätsladden drogs från servern i det ögonblick som ögonblicksbilden togs.<br/><br/> En kraschkonsekvent garanterar inte datakonsekvens för operativsystemet eller för appar på den virtuella datorn. | Site Recovery skapar kraschkonsekventa återställningspunkter var femte minut som standard. Det går inte att ändra den här inställningen.<br/><br/>  | Idag kan de flesta appar återhämta sig väl från kraschkonsekventa punkter.<br/><br/> Kraschkonsekventa återställningspunkter är vanligtvis tillräckliga för replikering av operativsystem och appar som DHCP-servrar och utskriftsservrar.

### <a name="app-consistent"></a>Appkonsekvent

**Beskrivning** | **Detaljer** | **Rekommendation**
--- | --- | ---
Appkonsekventa återställningspunkter skapas från appkonsekventa ögonblicksbilder.<br/><br/> En appkonsekvent ögonblicksbild innehåller all information i en kraschkonsekvent ögonblicksbild, plus alla data i minne och pågående transaktioner. | Appkonsekventa ögonblicksbilder använder VSS (Volume Shadow Copy Service):<br/><br/>   1) När en ögonblicksbild initieras utför VSS en copy-on-write (COW) åtgärd på volymen.<br/><br/>   2) Innan den utför COW, vss informerar varje app på maskinen att den behöver för att spola sitt minne-resident data till disk.<br/><br/>   3) VSS tillåter sedan backup / katastrofåterställning app (i detta fall Site Recovery) för att läsa ögonblicksbild data och fortsätta. | Appkonsekventa ögonblicksbilder tas i enlighet med den frekvens du anger. Den här frekvensen bör alltid vara mindre än du har angett för att behålla återställningspunkter. Om du till exempel behåller återställningspunkter med standardinställningen 24 timmar bör du ställa in frekvensen på mindre än 24 timmar.<br/><br/>De är mer komplexa och tar längre tid att slutföra än kraschkonsekventa ögonblicksbilder.<br/><br/> De påverkar prestanda för appar som körs på en virtuell dator som är aktiverad för replikering. 

## <a name="replication-process"></a>Replikeringsprocessen

När du aktiverar replikering för en virtuell Azure-dator händer följande:

1. Tjänsttillägget Site Recovery Mobility installeras automatiskt på den virtuella datorn.
2. Tillägget registrerar den virtuella datorn med Platsåterställning.
3. Kontinuerlig replikering börjar för den virtuella datorn.  Diskskrivningar överförs omedelbart till cachelagringskontot på källplatsen.
4. Site Recovery bearbetar data i cacheminnet och skickar dem till mållagringskontot eller till replikhanterade diskar.
5. När data har bearbetats genereras kraschkonsekventa återställningspunkter var femte minut. Appkonsekventa återställningspunkter genereras enligt den inställning som anges i replikeringsprincipen.

![Aktivera replikeringsprocess, steg 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Replikeringsprocessen**

## <a name="connectivity-requirements"></a>Anslutningskrav

 De virtuella Azure-datorer som du replikerar behöver utgående anslutning. Site Recovery behöver aldrig inkommande anslutning till den virtuella datorn. 

### <a name="outbound-connectivity-urls"></a>Utgående anslutning (webbadresser)

Om utgående åtkomst för virtuella datorer styrs med webbadresser tillåter du dessa webbadresser.

| **URL** | **Detaljer** |
| ------- | ----------- |
| *.blob.core.windows.net | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| login.microsoftonline.com | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| *.hypervrecoverymanager.windowsazure.com | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| *.servicebus.windows.net | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |
| *.vault.azure.net | Ger åtkomst för att aktivera replikering för ADE-aktiverade virtuella datorer via portal
| *.automation.ext.azure.com | Gör det möjligt att aktivera automatisk uppgradering av mobilitetsagent för ett replikerat objekt via portal

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du vill styra utgående anslutning för virtuella datorer med IP-adresser tillåter du dessa adresser.
Observera att information om kraven på nätverksanslutning finns i [nätverk white paper](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Regler för källregion

**Regel** |  **Detaljer** | **Tjänsttagg**
--- | --- | --- 
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar lagringskonton i källregionen | Lagring. \<regionnamn>
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure Active Directory (Azure AD)  | AzureActiveDirectory
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Händelsehubben i målregionen. | EventsHub. \<regionnamn>
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure Site Recovery  | AzureSiteRecovery
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure Key Vault (Detta krävs endast för att aktivera replikering av ADE-aktiverade virtuella datorer via portal) | AzureKeyVault
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure Automation Controller (Detta krävs endast för att aktivera automatisk uppgradering av mobilitetsagent för ett replikerat objekt via portal) | GästOchHybridManagement

#### <a name="target-region-rules"></a>Regler för målområde

**Regel** |  **Detaljer** | **Tjänsttagg**
--- | --- | --- 
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar lagringskonton i målregionen | Lagring. \<regionnamn>
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure AD  | AzureActiveDirectory
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Händelsehubben i källregionen. | EventsHub. \<regionnamn>
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure Site Recovery  | AzureSiteRecovery
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure Key Vault (Detta krävs endast för att aktivera replikering av ADE-aktiverade virtuella datorer via portal) | AzureKeyVault
Tillåt HTTPS-utgående: port 443 | Tillåt intervall som motsvarar Azure Automation Controller (Detta krävs endast för att aktivera automatisk uppgradering av mobilitetsagent för ett replikerat objekt via portal) | GästOchHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Styra åtkomst med NSG-regler

Om du styr VM-anslutning genom att filtrera nätverkstrafik till och från Azure-nätverk/undernät med [NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview)bör du tänka på följande krav:

- NSG-regler för käll azure-regionen bör tillåta utgående åtkomst för replikeringstrafik.
- Vi rekommenderar att du skapar regler i en testmiljö innan du sätter dem i produktion.
- Använd [tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) i stället för att tillåta enskilda IP-adresser.
    - Tjänsttaggar representerar en grupp IP-adressprefix som samlats in för att minimera komplexiteten när du skapar säkerhetsregler.
    - Microsoft uppdaterar automatiskt tjänsttaggar över tid. 
 
Läs mer om [utgående anslutning](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) för webbplatsåterställning och [kontrollera anslutningen med NSG.](concepts-network-security-group-with-site-recovery.md)


### <a name="connectivity-for-multi-vm-consistency"></a>Anslutning för konsekvens för flera virtuella datorer

Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004.
- Se till att det inte finns någon brandvägg som blockerar den interna kommunikationen mellan de virtuella datorerna på port 20004.
- Om du vill att virtuella datorer med Linux ska vara med i en replikeringsgrupp måste du se till att du manuellt öppnar för utgående trafik på port 20004 enligt riktlinjerna för den specifika Linux-versionen.




## <a name="failover-process"></a>Redundansprocess

När du initierar en redundans skapas de virtuella datorerna i målresursgruppen, målvirtos nätverk, målundernät och i uppsättningen måltillgänglighet. Under en redundans kan du använda valfri återställningspunkt.

![Redundansprocess](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Nästa steg

[Replikera snabbt](azure-to-azure-quickstart.md) en virtuell Azure-dator till en sekundär region.
