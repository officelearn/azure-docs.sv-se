---
title: Hur fungerar Hyper-V-migrering i Azure Migrate?
description: Lär dig mer om Hyper-V-migrering med Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185885"
---
# <a name="how-does-hyper-v-replication-work"></a>Hur fungerar Hyper-V-replikering?

Den här artikeln innehåller en översikt över arkitekturen och processerna som används när du migrerar virtuella virtuella hyper-v-datorer med verktyget Migrera server för Azure.This article provides an overview of the architecture and processes used when yourate Hyper-V VMs with the Azure Migrate Server Migration tool.

[Azure Migrate](migrate-services-overview.md) är en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer i molnet till Azure. Hubben tillhandahåller Azure Migrate-verktyg för utvärdering och migrering samt ISV-erbjudanden (Independent Software Vendor) från tredje part.

## <a name="agentless-migration"></a>Agentlös migrering

Azure Migrate Server Migration verktyget ger agentless replikering för lokala Hyper-V virtuella datorer, med hjälp av ett migreringsarbetsflöde som är optimerat för Hyper-V. Du installerar en programvaruagent endast på Hyper-V-värdar eller klusternoder. Ingenting behöver installeras på virtuella hyper-virtuella datorer.

## <a name="server-migration-and-azure-site-recovery"></a>Servermigrering och Azure Site Recovery

Azure Migrera servermigrering är ett verktyg för att migrera lokala arbetsbelastningar och molnbaserade virtuella datorer till Azure. Site Recovery är ett verktyg för haveriberedskap. Verktygen delar några vanliga teknikkomponenter som används för datareplikering, men tjänar olika syften. 


## <a name="architectural-components"></a>Arkitekturkomponenter

![Arkitektur](./media/hyper-v-replication-architecture/architecture.png)



**Komponent** | **Distribution** | 
--- | --- 
**Replikeringsprovider** | Microsoft Azure Site Recovery-providern installeras på Hyper-V-värdar och registreras med Azure Migration Server-migrering.<br/> Providern dirigerar replikering för virtuella hyper-virtuella datorer.
**Agent för återställningstjänster** | Microsoft Azure Recovery Service-agenten hanterar datareplikering. Det fungerar med providern för att replikera data från virtuella hyper-virtuella datorer till Azure.<br/> Replikerade data överförs till ett lagringskonto i din Azure-prenumeration. Verktyget Servermigrering bearbetar replikerade data och tillämpar dem på replikdiskar i prenumerationen. Replikdiskarna används för att skapa virtuella Azure-datorer när du migrerar.

- Komponenter installeras av en enda installationsfil som hämtas från Azure Migrate Server Migration i portalen.
- Providern och installationen använder utgående HTTPS-port 443-anslutningar för att kommunicera med Azure Migrate Server Migration.
- Kommunikation från leverantören och agenten är säker och krypterad.


## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell hyper-V-dator börjar den första replikeringen.
2. En ögonblicksbild av virtuella datorer med hyper-V tas.
3. Virtuella hårddiskar på den virtuella datorn replikeras en efter en, tills de alla kopieras till Azure. Inledande replikeringstid beror på den virtuella datorns storlek och nätverksbandbredd.
4. Diskändringar som sker under den första replikeringen spåras med Hyper-V-replik och lagras i loggfiler (hrl-filer).
    - Loggfiler finns i samma mapp som diskarna.
    - Varje disk har en associerad hrl-fil som skickas till sekundär lagring.
    - Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den första replikeringen är klar tas ögonblicksbilden av den virtuella datorn bort och deltareplikeringen börjar.
5. Inkrementella diskändringar spåras i HRL-filer. Replikeringsloggar överförs regelbundet till ett Azure-lagringskonto av Recovery Services-agenten.


## <a name="performance-and-scaling"></a>Prestanda och skalning

Replikeringsprestanda för Hyper-V påverkas av faktorer som inkluderar VM-storlek, dataändringshastigheten (omsättning) för de virtuella datorerna, tillgängligt utrymme på Hyper-V-värden för loggfilslagring, överför bandbredd för replikeringsdata och mållagring i Azure.

- Om du replikerar flera datorer samtidigt använder du [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) för Hyper-V för att optimera replikering.
- Planera hyper-V-replikeringen och distribuera replikering över Azure-lagringskonton, i enlighet med kapaciteten.

### <a name="control-upload-throughput"></a>Kontrollera dataflöde för uppladdning

Du kan begränsa mängden bandbredd som används för att överföra data till Azure på varje Hyper-V-värd. Var försiktig. Om du ställer in värdena för lågt kommer det att påverka replikeringen negativt och fördröja migreringen.


1. Logga in på Hyper-V-värden eller klusternoden.
2. Kör **C:\Program\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**för att öppna snapin-modulen Windows Azure Backup MMC.
3. Välj **Ändra egenskaper**i snapin-modulen .
4. I **Begränsning**väljer du Aktivera begränsning av **internetbandbreddsanvändning för säkerhetskopieringsåtgärder**. Ange gränser för arbete och icke-arbetstid. Giltiga intervall är från 512 kbit/s till 1 023 Mbit/s.
I

### <a name="influence-upload-efficiency"></a>Påverka uppladdningseffektiviteten

Om du har ledig bandbredd för replikering och vill öka uppladdningarna kan du öka antalet trådar som allokerats för uppladdningsuppgiften enligt följande:

1. Öppna registret med Regedit.
2. Navigera till viktiga HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Öka värdet för antalet trådar som används för dataöverföring för varje replikerande virtuell dator. Standardvärdet är 4 och maxvärdet är 32. 




## <a name="next-steps"></a>Nästa steg

Prova [Hyper-V-migrering](tutorial-migrate-hyper-v.md) med Azure Migrate Server Migration.
