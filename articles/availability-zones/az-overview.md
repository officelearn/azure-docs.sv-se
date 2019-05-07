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
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 31c85c4911642ece588340bd0e4a3a814aae4a20
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200767"
---
# <a name="what-are-availability-zones-in-azure"></a>Vad är Tillgänglighetszoner i Azure?
Tillgänglighetszoner är en hög tillgänglighet erbjudande som skyddar dina program och data från datacenter havererar. Tillgänglighetszoner är unika, fysiska platser inom en Azure-region. Varje zon består av en eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverkstjänster. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zonredundant tjänster replikera dina program och data i olika Tillgänglighetszoner och skydda från single punkter av fel. Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

Skapa hög tillgänglighet i programarkitekturen genom att samplacera dina resurser för beräkning, lagring, nätverk och data inom en zon och replikera i andra zoner. Azure-tjänster som stöd för Tillgänglighetszoner indelade i två kategorier:

- **Zonindelade tjänster** – du fästa resursen till en viss zon (till exempel virtuella datorer, hanterade diskar, IP-adresser), eller
- **Zonredundant tjänster** – plattform replikeras automatiskt mellan zoner (till exempel zonredundant lagring, SQL-databas).

Skapa programarkitekturen med hjälp av en kombination av Tillgänglighetszoner med Azure-region-par för att uppnå omfattande affärskontinuitet i Azure. Du synkront replikera dina program och data med hjälp av Tillgänglighetszoner inom en Azure-region för hög tillgänglighet och replikera asynkront i Azure-regioner för disaster recovery-skydd.
 
![Konceptuell visning av en zon som slutar fungera i en region](./media/az-overview/az-graphic-two.png)

## <a name="services-support-by-region"></a>Support för tjänster efter region

Kombinationer av Azure-tjänster och regioner som har stöd för Tillgänglighetszoner är:


|                                 |Nord- och Sydamerika |              |           |           | Europa |              |          |              | Asien och stillahavsområdet |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |Centrala USA|Östra USA|USA, östra 2|Västra USA 2|Frankrike, centrala|Norra Europa|Storbritannien, södra|Västra Europa|Östra Japan|Sydostasien|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |
| Virtuella Linux-datorer          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtuella Windows-datorer        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zone-redundant Storage          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Nätverk**                     |            |              |           |           |                |              |          |             |            |                |
| IP-standardadress        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway                     | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| ExpressRoute                    | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| Application Gateway   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Databaser**                     |            |              |           |           |                |              |          |             |            |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |            | &#10003;       |
| **Analys**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Integrering**                     |            |              |           |           |                |              |          |             |            |                |
| Service Bus (endast Premiumnivån) | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |



## <a name="services-resiliency"></a>Tjänster återhämtning
Alla Azure-hanteringstjänster arkitektur för att vara flexibel från fel region på zonnivå. Ett eller flera Tillgänglighetszon fel inom en region har ett mindre fel radius jämfört med en hel region-fel i spektrumet fel. Azure kan återställa från fel zon på servernivå med hanteringstjänster för regionen eller från en annan Azure-region. Azure utför kritiska Underhåll en zon i taget inom en region, för att förhindra eventuella fel som påverkar kundresurser som har distribuerats i olika Tillgänglighetszoner inom en region.

## <a name="pricing"></a>Prissättning
Det finns ingen extra kostnad för virtuella datorer som distribueras i en Tillgänglighetszon. 99,99% drifttid för virtuella datorer serviceavtal (SLA) erbjuds när två eller flera virtuella datorer distribueras över två eller fler Tillgänglighetszoner inom en Azure-region. Det kommer att ytterligare kommunikation mellan tillgänglighet zon VM-till-VM kostnader för dataöverföring. Mer information finns i [bandbredd priser](https://azure.microsoft.com/pricing/details/bandwidth/) sidan.


## <a name="get-started-with-availability-zones"></a>Kom igång med Tillgänglighetszoner
- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägg till en hanterad Disk med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa zonen redundant VM scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer mellan zoner med en Standardbelastningsutjämnare med en zonredundant klientdel](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Belastningsutjämna virtuella datorer inom en zon med hjälp av en Standardbelastningsutjämnare med en zonindelad klientdel](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zonredundant lagring](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Nästa steg
- [Snabbstartsmallar](https://aka.ms/azqs)
