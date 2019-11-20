---
title: Agent-baserad migrering i Azure Migrate Server-migrering
description: Innehåller en översikt över agentbaserade VMware VM-migrering med Azure Migrate Server-migrering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a8477b4c10ccbc76f36eed4d64ac12e8bb648a28
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186092"
---
# <a name="agent-based-migration-architecture"></a>Agentbaserad migreringsarkitektur

Den här artikeln innehåller en översikt över arkitekturen och processerna som används för agent-baserad replikering med verktyget för migrering av Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och AWS/GCP VM-instanser till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

## <a name="agent-based-replication"></a>Agent-baserad replikering

En agent-baserad replikering i Azure Migrate Server Replication-verktyget används för att migrera lokala virtuella VMware-datorer och fysiska servrar till Azure. Den kan också användas för att migrera andra lokala virtualiserade servrar, samt privata och offentliga virtuella moln, inklusive AWS-instanser och GCP-VM: ar.

För VMware-migrering erbjuder Migreringsverktyg för Azure Migrate Server ett par alternativ:

- Migrering med agent-baserad replikering enligt beskrivningen i den här artikeln.
- Replikering utan agent, för att migrera virtuella datorer utan att behöva installera något på dem.

Läs mer om hur du [väljer en migrations metod för VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Server migrering och Azure Site Recovery

Azure Migrate Server-migrering är ett verktyg för att migrera lokala och offentliga moln arbets belastningar till Azure. Den är optimerad för migrering. Site Recovery är ett haveri återställnings verktyg. Azure Server-migrering och Site Recovery dela några vanliga teknik komponenter som används för datareplikering, men fungerar på olika sätt.

## <a name="architectural-components"></a>Arkitekturkomponenter

![Arkitektur](./media/agent-based-replication-architecture/architecture.png)

Tabellen sammanfattar de komponenter som används för agent-baserad migrering.

**Komponent** | **Information** | **Installation**
--- | --- | ---
**Replikeringsfil** | Replikeringstjänsten (konfigurations Server) är en lokal dator som fungerar som en brygga mellan den lokala miljön och Migreringsverktyg för Azure Migrate Server. Installations programmet identifierar den lokala VM-inventeringen så att Azure Server-migreringen kan dirigera replikering och migrering. Enheten har två komponenter:<br/><br/> **Konfigurations Server**: ansluter till Azure Migrate Server-migrering och samordnar replikering.<br/> **Processerver**: hanterar datareplikering. Den tar emot VM-data, komprimerar och krypterar den och skickar dem till Azure-prenumerationen. Då skriver server-migrering data till Managed disks. | Som standard installeras processervern tillsammans med konfigurations servern på replikerings enheten.
**Mobilitetstjänsten** | Mobilitets tjänsten är en agent som är installerad på varje dator som du vill replikera och migrera. Den skickar replikeringsdata från datorn till processervern. Det finns ett antal olika mobila tjänst agenter som är tillgängliga. | Installationsfilerna för mobilitets tjänsten finns på replikerings enheten. Du kan hämta och installera den agent du behöver, i enlighet med operativ systemet och den version av datorn som du vill replikera.

### <a name="mobility-service-installation"></a>Installation av mobilitetstjänsten

Du kan distribuera mobilitets tjänsten på följande sätt:

- **Push-installation**: mobilitets tjänsten installeras av processervern när du aktiverar skydd för en dator. 
- **Installera manuellt**: du kan installera mobilitets tjänsten manuellt på varje dator via UI eller kommando tolken.

Mobilitets tjänsten kommunicerar med replikerings enheten och de replikerade datorerna. Om du har antivirus program som körs på replikerings enheten, bearbetar servrar eller datorer som replikeras, ska följande mappar undantas från genomsökningen:


- C:\Program\Microsoft Azure Recovery Services agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (på Windows-datorer med mobilitets tjänsten installerad)

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den inledande replikeringen till Azure.
2. Under den inledande replikeringen läser mobilitets tjänsten data från dator diskarna och skickar dem till processervern.
3. Dessa data används för att dirigera en kopia av disken i din Azure-prenumeration. 
4. När den inledande replikeringen har slutförts börjar replikeringen av delta ändringar till Azure. Replikering är block nivå och nästan kontinuerlig.
4. Mobilitets tjänsten fångar upp skrivningar till disk minne för virtuella datorer genom att integrera med underlag rings systemet i operativ systemet. Den här metoden undviker disk-I/O-åtgärder på den replikerande datorn för stegvis replikering. 
5. Spårade ändringar för en dator skickas till processervern på port HTTPS 9443 inkommande. Den här porten kan ändras. Processervern komprimerar och krypterar den och skickar den till Azure. 

## <a name="ports"></a>Portar

**Anordningar** | **Anslutning**
--- | --- 
Virtuella datorer | Mobilitets tjänsten som körs på virtuella datorer kommunicerar med den lokala replikeringstjänsten på port HTTPS 443 inkommande, för hantering av replikering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (körs på replikerings enheten som standard) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Replikeringsfil | Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
Processerver | Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.


## <a name="performance-and-scaling"></a>Prestanda och skalning

Som standard distribuerar du en enda replikeringsfil som kör både konfigurations servern och processervern. Om du bara replikerar några få datorer räcker den här distributionen. Men om du replikerar och migrerar hundratals datorer kanske en enskild processerver inte kan hantera all replikeringstrafik. I det här fallet kan du distribuera ytterligare, skalbara process servrar.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery distributions planerare för VMware

Om du replikerar virtuella VMware-datorer kan du använda den [Site Recovery distributions planeraren](../site-recovery/site-recovery-deployment-planner.md) för VMware för att fastställa prestanda krav, inklusive den dagliga data ändrings takten och de process servrar som du behöver.

### <a name="replication-appliance-capacity"></a>Kapacitet för replikerings utrustning

Värdena i den här tabellen kan användas för att avgöra om du behöver en ytterligare processerver i distributionen.

- Om din dagliga ändrings takt (omsättnings pris) är över 2 TB distribuerar du en ytterligare processerver.
- Om du replikerar fler än 200 datorer kan du distribuera en ytterligare replikeringsfil.

**CPU** | **Minnesoptimerade** | **Ledigt utrymme för cachelagring av data** | **Omsättnings pris** | **Begränsningar för replikering**
--- | --- | --- | --- | ---
8 virtuella processorer (2 Sockets * 4 kärnor \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | < 100-datorer 
12 virtuella processorer (2 Sockets * 6 kärnor \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB till 1 TB | 100-150-datorer.
16 virtuella processorer (2 Sockets * 8 kärnor \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB till 2 TB | 151-200-datorer.

### <a name="scale-out-process-server-sizing"></a>Storlek för skalnings process Server

Om du behöver distribuera en skalbar processerver kan den här tabellen hjälpa dig att räkna upp Server storlek.

**Processervern** | **Ledigt utrymme för cachelagring av data** | **Omsättnings pris** | **Begränsningar för replikering**
--- | --- | --- | --- 
4 virtuella processorer (2 Sockets * 2 kärnor \@ 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Upp till 85 datorer 
8 virtuella processorer (2 Sockets * 4 kärnor \@ 2,5 GHz), 12 GB minne | 600 GB | 251 GB till 1 TB    | 86-150-datorer.
12 virtuella processorer (2 Sockets * 6 kärnor \@ 2,5 GHz), 24 GB minne | 1 TB | 1-2 TB | 151-225-datorer.

## <a name="control-upload-throughput"></a>Styr uppladdnings data flöde


 VMware-trafik som replikeras till Azure går genom en speciell processerver. Du kan begränsa uppladdnings flödet genom att begränsa bandbredden på datorerna som körs som process servrar. Du kan påverka bandbredden med hjälp av den här register nyckeln:

- Registervärdet HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM anger antalet trådar som används för data överföring (inledande eller delta-replikering) på en disk. Ett högre värde ökar nätverks bandbredden som används för replikering. Standardvärdet är fyra. Det maximala värdet är 32. Övervaka trafiken för att optimera värdet.
- Dessutom kan du begränsa bandbredden på processervern på följande sätt:

    1. Öppna snapin-modulen Azure Backup MMC på processervern. Det finns en genväg på Skriv bordet eller i mappen C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. Välj **ändra egenskaper**i snapin-modulen.
    3. Under **begränsning**väljer du **Aktivera användnings begränsning för Internet bandbredd för säkerhets kopierings åtgärder**. Ange gränserna för arbets tid och ledig tid. Giltiga intervall är mellan 512 och 1 023 Mbit/s.


## <a name="next-steps"></a>Nästa steg

Prova agent-baserad [virtuell VMware-migrering](tutorial-migrate-vmware-agent.md) med hjälp av Azure Migrate Server-migrering.
