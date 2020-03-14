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
ms.openlocfilehash: 21e35afc72aa9824baa1076abe0e767ce4935c28
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279707"
---
# <a name="virtual-network-service-tags"></a>Tjänst taggar för virtuellt nätverk
<a name="network-service-tags"></a>

En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler.

Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) eller [Azure-brandväggen](https://docs.microsoft.com/azure/firewall/service-tags). Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (till exempel **API Management**) i rätt *käll* eller *målets* fält för en regel, kan du tillåta eller neka trafiken för motsvarande tjänst.

Du kan använda service märken för att uppnå nätverks isolering och skydda dina Azure-resurser från det allmänna Internet samtidigt som du får åtkomst till Azure-tjänster som har offentliga slut punkter. Skapa regler för inkommande/utgående nätverks säkerhets grupper för att neka trafik till/från **Internet** och tillåta trafik till/från **AzureCloud** eller andra [tillgängliga Service märken](#available-service-tags) för vissa Azure-tjänster.

## <a name="available-service-tags"></a>Tillgängliga tjänst etiketter
Följande tabell innehåller alla tjänst taggar som är tillgängliga för användning i regler för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Kolumnerna visar om taggen:

- Är lämpligt för regler som avser inkommande eller utgående trafik.
- Stöder [regional](https://azure.microsoft.com/regions) omfattning.
- Kan användas i [Azure brand Väggs](https://docs.microsoft.com/azure/firewall/service-tags) regler.

Som standard återspeglar service märken intervallen för hela molnet. Vissa service märken ger också mer detaljerad kontroll genom att begränsa motsvarande IP-intervall till en angiven region. Till exempel representerar service tag- **lagringen** Azure Storage för hela molnet, men **Storage. väst** begränsar intervallet till endast lagrings-IP-adressintervall från regionen Väst. Följande tabell visar om varje service tag stöder det regionala omfånget.  

| Tagga | Syfte | Kan använda inkommande eller utgående? | Kan regionala? | Kan använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Hanterings trafik för Azure API Management dedikerade distributioner. <br/><br/>*Obs:* Den här taggen representerar Azure API Management-tjänstens slut punkt för kontroll planet per region. Detta gör det möjligt för kunderna att utföra hanterings åtgärder för API: er, åtgärder, principer, NamedValues som kon figurer ATS på API Management tjänsten.  | Inkommande | Ja | Ja |
| **ApplicationInsightsAvailability** | Application Insights tillgänglighet. | Inkommande | Nej | Nej |
| **AppService**    | Azure App Service. Den här taggen rekommenderas för utgående säkerhets regler till webbappens frontend-sidor. | Utgående | Ja | Ja |
| **AppServiceManagement** | Hanterings trafik för distributioner avsedda för App Service-miljön. | Båda | Nej | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Utgående | Nej | Ja |
| **AzureActiveDirectoryDomainServices** | Hanterings trafik för distributioner avsedda för Azure Active Directory Domain Services. | Båda | Nej | Ja |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Utgående | Nej | Nej |
| **AzureBackup** |Azure Backup.<br/><br/>*Obs:* Den här taggen har ett beroende på **lagrings** -och **AzureActiveDirectory** -taggarna. | Utgående | Nej | Ja |
| **AzureBotService** | Azure Bot Service. | Utgående | Nej | Nej |
| **AzureCloud** | Alla [offentliga IP-adresser för data Center](https://www.microsoft.com/download/details.aspx?id=56519). | Utgående | Ja | Ja |
| **AzureCognitiveSearch** | Azure-Kognitiv sökning. <br/><br/>Den här taggen eller de IP-adresser som omfattas av den här taggen kan användas för att ge indexerare säker åtkomst till data källor. Mer information finns i [anslutnings dokumentationen för indexeraren](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) . <br/><br/> *Obs!* IP-adressen för Sök tjänsten ingår inte i listan över IP-intervall för den här tjänst tag gen och **måste också läggas** till i IP-brandväggen för data källor. | Inkommande | Nej | Nej |
| **AzureConnectors** | Azure Logic Apps anslutningar för avsökning/backend-anslutningar. | Inkommande | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry. | Utgående | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Utgående | Ja | Ja |
| **AzureDatabricks** | Azure Databricks. | Båda | Nej | Nej |
| **AzureDataExplorerManagement** | Hantering av Azure-Datautforskaren. | Inkommande | Nej | Nej |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Utgående | Nej | Ja |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*Obs:* Den här taggen täcker Azure Event Grid slut punkter i södra centrala USA, östra USA, östra USA 2, västra USA 2 och endast USA, Central. | Båda | Nej | Nej |
| **AzureFrontDoor** | Azure-front dörr. | Båda | Nej | Nej |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Obs:* Den här taggen har ett beroende för **AzureActiveDirectory** -och **AzureFrontDoor. frontend** -taggarna. Du kan också vitlista följande IP-adresser (detta beroende kommer snart): 13.107.6.181 & 13.107.9.181. | Utgående | Nej | Nej |
| **AzureIoTHub** | Azure-IoT Hub. | Utgående | Nej | Nej |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Obs:* Den här taggen har ett beroende av **AzureActiveDirectory** -taggen. | Utgående | Ja | Ja |
| **AzureLoadBalancer** | Azure Infrastructure belastningsutjämnare. Taggen översätts till den [virtuella IP-adressen för värden](security-overview.md#azure-platform-considerations) (168.63.129.16) där Azures hälso avsökningen kommer. Detta omfattar inte trafik till din Azure Load Balancer-resurs. Om du inte använder Azure Load Balancer kan du åsidosätta den här regeln. | Båda | Nej | Nej |
| **AzureMachineLearning** | Azure Machine Learning. | Båda | Nej | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon och anpassade mått (GB-slutpunkter).<br/><br/>*Obs:* För Log Analytics har den här taggen ett beroende på **lagrings** tag gen. | Utgående | Nej | Ja |
| **AzurePlatformDNS** | Standard-DNS-tjänsten (Basic Infrastructure).<br/><br>Du kan använda den här taggen för att inaktivera standard-DNS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Vi rekommenderar också att du utför testningen innan du använder den här taggen. | Utgående | Nej | Nej |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), som är en grundläggande infrastruktur tjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standard IMDS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Vi rekommenderar också att du utför testningen innan du använder den här taggen. | Utgående | Nej | Nej |
| **AzurePlatformLKM** | Windows-licensiering eller nyckel hanterings tjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standardvärdena för licensiering. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Vi rekommenderar också att du utför testningen innan du använder den här taggen. | Utgående | Nej | Nej |
| **AzureResourceManager** | Azure Resource Manager. | Utgående | Nej | Nej |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Obs:* Den här taggen har ett beroende på taggarna **Storage**, **AzureActiveDirectory**och **EventHub** . | Utgående | Nej | Nej |
| **AzureTrafficManager** | IP-adresser för Azure Traffic Manager-avsökning.<br/><br/>Mer information om Traffic Manager avsöknings-IP-adresser finns i [vanliga frågor och svar om Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Inkommande | Nej | Ja |  
| **BatchNodeManagement** | Hanterings trafik för distributioner avsedda för Azure Batch. | Båda | Nej | Ja |
| **CognitiveServicesManagement** | Adress intervall för trafik för Azure-Cognitive Services. | Utgående | Nej | Nej |
| **Dynamics365ForMarketingEmail** | Adress intervallen för Marketing e-posttjänsten för Dynamics 365. | Utgående | Ja | Nej |
| **ElasticAFD** | Elastisk Azure-front dörr. | Båda | Nej | Nej |
| **EventHub** | Azure-Event Hubs. | Utgående | Ja | Ja |
| **GatewayManager** | Hanterings trafik för distributioner avsedda för Azure VPN Gateway och Application Gateway. | Inkommande | Nej | Nej |
| **GuestAndHybridManagement** | Azure Automation-och gäst konfiguration. | Utgående | Nej | Ja |
| **HDInsight** | Azure HDInsight. | Inkommande | Ja | Nej |
| **E** | IP-adressutrymmet som ligger utanför det virtuella nätverket och som kan användas av det offentliga Internet.<br/><br/>Adress intervallet omfattar det [offentliga IP-adressutrymmet som ägs av Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Båda | Nej | Nej |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Utgående | Nej | Nej |
| **MicrosoftContainerRegistry** | Container Registry för Microsoft container-avbildningar. <br/><br/>*Obs:* Vitlista följande IP-adress (det här beroendet kommer snart att tas bort): 204.79.197.219. | Utgående | Ja | Ja |
| **Service Bus** | Azure Service Bus trafik som använder Premium-tjänstens nivå. | Utgående | Ja | Ja |
| **ServiceFabric** | Azure-Service Fabric.<br/><br/>*Obs:* Den här taggen representerar Service Fabric tjänstens slut punkt för kontroll planet per region. Detta gör det möjligt för kunderna att utföra hanterings åtgärder för sina Service Fabric-kluster från sitt VNET (t. ex. slut punkt. https://westus.servicefabric.azure.com) | Båda | Nej | Nej |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL och Azure SQL Data Warehouse.<br/><br/>*Obs:* Den här taggen representerar tjänsten, men inte vissa instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure SQL Database, men inte en specifik SQL-databas eller -server. Den här taggen gäller inte för SQL-hanterad instans. | Utgående | Ja | Ja |
| **SqlManagement** | Hanterings trafik för SQL-dedikerade distributioner. | Båda | Nej | Ja |
| **Storage** | Azure Storage. <br/><br/>*Obs:* Den här taggen representerar tjänsten, men inte vissa instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure Storage, men inte ett specifikt Azure Storage-konto. | Utgående | Ja | Ja |
| **VirtualNetwork** | Det virtuella nätverkets adress utrymme (alla IP-adressintervall som definierats för det virtuella nätverket), alla anslutna lokala adress utrymmen, [peer](virtual-network-peering-overview.md) -kopplade virtuella nätverk, virtuella nätverk som är anslutna till en [virtuell nätverksgateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), den [virtuella IP-adressen för värden](security-overview.md#azure-platform-considerations)och de adressprefix som används för [användardefinierade vägar](virtual-networks-udr-overview.md). Den här taggen kan också innehålla standard vägar. | Båda | Nej | Nej |

>[!NOTE]
>I den klassiska distributions modellen (före Azure Resource Manager) stöds en delmängd av de taggar som anges i föregående tabell. Dessa taggar har stavats på olika sätt:
>
>| Klassisk stavning | Motsvarande Resource Manager-tagg |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Service taggar för Azure-tjänster anger de adressprefix från det angivna molnet som används. Till exempel kommer de underliggande IP-intervall som motsvarar **SQL** -taggnamnet i det offentliga Azure-molnet att skilja sig från de underliggande områdena i Azure Kina-molnet.

> [!NOTE]
> Om du implementerar en [tjänstslutpunkt för virtuellt nätverk](virtual-network-service-endpoints-overview.md) för en viss tjänst, till exempel Azure Storage eller Azure SQL Database, lägger Azure till en [väg](virtual-networks-udr-overview.md#optional-default-routes) till ett undernät för virtuella nätverk för tjänsten. Adressprefix i vägen är samma adressprefix eller CIDR-intervall, som de för motsvarande service tag.

## <a name="service-tags-on-premises"></a>Lokala tjänst Taggar  
Du kan hämta aktuell service tag-information och intervall information som ska ingå som en del av dina lokala brand Väggs konfigurationer. Den här informationen är den aktuella tidpunkts listan för de IP-intervall som motsvarar varje service tag. Du kan hämta informationen via programmering eller via en nedladdning av en JSON-fil, enligt beskrivningen i följande avsnitt.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Använd API för identifiering av service tag (offentlig för hands version)
Du kan hämta den aktuella listan över service märken program mässigt tillsammans med information om IP-adressintervall:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Även om det finns i en offentlig för hands version kan identifierings-API: t returnera information som är mindre aktuell än information som returneras av JSON-nedladdningar. (Se nästa avsnitt.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Identifiera service märken med hjälp av nedladdnings bara JSON-filer 
Du kan ladda ned JSON-filer som innehåller den aktuella listan över tjänst Taggar tillsammans med information om IP-adressintervall. Listorna uppdateras och publiceras varje vecka. Platser för varje moln är:

- [Offentlig Azure-](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure för amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>En delmängd av den här informationen har publicerats i XML-filer för [Azure offentlig](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Kina](https://www.microsoft.com/download/details.aspx?id=42064)och [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). Dessa XML-nedladdningar kommer att bli föråldrade den 30 juni 2020 och kommer inte längre att vara tillgängliga efter det datumet. Du bör migrera till med hjälp av identifierings-API: t eller JSON-filens nedladdningar enligt beskrivningen i föregående avsnitt.

### <a name="tips"></a>Tips 
- Du kan identifiera uppdateringar från en publikation till nästa genom att notera ökade *changeNumber* -värden i JSON-filen. Varje underavsnitt (till exempel **lagring. väst**) har en egen *changeNumber* som ökar när ändringar sker. Den översta nivån i filens *changeNumber* ökar när något av underavsnitten ändras.
- Exempel på hur du kan parsa service tag-informationen (till exempel hämta alla adress intervall för lagring i väster) finns i dokumentationen för [service tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [skapar en nätverks säkerhets grupp](tutorial-filter-network-traffic.md).
