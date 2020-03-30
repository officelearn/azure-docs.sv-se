---
title: Agentbaserad migrering i Azure Migrera servermigrering
description: Ger en översikt över agentbaserad VMware VM-migrering i Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425873"
---
# <a name="agent-based-migration-architecture"></a>Agentbaserad migreringsarkitektur

Den här artikeln innehåller en översikt över arkitekturen och processerna som används för agentbaserad replikering av virtuella datorer med Virtuella datorer med [Azure Migrate: Server Migration.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Med Azure Migrate: Servermigrering kan du replikera virtuella VMware-datorer med ett par alternativ:

- Migrera virtuella datorer med hjälp av agentbaserad replikering, enligt beskrivningen i den här artikeln.
- Migrera virtuella datorer med VMware med hjälp av agentless replikering. Detta migrerar virtuella datorer utan att behöva installera något på dem.

Läs mer om [hur du väljer och jämför](server-migrate-overview.md) migreringsmetoder för virtuella datorer med VMware. 


## <a name="agent-based-migration"></a>Agentbaserad migrering

Agentbaserad migrering används för att migrera lokala virtuella datorer och fysiska servrar till Azure. Den kan också användas för att migrera andra lokala virtualiserade servrar, samt privata och offentliga virtuella moln-datorer, inklusive AWS-instanser och GCP-virtuella datorer. Agentbaserad migrering i Azure Migrate använder vissa backend-funktioner från [Azure Site Recovery-tjänsten.](../site-recovery/site-recovery-overview.md)


## <a name="architectural-components"></a>Arkitekturkomponenter

Diagrammet illustrerar de komponenter som ingår i agentbaserad migrering.

![Arkitektur](./media/agent-based-replication-architecture/architecture.png)

Tabellen sammanfattar de komponenter som används för agentbaserad migrering.

**Komponent** | **Detaljer** | **Installation**
--- | --- | ---
**Replikeringsverktyget** | Replikeringsverktyget (konfigurationsserver/processserver) är en lokal dator som fungerar som en brygga mellan den lokala miljön och servermigrering. Installationen identifierar den lokala datorinventeringen så att servermigrering kan dirigera replikering och migrering. Apparaten har två komponenter:<br/><br/> **Konfigurationsserver**: Ansluter till servermigrering och koordinerar replikering.<br/> **Processserver**: Hanterar datareplikering. Processservern tar emot datordata, komprimerar och krypterar dem och skickar till Azure. I Azure skriver servermigrering data till hanterade diskar. | Som standard installeras processservern tillsammans med konfigurationsservern på replikeringsverktyget.
**Mobilitetstjänst** | Mobilitetstjänsten är en agent som är installerad på varje dator som du vill replikera och migrera. Replikeringsdata skickas från datorn till processservern. | Installationsfiler för olika versioner av mobilitetstjänsten finns på replikeringsverktyget. Du hämtar och installerar den agent du behöver, i enlighet med operativsystemet och versionen av den dator som du vill replikera.

## <a name="mobility-service-installation"></a>Installation av mobilitetstjänsten

Du kan distribuera mobilitetstjänsten med följande metoder:

- **Push-installation**: Mobilitetstjänsten installeras av processservern när du aktiverar skydd för en maskin. 
- **Installera manuellt:** Du kan installera mobilitetstjänsten manuellt på varje dator via användargränssnittet eller kommandotolken.

Mobilitetstjänsten kommunicerar med replikeringsverktyget och replikerade datorer. Om du har ett antivirusprogram som körs på replikeringsverktyget, processservrarna eller datorerna som replikeras bör följande mappar uteslutas från skanningen:


- C:\Program\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Återställning av Microsoft Azure-webbplatser
- C:\Program-filer (x86)\Återställning av Microsoft Azure-webbplatser
- C:\ProgramData\ASR\agent (på Windows-datorer med mobilitetstjänsten installerad)

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en dator börjar den första replikeringen till Azure.
2. Under den första replikeringen läser mobilitetstjänsten data från datorns diskar och skickar den till processservern.
3. Dessa data används för att dirigera en kopia av disken i din Azure-prenumeration. 
4. När den första replikeringen är klar börjar replikering av deltaändringar till Azure. Replikeringen är blocknivå och nästan kontinuerlig.
4. Mobilitetstjänsten fångar upp skrivningar till diskminne genom att integrera med undersystemet lagring i operativsystemet. Med den här metoden undviks disk-I/O-åtgärder på den replikerande datorn för inkrementell replikering. 
5. Spårade ändringar för en dator skickas till processservern på inkommande port HTTPS 9443. Den här porten kan ändras. Processservern komprimerar och krypterar den och skickar den till Azure. 

## <a name="ports"></a>Portar

**Enhet** | **Anslutning**
--- | --- 
**Replikera maskiner** | Mobilitetstjänsten som körs på virtuella datorer kommunicerar med den lokala replikeringsverktyget på inkommande port HTTPS 443 för replikeringshantering.<br/><br/> Datorer skickar replikeringsdata till processservern på inkommande port HTTPS 9443. Den här porten kan ändras.
**Replikeringsverktyget** | Replikeringsverktyget dirigerar replikering med Azure via port HTTPS 443 utgående.
**Bearbeta server** | Processservern tar emot replikeringsdata, optimerar och krypterar dem och skickar den till Azure-lagring via port 443 utgående.


## <a name="performance-and-scaling"></a>Prestanda och skalning

Som standard distribuerar du en enda replikeringsinstallation som kör både konfigurationsservern och processservern. Om du bara replikerar ett fåtal datorer är den här distributionen tillräcklig. Men om du replikerar och migrerar hundratals datorer kanske en enda processserver inte kan hantera all replikeringstrafik. I det här fallet kan du distribuera ytterligare, utskalningsprocessservrar.

### <a name="plan-vmware-deployment"></a>Planera VMware-distribution

Om du replikerar virtuella virtuella datorer med VMware kan du använda [site recovery deployment planner för VMware](../site-recovery/site-recovery-deployment-planner.md)för att fastställa prestandakrav, inklusive den dagliga dataändringshastigheten och de processservrar du behöver.

### <a name="replication-appliance-capacity"></a>Kapacitet för replikeringsanordning

Använd värdena i den här tabellen för att ta reda på om du behöver ytterligare en processserver i distributionen.

- Om din dagliga ändringshastighet (omsättningshastighet) är över 2 TB distribuerar du ytterligare en processserver.
- Om du replikerar fler än 200 datorer distribuerar du ytterligare en replikeringsinstallation.

**Cpu** | **Minne** | **Cachelagring av lediga utrymmesdata** | **Omsättningshastighet** | **Replikeringsgränser**
--- | --- | --- | --- | ---
8 virtuella processorer (2 uttag * \@ 4 kärnor 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | < 100 maskiner 
12 virtuella processorer (2 uttag * \@ 6 kärnor 2,5 GHz) | 18 GB | 600 GB | 501 GB till 1 TB | 100-150 maskiner.
16 vCPUs (2 uttag * \@ 8 kärnor 2,5 GHz) | 32 G1 (på andra) sätt |  1 TB | 1 TB till 2 TB | 151-200 maskiner.

### <a name="sizing-scale-out-process-servers"></a>Storleksskalningsprocessservrar

Om du behöver distribuera en skalningsprocessserver använder du den här tabellen för att ta reda på serverstorlek.

**Bearbeta server** | **Ledigt utrymme för cachelagring av data** | **Omsättningshastighet** | **Replikeringsgränser**
--- | --- | --- | --- 
4 vCPUs (2 uttag * \@ 2 kärnor 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Upp till 85 maskiner 
8 virtuella processorer (2 uttag * \@ 4 kärnor 2,5 GHz), 12 GB minne | 600 GB | 251 GB till 1 TB    | 86-150 maskiner.
12 virtuella processorer (2 uttag * \@ 6 kärnor 2,5 GHz), 24 GB minne | 1 TB | 1-2 TB | 151-225 maskiner.

## <a name="throttle-upload-bandwidth"></a>Gasspjälluppladdningsbandbredd.

VMware-trafik som replikeras till Azure går via en specifik processserver. Du kan begränsa överföringsdataflödet genom att begränsa bandbredden på de datorer som körs som processservrar. Du kan påverka bandbredden med den här registernyckeln:

- Registervärdet HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM anger antalet trådar som används för dataöverföring (initial eller deltareplikering) på en disk. Ett högre värde ökar nätverksbandbredden som används för replikering. Standardvärdet är fyra. Det maximala värdet är 32. Övervaka trafiken för att optimera värdet.
- Dessutom kan du begränsa bandbredden på processservermaskinen enligt följande:

    1. Öppna snapin-modulen Azure Backup MMC på processserverdatorn. Det finns en genväg på skrivbordet eller i mappen C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. Välj **Ändra egenskaper**i snapin-modulen .
    3. I **Begränsning**väljer du Aktivera begränsning av **internetbandbreddsanvändning för säkerhetskopieringsåtgärder**. Ange gränser för arbete och icke-arbetstid. Giltiga intervall är från 512 kbit/s till 1 023 Mbit/s.


## <a name="next-steps"></a>Nästa steg

Prova [agentbaserad migrering](tutorial-migrate-vmware-agent.md) för [VMware](tutorial-migrate-vmware-agent.md) eller [fysiska servrar](tutorial-migrate-physical-virtual-machines.md).
