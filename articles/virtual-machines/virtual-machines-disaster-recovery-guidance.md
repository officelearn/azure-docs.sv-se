---
title: "Disaster recovery scenarier för Azure Virtual Machines | Microsoft Docs"
description: "Lär dig vad du gör i händelse av att ett avbrott i Azure-tjänsten påverkar virtuella Azure-datorer."
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb986a41e33501ee71c93a48457ac4114e33c671
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Vad du gör i händelse av att ett avbrott i Azure-tjänsten påverkar virtuella Azure-datorer
På Microsoft är arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga för dig när du behöver dem. Framtvingar utanför vårt kontroll påverka oss på ett sätt som orsakar avbrott i tjänsten oplanerad ibland.

Microsoft tillhandahåller en serviceavtalet (SLA) för dess tjänster som ett åtagande för drifttid och anslutningar. SLA för enskilda Azure-tjänster finns på [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda funktioner som stöder hög tillgänglighet program. Mer information om dessa tjänster läsa [katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Den här artikeln beskriver en true för katastrofåterställning när en hela region påträffar ett avbrott på grund av större naturkatastrof eller omfattande driftstopp. Det är sällsynt förekomster men måste du förbereda för möjligheten att det finns ett avbrott för en hel region. Om en hel region får ett avbrott i tjänsten, är lokalt redundanta kopior av dina data tillfälligt inte tillgänglig. Om du har aktiverat geo-replikering, lagras tre fler kopior av dina Azure Storage-blobbar och tabeller i en annan region. Vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas Azure omvandlar när alla DNS-poster för georeplikerad regionen.

För att hantera dessa sällsynta förekomster ska ger vi följande riktlinjer för virtuella Azure-datorer när det gäller ett avbrott i tjänsten för hela region där Azure-dator som programmet distribueras.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Alternativ 1: Initiera redundans med hjälp av Azure Site Recovery
Du kan konfigurera Azure Site Recovery för din virtuella dator så att du kan återställa ditt program med en enda klickning i frågan minuter. Du kan replikera till Azure-region önskat och begränsad inte till parad regioner. Du kan komma igång med [replikering av virtuella datorer](https://aka.ms/a2a-getting-started). Du kan [skapa en återställningsplan](../site-recovery/site-recovery-create-recovery-plans.md) så att du kan automatisera hela redundans för ditt program. Du kan [din redundanstestningen](../site-recovery/site-recovery-test-failover-to-azure.md) förhand utan att påverka produktionsprogram eller pågående replikering. Om en primär region-avbrott som du precis [påbörja en växling](../site-recovery/site-recovery-failover.md) och se till att ditt program i målregionen.


## <a name="option-2-wait-for-recovery"></a>Alternativ 2: Vänta tills återställningen
I så fall krävs ingen åtgärd. Vet att det fungerar ordentligt om du vill återställa tjänsttillgänglighet. Du kan se aktuell status för tjänsten på vår [Azure Hälsoinstrumentpanelen](https://azure.microsoft.com/status/).

Detta är det bästa alternativet om du inte har ställt in Azure Site Recovery, geo-redundant lagring med läsbehörighet eller geo-redundant lagring innan avbrott. Om du har ställt in geo-redundant lagring eller läsbehörighet geo-redundant lagring för lagringskontot där dina VM virtuella hårddiskar (VHD) lagras, kan du se ut om du vill återställa basavbildningen VHD och försöka att etablera en ny virtuell dator från den. Detta är inte en förvalda alternativet eftersom det finns inga garantier för synkronisering av data. Det här alternativet är därför inte säkert att fungera.


> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen och sker endast för avbrott i region hela tjänsten. Därför måste du också lita på andra programspecifika säkerhetskopiering strategier för att uppnå den högsta nivån av tillgänglighet. Mer information finns i avsnittet på [Data strategier för katastrofåterställning](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Nästa steg

- Starta [skyddar ditt program som körs på Azure virtual machines](https://aka.ms/a2a-getting-started) med hjälp av Azure Site Recovery

- Läs mer om hur du implementerar en katastrofåterställning och strategi för hög tillgänglighet i [katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- För att utveckla en detaljerad tekniska förståelse av en molnplattform funktioner finns [Azure återhämtning teknisk vägledning](../resiliency/resiliency-technical-guidance.md).


- Om anvisningarna är inte avmarkera, eller om du vill att Microsoft ska utföra åtgärder för din räkning, kontakta [kundsupport](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
