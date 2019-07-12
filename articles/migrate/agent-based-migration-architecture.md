---
title: Agentbaserad migreringsarkitektur i Azure Migrate-servermigrering
description: Översikt över agentbaserad VMware VM-migrering med Azure Migrate servermigrering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811159"
---
# <a name="agent-based-migration-architecture"></a>Agentbaserad migreringsarkitektur

Den här artikeln innehåller en översikt över arkitektur och processer som används för agentbaserad replikering med verktyget Azure Migrate servermigrering.

[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och AWS/GCP VM-instanser till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.

## <a name="agent-based-replication"></a>Agent-baserad replikering

Agent-baserad replikering i Azure Migrate serverreplikeringen verktyget används för att migrera lokala virtuella VMware-datorer och fysiska servrar till Azure. Det kan också användas för att migrera andra lokala virtualiserade servrar, samt privata och offentliga moln virtuella datorer, inklusive AWS-instanser och GCP virtuella datorer.

Migreringsverktyget Azure Migrate Server erbjuder ett par alternativ för VMware-migrering:

- Migrering med agentbaserad replikering, enligt beskrivningen i den här artikeln.
- Replikering, att migrera virtuella datorer utan att behöva installera något på dem utan Agent.

Läs mer om [att välja en migreringsmetod för VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migrering av Server och Azure Site Recovery

Azure Migrate-servermigrering är ett verktyg för att migrera lokala och offentliga molnarbetsbelastningar till Azure. Det är optimerat för migrering. Site Recovery är ett verktyg för disaster recovery. Azure-servermigrering och Site Recovery dela vissa vanliga teknik-komponenter som används för replikering av data, men har olika syften.

## <a name="architectural-components"></a>Arkitekturkomponenter

![Arkitektur](./media/agent-based-replication-architecture/architecture.png)

I tabell sammanfattas de komponenter som används för agentbaserad migrering.

**Komponent** | **Detaljer** | **Installation**
--- | --- | ---
**Installation av replikering** | Replikering installation (konfigurationsserver) är en lokal dator som fungerar som en brygga mellan den lokala miljön och Azure Migrate servermigrering tool. Installationen identifierar lokala virtuella datorn lagret, så att Azure-servermigrering kan samordna replikering och migrering. Installationen har två komponenter:<br/><br/> **Konfigurationsservern**: Ansluter till Azure Migrate servermigrering och samordnar replikering.<br/> **Processerver**: Hanterar datareplikeringen. Den tar emot data för virtuell dator, komprimerar och krypterar dem och skickar till Azure-prenumeration. Där kan skriver servermigrering data till hanterade diskar. | Som standard installeras processervern tillsammans med konfigurationsservern på replikering-installationen.
**Mobilitetstjänsten** | Mobilitetstjänsten är en agent installeras på varje dator som du vill replikera och migrera. Den skickar replikeringsdata från datorn till processervern. Det finns ett antal olika mobilitetstjänstagenter. | Installationsfilerna för mobilitetstjänsten finns på installationen för replikering. Du kan hämta och installera agenten som du behöver, i enlighet med operativsystem och version för den datorn som du vill replikera.

### <a name="mobility-service-installation"></a>Installation av mobilitetstjänsten

Du kan distribuera Mobilitetstjänsten med följande metoder:

- **Push-installation**: Mobilitetstjänsten installeras av processervern när du aktiverar skydd för en dator. 
- **Installera manuellt**: Du kan installera mobilitetstjänsten manuellt på varje dator via Användargränssnittet eller Kommandotolken.

Mobilitetstjänsten kommunicerar med installationen för replikering och replikerade datorer. Om du har ett antivirusprogram som körs på replikering enheten, processervrar eller datorer som replikeras, bör följande mappar undantas från genomsökning:


- C:\Program Files\Microsoft Azure Recovery Services-agenten
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (på Windows-datorer med mobilitetstjänsten installerad)

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator, börjar den inledande replikeringen till Azure.
2. Under den inledande replikeringen mobilitetstjänsten läser data från datordiskar och skickar dem till processervern.
3. Dessa data används för att dirigera en kopia av disken i Azure-prenumerationen. 
4. När den inledande replikeringen är klar börjar replikeringen av deltaändringar till Azure. Replikering är på blocknivå och nära kontinuerlig.
4. Mobilitetstjänsten fångar upp skrivningar till datorminnet disk genom att integrera med underlagringssystemet av operativsystemet. Den här metoden undviker disk i/o-åtgärder på den replikerande datorn för inkrementell replikering. 
5. Spårade ändringar för en dator skickas till processervern på port HTTPS 9443 inkommande. Den här porten kan ändras. Processervern komprimerar och krypterar dem och skickar det till Azure. 

## <a name="ports"></a>Portar

**enheten** | **anslutning**
--- | --- 
Virtuella datorer | Mobilitetstjänsten på virtuella datorer kommunicerar med lokala replikering installationen på port HTTPS 443 inkommande, för replikeringshantering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (körs på enheten som replikering som standard) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Installation av replikering | Replikering installationen arrangerar replikeringen med Azure via port HTTPS 443 utgående.
Processervern | Processervern tar emot replikeringsdata, optimerar och krypterar dem och skickar dem till Azure storage över port 443 utgående.


## <a name="performance-and-scaling"></a>Prestanda och skalning

Som standard kan du distribuera en enda replikering-installation som körs både konfigurationsservern och processervern. Om du replikerar bara några få datorer, är den här distributionen tillräckligt. Men om du replikerar och migrera hundratals datorer, kanske en enda processerver inte kan hantera alla replikeringstrafiken. I det här fallet kan du distribuera ytterligare, skala ut processervrar.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Distributionshanteraren för site Recovery för VMware

Om du replikerar virtuella VMware-datorer kan du använda den [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) för VMware för att avgöra prestandakrav, inklusive den dagliga datavolymen på ändra frekvensen och processervrar som du behöver.

### <a name="replication-appliance-capacity"></a>Kapacitet för installation av replikering

Värdena i den här tabellen kan användas för att ta reda på om du behöver en ytterligare processerver i distributionen.

- Om din dagliga förändringstakten (omsättningen pris) är över 2 TB, kan du distribuera en kompletterande processervern.
- Om du replikerar mer än 200 datorer kan du distribuera en ytterligare replikering-installation.

**CPU** | **Minne** | **Ledigt utrymme för datacachelagring av** | **Dataomsättningsfrekvensen** | **Begränsningar för replikering**
--- | --- | --- | --- | ---
8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | < 100 datorer 
12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB till 1 TB | 100-150 datorer.
16 virtuella processorer (2 platser * 8 kärnor \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB till 2 TB | 151-200 datorer.

### <a name="scale-out-process-server-sizing"></a>Skalbara process server storlek

Om du vill distribuera en skalbar processerver kan kan den här tabellen hjälpa dig att ta reda på server-storlek.

**Processervern** | **Ledigt utrymme för datacachelagring av** | **Dataomsättningsfrekvensen** | **Begränsningar för replikering**
--- | --- | --- | --- 
4 virtuella processorer (2 platser * 2 kärnor \@ 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Upp till 85 datorer 
8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 GHz), 12 GB minne | 600 GB | 251 GB till 1 TB    | 86 150 datorer.
12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz), 24 GB minne | 1 TB | 1-2 TB | 151 225 datorer.

## <a name="control-upload-throughput"></a>Kontrollen uppladdningsdataflödet

Du kan begränsa mängden bandbredd som används för att ladda upp data till Azure på varje Hyper-V-värd. Var försiktig. Om du har angett värden för lågt påverkar negativt replikering och fördröjning migrering.


1. Logga in på noden för Hyper-V-värden eller klustret.
2. Kör **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, för att öppna snapin-modulen för Windows Azure Backup MMC.
3. I snapin-modulen, väljer **ändra egenskaper för**.
4. I **begränsning**väljer **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder**. Ange begränsningarna för arbetstid och icke-tid. Giltiga intervall är från 512 kbit/s till 1,023 Mbit/s.
I

### <a name="influence-upload-efficiency"></a>Möjlighet att påverka uppladdning effektivitet

Om du har ledig bandbredd för replikering och vill utöka överföringar, kan du öka antalet trådar som allokerats för överföringsaktiviteten, enligt följande:

1. Öppna registret med Regedit.
2. Gå till nyckeln HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Öka värdet för antalet trådar som används för överföring av data för varje replikerande virtuella datorn. Standardvärdet är 4 och maxvärdet är 32. 

## <a name="next-steps"></a>Nästa steg

Testa agentbaserad [VMware VM-migrering](tutorial-migrate-vmware-agent.md) med hjälp av Azure Migrate servermigrering.
