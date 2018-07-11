---
title: VMware till Azure-replikeringsarkitektur i Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används för att replikera lokala virtuella VMware-datorer till Azure med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 48adf61dc0f1796b820e1e14ca509d4618c6256b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920576"
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware till Azure-replikering-arkitektur

Den här artikeln beskrivs arkitekturen och processer som används när du replikera, redundansväxla och återställa virtuella VMware-datorer (VM) mellan en lokal VMware-platsen och Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en översikt över de komponenter som används för VMware-replikering till Azure.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure Storage-konto och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i lagringskontot. Virtuella Azure-datorer skapas med replikerad data när du kör en redundans från lokalt till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Configuration server-dator** | En enda lokal dator. Vi rekommenderar att du ska köra den som en VMware-VM som kan distribueras från en nedladdade OVF-mall.<br/><br/> Datorn kör alla lokala Site Recovery-komponenter, bland annat konfigurationsservern, processervern och huvudmålservern. | **Konfigurationsservern**: samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.<br/><br/> **Processervern**: installeras som standard på konfigurationsservern. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Processervern installerar också Azure Site Recovery-Mobilitetstjänsten på virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala datorer. Allt eftersom distributionen växer kan du lägga till ytterligare, separat processervrar för att hantera större mängder replikeringstrafik.<br/><br/> **Huvudmålservern**: installeras som standard på konfigurationsservern. Den hanterar replikeringsdata vid återställning från Azure. För stora distributioner, kan du lägga till en ytterligare, separat huvudmålserver för återställning efter fel.
**VMware-servrar** | Virtuella VMware-datorer finns på den lokala vSphere ESXi-servrar. Vi rekommenderar en vCenter-server hanterar värdarna. | Under distributionen av Site Recovery måste du lägga till VMware-servrar till Recovery Services-valvet.
**Replikerade datorer** | Mobilitetstjänsten installeras på varje VMware VM som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processervern. Du kan också installera tjänsten manuellt eller använda en metod för automatisk distribution, till exempel System Center Configuration Manager.

**Arkitektur för VMware till Azure**

![Komponenter](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Konfigurationssteg

Generella steg för att konfigurera VMware för Azure-haveriberedskap eller migrering är följande:

1. **Konfigurera Azure-komponenter**. Du behöver ett Azure-konto med rätt behörigheter, ett Azure storage-konto, ett Azure-nätverk och ett Recovery Services-valv. [Läs mer](tutorial-prepare-azure.md).
2. **Konfigurera lokala**. Dessa omfattar att konfigurera ett konto på VMware-servern så att Site Recovery kan automatiskt identifiera virtuella datorer för replikering, skapa ett konto som kan användas för att installera mobilitetstjänsten på virtuella datorer du vill replikera och verifiera att VMware-servrarna och virtuella datorer som uppfyller kraven. Du kan också välja förbereda att ansluta till dessa virtuella Azure-datorer efter redundansväxling. Site Recovery replikerar VM-data till ett Azure storage-konto och skapar virtuella Azure-datorer med hjälp av data när du kör en redundansväxling till Azure. [Läs mer](vmware-azure-tutorial-prepare-on-premises.md).
3. **Konfigurera replikering**. Du kan välja var du vill replikera till. Du kan konfigurera källmiljön för replikering genom att konfigurera en enda lokal VMware VM (konfigurationsserver) som kör alla lokala Site Recovery-komponenter som du behöver. Efter installationen registrerar configuration server-dator i Recovery Services-valvet. Sedan kan välja du målinställningarna. [Läs mer](vmware-azure-tutorial.md).
4. **Skapa en replikeringsprincip**. Du skapar en replikeringsprincip som anger hur replikering ska ske. 
    - **Tröskelvärde för Replikeringspunktmål**: den här övervakningen som inställning tillstånd som om replikeringen inte inträffar inom den angivna tiden, en avisering (och eventuellt ett e-postmeddelande) utfärdas. Om du anger tröskelvärde för Replikeringspunktmål till 30 minuter och ett problem hindrar replikering inträffar i 30 minuter, till exempel genereras en händelse. Den här inställningen påverkar inte replikering. Replikeringen är kontinuerlig och återställningspunkter skapas några minuters mellanrum
    - **Kvarhållning**: återställningspunkt kvarhållning anger hur länge återställningspunkterna ska lagras i Azure. Du kan ange ett värde mellan 0 och 24 timmar för premium-lagring eller upp till 72 timmar för standardlagring. Du kan växla över till den senaste återställningspunkten, eller till en lagrad om du ställer in värdet som är större än noll. Efter kvarhållningsperioden rensas återställningspunkterna.
    - **Kraschkonsekventa ögonblicksbilder**: som standard Site Recovery tar kraschkonsekventa ögonblicksbilder och skapar återställningspunkter med dem med några minuters mellanrum. En återställningspunkt är kraschkonsekventa om alla relaterade data-komponenter är konsekvent för skrivning ordning, som de befann sig vid ögonblicket återställningspunkten skapades. För att bättre förstå anta att status för data på hårddisken PC efter ett strömavbrott eller en liknande händelse. En kraschkonsekvent återställningspunkt räcker vanligtvis om ditt program är utformat för att återställa från en krasch utan eventuella inkonsekvenser i data.
    - **Appkonsekventa ögonblicksbilder**: om det här värdet inte är noll, den mobilitetstjänstversion som körs på den virtuella datorn försöker generera system-konsekventa ögonblicksbilder och återställningspunkter. Första ögonblicksbilden tas när den inledande replikeringen är klar. Ögonblicksbilder tas sedan med den frekvens som du anger. En återställningspunkt är programkonsekventa om, förutom att skriva ordning konsekvent och pågående program har slutfört alla sina åtgärder och tömma buffertarna till disk (programmet offline stegvis). Appkonsekventa återställningspunkter rekommenderas för databasprogram som SQL, Oracle och Exchange. Om en kraschkonsekventa ögonblicksbilder är tillräcklig kan det här värdet anges till 0.  
    - **Konsekvens**: du kan också skapa en replikeringsgrupp. Sedan, när du aktiverar replikering kan du samla in virtuella datorer i gruppen. Virtuella datorer i en replikering gruppera replikera och har delade kraschkonsekventa och appkonsekventa återställningspunkter vid redundansväxling. Du bör använda det här alternativet noggrant, eftersom det kan påverka arbetsbelastningens prestanda som ögonblicksbilder som behövs för att samlas in över flera datorer. Endast göra detta om virtuella datorer kör samma arbetsbelastning och måste vara konsekvent och virtuella datorer har liknande churns. Du kan lägga till upp till 8 virtuella datorer till en grupp. 
5. **Aktivera replikering av virtuella datorer**. Slutligen kan aktivera du replikering för din lokala VMware-datorer. Om du har skapat ett konto för att installera mobilitetstjänsten och anges att Site Recovery bör göra en push-installation, installeras mobilitetstjänsten på varje virtuell dator som du aktiverar replikering. [Läs mer](vmware-azure-tutorial.md#enable-replication). Om du har skapat en replikeringsgruppen för konsekvens för flera datorer kan du lägga till virtuella datorer i gruppen.
6. **Testa redundans**. När allt har konfigurerats, kan du göra ett redundanstest för att kontrollera att virtuella datorer redundansväxlar till Azure som förväntat. [Läs mer](tutorial-dr-drill-azure.md).
7. **Redundans**. Om du bara migrerar de virtuella datorerna till Azure – kan du köra en fullständig redundans för att göra detta. Om du konfigurerar haveriberedskap, kan du köra en fullständig växling vid fel eftersom du behöver. För fullständig haveriberedskap, kan efter en redundansväxling till Azure, du växla tillbaka till din lokala plats och när den är tillgänglig. [Läs mer](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator, börjar den replikera enligt replikeringsprincipen. 
2. Trafik som replikeras till Azure storage-offentliga slutpunkter för via internet. Alternativt kan du använda Azure ExpressRoute med [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replikering av trafik via ett plats-till-plats virtuellt privat nätverk (VPN) från en lokal plats till Azure stöds inte.
3. En inledande kopia av virtuella datorns data replikeras till Azure storage.
4. När den inledande replikeringen är klar börjar replikeringen av deltaändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
5. Kommunikation händer följande:

    - Virtuella datorer kommunicera med den lokala konfigurationsservern på port HTTPS 443 inkommande, för replikeringshantering.
    - Konfigurationsservern samordnar replikeringen med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (som körs på configuration server-datorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure storage över port 443 utgående.


**VMware till Azure replikeringsprocessen**

![Replikeringsprocessen](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikering har ställts in och du kör ett programåterställningstest (testa redundans) om du vill kontrollera att allt fungerar som förväntat kan köra du redundans och återställning efter fel som du behöver.

1. Du kör misslyckas för en enskild dator eller skapa en återställning tillsammans att växla över flera virtuella datorer på samma gång. Fördelen med en återställningsplan snarare än en enda dator redundans inkluderar:
    - Du kan utforma appen beroenden genom att inkludera alla virtuella datorer i appen i en enda återställningsplan.
    - Du kan lägga till skript, Azure-runbooks och pausa för manuella åtgärder.
2. Du etablerar den för att få åtkomst till arbetsbelastningen från den virtuella Azure-datorer efter första redundansen.
3. När din primära lokala plats är tillgänglig igen, kan du förbereda för återställning vid fel. För att kunna återställa dit du behöva ställa in en infrastruktur för återställning efter fel, inklusive:

    * **Tillfällig processerver i Azure**: Om du vill redundansväxla från Azure, du har konfigurerat en Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    * **VPN-anslutningen**: för att återställa, du behöver en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
    * **Separat huvudmålserver**: som standard som installerades med konfigurationsservern på en lokal VMware VM huvudmålservern hanterar återställning efter fel. Om du vill växla tillbaka stora mängder trafik kan du konfigurera en separat lokal huvudmålserver för detta ändamål.
    * **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapades automatiskt när du skapade din replikeringsprincip från en lokal plats till Azure.
4. När komponenterna är på plats så utförs återställning efter fel i tre åtgärder:

    - Steg 1: Återaktivera skyddet av virtuella Azure-datorer så att de replikera från Azure till lokala VMware-datorer.
    -  Steg 2: Kör en redundansväxling till den lokala platsen.
    - Steg 3: När arbetsbelastningar har återställts du återaktivera replikering för de lokala virtuella datorerna.
    
 
**VMware återställning efter fel från Azure**

![Återställning efter fel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [den här självstudien](vmware-azure-tutorial.md) att aktivera VMware till Azure-replikering.
