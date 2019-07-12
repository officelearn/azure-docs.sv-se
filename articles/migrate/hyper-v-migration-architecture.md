---
title: Hur fungerar Hyper-V-migrering med Azure Migrate servermigrering? | Microsoft Docs
description: Översikt över Hyper-V-migrering i Azure Migrate-servermigrering
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811718"
---
# <a name="how-does-hyper-v-replication-work"></a>Hur fungerar Hyper-V-replikering?

Den här artikeln innehåller en översikt över arkitekturen och processer som används när du migrerar Hyper-V-datorer med verktyget Azure Migrate servermigrering.

[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.

## <a name="agentless-migration"></a>Migrering utan agenter

Verktyget Azure Migrate servermigrering kan utan Agent för en lokal Hyper-V-datorer med hjälp av ett arbetsflöde för migrering som är optimerad för Hyper-V. Du kan installera en programvaruagent endast på Hyper-V-värdar eller noder i klustret. Inget behöver installeras på Hyper-V-datorer.

## <a name="server-migration-and-azure-site-recovery"></a>Migrering av Server och Azure Site Recovery

Azure Migrate-servermigrering är ett verktyg för att migrera lokala arbetsbelastningar och molnbaserade virtuella datorer till Azure. Site Recovery är ett verktyg för disaster recovery. Verktygen delar vissa komponenter för vanliga teknik som används för replikering av data, men har olika syften. 


## <a name="architectural-components"></a>Arkitekturkomponenter

![Arkitektur](./media/hyper-v-replication-architecture/architecture.png)



**Komponent** | **Distribution** | 
--- | --- 
**Replikeringsprovidern** | Microsoft Azure Site Recovery-providern är installerad på Hyper-V-värdar och registrerad servermigrering för Azure-migrering.<br/> Providern samordnar replikering för Hyper-V-datorer.
**Recovery Services-agenten** | Microsoft Azure Recovery Services-agenten hanterar datareplikeringen. Det fungerar med provider för att replikera data från Hyper-V-datorer till Azure.<br/> Replikerade data laddas upp till ett lagringskonto i din Azure-prenumeration. Migreringen av verktyget processer replikerade data och tillämpar den på replikeringsdiskar i prenumerationen. Replikeringsdiskar används för att skapa virtuella Azure-datorer när du migrerar.

- Komponenter installeras som en enda installationsfil ned från Azure Migrate servermigrering i portalen.
- Providern och installationen kan du använda utgående HTTPS-port 443-anslutningar för att kommunicera med Azure Migrate servermigrering.
- Kommunikation från providern och agenten är säker och krypterad.


## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell Hyper-V-dator, börjar den inledande replikeringen.
2. En Hyper-V VM-ögonblicksbild tas.
3. Virtuella hårddiskar på den virtuella datorn är replikerade en i taget, tills alla har kopierats till Azure. Tid för inledande replikering är beroende av VM-storlek och nätverkets bandbredd.
4. Diskändringar som sker under inledande replikering spåras med hjälp av Hyper-V-replikering och lagras i loggfiler (hrl-filer).
    - Loggfilerna är i samma mapp som diskarna.
    - Varje disk har en associerad hrl-fil som skickas till sekundär lagring.
    - Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När replikeringen har slutförts, VM-ögonblicksbild tas bort och deltareplikering börjar.
5. Inkrementell diskändringar spåras i hrl-filerna. Replikeringsloggar överförs med jämna mellanrum till ett Azure storage-konto med Recovery Services-agenten.


## <a name="performance-and-scaling"></a>Prestanda och skalning

Replikeringsprestanda för Hyper-V påverkas av faktorer som innehåller VM-storlek, dataändringshastigheten (omsättningen) för de virtuella datorerna, tillgängligt utrymme på Hyper-V-värden för log file storage, bandbredden för uppladdning för replikeringsdata och mål-lagringskontot i Azure.

- Om du replikerar flera datorer på samma gång, Använd den [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) för Hyper-V, för att optimera replikeringen.
- Planera din Hyper-V-replikering och distribuera replikering över Azure storage-konton, i enlighet med kapacitet.

### <a name="control-upload-throughput"></a>Kontrollen uppladdningsdataflödet

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

Prova att använda [Hyper-V migrering](tutorial-migrate-hyper-v.md) med hjälp av Azure Migrate servermigrering.
