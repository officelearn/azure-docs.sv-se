---
title: Begränsningar och kvoter för Azure-prenumeration
description: Innehåller en lista över vanliga gränser, kvoter och begränsningar för Azure-prenumerationer och tjänster. Den här artikeln innehåller information om hur du ökar gränserna tillsammans med högsta värden.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 1c30d9e70facaf9ab47bd33e5ca1fc1d35c6c979
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405851"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-prenumeration och tjänstbegränsningar, kvoter och krav
Det här dokumentet innehåller några av de vanligaste Microsoft Azures gränserna, som ibland kallas kvoter. Det här dokumentet avser för närvarande inte alla Azure-tjänster. Med tiden kommer listan att expanderas och uppdateras för att avse fler tjänster.

Mer information om priser för Azure finns i [Översikt över Azure-priser](https://azure.microsoft.com/pricing/). Där kan du beräkna dina kostnader med hjälp av [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/). Du kan också gå till sidan med pris information för en viss tjänst, till exempel [virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Tips som hjälper dig att hantera dina kostnader finns i [förebygga oväntade kostnader med fakturering och kostnads hantering i Azure](billing/billing-getting-started.md).

> [!NOTE]
> Om du vill höja gränsen eller kvoten över standard gränsen kan du [öppna en kund support förfrågan online utan kostnad](azure-resource-manager/resource-manager-quota-errors.md). Gränserna kan inte höjas över det maximala gräns värde som visas i följande tabeller. Om det inte finns någon övre gräns kolumn har inte resursen några justerbara gränser.
>
> De [kostnads fria utvärderings prenumerationerna](https://azure.microsoft.com/offers/ms-azr-0044p) är inte berättigade till begränsning eller kvot ökningar. Om du har en [kostnadsfri utvärderingsprenumeration](https://azure.microsoft.com/offers/ms-azr-0044p), du kan uppgradera till en [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) prenumeration. Mer information finns i [uppgradera din kostnads fria utvärderings prenumeration på Azure till en prenumeration där du betalar per](billing/billing-upgrade-azure-subscription.md) användning och [vanliga frågor och svar om utvärderings prenumerationen](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Gränser och Azure Resource Manager
Nu är det möjligt att kombinera flera Azure-resurser till en enda Azure-resurs grupp. När du använder resurs grupper, begränsar det att en gång har global hantering på regional nivå med Azure Resource Manager. Mer information om Azures resurs grupper finns i [Azure Resource Manager översikt](azure-resource-manager/resource-group-overview.md).

I följande lista över gränser visar en ny tabell eventuella skillnader i gränser när du använder Azure Resource Manager. Det finns till exempel en **prenumerations begränsnings** tabell och en prenumerations **gräns – Azure Resource Manager** tabell. När en gräns gäller för båda scenarierna visas den bara i den första tabellen. Om inget annat anges är gränserna globalt i alla regioner.

> [!NOTE]
> Kvoter för resurser i Azure-resurs grupper är per region som är tillgängliga för din prenumeration, inte per prenumeration som tjänst hanterings kvoterna. Vi använder vCPU-kvoter som exempel. Om du vill begära en kvot ökning med stöd för virtuella processorer, måste du bestämma hur många virtuella processorer du vill använda i vilka regioner. Sedan gör du en speciell begäran för Azures resurs grupp vCPU kvoter för de mängder och regioner som du vill ha. Om du behöver använda 30 virtuella processorer i Västeuropa för Europa för att köra ditt program där, så kan du begära 30 virtuella processorer i Västeuropa. Din vCPU-kvot ökas inte i någon annan region – endast Västeuropa har 30-vCPU-kvoten.
> <!-- -->
> Därför bör du bestämma vilka Azure-resurs grupps kvoter som krävs för din arbets belastning i en region. Sedan kan du begära detta belopp i varje region som du vill distribuera till. Information om hur du avgör dina aktuella kvoter för specifika regioner finns i [Felsöka distributions problem](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Tjänstspecifika gränser
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automation](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure-brandvägg](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning tjänst](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure SignalR-tjänsten](#azure-signalr-service-limits)
* [Säkerhetskopiering](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Frontend-tjänst](#azure-front-door-service-limits)
* [Identitets hanterare](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub Device Provisioning Service](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Nätverk](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure-tjänsten för front dörr](#azure-front-door-service-limits)
  * [Azure-brandvägg](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Lastbalanserare](#load-balancer)
  * [Offentlig IP-adress](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Notification Hubs](#notification-hubs-limits)
* [Resursgrupp](#resource-group-limits)
* [Rollbaserad åtkomstkontroll](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple-system](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Prenumeration](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Skalnings uppsättningar för virtuella datorer](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Prenumerationsgränser
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Prenumerations gränser – Azure Service Management (klassisk distributions modell)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Prenumerations gränser – Azure Resource Manager
Följande begränsningar gäller när du använder Azure Resource Manager och Azure-resurs grupper. Gränser som inte har ändrats med Azure Resource Manager finns inte med i listan. Se föregående tabell för dessa gränser.

Information om Läs-och skriv gränser i Resource Manager API finns i [begränsa Resource Manager-begäranden](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Resurs grupps gränser
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines gränser
#### <a name="virtual-machines-limits"></a>Virtual Machines gränser
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines gränser – Azure Resource Manager
Följande begränsningar gäller när du använder Azure Resource Manager och Azure-resurs grupper. Gränser som inte har ändrats med Azure Resource Manager finns inte med i listan. Se föregående tabell för dessa gränser.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Begränsningar för delade avbildnings Galleri

Det finns gränser per prenumeration för att distribuera resurser med hjälp av delade avbildnings gallerier:
- 100 delade avbildnings gallerier, per prenumeration, per region
- 1 000 avbildnings definitioner, per prenumeration, per region
- 10 000 avbildnings versioner, per prenumeration, per region

### <a name="virtual-machine-scale-sets-limits"></a>Begränsningar för skalnings uppsättningar för virtuell dator
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances gränser
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry gränser
I följande tabell beskrivs funktionerna och begränsningarna för [tjänst nivåerna](./container-registry/container-registry-skus.md)Basic, standard och Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Service gränser i Azure Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Azure Machine Learning tjänst gränser
Du hittar de senaste värdena för Azure Machine Learning Compute-kvoter på sidan för [Azure Machine Learning kvot](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Begränsningar för nätverk
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute-gränser
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway gränser

Följande tabell gäller v1, v2, standard och WAF SKU: er om inget annat anges.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher gränser
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager gränser
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS gränser
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Begränsningar för Azure-brandvägg
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Azure frontend-tjänstens gränser
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Lagrings gränser
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Mer information om begränsningar för lagrings konton finns i [Azure Storage skalbarhets-och prestanda mål](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Begränsningar för lagrings resurs leverantörer 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob Storage-gränser
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files gränser
Mer information om Azure Files gränser finns i [Azure Files skalbarhets-och prestanda mål](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure File Sync gränser
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Lagrings gränser i Azure Queue
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Lagrings gränser i Azure-tabell
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Disk gränser för virtuell dator
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Mer information finns i [storlekar för virtuella datorer](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Hanterade virtuella dator diskar

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Ohanterade virtuella dator diskar

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services-gränser
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service gränser
Följande App Service gränser inkluderar gränser för Web Apps, Mobile Apps och API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Funktions gränser
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Scheduler-gränser
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch-gränser
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services gränser
I följande tabell visas gränserna för Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB gränser
För Azure Cosmos DB gränser, se [gränser i Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
För Azure Database for MySQL gränser, se [begränsningar i Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
För Azure Database for PostgreSQL gränser, se [begränsningar i Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Azure Search gränser
Pris nivåer avgör din Sök tjänsts kapacitet och begränsningar. Nivåerna är:

* En **kostnads fri** tjänst för flera innehavare, som delas med andra Azure-prenumeranter, är avsedd för utvärdering och små utvecklings projekt.
* **Basic** tillhandahåller dedikerade data bearbetnings resurser för produktions arbets belastningar i en mindre skala, med upp till tre repliker för arbets belastningar med hög tillgänglighet.
* **Standard**, som omfattar S1, S2, S3 och S3, är för större produktions arbets belastningar. Det finns flera nivåer på standard nivån så att du kan välja en resurs konfiguration som bäst matchar din arbets belastnings profil.

**Gränser per prenumeration**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Gränser per Sök tjänst**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Om du vill veta mer om begränsningar på en mer detaljerad nivå, till exempel dokument storlek, frågor per sekund, nycklar, förfrågningar och svar, se [tjänst begränsningar i Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Media Services gränser
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network gränser
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services gränser
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Monitor gränser

#### <a name="alerts"></a>Aviseringar

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Åtgärdsgrupper

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Logg frågor och språk

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs gränser
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs gränser
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus gränser
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub gränser
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service gränser
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory gränser
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics gränser
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store gränser
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service gränser
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics gränser
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory gränser
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid gränser
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps gränser
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Policy gränser
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple system gränser
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Säkerhets kopierings gränser
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Service gränser i Azure SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Gränser för Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>API Management gränser
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Azure cache för Redis-gränser
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault gränser
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication-gränser
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation-begränsningar
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager-gränser
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Rollbaserade åtkomst kontroll gränser
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database gränser
För SQL Database gränser, se [SQL Database resurs gränser för enskilda databaser](sql-database/sql-database-vcore-resource-limits-single-databases.md), [SQL Database resurs gränser för elastiska pooler och databaser i pooler](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)och [SQL Database resurs gränser för hanterade instanser](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse gränser
SQL Data Warehouse gränser finns i [SQL Data Warehouse resurs gränser](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Se också
- [Förstå Azure-gränser och ökningar](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Virtuella datorer och moln tjänst storlekar för Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Storlekar för Azure-Cloud Services](cloud-services/cloud-services-sizes-specs.md)
