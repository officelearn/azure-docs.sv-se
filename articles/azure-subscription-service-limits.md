---
title: Azure-prenumerationsbegränsningar och kvoter
description: Innehåller en lista över vanliga Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar. Detta omfattar information om hur du ökar begränsningar tillsammans med högsta värden.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 07/13/2018
ms.author: byvinyal
ms.openlocfilehash: 570eee6cc849c474cfeacf5a9ba1798f64f27183
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188088"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-prenumeration och tjänstbegränsningar, kvoter och krav
Det här dokumentet innehåller några av de vanligaste Microsoft Azure-gränserna, som kallas ibland också kvoter. Det här dokumentet täcker inte för närvarande alla Azure-tjänster. Med tiden, kommer listan utökats och uppdaterats så att den täcker flera av plattformen.

Besök [översikt över priser för Azure](https://azure.microsoft.com/pricing/) mer information om priser för Azure. Där kan du kan beräkna dina kostnader med hjälp av den [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) eller genom att gå till sidan med prisinformation för en tjänst (till exempel [Windows virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Tips för att hantera dina kostnader finns i [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing/billing-getting-started.md).

> [!NOTE]
> Om du vill höja gränsen eller kvot ovan den **standard gränsen**, [öppna en kundsupportärende utan kostnad](azure-resource-manager/resource-manager-quota-errors.md). Gränserna kan inte höjas ovanför den **maxgränsen** värdet som visas i följande tabeller. Om det finns inga **maxgränsen** kolumnen och sedan resursen saknar justerbara gränser.
>
> [Kostnadsfria utvärderingsversioner](https://azure.microsoft.com/offers/ms-azr-0044p) berättigar inte till gränsen eller kvot ökar. Om du har en [kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/offers/ms-azr-0044p), du kan uppgradera till en [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) prenumeration. Mer information finns i [uppgradera kostnadsfri utvärderingsversion av Azure till betala per användning](billing/billing-upgrade-azure-subscription.md) och [kostnadsfri utvärderingsprenumeration vanliga frågor och svar](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Gränser och Azure Resource Manager
Nu är det möjligt att kombinera flera Azure-resurser i en enda Azure-resursgrupp. När du använder resursgrupper, hanteras gränser som en gång var globala på regional nivå med Azure Resource Manager. Läs mer om Azure-resursgrupper, [översikt över Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

En ny tabell har lagts till återspeglar eventuella skillnader i begränsningar när du använder Azure Resource Manager i gränserna nedan. Det finns till exempel en **prenumerationsbegränsningar** tabell och en **prenumerationsbegränsningar – Azure Resource Manager** tabell. När det gäller en gräns för båda scenarierna, visas den bara i den första tabellen. Om inget annat anges är gränserna globala i alla regioner.

> [!NOTE]
> Det är viktigt att betona att kvoter för resurser i Azure-resursgrupper är per region tillgänglig för din prenumeration och är inte per prenumeration som kvoter för service management. Nu ska vi använda vCPU-kvoter som ett exempel. Om du vill öka kvoten med stöd för virtuella processorer, måste du bestämma hur många virtuella processorer som du vill använda i vilka regioner och gör sedan en specifik begäran för Azure-resursgrupp vCPU-kvoter för belopp och regioner som du vill. Om du behöver för att köra ditt program det med 30 virtuella processorer i Europa, västra, bör du därför uttryckligen begära 30 virtuella processorer i Västeuropa. Men du behöver inte en vCPU-kvot som ökar med alla andra regioner – endast Västeuropa har 30 vCPU-kvoten.
> <!-- --> Du kan därför vara bra att tänka på följande beslutar vad dina Azure-resursgrupp kvoter måste vara för din arbetsbelastning i valfri en region och begära det beloppet i varje region där du funderar på att distributionen. Se [felsöka distributionsproblem](resource-manager-common-deployment-errors.md) mer hjälp för att identifiera dina aktuella kvoter för vissa regioner.
>
>

## <a name="service-specific-limits"></a>Tjänstspecifika gränser
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure Event Grid](#azure-event-grid-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Säkerhetskopiering](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Kubernetes-tjänst](#kubernetes-service-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [Azure-brandväggen](#azure-firewall-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub Device Provisioning Service](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Hanterad identitet](#managed-identity-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Mobila tjänster](#mobile-services-limits)
* [Övervaka](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Nätverk](#networking-limits)
* [Network Watcher](#network-watcher-limits)
* [Notification Hub-tjänsten](#notification-hub-service-limits)
* [Resursgrupp](#resource-group-limits)
* [Rollbaserad åtkomstkontroll](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Sök](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple-systemet](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Prenumeration](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Virtual Machine Scale Sets](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Prenumerationsgränser
#### <a name="subscription-limits"></a>Prenumerationsgränser
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Prenumerationsbegränsningar – Azure Resource Manager
Följande begränsningar gäller när du använder Azure Resource Manager och Azure-resursgrupper. Gränser som inte har ändrats med Azure Resource Manager visas inte nedan. Se tabellen ovan för de här gränserna.

Information om hur du hanterar gränser för Resource Manager-förfrågningar finns i [begränsning resurshanteraren begär](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Gränser för resursgrupp
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Gränser för virtuella datorer
#### <a name="virtual-machine-limits"></a>Gränser för virtuell dator
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Gränser för virtuella datorer – Azure Resource Manager
Följande begränsningar gäller när du använder Azure Resource Manager och Azure-resursgrupper. Gränser som inte har ändrats med Azure Resource Manager visas inte nedan. Se tabellen ovan för de här gränserna.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Begränsningar för Virtual Machine Scale Sets
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances gränser
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry begränsar
I följande tabell beskrivs de funktioner och begränsningar för Basic, Standard och Premium [tjänstnivåer](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Tjänstbegränsningar för Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Begränsningar för nätverk
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Begränsningar för nätverk
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway begränsar
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher begränsar
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager-begränsningar
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS-gränser
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure-brandväggen begränsar
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

### <a name="storage-limits"></a>Lagringsgränser
Ytterligare information om begränsningar för lagringskonton finns i [skalbarhet för lagring av Azure- och prestandamål](storage/common/storage-scalability-targets.md).

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob storage-begränsningar
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure filer-gränser
Ytterligare information om begränsningar för Azure Files finns i [skalbarhets- och prestandamål i Azure Files](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure File Sync-gränser
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue storage-begränsningar
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table storage-begränsningar
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Diskgränser för virtuella datorer
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Se [storlekar för virtuella datorer](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för ytterligare information.

#### <a name="managed-virtual-machine-disks"></a>Hanterade virtuella datordiskar

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Ohanterade virtuella datordiskar

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Gränser för cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Gränser för Apptjänst
Följande begränsningar för App Service innehåller begränsningar för Web Apps, Mobile Apps, API Apps och Logic Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Scheduler-gränser
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Gränser för batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services gränser
I följande tabell visar gränserna för Azure Biztalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB-gränser
Azure Cosmos DB är en global skala databas där dataflöde och lagring kan skalas för att hantera vad ditt program kräver. Om du har frågor om skalbarhet som tillhandahåller Azure Cosmos DB kan du skicka e-postmeddelande till askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Azure Database för MySQL-gränser, se [begränsningar i Azure Database för MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Azure Database för PostgreSQL-gränser, se [begränsningar i Azure Database för PostgreSQL](postgresql/concepts-limits.md).

### <a name="mobile-engagement-limits"></a>Mobile Engagement gränser
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Sök gränser
Prisnivåer avgör kapacitet och begränsningar för search-tjänsten. Nivåerna ingår:

* *Kostnadsfria* multiklienttjänst, delas med andra Azure-prenumeranter, avsedd för utvärdering och små självständiga projekt.
* *Grundläggande* tillhandahåller dedikerade resurser för produktionsarbetsbelastningar i mindre skala, med upp till tre repliker för hög tillgänglighet frågearbetsbelastningar.
* *Standard (S1, S2, S3, S3 High Density)* är större för arbetsbelastningar under produktion. Flera nivåer som finns i standard-nivån så att du kan välja en resurskonfiguration som bäst passar din arbetsbelastning-profil.

**Gränserna per prenumeration**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Gränserna per söktjänst**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Mer information om gränser för en mer detaljerad nivå, till exempel dokumentstorlek, frågor per sekund, nycklar, begäranden och svar, se [tjänstbegränsningar i Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Begränsningar för Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>CDN-begränsningar
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Begränsningar för mobiltjänster
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Övervaka gränser
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Tjänstbegränsningar för Notification Hub
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs gränser
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus-gränser
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Begränsningar för IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Begränsningar för IoT Hub Device Provisioning-tjänsten
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Datafabriksgränser
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics-gränser
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store-gränser
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service begränsar
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics-gränser
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory-gränser
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Azure Event Grid-gränser
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps-gränser
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple-systemet gränser
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics gränser
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Begränsningar för säkerhetskopiering
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Gränser för Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights gränser
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API Management begränsar
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis Cache-gränser
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault-begränsningar
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation-gränser
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="managed-identity-limits"></a>Hanterad identitet gränser
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Rollbaserad åtkomstkontroll gränser
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database-gränser
SQL Database-gränser för finns i [SQL Database-Resursgränser för enskilda databaser](sql-database/sql-database-vcore-resource-limits-single-databases.md) och [SQL Database-Resursgränser för elastiska pooler och databaser i pooler](sql-database/sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse gränser
SQL Data Warehouse gränser för finns i [Resursgränser för SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Se också
[Förstå Azure-gränser och ökar](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Virtuell dator och Molntjänststorlekar för Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Storlekar för Cloud Services](cloud-services/cloud-services-sizes-specs.md)
