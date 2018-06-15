---
title: VMware till Azure replikeringsarkitektur i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och används för att replikera lokala virtuella VMware-datorer till Azure med Azure Site Recovery-arkitektur
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30184589"
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware till Azure replikeringsarkitektur

Den här artikeln beskriver arkitektur och processer som används när du replikera växla över och återställa virtuella VMware-datorer (VM) mellan en lokal VMware-platsen och Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en övergripande bild av de komponenter som används för VMware-replikering till Azure.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration, Azure Storage-konto och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerade data när du kör en växling vid fel från lokal till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Configuration server-dator** | En enda lokal dator. Vi rekommenderar att du ska köra den som en VMware VM som kan distribueras från en nedladdad OVF-mall.<br/><br/> Datorn kör alla lokala Site Recovery-komponenter, bland annat konfigurationsservern, processervern och huvudmålservern. | **Konfigurationsservern**: samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.<br/><br/> **Processervern**: installeras som standard på konfigurationsservern. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering; och skickar den till Azure Storage. Processervern installeras också Azure Site Recovery Mobility-tjänsten på virtuella datorer du vill replikera, och utför automatisk identifiering av lokala datorer. Eftersom distributionen växer kan du lägga till ytterligare, separat servrar för att hantera större mängder replikeringstrafik.<br/><br/> **Huvudmålservern**: installeras som standard på konfigurationsservern. Den hanterar replikeringsdata vid återställning från Azure. Du kan lägga till en ytterligare, separat huvudmålservern för återställning efter fel för stora distributioner.
**VMware-servrar** | Virtuella VMware-datorer finns på lokala vSphere ESXi servrar. Vi rekommenderar en vCenter-server för att hantera värdar. | Under distributionen av Site Recovery kan du lägga till VMware-servrar till Recovery Services-valvet.
**Replikerade datorer** | Mobilitetstjänsten installeras på varje VMware VM som du vill replikera. | Vi rekommenderar att du automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en automatisk distributionsmetod, till exempel System Center Configuration Manager.

**Arkitektur för VMware till Azure**

![Komponenter](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Konfigurationssteg

Generella steg för att skapa VMware som Azure katastrofåterställning eller migrering är följande:

1. **Skapa Azure komponenter**. Du behöver ett Azure-konto med rätt behörighet för, ett Azure storage-konto, ett virtuellt Azure-nätverk och Recovery Services-valvet. [Läs mer](tutorial-prepare-azure.md).
2. **Konfigurera lokala**. Dessa omfattar att konfigurera ett konto i VMware server så att Site Recovery kan automatiskt identifiera virtuella datorer för replikering, ställa in ett konto som kan användas för att installera mobilitetstjänsten på virtuella datorer du vill replikera och verifiera att VMware-servrar och virtuella datorer som uppfyller kraven. Du kan även förbereda att ansluta till dessa virtuella Azure-datorer efter redundans. Site Recovery replikerar VM-data till ett Azure storage-konto och skapar virtuella Azure-datorer med hjälp av data när du kör en redundansväxling till Azure. [Läs mer](vmware-azure-tutorial-prepare-on-premises.md).
3. **Konfigurera replikering**. Du kan välja var du vill replikera till. Du konfigurerar replikering källmiljön genom att skapa en enda lokal VMware VM (konfigurationsserver) som kör alla lokal Site Recovery-komponenter som du behöver. Efter installationen registrerar du configuration server-dator i Recovery Services-valvet. Sedan väljer du inställningarna för målet. [Läs mer](vmware-azure-tutorial.md).
4. **Skapa en replikeringsprincip**. Du kan skapa en replikeringsprincip som anger hur replikering ska ske. 
    - **Tröskelvärde för Återställningspunktmål**: den här övervakningsinställning tillstånd som om replikeringen inte inträffar inom den angivna tiden, en avisering (och eventuellt ett e-postmeddelande) utfärdas. Om du anger tröskelvärdet för Återställningspunktmål till 30 minuter och ett problem hindrar replikering inträffar under 30 minuter, skapas en händelse. Den här inställningen påverkar inte replikering. Replikeringen är kontinuerlig och några minuters mellanrum skapas återställningspunkter
    - **Kvarhållning**: återställningspunkt kvarhållning anger hur länge återställningspunkter bör hållas i Azure. Du kan ange ett värde mellan 0 och 24 timmar för premium-lagring eller upp till 72 timmar för standardlagring. Du kan redundansväxla till den senaste återställningspunkten eller till en lagrad om du anger värdet som är större än noll. Efter kvarhållningsperiod rensas återställningspunkter.
    - **Kraschkonsekvent ögonblicksbilder**: som standard Site Recovery tar kraschkonsekvent ögonblicksbilder och skapar återställningspunkter med några minuters mellanrum. En återställningspunkt är kraschkonsekventa om alla komponenter relaterade data write-ordning konsekvent, som de var på ögonblicket återställningspunkten skapades. För att bättre förstå anta att statusen för data på hårddisken PC efter ett strömavbrott eller en liknande händelse. En kraschkonsekvent återställningspunkt är normalt tillräckligt om ditt program är utformat för att återställa från en krasch utan inkonsekvenser data.
    - **Programkonsekventa ögonblicksbilder**: om det här värdet inte är noll, mobilitetstjänsten körs på den virtuella datorn försöker generera file system-programkonsekventa ögonblicksbilder och återställningspunkter. Första ögonblicksbilden tas när den inledande replikeringen är klar. Ögonblicksbilder tas sedan för den frekvens som du anger. En återställningspunkt är programkonsekventa om förutom att skriva ordning konsekvent, köra program Slutför alla sina åtgärder och rensa sina buffertar till disk (programmet offline stegvis). Programkonsekventa återställningspunkter rekommenderas för databasprogram som SQL, Oracle och Exchange. Om en kraschkonsekvent ögonblicksbild räcker anges det här värdet till 0.  
    - **Konsekvens för flera**: du kan också skapa en replikeringsgrupp. När du aktiverar replikering kan samla du virtuella datorer i gruppen. Gruppera replikera virtuella datorer i en replikering och har delat kraschkonsekvent och programkonsekventa återställningspunkter vid redundansväxling. Du bör använda det här alternativet noggrant, eftersom det kan påverka arbetsbelastningens prestanda som ögonblicksbilder som behövs för att samlas in på flera datorer. Gör endast detta om virtuella datorer som kör samma arbetsbelastning och måste vara konsekvent och virtuella datorer har liknande kannor. Du kan lägga till upp till 8 virtuella datorer till en grupp. 
5. **Aktivera replikering av Virtuella**. Slutligen kan aktivera du replikering för din lokala VMwares virtuella datorer. Om du har skapat ett konto för att installera mobilitetstjänsten och anges att Site Recovery bör göra en push-installation, kommer mobilitetstjänsten att installeras på varje virtuell dator som du aktiverar replikering. [Läs mer](vmware-azure-tutorial.md#enable-replication). Om du har skapat en replikeringsgrupp för konsekvens för flera kan du lägga till virtuella datorer i gruppen.
6. **Testa redundans**. När allting har konfigurerats, kan du göra ett redundanstest för att kontrollera att virtuella datorer som redundansväxlar till Azure som förväntat. [Läs mer](tutorial-dr-drill-azure.md).
7. **Redundans**. Om du bara migrerar de virtuella datorerna till Azure - kör en fullständig redundans för att göra det. Om du ställer in katastrofåterställning, kan du köra en fullständig växling vid fel som du behöver. För fullständig återställning kan efter en redundansväxling till Azure, du växla tillbaka till din lokala plats som och när den är tillgänglig. [Läs mer](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator, börjar den att replikera i enlighet med replikeringsprincipen. 
2. Trafik som replikeras till Azure storage offentliga slutpunkter på Internet. Du kan också använda Azure ExpressRoute med [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikera trafik via ett plats-till-plats virtuellt privat nätverk (VPN) från en lokal plats till Azure stöds inte.
3. En inledande kopia av VM-data replikeras till Azure-lagring.
4. När replikeringen har slutförts börjar replikering av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
5. Kommunikation händer följande:

    - Virtuella datorer kommunicera med konfigurationsservern lokalt på port 443 för HTTPS inkommande för replikeringshantering av.
    - Konfigurationsservern samordnar replikering med Azure via port 443 för HTTPS utgående.
    - Virtuella datorer skicka replikeringsdata till processerver (som körs på server-datorn konfigurationen) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Tar emot replikeringsdata processervern optimerar och krypteras det och skickar det till Azure storage via port 443 utgående.


**VMware till Azure replikeringen**

![Replikeringsprocessen](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har ställts in och du kör en katastrof återställningsgranskning (testa redundans) för att kontrollera att allt fungerar som förväntat, kan du köra redundans och återställning som du behöver.

1. Du kör misslyckas för en enskild dator eller skapar du en återställningspunkt planer för att växla över flera virtuella datorer på samma gång. Fördelen med en återställningsplan snarare än en enskild dator redundans inkluderar:
    - Du kan modellen app beroenden genom att inkludera alla virtuella datorer över appen i en enda återställningsplan.
    - Du kan lägga till skript, Azure-runbooks och pausa för manuella åtgärder.
2. Efter utlöser inledande redundans kan genomföra den för att starta åt arbetsbelastningen från den virtuella Azure-datorn.
3. När din primära lokal plats är tillgänglig igen kan du förbereda för återställning vid fel. För att kunna återställas, du måste ställa in en infrastruktur för återställning efter fel, inklusive:

    * **Tillfällig processerver i Azure**: Om du vill växla tillbaka från Azure du ställer in en Azure VM att fungera som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    * **VPN-anslutning**: för att växla tillbaka du behöver en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
    * **Separata huvudmålservern**: som standard som installerades med konfigurationsservern på lokal VMware VM huvudmålservern hanterar återställning efter fel. Om du måste växla tillbaka stora volymer av trafik, kan du ställa in en separat lokal huvudmålserver för detta ändamål.
    * **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapade din replikeringsprincip från lokal till Azure.
4. När komponenterna är på plats så utförs återställning efter fel i tre åtgärder:

    - Steg 1: Skapa nytt virtuella Azure-datorer så att de replikera från Azure till lokala VMwares virtuella datorer.
    -  Steg 2: Kör en redundansväxling till den lokala platsen.
    - Steg 3: När arbetsbelastningar misslyckas igen kan du återaktivera replikering för de lokala virtuella datorerna.
    
 
**VMware återställning från Azure**

![Återställning efter fel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [självstudierna](vmware-azure-tutorial.md) att aktivera VMware till Azure-replikering.
