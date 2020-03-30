---
title: Azure-prenumerationsbegränsningar och kvoter
description: Innehåller en lista över vanliga Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar. Den här artikeln innehåller information om hur du ökar gränserna tillsammans med högsta värden.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 325f7b3d03435945779c1f42e13681dcfd9604b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334658"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar

Det här dokumentet innehåller några av de vanligaste Microsoft Azure-gränserna, som ibland också kallas kvoter.

Mer information om Azure-priser finns i [Azure-prisöversikt](https://azure.microsoft.com/pricing/). Där kan du uppskatta dina kostnader med hjälp av [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/). Du kan också gå till prisinformationssidan för en viss tjänst, till exempel [Windows virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Tips för att hantera dina kostnader finns i [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](../../billing/billing-getting-started.md).

## <a name="managing-limits"></a>Hantera gränser

> [!NOTE]
> Vissa tjänster har justerbara gränser.
>
> När en tjänst inte har justerbara gränser använder följande tabeller rubriken **Gräns**. I sådana fall är standard- och maximigränserna desamma.
>
> När gränsen kan justeras innehåller tabellerna **standardgräns** och **maxgränsrubriker.** Gränsen kan höjas över standardgränsen men inte över maxgränsen.
>
> Om du vill höja gränsen eller kvoten över standardgränsen [öppnar du en kundsupportbegäran online utan kostnad](../templates/error-resource-quota.md).

[Kostnadsfria utvärderingsprenumerationer](https://azure.microsoft.com/offers/ms-azr-0044p) är inte berättigade till limit- eller kvothöjningar. Om du har en [kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/offers/ms-azr-0044p)kan du uppgradera till en [prenumeration på användningsbaserad betalning.](https://azure.microsoft.com/offers/ms-azr-0003p/) Mer information finns i [Uppgradera din Azure Free Trial-prenumeration till en prenumeration med användningsbaserad betalning](../../billing/billing-upgrade-azure-subscription.md) och vanliga frågor om kostnadsfri utvärderingsversion av kostnadsfri [utvärderingsversion](https://azure.microsoft.com/free/free-account-faq).

Vissa gränser hanteras på regional nivå.

Låt oss använda vCPU-kvoter som exempel. Om du vill begära en kvotökning med stöd för virtuella processorer måste du bestämma hur många virtuella processorer du vill använda i vilka regioner. Du gör sedan en specifik begäran om Azure-resursgrupp vCPU-kvoter för de belopp och regioner som du vill ha. Om du behöver använda 30 virtuella processorer i Västeuropa för att köra ditt program där, begär du specifikt 30 virtuella processorer i Västeuropa. Din vCPU-kvot ökar inte i någon annan region - bara Västeuropa har 30-vCPU kvot.

Därför bestämmer du vad dina Azure-resursgruppkvoter måste vara för din arbetsbelastning i en och samma region. Begär sedan det beloppet i varje region som du vill distribuera. Mer information om hur du fastställer dina aktuella kvoter för specifika regioner finns i [Lösa fel för resurskvoter](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Allmänna gränser

Begränsningar för resursnamn finns i [Namngivningsregler och begränsningar för Azure-resurser](resource-name-rules.md).

Information om Resurshanterarens API läs- och skrivgränser finns i [Begränsningsresurshanterarens begäranden](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Gränser för ledningsgruppen

Följande gränser gäller för [hanteringsgrupper](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Prenumerationsgränser

Följande gränser gäller när du använder Azure Resource Manager och Azure-resursgrupper.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Begränsningar för resursgrupp

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory-gränser

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Begränsningar för API-hantering

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Begränsningar för apptjänst

Följande begränsningar för App Service inkluderar begränsningar för webbappar, mobilappar och API-appar.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Begränsningar för automatisering

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure Cache för Redis-gränser

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure Cloud Services-gränser

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search-gränser

Prisnivåer bestämmer kapaciteten och gränserna för din söktjänst. Nivåer inkluderar:

* **Kostnadsfri** tjänst med flera innehavare, som delas med andra Azure-prenumeranter, är avsedd för utvärdering och små utvecklingsprojekt.
* **Basic** tillhandahåller dedikerade datorresurser för produktionsarbetsbelastningar i mindre skala, med upp till tre repliker för högtillgängliga frågearbetsbelastningar.
* **Standard**, som inkluderar S1, S2, S3 och S3 High Density, är för större produktionsarbetsbelastningar. Det finns flera nivåer på standardnivån så att du kan välja en resurskonfiguration som bäst matchar din arbetsbelastningsprofil.

**Gränser per prenumeration**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Gränser per söktjänst**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Mer information om begränsningar på en mer detaljerad nivå, till exempel dokumentstorlek, frågor per sekund, nycklar, begäranden och svar, finns [i Tjänstgränser i Azure Cognitive Search](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Begränsningar för Azure Cognitive Services

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB-gränser

För Azure Cosmos DB-gränser finns [i Gränser i Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Azure Data Explorer-gränser

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

För Azure-databas för MySQL-gränser finns [i Begränsningar i Azure Database för MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

För Azure-databas för PostgreSQL-gränser finns [i Begränsningar i Azure Database för PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Begränsningar för Azure-funktioner

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes-tjänstgränser

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning-gränser

De senaste värdena för Azure Machine Learning Compute-kvoter finns på [kvotsidan för Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Azure Maps-gränser

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure Monitor-gränser

### <a name="alerts"></a>Aviseringar

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Åtgärdsgrupper

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Loggfrågor och språk

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Begränsningar för Azure-principen

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure SignalR-tjänstgränser

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Begränsningar för säkerhetskopiering

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batchgränser

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Begränsningar för klassiska distributionsmodeller

Om du använder klassisk distributionsmodell i stället för Azure Resource Manager-distributionsmodellen gäller följande gränser.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Begränsningar för behållarinstanser

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Begränsningar för behållarregister

I följande tabell beskrivs funktionerna och gränserna för [tjänstnivåerna](../../container-registry/container-registry-skus.md)Basic, Standard och Premium .

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Begränsningar för innehållsleveransnätverk

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Gränser för datafabrik

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Gränser för DataSjöanalys

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Begränsningar för Datasjölagring

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Gränser för datadelning

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Gränser för tjänsten för databasmigrering

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Gränser för händelserutnät

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Gränser för händelsehubbar

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Gränser för identitetshanteraren

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>Centrala IoT-gränser
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub-gränser

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Begränsningar för etablering av IoT-hubbenheter

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Gränser för nyckelvalv

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Begränsningar av medietjänster

[!INCLUDE [azure-mediaservices-limits](../../../includes/azure-mediaservices-limits.md)]

## <a name="mobile-services-limits"></a>Begränsningar för mobila tjänster

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Gränser för multifaktorautentisering

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Begränsningar för nätverk

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>ExpressRoute-gränser

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Virtuella WAN-gränser

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Begränsningar för programgateway

Följande tabell gäller v1, v2, Standard och WAF SKU om inte annat anges.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Begränsningar för nätverksbevakare

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link-gränser

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Trafikhanterarens gränser

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure Bastion-gränser

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS-gränser

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Begränsningar för Azure-brandväggen

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure-gränsers tjänstgränser

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Gränser för meddelandehubbar

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Rollbaserade åtkomstkontrollgränser

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Servicebussgränser

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Gränser för Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Begränsningar för SQL-databas

För SQL Database-begränsningar finns i [SQL Database-resursgränser för enskilda databaser,](../../sql-database/sql-database-vcore-resource-limits-single-databases.md) [SQL Database-resursgränser för elastiska pooler och poolade databaser](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)och [resursbegränsningar för SQL Database för hanterade instanser](../../sql-database/sql-database-managed-instance-resource-limits.md).

## <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse gränser

Information om SQL Data Warehouse-begränsningar finns i [SQL Data Warehouse-resursgränser](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Begränsningar för lagring

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Mer information om begränsningar för standardlagringskonton finns i [Skalbarhetsmål för standardlagringskonton](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Begränsningar för lagringsresursprovider

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob lagringsgränser

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Begränsningar för Azure-filer

Mer information om Azure Files-gränser finns i [Azure Files skalbarhet och prestandamål](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Begränsningar för Synkronisering av Azure-filer

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Lagringsgränser för Azure-kö

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Lagringsgränser för Azure Table

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Diskgränser för virtuella datorer

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Mer information finns i [Storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="managed-virtual-machine-disks"></a>Hanterade hårddiskar för virtuella datorer

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Ohanterliga virtuella datordiskar

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Begränsningar för StorSimple-system

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Begränsa Analytics-gränser

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Begränsningar för virtuella datorer

### <a name="virtual-machines-limits"></a>Begränsningar för virtuella datorer

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Begränsningar för virtuella datorer - Azure Resource Manager

Följande gränser gäller när du använder Azure Resource Manager och Azure-resursgrupper.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Gränser för delat bildgalleri

Det finns gränser per prenumeration för att distribuera resurser med delade avbildningsgallerier:

- 100 delade bildgallerier, per prenumeration, per region
- 1 000 bilddefinitioner per prenumeration, per region
- 10 000 bildversioner, per prenumeration, per region

## <a name="virtual-machine-scale-sets-limits"></a>Skala för virtuell dator anger gränser

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Se även

* [Förstå Azure-gränser och ökningar](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Storlekar för virtuella datorer och molntjänster för Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Storlekar för Azure Cloud Services](../../cloud-services/cloud-services-sizes-specs.md)
* [Namngivningsregler och begränsningar för Azure-resurser](resource-name-rules.md)
