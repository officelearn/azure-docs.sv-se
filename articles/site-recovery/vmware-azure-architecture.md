---
title: VMware VM-katastrofåterställningsarkitektur i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används vid inställning av haveriberedskap för lokala virtuella virtuella datorer med VMware till Azure med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 77b4dd4c0efbe6d03e64865f18c2c87614aaecb5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632531"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware till Azure-arkitektur för haveriberedskap

I den här artikeln beskrivs arkitekturen och processerna som används när du distribuerar haveriberedskapsreplikering, redundans och återställning av virtuella VMware-datorer (VMware) mellan en lokal VMware-plats och Azure med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och grafik ger en vy på hög nivå av de komponenter som används för VMware-haveriberedskap till Azure.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure Storage-konto för cache, hanterad disk och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i Azure-lagring. Virtuella Azure-datorer skapas med replikerade data när du kör en redundans från lokalt till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Servermaskin för konfiguration** | En enda lokal maskin. Vi rekommenderar att du kör den som en virtuell virtuell VMware-dator som kan distribueras från en nedladdad OVF-mall.<br/><br/> Datorn kör alla lokala site recovery-komponenter, som inkluderar konfigurationsservern, processservern och huvudmålservern. | **Konfigurationsserver**: Samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.<br/><br/> **Processserver**: Installeras som standard på konfigurationsservern. Den tar emot replikeringsdata. optimerar den med cachelagring, komprimering och kryptering; och skickar den till Azure Storage. Processervern installerar också mobilitetstjänsten Azure Site Recovery på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer. När distributionen växer kan du lägga till ytterligare, separata processservrar för att hantera större volymer replikeringstrafik.<br/><br/> **Huvudmålserver:** Installeras som standard på konfigurationsservern. Den hanterar replikeringsdata under återställning efter fel från Azure. För stora distributioner kan du lägga till ytterligare en separat huvudmålserver för återställning efter fel.
**VMware-servrar** | Virtuella datorer med VMware finns på lokala vSphere ESXi-servrar. Vi rekommenderar en vCenter-server för att hantera värdarna. | Under distributionen av site recovery lägger du till VMware-servrar i recovery services-valvet.
**Replikerade datorer** | Mobilitetstjänsten installeras på varje virtuell virtuell dator med VMware som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processservern. Du kan också installera tjänsten manuellt eller använda en automatiserad distributionsmetod, till exempel Configuration Manager.

**Arkitektur för VMware till Azure**

![Komponenter](./media/vmware-azure-architecture/arch-enhanced.png)


## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den första replikeringen till Azure-lagring med den angivna replikeringsprincipen. Observera följande:
    - För virtuella datorer med VMware är replikeringen blocknivå, nästan kontinuerlig, med hjälp av mobilitetsserviceagenten som körs på den virtuella datorn.
    - Alla replikeringsprincipinställningar tillämpas:
        - **RPO-tröskel**. Den här inställningen påverkar inte replikeringen. Det hjälper till med övervakning. En händelse utlöses och eventuellt ett e-postmeddelande som skickas, om den aktuella RPO överskrider tröskelvärdet som du anger.
        - **Kvarhållning av återställningspunkt**. Den här inställningen anger hur långt tillbaka i tiden du vill gå när ett avbrott inträffar. Maximal retention på premiumlagring är 24 timmar. På standardförvaring är det 72 timmar. 
        - **Appkonsekventa ögonblicksbilder**. Appkonsekvent ögonblicksbild kan tas var 1 till 12 timmar, beroende på appens behov. Ögonblicksbilder är standard azure blob ögonblicksbilder. Mobilitetsagenten som körs på en virtuell dator begär en VSS-ögonblicksbild i enlighet med den här inställningen och bokmärken som pekar i tid som en konsekvent programpunkt i replikeringsströmmen.

2. Trafik replikerar till offentliga slutpunkter för Azure-lagring via internet. Alternativt kan du använda Azure ExpressRoute med [Microsoft peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Det går inte att replikera trafik över ett virtuellt privat nätverk (VPN) från en lokal plats till Azure.
3. Inledande replikeringsåtgärd säkerställer att hela data på datorn vid tidpunkten för aktivera replikering skickas till Azure. När den första replikeringen är klar börjar replikering av deltaändringar till Azure. Spårade ändringar för en dator skickas till processservern.
4. Kommunikation sker på följande sätt:

    - Virtuella datorer kommunicerar med den lokala konfigurationsservern på inkommande port HTTPS 443 för replikeringshantering.
    - Konfigurationsservern dirigerar replikering med Azure via utgående port-port HTTPS 443.
    - Virtuella datorer skickar replikeringsdata till processservern (som körs på konfigurationsservermaskinen) på inkommande port HTTPS 9443. Den här porten kan ändras.
    - Processservern tar emot replikeringsdata, optimerar och krypterar dem och skickar den till Azure-lagring via utgående port 443.
5. Replikeringsdataloggarna landar först i ett cachelagringskonto i Azure. Dessa loggar bearbetas och data lagras i en Azure Managed Disk (kallas som asr-seed disk). Återställningspunkterna skapas på den här disken.

**VMware till Azure-replikeringsprocess**

![Replikeringsprocessen](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Omsynkroniseringsprocess

1. Ibland, under inledande replikering eller när deltaändringar överförs, kan det finnas problem med nätverksanslutning mellan källdator för att bearbeta servern eller mellan processservern till Azure. Båda dessa kan leda till fel i dataöverföring till Azure tillfälligt.
2. För att undvika problem med dataintegritet och minimera dataöverföringskostnader markerar Site Recovery en dator för omsynkronisering.
3. En dator kan också markeras för omsynkronisering i situationer som följande för att upprätthålla konsekvensen mellan källdator och data som lagras i Azure
    - Om en maskin genomgår force shut-down
    - Om en dator genomgår konfigurationsändringar som storleksändring av disk (ändra storleken på disken från 2 TB till 4 TB)
4. Omsynkronisering skickar endast deltadata till Azure. Dataöverföring mellan lokala och Azure genom att minimera genom att beräkna kontrollsummer av data mellan källdatorn och data som lagras i Azure.
5. Som standard är omsynkronisering schemalagd att köras automatiskt utanför kontorstid. Om du inte vill vänta på standardresynkronisering utanför timmar kan du synkronisera om en virtuell dator manuellt. Det gör du genom att gå till Azure-portalen och välja den virtuella datorn > **synkronisera om**.
6. Om standardresynkronisering misslyckas utanför kontorstid och en manuell åtgärd krävs, genereras ett fel på den specifika datorn i Azure-portalen. Du kan lösa felet och utlösa omsynkroniseringen manuellt.
7. Efter slutförandet av omsynkronisering kommer replikering av deltaändringar att återupptas.

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har konfigurerats och du kör en haveriberedskapsövning (test redundans) för att kontrollera att allt fungerar som förväntat kan du köra redundans och redundans som du behöver.

1. Du kör misslyckas för en enda dator eller skapar en återställningsplaner för att växla över flera virtuella datorer samtidigt. Fördelen med en återställningsplan snarare än en maskin redundans inkluderar:
    - Du kan modellera appberoenden genom att inkludera alla virtuella datorer i appen i en enda återställningsplan.
    - Du kan lägga till skript, Azure-runbooks och pausa för manuella åtgärder.
2. När du har utlöst den första redundansen måste du påbörja den för att börja komma åt arbetsbelastningen från den virtuella Azure-datorn.
3. När din primära lokala webbplats är tillgänglig igen kan du förbereda dig för återställning. För att misslyckas måste du konfigurera en infrastruktur för återställning efter fel, inklusive:

    * **Tillfällig processserver i Azure:** För att växla tillbaka från Azure konfigurerar du en Virtuell Azure-dator för att fungera som en processserver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    * **VPN-anslutning:** För att växla tillbaka behöver du en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
    * **Separat huvudmålserver:** Som standard hanterar huvudmålservern som installerades med konfigurationsservern på den lokala virtuella datorn för VMware återställning. Om du behöver växla tillbaka stora trafikvolymer ställer du in en separat lokal huvudmålserver för detta ändamål.
    * **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapar en replikeringsprincip från lokalt till Azure.
4. När komponenterna är på plats sker återställning efter fel i tre åtgärder:

    - Steg 1: Rotera om Azure-virtuella datorer så att de replikeras från Azure tillbaka till de lokala virtuella datorerna för VMware.
    -  Steg 2: Kör en redundans till den lokala platsen.
    - Steg 3: När arbetsbelastningar har misslyckats tillbaka kan du återuppstämda replikering för de lokala virtuella datorerna.
    
 
**VMware-återställning från Azure**

![Återställning efter fel](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [den här självstudien](vmware-azure-tutorial.md) för att aktivera VMware till Azure-replikering.
