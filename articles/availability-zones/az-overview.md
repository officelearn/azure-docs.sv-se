---
title: Vad är Azure-tillgänglighetszoner? | Microsoft Docs
description: Om du vill skapa hög tillgängliga och elastiska program i Azure kan Tillgänglighetszoner tillhandahålla fysiskt åtskilda platser som du kan använda för att köra dina resurser.
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
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1733493c88129f465782af8ac5e6c4914fd213ca
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73957949"
---
# <a name="what-are-availability-zones-in-azure"></a>Vad är Tillgänglighetszoner i Azure?
Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zoner – redundanta tjänster replikerar dina program och data över Tillgänglighetszoner för att skydda från enskilda platser. Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

Bygg hög tillgänglighet i din program arkitektur genom att samplacera din beräkning, lagring, nätverk och data resurser inom en zon och replikera i andra zoner. Azure-tjänster som stöder Tillgänglighetszoner delas in i två kategorier:

- **Zonindelade Services** – du fäster resursen till en speciell zon (till exempel virtuella datorer, hanterade diskar, standard-IP-adresser) eller
- **Zoner – redundanta tjänster** – plattform replikeras automatiskt mellan zoner (till exempel zon redundant lagring, SQL Database).

För att uppnå omfattande affärs kontinuitet i Azure kan du bygga din program arkitektur genom att kombinera Tillgänglighetszoner med Azures region par. Du kan synkront replikera dina program och data med hjälp av Tillgänglighetszoner inom en Azure-region för hög tillgänglighet och asynkront replikera över Azure-regioner för haveri beredskaps skydd.
 
![Konceptuell visning av en zon i en region](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Tillgänglighets zon identifierarna (talen 1, 2 och 3 i bilden ovan) mappas logiskt till de faktiska fysiska zonerna för varje prenumeration oberoende av varandra. Det innebär att tillgänglighets Zon 1 i en specifik prenumeration kan referera till en annan fysisk zon än tillgänglighets Zon 1 i en annan prenumeration. Därför rekommenderar vi att du inte förlitar sig på tillgänglighets zon-ID: n mellan olika prenumerationer för virtuell dator placering.

## <a name="services-support-by-region"></a>Support tjänster per region

De kombinationer av Azure-tjänster och regioner som stöder Tillgänglighetszoner är:


|                                 |Nord- och Sydamerika |              |           |           | Europa |              |          |              | Asien och stillahavsområdet |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |Centrala USA|Östra USA|USA, östra 2|Västra USA 2|Frankrike, centrala|Norra Europa|Storbritannien, södra|Västra Europa|Östra Japan|Sydostasien|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |
| Virtuella Linux-datorer          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtuella Windows-datorer        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Skalningsuppsättningar för Virtual Machines      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Lagring**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zon-redundant lagring          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Nätverk**                     |            |              |           |           |                |              |          |             |            |                |
| Standard-IP-adress        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute-Gateway   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Application Gateway (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Databaser**                     |            |              |           |           |                |              |          |             |            |                |
| Azure-datautforskaren                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;Förhandsgranskningsvyn      | &#10003;       |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analyser**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Samordning**                     |            |              |           |           |                |              |          |             |            |                |
| Service Bus (endast Premium-nivå) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identitet**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Tjänste återhämtning
Alla Azures hanterings tjänster är utformad för att vara elastiska från problem på regions nivå. I spektrumet av problem har en eller flera tillgänglighets zon problem inom en region en mindre felradie jämfört med ett hela regions haveri. Azure kan återställas från ett fel i hanterings tjänster på zon nivå i regionen eller från en annan Azure-region. Azure utför kritiska underhåll en zon i taget inom en region, för att förhindra att eventuella problem påverkar kund resurser som distribueras i Tillgänglighetszoner inom en region.

## <a name="pricing"></a>Priser
Det kostar ingen ytterligare kostnad för virtuella datorer som distribueras i en tillgänglighets zon. service nivå avtal för 99,99% VM-drift tid erbjuds när två eller flera virtuella datorer distribueras över två eller fler Tillgänglighetszoner inom en Azure-region. Det kommer att finnas ytterligare avgifter för VM-till-VM-dataöverföringar mellan olika tillgänglighets zoner. Mer information finns på [prissättnings](https://azure.microsoft.com/pricing/details/bandwidth/) sidan för bandbredd.


## <a name="get-started-with-availability-zones"></a>Kom igång med Tillgänglighetszoner
- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägga till en hanterad disk med PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa en virtuell dators skalnings uppsättning för redundant virtuell dator](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Belastningsutjämna virtuella datorer över zoner med hjälp av en Standard Load Balancer med en zon redundant klient del](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Belastningsutjämna virtuella datorer inom en zon med hjälp av en Standard Load Balancer med en zonindelade-frontend](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zonredundant lagring](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Lägg till redundant region i zonen för Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Komma igång Azure cache för Redis Tillgänglighetszoner](https://aka.ms/redis/az/getstarted)
- [Skapa en Azure Active Directory Domain Services-instans](../active-directory-domain-services/tutorial-create-instance.md)

## <a name="next-steps"></a>Nästa steg
- [Snabbstartsmallar](https://aka.ms/azqs)
