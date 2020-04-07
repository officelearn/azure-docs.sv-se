---
title: Vad är en Privat Azure-slutpunkt?
description: Lär dig mer om Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: b8f55b0693b7ffdf10516cfb19b67ccc5a94dc5a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742904"
---
# <a name="what-is-azure-private-endpoint"></a>Vad är Azure Private Endpoint?

Azure Private Endpoint är ett nätverksgränssnitt som ger dig en privat och säker anslutning till en tjänst som drivs av Azure Private Link. Private Endpoint använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. Tjänsten kan vara en Azure-tjänst som Azure Storage, Azure Cosmos DB, SQL, etc. eller din egen [Private Link Service](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Egenskaper för privat slutpunkt 
 En privat slutpunkt anger följande egenskaper: 


|Egenskap  |Beskrivning |
|---------|---------|
|Namn    |    Ett unikt namn inom resursgruppen.      |
|Undernät    |  Undernätet för att distribuera och allokera privata IP-adresser från ett virtuellt nätverk. Information om undernätskrav finns i avsnittet Begränsningar i den här artikeln.         |
|Privat länk resurs    |   Den privata länkresursen som du vill ansluta med hjälp av resurs-ID eller alias från listan över tillgängliga typer. En unik nätverksidentifierare genereras för all trafik som skickas till den här resursen.       |
|Underkälla för mål   |      Den underresurs som ska anslutas. Varje privat länkresurstyp har olika alternativ att välja baserat på inställningar.    |
|Metod för godkännande av anslutning    |  Automatisk eller manuell. Baserat på RBAC-behörigheter (Role-based Access Control) kan din privata slutpunkt godkännas automatiskt. Om du försöker ansluta till en privat länkresurs utan RBAC använder du den manuella metoden för att tillåta resursägaren att godkänna anslutningen.        |
|Begär meddelande     |  Du kan ange ett meddelande om att begärda anslutningar ska godkännas manuellt. Det här meddelandet kan användas för att identifiera en specifik begäran.        |
|Anslutningsstatus   |   En skrivskyddad egenskap som anger om den privata slutpunkten är aktiv. Endast privata slutpunkter i ett godkänt tillstånd kan användas för att skicka trafik. Ytterligare tillgängliga tillstånd: <br>-**Godkänd**: Anslutningen godkändes automatiskt eller manuellt och är klar att användas.</br><br>-**Väntar:** Anslutningen skapades manuellt och väntar på godkännande av den privata länkresursägaren.</br><br>-**Avvisad**: Anslutningen avvisades av ägaren till den privata länkresursen.</br><br>-**Frånkopplad**: Anslutningen togs bort av ägaren till den privata länkresursen. Den privata slutpunkten blir informativ och bör tas bort för rensning. </br>|

Här är några viktiga detaljer om privata slutpunkter: 
- Privat slutpunkt möjliggör anslutning mellan konsumenter från samma virtuella nätverk, regionalt peered virtuella nätverk, globalt peered virtuella nätverk och lokalt med hjälp av [VPN](https://azure.microsoft.com/services/vpn-gateway/) eller [Express Route](https://azure.microsoft.com/services/expressroute/) och tjänster som drivs av Private Link.
 
- När du skapar en privat slutpunkt skapas också ett nätverksgränssnitt för resursens livscykel. Gränssnittet tilldelas en privat IP-adress från undernätet som mappar till Private Link Service.
 
- Den privata slutpunkten måste distribueras i samma region som det virtuella nätverket. 
 
- Den privata länkresursen kan distribueras i en annan region än det virtuella nätverket och den privata slutpunkten.
 
- Flera privata slutpunkter kan skapas med samma privata länkresurs. För ett enda nätverk med en gemensam DNS-serverkonfiguration är den rekommenderade metoden att använda en enda privat slutpunkt för en viss privat länkresurs för att undvika dubblettposter eller konflikter i DNS-upplösning. 
 
- Flera privata slutpunkter kan skapas i samma eller olika undernät i samma virtuella nätverk. Det finns gränser för hur många privata slutpunkter du kan skapa i en prenumeration. Mer information finns i [Azure-gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Privat länkresurs 
En privat länkresurs är målmålet för en viss privat slutpunkt. Följande är en lista över tillgängliga privata länkresurstyper: 
 
|Namn på privat länkresurs  |Resurstyp   |Underresurser  |
|---------|---------|---------|
|**Private Link Service** (din egen tjänst)   |  Microsoft.Network/privateLinkServices       | tomt |
|**Azure SQL Database** | Microsoft.Sql/servrar    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servrar    |  Sql Server (sqlServer)        | 
|**Azure-lagring**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tabell (tabell, table_secondary)<BR> Kö (kö, queue_secondary)<BR> Fil (fil, file_secondary)<BR> Webb (webb, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databasKonton | Sql, MongoDB, Cassandra, Gremlin, Tabell|
|**Azure-databas för PostgreSQL -Enkel server** | Microsoft.DBforPostgreSQL/servrar   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servrar    | mysqlServer |
|**Azure-databas för MariaDB** | Microsoft.DBforMariaDB/servrar    | mariadbServer (mariadbServer) |
|**Azure Key Vault** | Microsoft.KeyVault/valv    | valv |
|**Azure Kubernetes-tjänst - Kubernetes API** | Microsoft.ContainerService/managedClusters | managedCluster |
|**Azure Search** | Microsoft.Search/searchService| sökTjänst|  
|**Azure-behållarregister** | Microsoft.Container-register/register  | registry |
|**Azure App Configuration** | Microsoft.Appkonfiguration/configurationStores   | configurationStore (configurationStore) |
|**Azure Backup** | Microsoft.RecoveryServices/valv   | valv |
|**Azure Event Hub** | Microsoft.EventHub/namnområden    | namnområde |
|**Azure Service Bus** | Microsoft.ServiceBus/namnområden | namnområde |
|**Azure Relay** | Microsoft.Relay/namnområden | namnområde |
|**Azure Event Grid** | Ämnen för Microsoft.EventGrid/Event  | ämne |
|**Azure Event Grid** | Microsoft.EventGrid/domäner | domän |
|**Azure WebApps** | Microsoft.Webbplatser    | webbplats |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/arbetsytor  | arbetsyta |
  
 
## <a name="network-security-of-private-endpoints"></a>Nätverkssäkerhet för privata slutpunkter 
När du använder privata slutpunkter för Azure-tjänster är trafiken skyddad till en specifik privat länkresurs. Plattformen utför en åtkomstkontroll för att validera nätverksanslutningar som endast når den angivna privata länkresursen. För att komma åt ytterligare resurser inom samma Azure-tjänst krävs ytterligare privata slutpunkter. 
 
Du kan helt låsa dina arbetsbelastningar från att komma åt offentliga slutpunkter för att ansluta till en Azure-tjänst som stöds. Den här kontrollen ger ett extra nätverkssäkerhetslag till dina resurser genom att tillhandahålla ett inbyggt exfiltreringsskydd som förhindrar åtkomst till andra resurser som finns på samma Azure-tjänst. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Åtkomst till en privat länkresurs med hjälp av arbetsflöde för godkännande 
Du kan ansluta till en privat länkresurs med följande metoder för godkännande av anslutningar:
- **Godkänns automatiskt** när du äger eller har behörighet för den specifika privata länkresursen. Behörigheten som krävs baseras på den privata länkresurstypen i följande format: Microsoft. \<Leverantör>/<resource_type>/privateEndpointConnectionApproval/åtgärd
- **Manuell** begäran när du inte har den behörighet som krävs och vill begära åtkomst. Ett arbetsflöde för godkännande initieras. Den privata slutpunkten och efterföljande privata slutpunktsanslutning skapas i ett väntande tillstånd. Ägaren till den privata länkresursen ansvarar för att godkänna anslutningen. När den har godkänts är den privata slutpunkten aktiverad för att skicka trafik normalt, vilket visas i följande arbetsflödesdiagram för godkännande.  

![godkännande av arbetsflöde](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Ägaren till den privata länkresursen kan utföra följande åtgärder via en privat slutpunktsanslutning: 
- Granska information om alla privata slutpunktsanslutningar. 
- Godkänn en privat slutpunktsanslutning. Motsvarande privata slutpunkt aktiveras för att skicka trafik till den privata länkresursen. 
- Avvisa en privat slutpunktsanslutning. Motsvarande privata slutpunkt uppdateras för att återspegla statusen.
- Ta bort en privat slutpunktsanslutning i valfritt tillstånd. Motsvarande privata slutpunkt uppdateras med ett frånkopplat tillstånd för att återspegla åtgärden, den privata slutpunktsägaren kan bara ta bort resursen just nu. 
 
> [!NOTE]
> Endast en privat slutpunkt i ett godkänt tillstånd kan skicka trafik till en viss privat länkresurs. 

### <a name="connecting-using-alias"></a>Ansluta med alias
Alias är en unik moniker som genereras när tjänstägaren skapar den privata länktjänsten bakom en standardbelastningsutjämnare. Tjänstägaren kan dela detta alias med sina konsumenter offline. Konsumenter kan begära en anslutning till privat länktjänst med hjälp av antingen resurs-URI eller Alias. Om du vill ansluta med Alias måste du skapa en privat slutpunkt med hjälp av manuell anslutningsgodkännandemetod. För att använda manuell anslutningsgodkännandemetod anger du parametern för manuell begäran till true under privat slutpunktsskapande flöde. Titta på [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) och [az network private-endpoint skapa](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) för mer information. 

## <a name="dns-configuration"></a>DNS-konfiguration 
När du ansluter till en privat länkresurs med ett fullständigt kvalificerat domännamn (FQDN) som en del av anslutningssträngen är det viktigt att korrekt konfigurera DNS-inställningarna så att de matchar den allokerade privata IP-adressen. Befintliga Azure-tjänster kanske redan har en DNS-konfiguration att använda när du ansluter via en offentlig slutpunkt. Detta måste åsidosättas för att ansluta med din privata slutpunkt. 
 
Nätverksgränssnittet som är associerat med den privata slutpunkten innehåller den fullständiga information som krävs för att konfigurera DNS, inklusive FQDN och privata IP-adresser som allokerats för en viss privat länkresurs. 
 
Du kan använda följande alternativ för att konfigurera DNS-inställningarna för privata slutpunkter: 
- **Använd värdfilen (rekommenderas endast för testning)**. Du kan använda värdfilen på en virtuell dator för att åsidosätta DNS.  
- **Använd en privat DNS-zon**. Du kan använda privata DNS-zoner för att åsidosätta DNS-lösningen för en viss privat slutpunkt. En privat DNS-zon kan länkas till ditt virtuella nätverk för att lösa specifika domäner.
- **Använd den anpassade DNS-servern**. Du kan använda din egen DNS-server för att åsidosätta DNS-lösningen för en viss privat länkresurs. Om [DNS-servern](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) finns i ett virtuellt nätverk kan du skapa en DNS-vidarebefordringsregel för att använda en privat DNS-zon för att förenkla konfigurationen för alla privata länkresurser.
 
> [!IMPORTANT]
> Det rekommenderas inte att åsidosätta en zon som används aktivt för att lösa offentliga slutpunkter. Anslutningar till resurser kan inte matchas korrekt utan att DNS vidarebefordras till den offentliga DNS:en. För att undvika problem, skapa ett annat domännamn eller följ det föreslagna namnet för varje tjänst nedan. 
 
För Azure-tjänster använder du de rekommenderade zonnamnen enligt beskrivningen i följande tabell:

|Resurstyp för privat länk   |Subresource (Delkälla)  |Zonnamn  |
|---------|---------|---------|
|SQL DB (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Lagringskonto (Microsoft.Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Lagringskonto (Microsoft.Storage/storageAccounts)    |    Tabell (tabell, table_secondary)      |   privatelink.table.core.windows.net       |
|Lagringskonto (Microsoft.Storage/storageAccounts)    |    Kö (kö, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Lagringskonto (Microsoft.Storage/storageAccounts)   |    Fil (fil, file_secondary)      |    privatelink.file.core.windows.net      |
|Lagringskonto (Microsoft.Storage/storageAccounts)     |  Webb (webb, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake File System Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake File System Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Tabell|privatelink.table.cosmos.azure.com|
|Azure-databas för PostgreSQL - En server (Microsoft.DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database för MySQL (Microsoft.DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure-databas för MariaDB (Microsoft.DBforMariaDB/servrar)|mariadbServer (mariadbServer)|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft.KeyVault/valv)|valv|privatelink.vaultcore.azure.net|
|Azure Kubernetes Service - Kubernetes API (Microsoft.ContainerService/managedClusters) | managedCluster | {guid}.privatelink. <region>.azmk8s.io|
|Azure Search (Microsoft.Search/searchServices)|sökTjänst|privatelink.search.windows.net|   
|Azure-behållarregister (Microsoft.Container Register/register) | registry | privatelink.azurecr.io |
|Azure-appkonfiguration (Microsoft.Appkonfiguration/configurationStores)| configurationStore (configurationStore) | privatelink.azconfig.io|
|Azure Backup (Microsoft.RecoveryServices/vaults)| valv |privatelink. {region}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/namespaces)| namnområde |privatelink.servicebus.windows.net|
|Azure Service Bus (Microsoft.ServiceBus/namespaces) | namnområde |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft.Relay/namespaces) | namnområde |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft.EventGrid/topics)   | ämne | Ämne. {region}.privatelink.eventgrid.azure.net|
|Azure Event Grid (Microsoft.EventGrid/domäner) | domän | Domän. {region}.privatelink.eventgrid.azure.net |
|Azure WebApps (Microsoft.Web/sites)    | webbplats | privatelink.azurewebsites.net |
|Azure Machine Learning(Microsoft.MachineLearningServices/workspaces)   | arbetsyta | privatelink.api.azureml.ms |
 
Azure skapar en kanonisk namn DNS-post (CNAME) på den offentliga DNS för att omdirigera upplösningen till de föreslagna domännamnen. Du kan åsidosätta upplösningen med den privata IP-adressen för dina privata slutpunkter. 
 
Dina program behöver inte ändra anslutnings-URL:en. När du försöker lösa med en offentlig DNS matchas DNS-servern nu till dina privata slutpunkter. Processen påverkar inte dina program. 
 
## <a name="limitations"></a>Begränsningar
 
Följande tabell innehåller en lista över kända begränsningar när du använder privata slutpunkter: 


|Begränsning |Beskrivning |Åtgärd  |
|---------|---------|---------|
|NSG-regler (Network Security Group) och användardefinierade vägar gäller inte för privat slutpunkt    |NSG stöds inte på privata slutpunkter. Även om undernät som innehåller den privata slutpunkten kan ha NSG associerat med den, kommer reglerna inte att vara effektiva på trafik som bearbetas av den privata slutpunkten. Du måste ha [nätverksprinciper inaktiverade](disable-private-endpoint-network-policy.md) för att distribuera privata slutpunkter i ett undernät. NSG tillämpas fortfarande på andra arbetsbelastningar som finns på samma undernät. Vägar i alla klientundernät kommer att använda ett /32-prefix, för att ändra standardroutningsbeteendet krävs en liknande UDR  | Styr trafiken med hjälp av NSG-regler för utgående trafik på källklienter. Distribuera enskilda vägar med /32 prefix för att åsidosätta privata slutpunktsvägar. NSG-flödesloggar och övervakningsinformation för utgående anslutningar stöds fortfarande och kan användas        |


## <a name="next-steps"></a>Nästa steg
- [Skapa en privat slutpunkt för SQL Database Server med Portal](create-private-endpoint-portal.md)
- [Skapa en privat slutpunkt för SQL Database Server med PowerShell](create-private-endpoint-powershell.md)
- [Skapa en privat slutpunkt för SQL Database Server med CLI](create-private-endpoint-cli.md)
- [Skapa en privat slutpunkt för lagringskonto med Portal](create-private-endpoint-storage-portal.md)
- [Skapa en privat slutpunkt för Azure Cosmos-konto med Portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Skapa din egen private link-tjänst med Azure PowerShell](create-private-link-service-powershell.md)
- [Skapa din egen privata länk för Azure Database för PostgreSQL - Single server med Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Skapa din egen privata länk för Azure Database för PostgreSQL - Single server med CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Skapa din egen privata länk för Azure Database för MySQL med Portal](../mysql/howto-configure-privatelink-portal.md)
- [Skapa din egen privata länk för Azure Database för MySQL med CLI](../mysql/howto-configure-privatelink-cli.md)
- [Skapa din egen privata länk för Azure Database för MariaDB med Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Skapa din egen privata länk för Azure-databas för MariaDB med CLI](../mariadb/howto-configure-privatelink-cli.md)
