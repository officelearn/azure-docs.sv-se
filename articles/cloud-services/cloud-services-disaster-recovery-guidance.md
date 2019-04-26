---
title: Vad du gör i händelse av en Azure-tjänsten avbrott som påverkar Azure Cloud Services | Microsoft Docs
description: Lär dig vad du gör i händelse av ett avbrott i Azure-tjänsten som påverkar Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mmccrory
ms.openlocfilehash: 4b355a779a2e9f78f4cbf8ed5425200ce1df2f1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337277"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Vad du gör i händelse av en Azure-tjänsten avbrott som påverkar Azure Cloud Services
På Microsoft är arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga för dig när du behöver dem. Framtvingar ligger utanför vårt kontroll påverka ibland oss på ett sätt som kan leda till oplanerade avbrott.

Microsoft tillhandahåller ett serviceavtal (SLA) för sina tjänster som ett åtagande gällande drifttid och anslutning. SERVICEAVTALET för enskilda Azure-tjänster finns på [Azure serviceavtal](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda funktioner som har stöd för program med hög tillgänglighet. Mer information om dessa tjänster läsa [haveriberedskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Den här artikeln innehåller en sant för katastrofåterställning när en hel region uppstår ett avbrott på grund av större naturkatastrofer eller utökas tjänstavbrott. Det här är ovanligt förekomster, men du måste beredd på att det finns ett avbrott i en hel region. Om en hel region uppstår ett avbrott i tjänsten kan blir lokalt redundant kopior av dina data tillfälligt otillgänglig. Om du har aktiverat geo-replikering, lagras tre ytterligare kopior av dina Azure Storage-blobbar och tabeller i en annan region. Vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas adressommappningar alla DNS-poster till den geo-replikerad regionen i Azure.

> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen och det inträffar endast för hela datacentret avbrott. Därför måste du också beroende andra programspecifika säkerhetskopieringsstrategier att uppnå den högsta möjliga tillgänglighet. Mer information finns i [haveriberedskap och hög tillgänglighet för program som bygger på Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Om du vill kunna påverka dina egna redundans kan du överväga att använda [läsåtkomst till geografiskt redundant lagring (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), vilket skapar en skrivskyddad kopia av dina data i en annan region.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Alternativ 1: Använda en säkerhetskopiering via Azure Traffic Manager-distribution
Den mest robusta haveriberedskapslösning innebär att underhålla flera distributioner av ditt program i olika regioner och sedan använda [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) att dirigera trafik mellan dem. Azure Traffic Manager tillhandahåller flera [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md), så att du kan välja om du vill hantera dina distributioner med en primär/backup-modell eller att dela upp trafiken mellan dem.

![Belastningsutjämning Azure Cloud Services i flera regioner med Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Snabbaste svaret till förlust av en region, är det viktigt att du konfigurerar Traffic Manager [slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Alternativ 2: Distribuera programmet till en ny region
Underhålla flera aktiva distributioner enligt beskrivningen i föregående alternativ medför ytterligare kostnader. Om din återställningstid (RTO) är tillräckligt flexibelt för att du har till Originalkoden eller kompilerade Cloud Services-paketet och kan du skapa en ny instans av ditt program i en annan region och uppdatera din DNS-poster så att den pekar till den nya distributionen.

Mer information om hur du skapar och distribuerar ett molnprogram för tjänsten finns i [hur du skapar och distribuerar en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md).

Beroende på din programdatakällor kan behöva du kontrollera återställningsproceduren för programmets datakälla.

* Azure Storage-datakällor finns [Azure Storage-replikering](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) för att läsa på de alternativ som är tillgängliga baserat på modellen välja replikering för ditt program.
* SQL Database-källor, läsa [översikt: Företag affärskontinuitet och databasen haveriberedskap med SQL-databas i molnet](../sql-database/sql-database-business-continuity.md) för att läsa på de alternativ som är tillgängliga baserat på den valda replikering-modellen för ditt program.


## <a name="option-3-wait-for-recovery"></a>Alternativ 3: Vänta tills återställningen
I så fall krävs ingen åtgärd från din sida, men tjänsten är inte tillgängliga förrän regionen har återställts. Du kan se aktuell status för tjänsten på den [Hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du implementerar en katastrofåterställning och strategi för hög tillgänglighet i [haveriberedskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

För att utveckla en detaljerad teknisk förståelse av en molnplattform funktioner kan se [Azure återhämtning, tekniska riktlinjer](../resiliency/resiliency-technical-guidance.md).