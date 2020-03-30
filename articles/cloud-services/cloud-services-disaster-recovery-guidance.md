---
title: Hantera ett Avbrott i Azure-tjänsten som påverkar Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Lär dig vad du kan göra i händelse av ett Avbrott i Azure-tjänsten som påverkar Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: e3f0fd88eb302dac208f43d0622ae28b31dcddc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157515"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Vad du ska göra i händelse av ett Avbrott i Azure-tjänsten som påverkar Azure Cloud Services
På Microsoft arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga för dig när du behöver dem. Krafter utanför vår kontroll påverkar oss ibland på ett sätt som orsakar oplanerade avbrott i tjänsten.

Microsoft tillhandahåller ett servicenivåavtal (SLA) för sina tjänster som ett åtagande för drifttid och anslutning. SLA för enskilda Azure-tjänster finns på [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda plattformsfunktioner som stöder program med hög tillgänglighet. Mer information om dessa tjänster finns i [Katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Den här artikeln beskriver ett verkligt katastrofåterställningsscenario, när en hel region drabbas av ett avbrott på grund av större naturkatastrof eller omfattande avbrott i tjänsten. Dessa är sällsynta händelser, men du måste förbereda dig för möjligheten att det finns ett avbrott i en hel region. Om en hel region drabbas av ett avbrott i tjänsten är de lokalt redundanta kopiorna av dina data för tillfället otillgängliga. Om du har aktiverat geo-replikering lagras ytterligare tre kopior av dina Azure Storage-blobbar och tabeller i en annan region. I händelse av ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återställas, mappar Azure om alla DNS-poster till den geo-replikerade regionen.

> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen, och det kommer endast att inträffa för datacenteromfattande tjänstavbrott. På grund av detta måste du också förlita dig på andra programspecifika säkerhetskopieringsstrategier för att uppnå högsta tillgänglighetsnivå. Mer information finns i [Haveriberedskap och hög tillgänglighet för program som bygger på Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Om du vill kunna påverka din egen redundans kanske du vill överväga användningen av [geoundundant lagring med läsåtkomst (RA-GRS),](../storage/common/storage-redundancy.md)vilket skapar en skrivskyddad kopia av dina data i en annan region.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Alternativ 1: Använda en distribution av säkerhetskopiering via Azure Traffic Manager
Den mest robusta katastrofåterställningslösningen innebär att underhålla flera distributioner av ditt program i olika regioner och sedan använda [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) för att dirigera trafik mellan dem. Azure Traffic Manager innehåller flera [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md), så att du kan välja om du vill hantera dina distributioner med hjälp av en primär/säkerhetskopieringsmodell eller dela upp trafiken mellan dem.

![Balansera Azure Cloud Services mellan regioner med Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

För det snabbaste svaret på förlusten av en region är det viktigt att du konfigurerar Traffic Manager [slutpunktsövervakning](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Alternativ 2: Distribuera ditt program till en ny region
Att underhålla flera aktiva distributioner enligt beskrivningen i det tidigare alternativet medför ytterligare löpande kostnader. Om återställningstiden (RTO) är tillräckligt flexibel och du har den ursprungliga koden eller kompilerat Cloud Services-paketet kan du skapa en ny instans av ditt program i en annan region och uppdatera DNS-posterna så att de pekar på den nya distributionen.

Mer information om hur du skapar och distribuerar ett molntjänstprogram finns i [Så här skapar och distribuerar du en molntjänst](cloud-services-how-to-create-deploy-portal.md).

Beroende på dina programdatakällor kan du behöva kontrollera återställningsprocedurerna för programdatakällan.

* För Azure Storage-datakällor läser du [Azure Storage-redundans](../storage/common/storage-redundancy.md) för att kontrollera de alternativ som är tillgängliga baserat på den valda redundansmodellen för ditt program.
* För SQL Database-källor läser du [Översikt: Molnkontinuitet och databaskatastrofåterställning med SQL Database](../sql-database/sql-database-business-continuity.md) för att kontrollera de alternativ som är tillgängliga baserat på den valda replikeringsmodellen för ditt program.


## <a name="option-3-wait-for-recovery"></a>Alternativ 3: Vänta på återhämtning
I det här fallet krävs ingen åtgärd från din sida, men tjänsten kommer inte att vara tillgänglig förrän regionen har återställts. Du kan se aktuell tjänststatus på [Azure Service Health Dashboard](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du implementerar en katastrofåterställning och en strategi för hög tillgänglighet finns i [Katastrofåterställning och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Information om hur du utvecklar en detaljerad teknisk förståelse av en molnplattforms funktioner finns i [teknisk vägledning för Azure resiliency](/azure/architecture/checklist/resiliency-per-service).