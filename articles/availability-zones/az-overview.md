---
title: Vad är Tillgänglighetszoner i Azure? | Microsoft Docs
description: Om du vill skapa tillgängliga och robusta program i Azure får Availability Zones du fysiskt separata platser som du kan använda för att köra dina resurser.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2018
ms.author: cynthn
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 2a119e110d9d3687f24ebf7f72b2b4506674d4e3
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310138"
---
# <a name="what-are-availability-zones-in-azure"></a>Vad är Tillgänglighetszoner i Azure?
Tillgänglighetszoner är en hög tillgänglighet erbjudande som skyddar dina program och data från datacenter havererar. Tillgänglighetszoner är unika, fysiska platser inom en Azure-region. Varje zon består av en eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverkstjänster. För att säkerställa återhämtning, finns det minst tre separata zoner i alla aktiverade regioner. Fysisk avgränsning av Tillgänglighetszoner inom en region skyddar program och data mot datacenterproblem. Zonredundant tjänster replikera dina program och data i olika Tillgänglighetszoner och skydda från single punkter av fel. Azure erbjuder branschens bästa 99,99% VM DRIFTTID med Tillgänglighetszoner. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

En Tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer mellan tre zoner i en Azure-region är till exempel dina virtuella datorer effektivt fördelade på tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen över uppdateringsdomäner för att se till att virtuella datorer i olika zoner inte uppdateras på samma gång.

Skapa hög tillgänglighet i programarkitekturen genom att samplacera dina resurser för beräkning, lagring, nätverk och data inom en zon och replikera i andra zoner. Azure-tjänster som stöd för Tillgänglighetszoner indelade i två kategorier:

- **Zonindelade tjänster** – du fästa resursen till en viss zon (till exempel virtuella datorer, hanterade diskar, IP-adresser), eller
- **Zonredundant tjänster** – plattform replikeras automatiskt mellan zoner (till exempel zonredundant lagring, SQL-databas).

Skapa programarkitekturen med hjälp av en kombination av Tillgänglighetszoner med Azure-region-par för att uppnå omfattande affärskontinuitet i Azure. Du synkront replikera dina program och data med hjälp av Tillgänglighetszoner inom en Azure-region för hög tillgänglighet och replikera asynkront i Azure-regioner för disaster recovery-skydd.
 
![Konceptuell visning av en zon som slutar fungera i en region](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regioner som har stöd för Tillgänglighetszoner

- Centrala USA
- Östra USA 2 (förhandsversion)
- Frankrike, centrala
- Norra Europa
- Sydostasien (förhandsversion)
- Västra Europa
- Västra USA 2



## <a name="services-that-support-availability-zones"></a>Tjänster som stöder Tillgänglighetszoner
Azure-tjänster som stöd för Tillgänglighetszoner är:

- Virtuella Linux-datorer
- Virtuella Windows-datorer
- Virtual Machine Scale Sets
- Managed Disks
- Lastbalanserare
- Offentlig IP-adress
- Zonredundant lagring
- SQL Database
- Event Hubs
- Service Bus
- VPN Gateway
- ExpressRoute
- Application Gateway (förhandsversion)


## <a name="pricing"></a>Prissättning
Det finns ingen extra kostnad för virtuella datorer som distribueras i en Tillgänglighetszon. 99,99% drifttid för virtuella datorer serviceavtal (SLA) erbjuds när två eller flera virtuella datorer distribueras över två eller fler Tillgänglighetszoner inom en Azure-region. Det kommer att ytterligare kommunikation mellan tillgänglighet zon VM-till-VM kostnader för dataöverföring. Mer information finns i [bandbredd priser](https://azure.microsoft.com/pricing/details/bandwidth/) sidan.


## <a name="get-started-with-availability-zones"></a>Kom igång med Tillgänglighetszoner
- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägg till en hanterad Disk med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa zonen redundant VM scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer mellan zoner med en Standardbelastningsutjämnare med en zonredundant klientdel](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Belastningsutjämna virtuella datorer inom en zon med hjälp av en Standardbelastningsutjämnare med en zonindelad klientdel](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zonredundant lagring](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones-preview)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones-preview)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Nästa steg
- [Snabbstartsmallar](http://aka.ms/azqs)
