---
title: Vad är Azure-tillgänglighetszoner?
description: För att skapa högtillgängliga och elastiska program i Azure tillhandahåller tillgänglighetszoner fysiskt separata platser som du kan använda för att köra dina resurser.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 9f1b0f1885019c75252a4c5b8333f342660fac0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057270"
---
# <a name="what-are-availability-zones-in-azure"></a>Vad är tillgänglighetszoner i Azure?
Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från datacenterfel. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zonuppsagda tjänster replikerar dina program och data över tillgänglighetszoner för att skydda mot enstaka felpunkter. Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

Skapa hög tillgänglighet i programarkitekturen genom att samlokalisera dina beräknings-, lagrings-, nätverks- och dataresurser inom en zon och replikera i andra zoner. Azure-tjänster som har stöd för tillgänglighetszoner är indelade i två kategorier:

- **Zontjänster** – du fäster resursen på en viss zon (till exempel virtuella datorer, hanterade diskar, standard-IP-adresser) eller
- **Zonredundanta tjänster** – plattformen replikeras automatiskt mellan zoner (till exempel zonredundant lagring, SQL-databas).

Skapa programarkitekturen med hjälp av kombinationen tillgänglighetszoner med Azure-regionpar för att uppnå omfattande affärskontinuitet på Azure. Du kan synkronisera replikera dina program och data med hjälp av tillgänglighetszoner i en Azure-region för hög tillgänglighet och asynkront replikera över Azure-regioner för skydd av katastrofåterställning.
 
![konceptuell bild av en zon som går ner i en region](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Identifierare för tillgänglighetszon (siffrorna 1, 2 och 3 i bilden ovan) mappas logiskt till de faktiska fysiska zonerna för varje prenumeration oberoende av varandra. Det innebär att tillgänglighetszon 1 i en viss prenumeration kan referera till en annan fysisk zon än tillgänglighetszon 1 i en annan prenumeration. Därför rekommenderas att du inte förlitar sig på tillgänglighetszon-ID:er för olika prenumerationer för placering av virtuella datorer.

## <a name="services-support-by-region"></a>Stöd till tjänster per region

Kombinationerna av Azure-tjänster och regioner som stöder tillgänglighetszoner är:


|                                 |Nord- och Sydamerika |              |           |           | Europa |              |          |              | Asien och stillahavsområdet |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |USA, centrala|USA, östra|USA, östra 2|USA, västra 2|Frankrike, centrala|Europa, norra|Storbritannien, södra|Europa, västra|Japan, östra|Sydostasien|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |
| Virtuella Linux-datorer          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtuella Windows-datorer        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Skalningsuppsättningar för Virtual Machines      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure App Service-miljöer ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Lagring**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zonupptant lagring          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Nätverk**                     |            |              |           |           |                |              |          |             |            |                |
| Standard-IP-adress        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standard belastningsutjämnare     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute-gateway   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Programgateway(V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Databaser**                     |            |              |           |           |                |              |          |             |            |                |
| Azure-datautforskaren                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003; (förhandsgranskning)      | &#10003;       |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analys**                       |            |              |           |           |                |              |          |             |            |                |
| Händelsehubbar                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integration**                     |            |              |           |           |                |              |          |             |            |                |
| Servicebuss (endast Premium-nivå) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identitet**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Tjänsternas återhämtningsförmåga
Alla Azure-hanteringstjänster är skapade för att vara motståndskraftiga mot fel på regionnivå. I spektrumet av fel har ett eller flera tillgänglighetszonfel i en region en mindre felradie jämfört med ett helt regionfel. Azure kan återställas från ett fel på zonnivå för hanteringstjänster inom regionen eller från en annan Azure-region. Azure utför kritiskt underhåll en zon i taget inom en region, för att förhindra eventuella fel som påverkar kundresurser som distribueras över tillgänglighetszoner inom en region.

## <a name="pricing"></a>Prissättning
Det finns ingen extra kostnad för virtuella datorer som distribueras i en tillgänglighetszon. 99,99% VM uptime SLA erbjuds när två eller flera virtuella datorer distribueras över två eller flera tillgänglighetszoner inom en Azure-region. Det kommer att finnas ytterligare avgifter för vm-till-VM-dataöverföring mellan tillgänglighetszoner. Mer information finns på [prissidan för bandbredd.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Komma igång med tillgänglighetszoner
- [Skapa en virtuell dator](../virtual-machines/windows/create-portal-availability-zone.md)
- [Lägga till en hanterad disk med PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Skapa en zon redundant skalningsuppsättning för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Virtuella datorer för belastningsutjämning mellan zoner med hjälp av en standardbelastningsutjämnare med en zon redundant frontend](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Virtuella datorer för belastningsutjämning inom en zon med hjälp av en standardbelastningsutdelningshanterare med en zonindel frontend](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zonredundant lagring](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geohaveriberedskap för Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geohaveriberedskap för Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Skapa en zonredundant virtuell nätverksgateway](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Lägga till zon redundant region för Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Komma igång Azure Cache för Redis tillgänglighetszoner](https://aka.ms/redis/az/getstarted)
- [Skapa en Azure Active Directory Domain Services-instans](../active-directory-domain-services/tutorial-create-instance.md)
- [Skapa ett AKS-kluster (Azure Kubernetes Service) som använder tillgänglighetszoner](../aks/availability-zones.md)

## <a name="next-steps"></a>Nästa steg
- [Snabbstartsmallar](https://aka.ms/azqs)
