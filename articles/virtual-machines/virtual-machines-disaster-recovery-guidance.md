---
title: Scenarier för katastrofåterställning
description: Lär dig vad du kan göra i händelse av att en Azure-tjänst avbrott påverkar Azure virtuella datorer.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: f2dc43e1f07d449bf2f8ed39ce4523c99b551dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115625"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Vad händer om ett Avbrott i Azure-tjänsten påverkar virtuella Azure-datorer
På Microsoft arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga för dig när du behöver dem. Krafter utanför vår kontroll påverkar oss ibland på ett sätt som orsakar oplanerade avbrott i tjänsten.

Microsoft tillhandahåller ett servicenivåavtal (SLA) för sina tjänster som ett åtagande för drifttid och anslutning. SLA för enskilda Azure-tjänster finns på [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda plattformsfunktioner som stöder program med hög tillgänglighet. Mer information om dessa tjänster finns i [Katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Den här artikeln beskriver ett verkligt katastrofåterställningsscenario, när en hel region drabbas av ett avbrott på grund av större naturkatastrof eller omfattande avbrott i tjänsten. Dessa är sällsynta händelser, men du måste förbereda dig för möjligheten att det finns ett avbrott i en hel region. Om en hel region drabbas av ett avbrott i tjänsten är de lokalt redundanta kopiorna av dina data för tillfället otillgängliga. Om du har aktiverat geo-replikering lagras ytterligare tre kopior av dina Azure Storage-blobbar och tabeller i en annan region. I händelse av ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återställas, mappar Azure om alla DNS-poster till den geo-replikerade regionen.

För att hjälpa dig att hantera dessa sällsynta händelser ger vi följande vägledning för virtuella Azure-datorer vid avbrott i tjänsten i hela regionen där ditt Azure-program för virtuella datorer distribueras.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Alternativ 1: Initiera en redundans med hjälp av Azure Site Recovery
Du kan konfigurera Azure Site Recovery för dina virtuella datorer så att du kan återställa ditt program med ett enda klick på några minuter. Du kan replikera till Azure-region som du väljer och inte begränsad till parkopplade regioner. Du kan komma igång genom [att replikera dina virtuella datorer](https://aka.ms/a2a-getting-started). Du kan [skapa en återställningsplan](../site-recovery/site-recovery-create-recovery-plans.md) så att du kan automatisera hela redundansprocessen för ditt program. Du kan [testa redundansen](../site-recovery/site-recovery-test-failover-to-azure.md) i förväg utan att påverka produktionsprogrammet eller den pågående replikeringen. I händelse av en primär region avbrott, du bara [starta en redundans](../site-recovery/site-recovery-failover.md) och ta med ditt program i målregionen.


## <a name="option-2-wait-for-recovery"></a>Alternativ 2: Vänta på återhämtning
I det här fallet krävs ingen åtgärd från din sida. Vet att vi arbetar hårt för att återställa tjänstens tillgänglighet. Du kan se aktuell tjänststatus på vår [Azure Service Health Dashboard](https://azure.microsoft.com/status/).

Detta är det bästa alternativet om du inte har konfigurerat Azure Site Recovery, läsåtkomst geo-redundant lagring eller geo-redundant lagring före avbrottet. Om du har konfigurerat geo redundant lagring eller geoupptund/redundant lagring med läsåtkomst för lagringskontot där virtuella hårddiskar för virtuella datorer med den virtuella datorn lagras kan du söka efter för att återställa basavbildningens virtuella hårddisk och försöka etablera en ny virtuell dator från det. Detta är inte ett alternativ som föredras eftersom det inte finns några garantier för synkronisering av data. Följaktligen är detta alternativ inte garanterat att fungera.


> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen, och det kommer bara att inträffa för regionomfattande avbrott i tjänsten. På grund av detta måste du också förlita dig på andra programspecifika säkerhetskopieringsstrategier för att uppnå högsta tillgänglighetsnivå. Mer information finns i avsnittet [om datastrategier för haveriberedskap](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Nästa steg

- Börja [skydda dina program som körs på virtuella Azure-datorer](https://aka.ms/a2a-getting-started) med Azure Site Recovery

- Mer information om hur du implementerar en katastrofåterställning och en strategi för hög tillgänglighet finns i [Katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Information om hur du utvecklar en detaljerad teknisk förståelse av en molnplattforms funktioner finns i [teknisk vägledning för Azure resiliency](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).


- Om instruktionerna inte är tydliga, eller om du vill att Microsoft ska utföra åtgärderna för din räkning, kontaktar du [kundtjänst](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
