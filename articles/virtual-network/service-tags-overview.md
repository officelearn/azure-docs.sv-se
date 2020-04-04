---
title: Översikt över Azure-tjänsttaggar
titlesuffix: Azure Virtual Network
description: Läs mer om tjänsttaggar. Tjänsttaggar hjälper till att minimera komplexiteten i skapandet av säkerhetsregeln.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 7da20a0bf87b33f05ea7f1d457157c5b7ee2ec7b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631520"
---
# <a name="virtual-network-service-tags"></a>Taggar för virtuella nätverkstjänster
<a name="network-service-tags"></a>

En tjänsttagg representerar en grupp IP-adressprefix från en viss Azure-tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras, vilket minimerar komplexiteten i frekventa uppdateringar av nätverkssäkerhetsregler.

Du kan använda tjänsttaggar för att definiera nätverksåtkomstkontroller i [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) eller [Azure-brandväggen](https://docs.microsoft.com/azure/firewall/service-tags). Använd tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (till exempel **ApiManagement**) i lämpligt *käll-* eller *målfält* för en regel kan du tillåta eller neka trafik för motsvarande tjänst.

Du kan använda tjänsttaggar för att uppnå nätverksisolering och skydda dina Azure-resurser från det allmänna Internet samtidigt som du använder Azure-tjänster som har offentliga slutpunkter. Skapa regler för inkommande/utgående nätverkssäkerhetsgrupp för att neka trafik till/från **Internet** och tillåta trafik till/från **AzureCloud** eller andra [tillgängliga tjänsttaggar](#available-service-tags) för specifika Azure-tjänster.

## <a name="available-service-tags"></a>Tillgängliga tjänsttaggar
Följande tabell innehåller alla tjänsttaggar som är tillgängliga för användning i [nätverkssäkerhetsgruppsregler.](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)

Kolumnerna anger om taggen:

- Är lämplig för regler som täcker inkommande eller utgående trafik.
- Stöder [regional](https://azure.microsoft.com/regions) räckvidd.
- Kan kan kanserbara i [Azure-brandväggsregler.](https://docs.microsoft.com/azure/firewall/service-tags)

Som standard återspeglar tjänsttaggar intervallen för hela molnet. Vissa tjänsttaggar tillåter också mer detaljerad kontroll genom att begränsa motsvarande IP-intervall till ett angivet område. Till exempel representerar servicetag **Storage** Azure Storage för hela molnet, men **Storage.WestUS** begränsar intervallet till endast lagrings-IP-adressen sträcker sig från WestUS-regionen. I följande tabell anges om varje servicetag stöder sådant regionalt scope.  

| Tagga | Syfte | Kan du använda inkommande eller utgående? | Kan vara regional? | Kan du använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Åtgärdsgrupp** | aktionsgruppen. | Inkommande | Inga | Inga |
| **ApiManagement (ApiHanagement)** | Hanteringstrafik för Azure API Management-dedikerade distributioner. <br/><br/>*Anm.:* Den här taggen representerar slutpunkten för Azure API Management-tjänsten för kontrollplan per region. Detta gör det möjligt för kunder att utföra hanteringsåtgärder på API:er, operationer, principer, namngivna värden som konfigurerats på API Management-tjänsten.  | Inkommande | Ja | Ja |
| **ApplicationInsightsTillgänglighet** | Tillgänglighet för programinsikter. | Inkommande | Inga | Inga |
| **AppKonfigurering** | App-konfiguration. | Utgående | Inga | Inga |
| **AppService**    | Azure App-tjänst. Den här taggen rekommenderas för utgående säkerhetsregler till webbappens klientslut. | Utgående | Ja | Ja |
| **AppServiceManagement** | Hanteringstrafik för distributioner som är avsedda för App Service Environment. | Båda | Inga | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Utgående | Inga | Ja |
| **AzureActiveDirectoryDomainServices** | Hanteringstrafik för distributioner som är dedikerade till Azure Active Directory Domain Services. | Båda | Inga | Ja |
| **AzureAdvancedThreatProtection** | Azure avancerat skydd mot hot. | Utgående | Inga | Inga |
| **AzureBackup** |Azure-säkerhetskopiering.<br/><br/>*Anm.:* Den här taggen har ett beroende av taggarna **Lagring** och **AzureActiveDirectory.** | Utgående | Inga | Ja |
| **AzureBotService** | Azure Bot-tjänst. | Utgående | Inga | Inga |
| **AzureCloud** | Alla [offentliga IP-adresser för datacenter](https://www.microsoft.com/download/details.aspx?id=56519). | Utgående | Ja | Ja |
| **AzureCognitiveSearch** | Azure kognitiv sökning. <br/><br/>Den här taggen eller IP-adresserna som omfattas av den här taggen kan användas för att ge indexerare säker åtkomst till datakällor. Mer information finns i dokumentationen för [indexerarens anslutning.](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) <br/><br/> *Obs:* IP för söktjänsten ingår inte i listan över IP-intervall för denna tjänst tag och **måste också läggas till** IP-brandväggen för datakällor. | Inkommande | Inga | Inga |
| **AzureConnectors** | Azure Logic Apps-kopplingar för avsöknings-/backend-anslutningar. | Inkommande | Ja | Ja |
| **AzureContainer-registret** | Azure-behållarregistret. | Utgående | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Utgående | Ja | Ja |
| **AzureDatabricks (** | Azure Databricks. | Båda | Inga | Inga |
| **AzureDataExplorerManagement** | Hantering av Azure Data Explorer. | Inkommande | Inga | Inga |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Utgående | Inga | Ja |
| **AzureDevSpaces** | Azure Dev Spaces. | Utgående | Inga | Inga |
| **AzureEventGrid** | Azure-händelserutnät. <br/><br/>*Anm.:* Den här taggen täcker azure Event Grid-slutpunkter endast i US South Central, US East, US East 2, US West 2 och US Central. | Båda | Inga | Inga |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azurblå ytterdörren. | Båda | Inga | Inga |
| **AzureInformationProtection** | Azure-informationsskydd.<br/><br/>*Anm.:* Den här taggen har ett beroende av taggarna **AzureActiveDirectory,** **AzureFrontDoor.Frontend** och **AzureFrontDoor.FirstParty.** | Utgående | Inga | Inga |
| **AzureIoTHub** | Azure IoT Hub. | Utgående | Inga | Inga |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Anm.:* Den här taggen har ett beroende av **Taggen AzureActiveDirectory.** | Utgående | Ja | Ja |
| **AzureLoadBalancer** | Azure-infrastrukturbelastningsutjämnaren. Taggen översätts till den [virtuella IP-adressen](security-overview.md#azure-platform-considerations) för värden (168.63.129.16) där Azure-hälsoavsökningarna kommer. Detta inkluderar inte trafik till din Azure Load Balancer-resurs. Om du inte använder Azure Load Balancer kan du åsidosätta den här regeln. | Båda | Inga | Inga |
| **AzureMachineLearning** | Azure Machine Learning. | Båda | Inga | Ja |
| **AzureMonitor (På andra)** | Log Analytics, Application Insights, AzMon och anpassade mått (GiG-slutpunkter).<br/><br/>*Anm.:* För Logganalys har den här taggen ett beroende av **lagringstaggen.** | Utgående | Inga | Ja |
| **AzureOpenDatasets** | Azure Öppna datauppsättningar.<br/><br/>*Anm.:* Den här taggen har ett beroende av **taggen AzureFrontDoor.Frontend** och **Storage.** | Utgående | Inga | Inga |
| **AzurePlatformDNS** | Den grundläggande DNS-tjänsten för infrastruktur (standard).<br/><br>Du kan använda den här taggen för att inaktivera standard-DNS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [Azure-plattformsöverväganden](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Vi rekommenderar också att du utför tester innan du använder den här taggen. | Utgående | Inga | Inga |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), som är en grundläggande infrastrukturtjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standard-IMDS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [Azure-plattformsöverväganden](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Vi rekommenderar också att du utför tester innan du använder den här taggen. | Utgående | Inga | Inga |
| **AzurePlatformLKM** | Windows-licensierings- eller nyckelhanteringstjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standardvärdena för licensiering. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [Azure-plattformsöverväganden](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Vi rekommenderar också att du utför tester innan du använder den här taggen. | Utgående | Inga | Inga |
| **AzureResourceManager** | Azure Resource Manager. | Utgående | Inga | Inga |
| **AzureSignalR (AzureSignalR)** | Azure SignalR. | Utgående | Inga | Inga |
| **AzureSiteRecovery** | Återställning av Azure-webbplatsen.<br/><br/>*Anm.:* Den här taggen har ett beroende av taggarna **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** och **Storage.** | Utgående | Inga | Inga |
| **AzureTrafficManager** | Azure Traffic Manager avsökning IP-adresser.<br/><br/>Mer information om Traffic Manager-avsöknings-IP-adresser finns i [vanliga frågor och svar om Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Inkommande | Inga | Ja |  
| **BatchNodeManagement** | Hanteringstrafik för distributioner som är dedikerade till Azure Batch. | Båda | Inga | Ja |
| **CognitiveServicesManagement** | Adressintervallen för trafik för Azure Cognitive Services. | Båda | Inga | Inga |
| **DataFactory (datafactory)**  | Azure Data Factory | Båda | Inga | Inga |
| **DataFactoryManagement** | Hanteringstrafik för Azure Data Factory. | Utgående | Inga | Inga |
| **Dynamics365FörmarketingEmail** | Adressintervallen för marknadsföringstjänsten för e-post i Dynamics 365. | Utgående | Ja | Inga |
| **ElasticAFD** | Elastisk Azurblå ytterdörr. | Båda | Inga | Inga |
| **EventHub** | Azure-händelsehubbar. | Utgående | Ja | Ja |
| **GatewayManager (GatewayManager)** | Hanteringstrafik för distributioner som är dedikerade till Azure VPN Gateway och Application Gateway. | Inkommande | Inga | Inga |
| **GästOchHybridManagement** | Azure Automation och gästkonfiguration. | Utgående | Inga | Ja |
| **HDInsight** | Azure HDInsight. | Inkommande | Ja | Inga |
| **Internet** | IP-adressutrymmet som ligger utanför det virtuella nätverket och kan nås av det offentliga internet.<br/><br/>Adressintervallet innehåller [det Azure-ägda offentliga IP-adressutrymmet](https://www.microsoft.com/download/details.aspx?id=41653). | Båda | Inga | Inga |
| **LogicApps** | Logikappar. | Båda | Inga | Inga |
| **LogicAppsManagement** | Hanteringstrafik för Logic Apps. | Inkommande | Inga | Inga |
| **MicrosoftCloudAppSäkerhet** | Microsoft Cloud App Säkerhet. | Utgående | Inga | Inga |
| **MicrosoftContainer-registret** | Behållarregistret för Microsoft-behållaravbildningar. <br/><br/>*Anm.:* Den här taggen har ett beroende av taggen **AzureFrontDoor.FirstParty.** | Utgående | Ja | Ja |
| **PowerQueryOnline (på samma sätt som)** | Power Query Online. | Båda | Inga | Inga |
| **ServiceBus** | Azure Service Bus-trafik som använder Premium-tjänstnivån. | Utgående | Ja | Ja |
| **ServiceFabric** | Azure-tjänst fabric.<br/><br/>*Anm.:* Den här taggen representerar tjänstslutpunkten Service Fabric för kontrollplan per region. Detta gör det möjligt för kunder att utföra hanteringsåtgärder för sina Service Fabric-kluster från sitt virtuella nätverk (t.ex. https:// westus.servicefabric.azure.com) | Båda | Inga | Inga |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL och Azure SQL Data Warehouse.<br/><br/>*Anm.:* Den här taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure SQL Database, men inte en specifik SQL-databas eller -server. Den här taggen gäller inte för SQL-hanterad instans. | Utgående | Ja | Ja |
| **SqlManagement (SQLManage)** | Hanteringstrafik för SQL-dedikerade distributioner. | Båda | Inga | Ja |
| **Lagring** | Azure Storage. <br/><br/>*Anm.:* Den här taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure Storage, men inte ett specifikt Azure Storage-konto. | Utgående | Ja | Ja |
| **StorageSyncService** | Tjänsten För lagringssynkronisering. | Båda | Inga | Inga |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Båda | Inga | Inga |
| **VirtualNetwork (VirtualNetwork)** | Det virtuella nätverksadressutrymmet (alla IP-adressintervall som definierats för det virtuella nätverket), alla anslutna lokala adressutrymmen, [peered-virtuella](virtual-network-peering-overview.md) nätverk, virtuella nätverk som är anslutna till en [virtuell nätverksgateway,](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json) [värdens virtuella IP-adress](security-overview.md#azure-platform-considerations)och adressprefix som används på [användardefinierade vägar](virtual-networks-udr-overview.md). Den här taggen kan också innehålla standardvägar. | Båda | Inga | Inga |

>[!NOTE]
>I den klassiska distributionsmodellen (före Azure Resource Manager) stöds en delmängd av taggarna i den tidigare tabellen. Dessa taggar stavas på olika sätt:
>
>| Klassisk stavning | Taggen Motsvarande Resurshanteraren |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Tjänsttaggar för Azure-tjänster anger adressprefixen från det specifika molnet som används. Till exempel kommer de underliggande IP-intervallen som motsvarar **Sql-taggvärdet** i Azure Public-molnet att skilja sig från de underliggande intervallen i Azure China-molnet.

> [!NOTE]
> Om du implementerar en [tjänstslutpunkt för virtuellt nätverk](virtual-network-service-endpoints-overview.md) för en viss tjänst, till exempel Azure Storage eller Azure SQL Database, lägger Azure till en [väg](virtual-networks-udr-overview.md#optional-default-routes) till ett undernät för virtuella nätverk för tjänsten. Adressprefixen i flödet är samma adressprefix, eller CIDR-intervall, som för motsvarande servicetag.

## <a name="service-tags-on-premises"></a>Servicetaggar lokalt  
Du kan hämta den aktuella servicetag- och intervallinformationen som du kan inkludera som en del av dina lokala brandväggskonfigurationer. Den här informationen är den aktuella point-in-time-listan över IP-intervall som motsvarar varje servicetagg. Du kan få informationen programmässigt eller via en JSON-fil nedladdning, som beskrivs i följande avsnitt.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Använda API:et för identifiering av Service Tag (offentlig förhandsversion)
Du kan programmässigt hämta den aktuella listan över tjänsttaggar tillsammans med IP-adressintervallinformation:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> I den offentliga förhandsversionen kan identifierings-API:et returnera information som är mindre aktuell än information som returneras av JSON-hämtningarna. (Se nästa avsnitt.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Upptäck tjänsttaggar med hjälp av nedladdningsbara JSON-filer 
Du kan hämta JSON-filer som innehåller den aktuella listan över tjänsttaggar tillsammans med IP-adressintervallinformation. Dessa listor uppdateras och publiceras varje vecka. Platser för varje moln är:

- [Azure, offentlig](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure amerikanska regeringen](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>En delmängd av den här informationen har publicerats i XML-filer för [Azure Public,](https://www.microsoft.com/download/details.aspx?id=41653) [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)och [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). Dessa XML-hämtningar kommer att vara inaktuella senast den 30 juni 2020 och kommer inte längre att vara tillgängliga efter det datumet. Du bör migrera till att använda hämtningen av Identifierings-API eller JSON enligt beskrivningen i föregående avsnitt.

### <a name="tips"></a>Tips 
- Du kan identifiera uppdateringar från en publikation till en annan genom att notera ökade *ändringsnummervärden* i JSON-filen. Varje underavsnitt (till exempel **Storage.WestUS)** har en egen *ändringAntal* som ökas när ändringar sker. Den översta nivån i filens *changeNumber* ökas när något av underavsnitten ändras.
- Exempel på hur du tolkar information om servicetag (till exempel hämta alla adressintervall för lagring i WestUS) finns i dokumentationen [för Service Tag Discovery API PowerShell.](https://aka.ms/discoveryapi_powershell)

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md).
