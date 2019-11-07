---
title: Översikt över Azure Service-Taggar
titlesuffix: Azure Virtual Network
description: Lär dig om service märken. Service märken hjälper till att minimera komplexiteten vid skapande av säkerhets regler.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 0eb15d1b9b56522b9caa1bb10890eb2b485714e8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587529"
---
# <a name="virtual-network-service-tags"></a>Tjänst taggar för virtuellt nätverk 
<a name="network-service-tags"></a>

En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Det bidrar till att minimera komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler. Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) eller [Azure-brandväggen](https://docs.microsoft.com/azure/firewall/service-tags). Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. **API Management**) i lämpligt *käll* -eller *mål* fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. 

## <a name="available-service-tags"></a>Tillgängliga tjänst etiketter
Följande tabell innehåller alla tjänst etiketter som är tillgängliga för användning i regler för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Kolumnerna visar om taggen är:

- Lämplig för regler som täcker inkommande eller utgående trafik
- Stöd för [regional](https://azure.microsoft.com/regions) omfattning 
- Kan användas i [Azure brand Väggs](https://docs.microsoft.com/azure/firewall/service-tags) regler

Som standard återspeglar service märken intervallen för hela molnet.  Vissa service märken ger också mer detaljerad kontroll genom att begränsa motsvarande IP-intervall till en angiven region.  Till exempel när service tag- **lagringen** representerar Azure Storage för hela molnet, begränsas **lagrings** utrymmet till endast lagrings-IP-adressintervall från regionen Väst.  Beskrivningarna av varje service tag nedan visar om de stöder det regionala omfånget.  



| Tagga | Syfte | Kan använda inkommande eller utgående? | Kan regionala? | Kan använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Hanterings trafik för APIM-dedikerade distributioner. | Båda | Nej | Ja |
| **AppService**    | App Service tjänst. Den här taggen rekommenderas för utgående säkerhets regler till WebApp-frontend. | Utgående | Ja | Ja |
| **AppServiceManagement** | Hanterings trafik för App Service-miljön dedikerade distributioner. | Båda | Nej | Ja |
| **AzureActiveDirectory** | Azure Active Directory tjänst. | Utgående | Nej | Ja |
| **AzureActiveDirectoryDomainServices** | Hanterings trafik för Azure Active Directory Domain Services dedikerade distributioner. | Båda | Nej | Ja |
| **AzureBackup** |Azure Backup tjänst.<br/><br/>*Obs:* Den här taggen har ett beroende på **lagrings** -och **AzureActiveDirectory** -taggarna. | Utgående | Nej | Ja |
| **AzureCloud** | Alla [offentliga IP-adresser för data Center](https://www.microsoft.com/download/details.aspx?id=41653). | Utgående | Ja | Ja |
| **AzureConnectors** | Logic Apps anslutningar för avsöknings-/backend-anslutningar. | Inkommande | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry tjänst. | Utgående | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos Database-tjänst. | Utgående | Ja | Ja |
| **AzureDataLake** | Azure Data Lake tjänst. | Utgående | Nej | Ja |
| **AzureKeyVault** | Azure-tjänsten för nyckel valv.<br/><br/>*Obs:* Den här taggen har ett beroende av **AzureActiveDirectory** -taggen. | Utgående | Ja | Ja |
| **AzureLoadBalancer** | Azures belastnings utjämning för infrastruktur. Taggen översätts till [värdens virtuella IP-adress](security-overview.md#azure-platform-considerations) (168.63.129.16) som Azures hälsoavsökningar kommer från. Du kan åsidosätta den här regeln om du inte använder Azures lastbalanserare. | Båda | Nej | Nej |
| **AzureMachineLearning** | Azure Machine Learning tjänst. | Utgående | Nej | Ja |
| **AzureMonitor** | Log Analytics, App Insights, AzMon och anpassade mått (GB-slutpunkter).<br/><br/>*Obs:* För Log Analytics är den här taggen beroende av **lagrings** tag gen. | Utgående | Nej | Ja |
| **AzurePlatformDNS** | Standard-DNS-tjänsten (Basic Infrastructure).<br/><br>Du kan använda den här taggen för att inaktivera standard-DNS. Var försiktig med att använda den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Testning rekommenderas innan du använder den här taggen. | Utgående | Nej | Nej |
| **AzurePlatformIMDS** | IMDS, som är en grundläggande infrastruktur tjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standard IMDS.  Var försiktig med att använda den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Testning rekommenderas innan du använder den här taggen. | Utgående | Nej | Nej |
| **AzurePlatformLKM** | Windows-licensiering eller nyckel hanterings tjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standardvärdena för licensiering. Var försiktig med att använda den här taggen.  Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Testning rekommenderas innan du använder den här taggen. | Utgående | Nej | Nej |
| **AzureTrafficManaged** | IP-adresser för Azure Traffic Manager-avsökning.<br/><br/>Mer information om IP-adresser för avsökning i Traffic Manager finns i [Vanliga frågor och svar om Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Inkommande | Nej | Ja |  
| **BatchNodeManagement** | Hanterings trafik för Azure Batch dedikerade distributioner. | Båda | Nej | Ja |
| **CognitiveServicesManagement** | Adress intervall för trafik för Cognitive Services | Utgående | Nej | Nej |
| **Dynamics365ForMarketingEmail** | Adress intervallen för Marketing e-posttjänsten för Dynamics 365. | Utgående | Ja | Nej |
| **EventHub** | Azure EventHub-tjänst. | Utgående | Ja | Ja |
| **GatewayManager** | Hanterings trafik för VPN/app Gateway-dedikerade distributioner. | Inkommande | Nej | Nej |
| **E** | IP-adressutrymmet som ligger utanför det virtuella nätverket och som kan användas av det offentliga Internet.<br/><br/>Adressintervallet omfattar det [offentliga IP-adressutrymmet som ägs av Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Båda | Nej | Nej |
| **MicrosoftContainerRegistry** | Microsoft Container Registry-tjänsten. | Utgående | Ja | Ja |
| **Service Bus** | Azure Service Bus tjänsten med Premium-tjänstens nivå. | Utgående | Ja | Ja |
| **ServiceFabric** | Service Fabric tjänst. | Utgående | Nej | Nej |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL och Azure SQL Data Warehouse-tjänster.<br/><br/>*Obs:* Den här taggen representerar tjänsten, men inte vissa instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure SQL Database, men inte en specifik SQL-databas eller -server. | Utgående | Ja | Ja |
| **SqlManagement** | Hanterings trafik för dedikerade SQL-distributioner. | Båda | Nej | Ja |
| **Storage** | Azure Storage tjänst. <br/><br/>*Obs:* Taggen representerar tjänsten, men inte vissa instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure Storage, men inte ett specifikt Azure Storage-konto. | Utgående | Ja | Ja |
| **VirtualNetwork** | Det virtuella nätverkets adress utrymme (alla IP-adressintervall som definierats för det virtuella nätverket), alla anslutna lokala adress utrymmen, [peer](virtual-network-peering-overview.md) -kopplade virtuella nätverk eller virtuella nätverk som är anslutna till en [virtuell](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)nätverksgateway, den [virtuella IP-adressen adressen till värd](security-overview.md#azure-platform-considerations) -och adressprefix som används på [användardefinierade vägar](virtual-networks-udr-overview.md). Tänk på att den här taggen även kan innehålla standard vägar. | Båda | Nej | Nej |

>[!NOTE]
>När du arbetar i den *klassiska* (före Azure Resource Manager) miljön stöds en SELECT-uppsättning av ovanstående taggar.  Dessa använder en alternativ stavning:

| Klassisk stavning | Motsvarande Resource Manager-tagg |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Servicetaggar för Azure-tjänster anger adressprefix från det specifika molnet som används. t. ex. de underliggande IP-intervall som motsvarar **SQL** -taggnamnet varierar mellan det offentliga Azure-molnet och Azure Kina-molnet.

> [!NOTE]
> Om du implementerar en [tjänstslutpunkt för virtuellt nätverk](virtual-network-service-endpoints-overview.md) för en viss tjänst, till exempel Azure Storage eller Azure SQL Database, lägger Azure till en [väg](virtual-networks-udr-overview.md#optional-default-routes) till ett undernät för virtuella nätverk för tjänsten. Vägens adressprefix är samma adressprefix, eller CIDR-intervall, som motsvarande tjänsttagg.



## <a name="service-tags-in-on-premises"></a>Service märken i lokalt  
Du kan hämta aktuell service tag-information och intervall information som ska ingå som en del av dina lokala brand Väggs konfigurationer.  Den här informationen är den aktuella tidpunkts listan för de IP-intervall som motsvarar varje service tag.  Informationen kan hämtas via programmering eller via JSON-fil hämtning på följande sätt.

### <a name="service-tag-discovery-api-public-preview"></a>API för identifiering av service tag (offentlig för hands version)
Du kan hämta den aktuella listan över tjänst etiketter med information om IP-adressintervall program mässigt:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> I den offentliga för hands versionen kan identifierings-API: t returnera information som inte är lika aktuell som JSON-nedladdningar (nedan).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Identifiera service märken med nedladdnings bara JSON-filer 
Du kan ladda ned JSON-filer som innehåller den aktuella listan över tjänst Taggar med information om IP-adressintervall. Dessa uppdateras och publiceras varje vecka.  Platser för varje moln är:

- [Offentlig Azure-](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>En delmängd av den här informationen har tidigare publicerats i XML-filer för [Azure offentlig](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Kina](https://www.microsoft.com/download/details.aspx?id=42064) och [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). Dessa XML-nedladdningar kommer att bli föråldrade den 30 juni 2020 och kommer inte längre att vara tillgängliga efter det datumet. Migrera till med hjälp av identifierings-API: t eller JSON-filens nedladdningar enligt beskrivningen ovan.

### <a name="tips"></a>Tips 
- Du kan identifiera uppdateringar från en publicering till nästa via ökade *changeNumber* -värden i JSON-filen. Varje underavsnitt (t. ex.- **lagring. väst**) har en egen *changeNumber* som ökar när ändringar sker.  Den översta nivån i filens *changeNumber* ökar när något av underavsnitten har ändrats.
- Exempel på hur du kan parsa service tag-informationen (t. ex. Hämta alla adress intervall för lagring i väster) finns i dokumentationen för [service tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Nästa steg
- Läs om hur du [skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md).

