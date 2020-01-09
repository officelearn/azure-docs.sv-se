---
title: Hantera ett avbrott i Azure-tjänsten som påverkar Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Lär dig hur du gör i händelse av ett avbrott i Azure-tjänsten som påverkar Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: f65b61d7265268b33d2c9a113503cc1b5a87db8e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75361219"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Vad du gör om ett avbrott uppstår i Azure-tjänsten som påverkar Azure Cloud Services
På Microsoft är arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga för dig när du behöver dem. Det kommer inte längre att påverka vår kontroll på sätt som orsakar oplanerade avbrott i tjänsten.

Microsoft tillhandahåller en Serviceavtal (SLA) för sina tjänster som ett åtagande för drift tid och anslutning. Service avtalet för enskilda Azure-tjänster finns på [Azure Service nivå avtal](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda plattforms funktioner som stöder program med hög tillgänglighet. Läs [haveri beredskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)om du vill ha mer information om dessa tjänster.

Den här artikeln beskriver ett verkligt haveri återställnings scenario, när en hel region upplever ett avbrott på grund av stor natur katastrof eller omfattande avbrott i tjänsten. Detta är ovanliga förekomster, men du måste förbereda dig för möjligheten att det uppstår ett avbrott i en hel region. Om en hel region upplever avbrott i tjänsten blir de lokalt redundanta kopiorna av dina data tillfälligt otillgängliga. Om du har aktiverat geo-replikering lagras tre ytterligare kopior av dina Azure Storage blobbar och tabeller i en annan region. I händelse av ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återskapas mappar Azure om alla DNS-poster till den geo-replikerade regionen.

> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen och att den endast kommer att ske för data Center som är i stor drifts störning. Därför måste du också förlita dig på andra programspecifika säkerhets kopierings strategier för att uppnå den högsta tillgängliga tillgänglighets nivån. Mer information finns i [haveri beredskap och hög tillgänglighet för program som bygger på Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Om du vill kunna påverka din egen redundans kanske du vill överväga att använda [Geo-redundant lagring med Läs behörighet (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), vilket skapar en skrivskyddad kopia av dina data i en annan region.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Alternativ 1: Använd en distribution av säkerhets kopiering via Azure Traffic Manager
Den mest robusta katastrof återställnings lösningen omfattar att underhålla flera distributioner av programmet i olika regioner och sedan använda [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) för att dirigera trafik mellan dem. Azure Traffic Manager tillhandahåller flera [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md), så du kan välja om du vill hantera distributioner med en primär/säkerhets kopierings modell eller dela trafik mellan dem.

![Balansera Azure Cloud Services över flera regioner med Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

För det snabbaste svaret på förlusten av en region är det viktigt att du konfigurerar Traffic Manager [slut punkts övervakning](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Alternativ 2: Distribuera ditt program till en ny region
Att upprätthålla flera aktiva distributioner enligt beskrivningen i föregående alternativ medför ytterligare löpande kostnader. Om ditt mål för återställnings tid (RTO) är tillräckligt flexibelt och du har den ursprungliga koden eller det kompilerade Cloud Services paketet kan du skapa en ny instans av programmet i en annan region och uppdatera dina DNS-poster så att de pekar på den nya distributionen.

Mer information om hur du skapar och distribuerar ett moln tjänst program finns i [så här skapar och distribuerar du en moln tjänst](cloud-services-how-to-create-deploy-portal.md).

Beroende på dina program data källor kan du behöva kontrol lera återställnings procedurerna för program data källan.

* Azure Storage data källor finns i [Azure Storage replikering](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) för att kontrol lera vilka alternativ som är tillgängliga baserat på den valda replikerings modellen för ditt program.
* För SQL Database källor, Läs [Översikt: Cloud affärs kontinuitet och databas haveri beredskap med SQL Database](../sql-database/sql-database-business-continuity.md) för att kontrol lera vilka alternativ som är tillgängliga baserat på den valda replikerings modellen för ditt program.


## <a name="option-3-wait-for-recovery"></a>Alternativ 3: vänta på återställning
I det här fallet krävs ingen åtgärd på din del, men tjänsten blir otillgänglig tills regionen har återställts. Du kan se aktuell status för tjänsten på [Azure Service Health instrument panelen](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du implementerar en strategi för haveri beredskap och hög tillgänglighet finns i [haveri beredskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Information om hur du utvecklar en detaljerad teknisk förståelse av moln plattformens funktioner finns i [teknisk vägledning för Azure-återhämtning](/azure/architecture/checklist/resiliency-per-service).