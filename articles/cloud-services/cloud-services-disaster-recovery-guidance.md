---
title: Vad du gör vid en Azure-tjänst avbrott som påverkar Azure Cloud Services | Microsoft Docs
description: Lär dig vad du ska göra om ett avbrott i Azure-tjänsten som påverkar Azure Cloud Services.
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
ms.openlocfilehash: 7028417c95aa6969793c00d0bb270c96e56164fb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Vad du gör vid en Azure-tjänst avbrott som påverkar Azure Cloud Services
På Microsoft är arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga för dig när du behöver dem. Framtvingar utanför vårt kontroll påverka oss på ett sätt som orsakar avbrott i tjänsten oplanerad ibland.

Microsoft tillhandahåller en serviceavtalet (SLA) för dess tjänster som ett åtagande för drifttid och anslutningar. SLA för enskilda Azure-tjänster finns på [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda funktioner som stöder hög tillgänglighet program. Mer information om dessa tjänster läsa [katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Den här artikeln beskriver en true för katastrofåterställning när en hela region påträffar ett avbrott på grund av större naturkatastrof eller omfattande driftstopp. Det är sällsynt förekomster men måste du förbereda för möjligheten att det finns ett avbrott för en hel region. Om en hel region får ett avbrott i tjänsten, är lokalt redundanta kopior av dina data tillfälligt inte tillgänglig. Om du har aktiverat geo-replikering, lagras tre fler kopior av dina Azure Storage-blobbar och tabeller i en annan region. Vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas Azure omvandlar när alla DNS-poster för georeplikerad regionen.

> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen och sker endast för avbrott i datacenter hela tjänsten. Därför måste du också lita på andra programspecifika säkerhetskopiering strategier för att uppnå den högsta nivån av tillgänglighet. Mer information finns i [katastrofåterställning och hög tillgänglighet för program som bygger på Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Om du vill kunna påverka egna redundans kanske du vill överväga användning av [geo-redundant lagring med läsbehörighet (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), vilket skapar en skrivskyddad kopia av dina data i en annan region.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Alternativ 1: Använd en säkerhetskopiering distribution via Azure Traffic Manager
Den mest robusta lösningen för katastrofåterställning innebär att underhålla flera distributioner av programmet i olika regioner och sedan använda [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) att dirigera trafiken mellan dem. Azure Traffic Manager innehåller flera [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md), så du kan välja om du vill hantera dina distributioner med hjälp av en primär eller säkerhetskopiering modell eller dela upp trafiken mellan dem.

![NLB Azure Cloud Services över regioner med Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Snabbaste svaret till förlust av en region, är det viktigt att du konfigurerar Traffic Manager [slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Alternativ 2: Distribuera appen till en ny region
Underhålla flera aktiva distributioner enligt beskrivningen i föregående alternativ ådrar sig ytterligare kostnader. Om din recovery tid mål för Återställningstid är tillräckligt flexibelt och du har den ursprungliga koden eller kompilerade molntjänster paketet, kan du skapa en ny instans av ditt program i en annan region och uppdatera DNS-posterna pekar på den nya distributionen.

Mer information om hur du skapar och distribuerar en moln-tjänstprogrammet finns [hur du skapar och distribuerar en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md).

Du kan behöva kontrollera återställningsproceduren för datakällan programmet beroende på dina program datakällor.

* Azure Storage-datakällor finns i [Azure Storage-replikering](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) för att kontrollera de alternativ som är tillgängliga baserat på valt replikering modellen för ditt program.
* SQL-databas källor, läsa [översikt: molnet business continuity och databasen katastrofåterställning med SQL Database](../sql-database/sql-database-business-continuity.md) för att kontrollera de alternativ som är tillgängliga baserat på den valda replikering modellen för ditt program.


## <a name="option-3-wait-for-recovery"></a>Alternativ 3: Vänta tills återställningen
I så fall krävs ingen åtgärd från dig, men tjänsten blir otillgänglig tills regionen har återställts. Du kan se aktuell status för tjänsten på den [Azure Hälsoinstrumentpanelen](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du implementerar en katastrofåterställning och strategi för hög tillgänglighet i [katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

För att utveckla en detaljerad tekniska förståelse av en molnplattform funktioner finns [Azure återhämtning teknisk vägledning](../resiliency/resiliency-technical-guidance.md).