---
title: Scenarier för haveriberedskap för virtuella Azure-datorer | Microsoft Docs
description: Lär dig vad du gör i händelse av att ett avbrott i Azure-tjänst påverkar Azure-datorer.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc71e8564b35f4fdd4153a04c66a3d8c5df88c30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478852"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Vad du gör i händelse av att ett avbrott i Azure-tjänst påverkar virtuella Azure-datorer
På Microsoft är arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga för dig när du behöver dem. Framtvingar ligger utanför vårt kontroll påverka ibland oss på ett sätt som kan leda till oplanerade avbrott.

Microsoft tillhandahåller ett serviceavtal (SLA) för sina tjänster som ett åtagande gällande drifttid och anslutning. SERVICEAVTALET för enskilda Azure-tjänster finns på [Azure serviceavtal](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda funktioner som har stöd för program med hög tillgänglighet. Mer information om dessa tjänster läsa [haveriberedskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Den här artikeln innehåller en sant för katastrofåterställning när en hel region uppstår ett avbrott på grund av större naturkatastrofer eller utökas tjänstavbrott. Det här är ovanligt förekomster, men du måste beredd på att det finns ett avbrott i en hel region. Om en hel region uppstår ett avbrott i tjänsten kan blir lokalt redundant kopior av dina data tillfälligt otillgänglig. Om du har aktiverat geo-replikering, lagras tre ytterligare kopior av dina Azure Storage-blobbar och tabeller i en annan region. Vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas adressommappningar alla DNS-poster till den geo-replikerad regionen i Azure.

För att hjälpa dig att hantera dessa sällsynta förekomster, tillhandahåller vi följande riktlinjer för Azure-datorer när det gäller ett avbrott i tjänsten på hela regionen där ditt program för Azure-dator distribueras.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Alternativ 1: Initiera redundans med hjälp av Azure Site Recovery
Du kan konfigurera Azure Site Recovery för dina virtuella datorer så att du kan återställa ditt program med ett enda klick på bara minuter. Du kan replikera till Azure-region valfri och begränsad inte till ihopparade regioner. Du kan komma igång med [replikera dina virtuella datorer](https://aka.ms/a2a-getting-started). Du kan [skapa en återställningsplan](../site-recovery/site-recovery-create-recovery-plans.md) så att du kan automatisera hela redundans för ditt program. Du kan [din redundanstestningen](../site-recovery/site-recovery-test-failover-to-azure.md) förhand utan att påverka produktionsprogram eller pågående replikering. I händelse av avbrott primära regionen, som du precis [initiera redundans](../site-recovery/site-recovery-failover.md) och ge ditt program i målregion.


## <a name="option-2-wait-for-recovery"></a>Alternativ 2: Vänta tills återställningen
I så fall krävs ingen åtgärd från din sida. Vet att vi arbetar hårt för att återställa tjänstens tillgänglighet. Du kan se aktuell status för tjänsten på vår [Hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/).

Detta är det bästa alternativet om du inte har konfigurerat Azure Site Recovery, läsåtkomst till geografiskt redundant lagring eller geo-redundant lagring innan du kan avbrott. Om du har ställt in geo-redundant lagring eller read-access geo-redundant lagring för lagringskontot där dina VM virtuella hårddiskar (VHD) lagras, kan du se om du vill återställa den base VHD-avbildningen och försök att etablera en ny virtuell dator från den. Detta är inte en Kontaktalternativ eftersom det finns inga garantier för synkronisering av data. Det här alternativet är därför inte säkert att fungera.


> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen och den avser endast regionomfattande avbrott. Därför måste du också beroende andra programspecifika säkerhetskopieringsstrategier att uppnå den högsta möjliga tillgänglighet. Mer information finns i avsnittet på [Data strategier för haveriberedskap](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Nästa steg

- Starta [skydda program som körs på Azure virtual machines](https://aka.ms/a2a-getting-started) med Azure Site Recovery

- Läs mer om hur du implementerar en katastrofåterställning och strategi för hög tillgänglighet i [haveriberedskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- För att utveckla en detaljerad teknisk förståelse av en molnplattform funktioner kan se [Azure återhämtning, tekniska riktlinjer](../resiliency/resiliency-technical-guidance.md).


- Om anvisningarna är inte rensa eller om du vill att Microsoft ska utföra åtgärder för din räkning, kontakta [kundsupport](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
