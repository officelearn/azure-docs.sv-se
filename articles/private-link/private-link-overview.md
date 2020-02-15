---
title: Vad är Azure Privat Link?
description: Lär dig hur du använder Azures privata länk för att få åtkomst till Azure PaaS Services (till exempel Azure Storage och SQL Database) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 88e3077fb64446f5fa70c1785ad3df1d3746de33
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209332"
---
# <a name="what-is-azure-private-link"></a>Vad är Azure Privat Link? 
Med Azures privata länk kan du komma åt Azure PaaS Services (till exempel Azure Storage, Azure Cosmos DB och SQL Database) och Azure-värdbaserade kund-/partner tjänster via en [privat slut punkt](private-endpoint-overview.md) i det virtuella nätverket. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan också skapa en egen [privat länk-tjänst](private-link-service-overview.md) i ditt virtuella nätverk (VNet) och leverera den privat för kunderna. Installations-och användnings upplevelsen med Azure Private Link är konsekvent i Azure PaaS, kundägda och delade partner tjänster.

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Information om kända begränsningar finns i [privat slut punkt](private-endpoint-overview.md#limitations) och [privat länk tjänst](private-link-service-overview.md#limitations).


![Översikt över privat slut punkt](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Viktiga fördelar
Azure Private-länken ger följande fördelar:  
- **Privat åtkomst till tjänster på Azure-plattformen**: Anslut ditt virtuella nätverk till tjänster som körs i Azure privat utan att behöva en offentlig IP-adress på källan eller målet. Tjänste leverantörer kan återge sina tjänster privat i sitt eget virtuella nätverk och konsumenter kan komma åt tjänsterna privat i det lokala virtuella nätverket. Den privata länk plattformen hanterar anslutningen mellan konsumenter och tjänster över Azures stamnät nätverk. 
 
- **Lokala och peer-baserade nätverk**: åtkomst tjänster som körs i Azure från lokala ExpressRoute privata peering/VPN-tunnlar (lokalt) och peer-baserade virtuella nätverk med privata slut punkter. Du behöver inte konfigurera offentlig peering eller gå över på Internet för att komma åt tjänsten. Den här möjligheten ger ett säkert sätt att migrera arbets belastningar till Azure.
 
- **Skydd mot data exfiltrering**: med Azures privata länk mappas den privata slut punkten i VNet till en specifik instans av kundens PaaS-resurs i stället för hela tjänsten. Användning av privat slut punkts konsumenter kan bara ansluta till den angivna resursen och inte till någon annan resurs i tjänsten. Detta i den inbyggda mekanismen ger skydd mot data exfiltrering-risker. 
 
- **Global räckvidd**: Anslut privat till tjänster som körs i andra regioner. Det innebär att användarens virtuella nätverk kan finnas i region A och det kan ansluta till tjänster bakom privat länk i region B.  
 
- **Utöka till dina egna tjänster**: utnyttja samma erfarenheter och funktionalitet för att återge din egen tjänst privat för dina konsumenter i Azure. Genom att placera tjänsten bakom en Standard Load Balancer kan du aktivera den för privat länk. Konsumenten kan sedan ansluta direkt till tjänsten med hjälp av en privat slut punkt i sitt eget VNet. Du kan hantera dessa anslutnings begär Anden med ett enkelt godkännande flöde. Azures privata länk fungerar även för konsumenter och tjänster som tillhör olika Active Directory klienter. 

## <a name="availability"></a>Tillgänglighet 
 I följande tabell visas de privata länk tjänsterna och de regioner där de är tillgängliga. 

|Scenario  |Tjänster som stöds  |Tillgängliga regioner | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Privat länk för kundägda tjänster|Privata länk tjänster bakom Standard Load Balancer | Alla offentliga regioner  | Förhandsversion  |
|Privat länk för Azure PaaS Services   | Azure Storage        |  Alla offentliga regioner      | Förhandsversion <br/> [Läs mer](/azure/storage/common/storage-private-endpoints).  |
|  | Azure Data Lake Storage Gen2        |  Alla offentliga regioner      | Förhandsversion <br/> [Läs mer](/azure/storage/common/storage-private-endpoints).  |
|  |  Azure SQL Database         | Alla offentliga regioner      |   Förhandsversion      |
||Azure SQL Data Warehouse| Alla offentliga regioner |Förhandsversion|
||Azure Cosmos DB| Västra centrala USA, västra USA, norra centrala USA |Förhandsversion|
|  |  Azure Database for PostgreSQL-enskild server         | Alla offentliga regioner      |   Förhandsversion      |
|  |  Azure Database for MySQL         | Alla offentliga regioner      |   Förhandsversion      |
|  |  Azure-databas för MariaDB         | Alla offentliga regioner      |   Förhandsversion      |
|  |  Azure Key Vault         | Alla offentliga regioner      |   Förhandsversion      |

Du hittar de senaste aviseringarna på [sidan för Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Den privata Azure-länken är integrerad med Azure Monitor som gör att du kan arkivera loggar till ett lagrings konto, strömma händelser till Händelsehubben eller skicka dem till Azure Monitor loggar. Du kan komma åt följande information på Azure Monitor: 
- **Privat slut punkt**: data som bearbetas av den privata slut punkten (in/ut)
 
- **Privat länk tjänst**:
    - Data som bearbetas av tjänsten för privat länk (IN/ut)
    - Tillgänglighet för NAT-Port  
 
## <a name="pricing"></a>Priser   
Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Vanliga frågor och svar  
Vanliga frågor och svar om [Azures privata länkar](private-link-faq.md).
 
## <a name="limits"></a>Begränsningar  
För gränser, se [gränser för privata Azure-länkar](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Nästa steg
- [Skapa en privat slut punkt för SQL Database servern med hjälp av portalen](create-private-endpoint-portal.md)
- [Skapa en privat slut punkt för SQL Database Server med PowerShell](create-private-endpoint-powershell.md)
- [Skapa en privat slut punkt för SQL Database Server med CLI](create-private-endpoint-cli.md)
- [Skapa en privat slut punkt för lagrings kontot med hjälp av portalen](create-private-endpoint-storage-portal.md)
- [Skapa en privat slut punkt för Azure Cosmos-konto med hjälp av portalen](../cosmos-db/how-to-configure-private-endpoints.md)
- [Skapa en egen privat länk-tjänst med hjälp av Azure PowerShell](create-private-link-service-powershell.md)


 
