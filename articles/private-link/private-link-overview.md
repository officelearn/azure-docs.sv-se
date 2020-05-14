---
title: Vad är Azure Privat Link?
description: Översikt över funktioner i Azure Private Link, arkitektur och implementering. Lär dig hur Azures privata slut punkter och Azure Private Link service fungerar och hur du använder dem.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 48ce72ee501252e35388f958b5c2e166edc2678f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196160"
---
# <a name="what-is-azure-private-link"></a>Vad är Azure Privat Link? 
Med Azures privata länk kan du få åtkomst till Azure PaaS-tjänster (till exempel Azure Storage och SQL Database) och Azure-värdbaserade/partner tjänster som ägs av en [privat slut punkt](private-endpoint-overview.md) i det virtuella nätverket.

Trafik mellan ditt virtuella nätverk och tjänsten flyttar Microsoft stamnät nätverket. Det är inte längre nödvändigt att exponera tjänsten för det offentliga Internet. Du kan skapa en egen [privat länk-tjänst](private-link-service-overview.md) i ditt virtuella nätverk och leverera den till dina kunder. Installation och användning med Azure Private Link är konsekvent i Azure PaaS, kundägda och delade partner tjänster.

> [!IMPORTANT]
> Azures privata länk är nu allmänt tillgänglig. Både privat och privat länk tjänst (tjänsten bakom en standard belastningsutjämnare) är allmänt tillgängliga. Olika Azure-PaaS kommer att publiceras på en privat Azure-länk vid olika scheman. Avsnittet om att kontrol lera [tillgänglighet](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) nedan för korrekt status för Azure-PaaS på en privat länk. Information om kända begränsningar finns i [privat slut punkt](private-endpoint-overview.md#limitations) och [privat länk tjänst](private-link-service-overview.md#limitations). 

![Översikt över privat slut punkt](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Viktiga fördelar
Azure Private-länken ger följande fördelar:  
- **Privat åtkomst till tjänster på Azure-plattformen**: Anslut ditt virtuella nätverk till tjänster i Azure utan en offentlig IP-adress på källan eller målet. Tjänste leverantörer kan återge sina tjänster i sitt eget virtuella nätverk och konsumenter kan komma åt dessa tjänster i sitt lokala virtuella nätverk. Den privata länk plattformen hanterar anslutningen mellan konsumenter och tjänster över Azures stamnät nätverk. 
 
- **Lokala och peer-baserade nätverk**: få åtkomst till tjänster som körs i Azure från lokala ExpressRoute privata peering, VPN-tunnlar och peer-baserade virtuella nätverk med privata slut punkter. Du behöver inte konfigurera en offentlig peering eller gå igenom Internet för att komma åt tjänsten. Privat länk är ett säkert sätt att migrera arbets belastningar till Azure.
 
- **Skydd mot data läckage**: en privat slut punkt mappas till en instans av en PaaS-resurs i stället för hela tjänsten. Konsumenter kan bara ansluta till den angivna resursen. Åtkomst till någon annan resurs i tjänsten blockeras. Den här mekanismen ger skydd mot data läcker risker. 
 
- **Global räckvidd**: Anslut privat till tjänster som körs i andra regioner. Användarens virtuella nätverk kan vara i region A och det kan ansluta till tjänster bakom privat länk i region B.  
 
- **Utöka till dina egna tjänster**: Aktivera samma upplevelse och funktionalitet för att återge din tjänst privat för konsumenter i Azure. Genom att placera tjänsten bakom en standard Azure Load Balancer kan du aktivera den för privat länk. Konsumenten kan sedan ansluta direkt till tjänsten med hjälp av en privat slut punkt i ett eget virtuellt nätverk. Du kan hantera anslutnings begär Anden med ett samtals flöde för godkännande. En privat Azure-länk fungerar för konsumenter och tjänster som tillhör olika Azure Active Directory klienter. 

## <a name="availability"></a>Tillgänglighet 
 I följande tabell visas de privata länk tjänsterna och de regioner där de är tillgängliga. 

|Tjänster som stöds  |Tillgängliga regioner | Status  |
|:-------------------|:-----------------|:--------|
|Privata länk tjänster bakom standard Azure Load Balancer | Alla offentliga regioner  | Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
| Azure Storage        |  Alla offentliga regioner       | Allmän tillgänglighet (GA) <br/> [Läs mer](/azure/storage/common/storage-private-endpoints)  |
| Azure Data Lake Storage Gen2        |  Alla offentliga regioner      | Allmän tillgänglighet (GA) <br/> [Läs mer](/azure/storage/common/storage-private-endpoints)  |
|  Azure SQL Database         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|Azure Synapse Analytics (SQL Data Warehouse)| Alla offentliga regioner |Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  Alla offentliga regioner |Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  Azure Database for PostgreSQL-enskild server         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  Azure Database for MySQL         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  Azure-databas för MariaDB         | Alla offentliga regioner      |   Allmän tillgänglighet (GA) <br/> [Läs mer](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | Alla offentliga regioner      |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Azure Kubernetes-tjänst – Kubernetes-API | Alla offentliga regioner      |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Azure Search | Alla offentliga regioner |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/search/search-security-overview#endpoint-access)    |
|Azure Container Registry | Alla offentliga regioner      |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Azure App Configuration | Alla offentliga regioner      |   Förhandsgranskning   |
|Azure Backup | Alla offentliga regioner     |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|Azure Event Hub | Alla offentliga regioner      |    Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Azure Service Bus | Alla offentliga regioner      |  Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Azure Relay | Alla offentliga regioner      |   Förhandsgranskning <br/> [Läs mer](https://docs.microsoft.com/azure/service-bus-relay/private-link-service)  |
|Azure Event Grid| Alla offentliga regioner       |   Allmän tillgänglighet (GA)   <br/> [Läs mer](https://docs.microsoft.com/azure/event-grid/network-security) |
|Azure Web Apps | ÖSTRA USA, VÄSTRA USA 2, SÖDRA CENTRALA USA      |   Förhandsgranskning   <br/> [Läs mer](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|Azure Machine Learning | ÖSTRA USA, VÄSTRA USA 2, SÖDRA CENTRALA USA      |   Förhandsgranskning   <br/> [Läs mer](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| IoT Hub | Alla offentliga regioner    |   Förhandsgranskning   <br/> [Läs mer](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | ÖSTRA USA, VÄSTRA USA 2, SÖDRA CENTRALA USA      |   Förhandsgranskning   <br/> [Läs mer](https://aka.ms/asrs/privatelink)   |

Du hittar de senaste aviseringarna på [sidan för Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Den privata Azure-länken har integrering med Azure Monitor. Med den här kombinationen kan du:

 - Arkivering av loggar till ett lagrings konto.
 - Strömning av händelser till Händelsehubben.
 - Azure Monitor loggning.

Du kan komma åt följande information på Azure Monitor: 
- **Privat slut punkt**: 
    - Data som bearbetas av den privata slut punkten (IN/ut)
 
- **Privat länk tjänst**:
    - Data som bearbetas av tjänsten för privat länk (IN/ut)
    - Tillgänglighet för NAT-Port  
 
## <a name="pricing"></a>Prissättning   
Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Vanliga frågor och svar  
Vanliga frågor och svar om [Azures privata länkar](private-link-faq.md).
 
## <a name="limits"></a>Begränsningar  
För gränser, se [gränser för privata Azure-länkar](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Serviceavtal
För SLA, se [SLA för Azures privata länk](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Nästa steg

- [Snabb start: skapa en privat slut punkt med hjälp av Azure Portal](create-private-endpoint-portal.md)
- [Snabb start: skapa en privat länk-tjänst med hjälp av Azure Portal](create-private-link-service-portal.md)


 
