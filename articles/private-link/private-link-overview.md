---
title: Vad är Azure Privat Link?
description: Översikt över Azure Private Link-funktioner, arkitektur och implementering. Lär dig hur Azure Private Endpoints och Azure Private Link-tjänsten fungerar och hur du använder dem.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 3218a2de890124915d62afa86fd5f3dca1e72b2e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80063085"
---
# <a name="what-is-azure-private-link"></a>Vad är Azure Privat Link? 
Med Azure Private Link kan du komma åt Azure PaaS Services (till exempel Azure Storage och SQL Database) och Azure-värd för kundägda/partnertjänster via en [privat slutpunkt](private-endpoint-overview.md) i ditt virtuella nätverk.

Trafiken mellan det virtuella nätverket och tjänsten färdas i Microsofts stamnätsnätverk. Att exponera din tjänst för det offentliga internet är inte längre nödvändigt. Du kan skapa din egen [privata länktjänst](private-link-service-overview.md) i ditt virtuella nätverk och leverera den till dina kunder. Installation och förbrukning med Azure Private Link är konsekvent i Azure PaaS, kundägda och delade partnertjänster.

> [!IMPORTANT]
> Azure Private Link är nu allmänt tillgänglig. Både Private Endpoint och Private Link-tjänsten (service bakom standardbelastningsutjämnare) är allmänt tillgängliga. Olika Azure PaaS kommer ombord till Azure Private Link på olika scheman. Kontrollera [availability](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) tillgänglighetsavsnittet nedan för korrekt status för Azure PaaS på Private Link. Kända begränsningar finns i [Private Endpoint](private-endpoint-overview.md#limitations) och [Private Link Service](private-link-service-overview.md#limitations). 

![Översikt över privata slutpunkter](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Viktiga fördelar
Azure Private Link ger följande fördelar:  
- **Privat åtkomst till tjänster på Azure-plattformen:** Anslut ditt virtuella nätverk till tjänster i Azure utan en offentlig IP-adress vid källan eller målet. Tjänsteleverantörer kan utföra sina tjänster i sitt eget virtuella nätverk och konsumenterna kan få tillgång till dessa tjänster i sitt lokala virtuella nätverk. Private Link-plattformen hanterar anslutningen mellan konsumenten och tjänsterna via Azure-stamnätet. 
 
- **Lokala och peer-nätverk:** Åtkomsttjänster som körs i Azure från lokala över ExpressRoute privat peering, VPN-tunnlar och peered virtuella nätverk med hjälp av privata slutpunkter. Det finns ingen anledning att ställa in offentliga peering eller korsa Internet för att nå tjänsten. Private Link är ett säkert sätt att migrera arbetsbelastningar till Azure.
 
- **Skydd mot dataläckage**: En privat slutpunkt mappas till en instans av en PaaS-resurs i stället för hela tjänsten. Konsumenterna kan bara ansluta till den specifika resursen. Åtkomsten till andra resurser i tjänsten är blockerad. Denna mekanism ger skydd mot dataläckage risker. 
 
- **Global räckvidd**: Anslut privat till tjänster som körs i andra regioner. Konsumentens virtuella nätverk kan vara i region A och det kan ansluta till tjänster bakom Private Link i region B.  
 
- **Utöka till dina egna tjänster:** Aktivera samma upplevelse och funktionalitet för att göra din tjänst privat till konsumenter i Azure. Genom att placera din tjänst bakom en standard Azure Load Balancer kan du aktivera den för Private Link. Konsumenten kan sedan ansluta direkt till din tjänst med hjälp av en privat slutpunkt i sitt eget virtuella nätverk. Du kan hantera anslutningsbegäranden med hjälp av ett godkännandeanropsflöde. Azure Private Link fungerar för konsumenter och tjänster som tillhör olika Azure Active Directory-klienter. 

## <a name="availability"></a>Tillgänglighet 
 I följande tabell visas de privata länktjänsterna och de regioner där de är tillgängliga. 

|Scenario  |Tjänster som stöds  |Tillgängliga regioner | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Privat länk för kundägda tjänster |Privata länktjänster bakom standard Azure Load Balancer | Alla offentliga regioner  | Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Privat länk för Azure PaaS-tjänster   | Azure Storage        |  Alla offentliga regioner      | Allmän tillgänglighet (GA) <br/> [Läs mer](/azure/storage/common/storage-private-endpoints)  |
|  | Azure Data Lake Storage Gen2        |  Alla offentliga regioner      | Allmän tillgänglighet (GA) <br/> [Läs mer](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|  |Azure Synapse Analytics (SQL Data Warehouse)| Alla offentliga regioner |Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|  |Azure Cosmos DB|  ÖSTRA USA, ÖSTRA USA2, VÄSTRA USA, VÄSTRA USA2, CENTRALA USA, SÖDRA CENTRALA USA, VÄSTRA CENTRALA USA, NORRA CENTRALA USA, NORDEUROPA, VÄSTEUROPA  |Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Azure-databas för PostgreSQL - Enkel server         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure-databas för MariaDB         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Alla offentliga regioner      |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|  |Azure Kubernetes-tjänst - Kubernetes API | Alla offentliga regioner      |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/aks/private-clusters)   |
|  |Azure Search | ÖSTRA USA, VÄSTRA US2, SÖDRA CENTRALA USA |   Förhandsversion    |
|  |Azure Container Registry | Alla offentliga regioner      |   Förhandsversion   |
|  |Azure App Configuration | Alla offentliga regioner      |   Förhandsversion   |
|  |Azure Backup | ÖSTRA USA, VÄSTRA US2, SÖDRA CENTRALA USA     |   Förhandsversion   |
|  |Azure Event Hub | Alla offentliga regioner      |   Förhandsversion    |
|  |Azure Service Bus | Alla offentliga regioner      |   Förhandsversion   |
|  |Azure Relay | Alla offentliga regioner      |   Förhandsversion   |
|  |Azure Event Grid| Alla offentliga regioner      |   Förhandsversion   <br/> [Läs mer](https://docs.microsoft.com/azure/event-grid/network-security)   |
|  |Azure Web Apps | ÖSTRA USA, VÄSTRA US2, SÖDRA CENTRALA USA      |   Förhandsversion   <br/> [Läs mer](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |

De senaste meddelandena finns på [sidan Uppdateringar av Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Azure Private Link har integrering med Azure Monitor. Denna kombination gör det möjligt:

 - Arkivering av loggar till ett lagringskonto.
 - Streaming av händelser till din eventhubb.
 - Azure Monitor-loggning.

Du kan komma åt följande information på Azure Monitor: 
- **Privat slutpunkt:** 
    - Data som bearbetas av den privata slutpunkten (IN/OUT)
 
- **Privat länk tjänst:**
    - Data som behandlas av tjänsten Private Link (IN/OUT)
    - TILLGÄNGLIGHET för NAT-port  
 
## <a name="pricing"></a>Prissättning   
Information om priser finns i [Azure Private Link-priser](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Vanliga frågor och svar  
Vanliga frågor och andra frågor finns i [vanliga frågor och frågor om Azure Private Link](private-link-faq.md).
 
## <a name="limits"></a>Begränsningar  
För begränsningar finns i [Azure Private Link-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Avtal om servicenivå
För SLA finns i [SLA för Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Skapa en privat slutpunkt med Azure-portal](create-private-endpoint-portal.md)
- [Snabbstart: Skapa en privat länktjänst med hjälp av Azure-portalen](create-private-link-service-portal.md)


 
