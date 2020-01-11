---
title: Vad är en privat Azure-slutpunkt?
description: Läs om den privata Azure-slutpunkten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 38f424287788537cc5711bab8da60b5798a84b3a
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867693"
---
# <a name="what-is-azure-private-endpoint"></a>Vad är en privat Azure-slutpunkt?

Azure Private Endpoint är ett nätverksgränssnitt som ger dig en privat och säker anslutning till en tjänst som drivs av Azure Private Link. Privat slut punkt använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt VNet. Tjänsten kan vara en Azure-tjänst, till exempel Azure Storage, Azure Cosmos DB, SQL osv. eller en egen [privat länk-tjänst](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Egenskaper för privat slut punkt 
 En privat slut punkt anger följande egenskaper: 


|Egenskap  |Beskrivning |
|---------|---------|
|Namn    |    Ett unikt namn inom resurs gruppen.      |
|Undernät    |  Under nätet för att distribuera och allokera privata IP-adresser från ett virtuellt nätverk. För under näts krav, se avsnittet begränsningar i den här artikeln.         |
|Privat länk resurs    |   Den privata länk resursen för att ansluta med resurs-ID eller alias i listan över tillgängliga typer. Ett unikt nätverks-ID skapas för all trafik som skickas till den här resursen.       |
|Mål under resurs   |      Den under resurs som ska anslutas. Varje privat länk resurs typ har olika alternativ för att välja baserat på preferens.    |
|Metod för godkännande av anslutning    |  Automatisk eller manuell. Utifrån rollbaserad åtkomst kontroll (RBAC) behörigheter kan din privata slut punkt godkännas automatiskt. Om du försöker ansluta till en privat länk resurs utan RBAC använder du den manuella metoden för att tillåta resursens ägare att godkänna anslutningen.        |
|Begär ande meddelande     |  Du kan ange ett meddelande för begärda anslutningar som ska godkännas manuellt. Det här meddelandet kan användas för att identifiera en speciell begäran.        |
|Anslutningsstatus   |   En skrivskyddad egenskap som anger om den privata slut punkten är aktiv. Endast privata slut punkter i ett godkänt tillstånd kan användas för att skicka trafik. Ytterligare tillstånd är tillgängliga: <br>-**godkänd**: anslutningen godkändes automatiskt eller manuellt och är redo att användas.</br><br>-**väntar**: anslutningen skapades manuellt och väntar på att godkännas av ägaren till den privata länk resursen.</br><br>-**avvisad**: anslutningen avvisades av resurs ägaren för privata länkar.</br><br>-**frånkopplad**: anslutningen togs bort av ägaren till den privata länk resursen. Den privata slut punkten blir informativ och bör tas bort för rensning. </br>|

Här följer några viktiga uppgifter om privata slut punkter: 
- Med privat slut punkt kan du ansluta mellan konsumenter från samma VNet, regionalt peer-virtuella nätverk, globalt peered virtuella nätverk och lokalt med hjälp av [VPN](https://azure.microsoft.com/services/vpn-gateway/) eller [Express Route](https://azure.microsoft.com/services/expressroute/) och tjänster som drivs av en privat länk.
 
- När du skapar en privat slut punkt skapas även ett nätverks gränssnitt för resursens livs cykel. Gränssnittet tilldelas en privat IP-adress från det undernät som mappar till den privata länk tjänsten.
 
- Den privata slut punkten måste distribueras i samma region som det virtuella nätverket. 
 
- Den privata länk resursen kan distribueras i en annan region än det virtuella nätverket och den privata slut punkten.
 
- Flera privata slut punkter kan skapas med samma privata länk resurs. Den rekommenderade metoden är att använda en enda privat slut punkt för en viss privat länk resurs för att undvika dubbla poster eller konflikter i DNS-matchning för ett enda nätverk som använder en gemensam DNS-serverkonfiguration. 
 
- Flera privata slut punkter kan skapas i samma eller olika undernät i samma virtuella nätverk. Det finns gränser för antalet privata slut punkter som du kan skapa i en prenumeration. Mer information finns i [Azure-gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Privat länk resurs 
En privat länk resurs är mål målet för en specifik privat slut punkt. Följande är en lista över tillgängliga privata länk resurs typer: 
 
|Resurs namn för privat länk  |Resurstyp   |Under resurser  |
|---------|---------|---------|
|**Privat länk tjänst** (din egen tjänst)   |  Microsoft. Network/privateLinkServices       | tomt |
|**Azure SQL Database** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure SQL Data Warehouse** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Tabell (tabell, table_secondary)<BR> Kö (kö, queue_secondary)<BR> Fil (fil, file_secondary)<BR> Webb (webb, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts | SQL, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL-enskild server** | Microsoft.DBforPostgreSQL/servers   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft. DBforMySQL/servers    | mysqlServer |
|**Azure Database för MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
 
## <a name="network-security-of-private-endpoints"></a>Nätverks säkerhet för privata slut punkter 
När du använder privata slut punkter för Azure-tjänster skyddas trafik till en enskild privat länk resurs. Plattformen utför en åtkomst kontroll för att verifiera att nätverks anslutningar når enbart den angivna privata länk resursen. För att få åtkomst till ytterligare resurser inom samma Azure-tjänst krävs ytterligare privata slut punkter. 
 
Du kan helt låsa dina arbets belastningar från att komma åt offentliga slut punkter för att ansluta till en Azure-tjänst som stöds. Den här kontrollen ger ytterligare ett nätverks säkerhets lager till dina resurser genom att tillhandahålla ett inbyggt exfiltrering skydd som förhindrar åtkomst till andra resurser som finns på samma Azure-tjänst. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Åtkomst till en privat länk resurs med hjälp av godkännande arbets flöde 
Du kan ansluta till en privat länk resurs med hjälp av följande metoder för godkännande av anslutning:
- **Automatiskt** godkänd när du äger eller har behörighet för den specifika privata länk resursen. Den nödvändiga behörigheten baseras på resurs typen privat länk i följande format: Microsoft.\<Provider >/< resource_type >/privateEndpointConnectionApproval/action
- **Manuell** begäran när du inte har behörighet som krävs och vill begära åtkomst. Ett arbets flöde för godkännande kommer att initieras. Den privata slutpunkten och efterföljande privata slutpunktsanslutning skapas i ett väntande tillstånd. Ägaren till den privata länkresursen ansvarar för att godkänna anslutningen. När den har godkänts är den privata slut punkten aktive rad för att skicka trafik normalt, som du ser i följande arbets flödes diagram för godkännande.  

![arbets flödes godkännande](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Resurs ägaren för privata länkar kan utföra följande åtgärder över en privat slut punkts anslutning: 
- Granska alla anslutningar för privat slut punkt. 
- Godkänn en privat slut punkts anslutning. Motsvarande privata slut punkt kommer att aktive ras för att skicka trafik till den privata länk resursen. 
- Avvisa en privat slut punkts anslutning. Motsvarande privata slut punkt kommer att uppdateras för att avspegla statusen.
- Ta bort en privat slut punkts anslutning i vilket tillstånd som helst. Motsvarande privata slut punkt kommer att uppdateras med frånkopplat tillstånd för att avspegla åtgärden. ägaren av den privata slut punkten kan bara ta bort resursen just nu. 
 
> [!NOTE]
> Endast en privat slut punkt i ett godkänt tillstånd kan skicka trafik till en specifik privat länk resurs. 

### <a name="connecting-using-alias"></a>Ansluta med alias
Alias är en unik moniker som skapas när tjänst ägaren skapar en privat länk tjänst bakom en standard belastningsutjämnare. Tjänstens ägare kan dela det här aliaset med sina konsumenter offline. Konsumenter kan begära en anslutning till privata länk tjänster med antingen resurs-URI eller alias. Om du vill ansluta med alias måste du skapa en privat slut punkt med hjälp av metoden för godkännande av manuellt anslutning. För att använda en manuell metod för godkännande av anslutning ställer du in en manuell begär ande parameter till sant under skapa flöde för privat slut punkt. Titta på [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) och [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) för information. 

## <a name="dns-configuration"></a>DNS-konfiguration 
När du ansluter till en privat länk resurs med hjälp av ett fullständigt kvalificerat domän namn (FQDN) som en del av anslutnings strängen, är det viktigt att konfigurera dina DNS-inställningar på rätt sätt för att matcha den allokerade privata IP-adressen. Befintliga Azure-tjänster kanske redan har en DNS-konfiguration som ska användas vid anslutning via en offentlig slut punkt. Detta måste åsidosättas för att ansluta med hjälp av din privata slut punkt. 
 
Nätverks gränssnittet som är kopplat till den privata slut punkten innehåller den fullständiga uppsättningen information som krävs för att konfigurera din DNS, inklusive FQDN och privata IP-adresser som allokerats för en specifik privat länk resurs. 
 
Du kan använda följande alternativ för att konfigurera dina DNS-inställningar för privata slut punkter: 
- **Använd värd filen (rekommenderas endast för testning)** . Du kan använda värd filen på en virtuell dator för att åsidosätta DNS.  
- **Använd en privat DNS-zon**. Du kan använda privata DNS-zoner för att åsidosätta DNS-matchningen för en specifik privat slut punkt. En privat DNS-zon kan länkas till det virtuella nätverket för att lösa vissa domäner.
- **Använd din anpassade DNS-Server**. Du kan använda din egen DNS-server för att åsidosätta DNS-matchningen för en specifik privat länk resurs. Om din [DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) finns i ett virtuellt nätverk kan du skapa en regel för vidarebefordran av DNS för att använda en privat DNS-zon för att förenkla konfigurationen för alla privata länk resurser.
 
> [!IMPORTANT]
> Vi rekommenderar inte att du åsidosätter en zon som används aktivt för att lösa offentliga slut punkter. Anslutningar till resurser kan inte lösas korrekt utan DNS-vidarebefordran till den offentliga DNS-tjänsten. Du kan undvika problem genom att skapa ett annat domän namn eller följa det föreslagna namnet för varje tjänst nedan. 
 
För Azure-tjänster använder du de rekommenderade zon namnen enligt beskrivningen i följande tabell:

|Resurs typ för privat länk   |Underresurs  |Zonnamn  |
|---------|---------|---------|
|SQL DB/DW (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Lagrings konto (Microsoft. Storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Lagrings konto (Microsoft. Storage/storageAccounts)    |    Tabell (tabell, table_secondary)      |   privatelink.table.core.windows.net       |
|Lagrings konto (Microsoft. Storage/storageAccounts)    |    Kö (kö, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Lagrings konto (Microsoft. Storage/storageAccounts)   |    Fil (fil, file_secondary)      |    privatelink.file.core.windows.net      |
|Lagrings konto (Microsoft. Storage/storageAccounts)     |  Webb (webb, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake Gen2 för fil system (Microsoft. Storage/storageAccounts)  |  Data Lake Gen2 för fil system (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Tabell|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL-enskild server (Microsoft. DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft. DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (Microsoft. DBforMariaDB/servers)|mariadbServer|privatelink.mariadb.database.azure.com|
 
Azure skapar en DNS-post för kanoniskt namn (CNAME) på den offentliga DNS-domänen för att omdirigera matchningen till föreslagna domän namn. Du kan åsidosätta upplösningen med den privata IP-adressen för dina privata slut punkter. 
 
Dina program behöver inte ändra anslutnings-URL: en. När du försöker matcha med hjälp av en offentlig DNS-server kommer DNS-servern nu att matcha dina privata slut punkter. Processen påverkar inte dina program. 
 
## <a name="limitations"></a>Begränsningar
 
Följande tabell innehåller en lista med kända begränsningar när du använder privata slut punkter: 


|Begränsning |Beskrivning |Åtgärd  |
|---------|---------|---------|
|Regler för nätverks säkerhets gruppen (NSG) och användardefinierade vägar gäller inte för privat slut punkt    |NSG stöds inte för privata slut punkter. Medan undernät som innehåller den privata slut punkten kan ha NSG kopplade till sig, gäller inte reglerna för trafik som bearbetas av den privata slut punkten. Du måste ha [aktiverat tvingande nätverks principer](disable-private-endpoint-network-policy.md) för att distribuera privata slut punkter i ett undernät. NSG tillämpas fortfarande på andra arbets belastningar som finns i samma undernät. Vägar i alla klient under nät kommer att använda ett/32-prefix, och om du ändrar standarduppförandet för routning krävs ett liknande UDR  | Styr trafiken genom att använda NSG regler för utgående trafik på käll klienter. Distribuera enskilda vägar med/32-prefix för att åsidosätta privata slut punkts flöden        |
|  Peer-Virtual Network med enbart privata slut punkter stöds inte   |   När du ansluter till privata slut punkter på en peer-Virtual Network utan någon annan arbets belastning stöds inte       | Distribuera en enskild virtuell dator på peer-Virtual Network för att aktivera anslutningen |
|Specialiserade arbets belastningar kan inte komma åt privata slut punkter    |   Följande tjänster som distribueras i det virtuella nätverket kan inte komma åt någon privat länk resurs med hjälp av privata slut punkter:<br>App Service-plan</br>Azure Container-instans</br>Azure NetApp Files</br>Dedikerad HSM i Azure<br>       |   Ingen minskning under för hands versionen.       |


## <a name="next-steps"></a>Nästa steg
- [Skapa en privat slut punkt för SQL Database servern med hjälp av portalen](create-private-endpoint-portal.md)
- [Skapa en privat slut punkt för SQL Database Server med PowerShell](create-private-endpoint-powershell.md)
- [Skapa en privat slut punkt för SQL Database Server med CLI](create-private-endpoint-cli.md)
- [Skapa en privat slut punkt för lagrings kontot med hjälp av portalen](create-private-endpoint-storage-portal.md)
- [Skapa en privat slut punkt för Azure Cosmos-konto med hjälp av portalen](../cosmos-db/how-to-configure-private-endpoints.md)
- [Skapa en egen privat länk-tjänst med hjälp av Azure PowerShell](create-private-link-service-powershell.md)
- [Skapa en egen privat länk för Azure Database for PostgreSQL-en server med hjälp av portalen](../postgresql/howto-configure-privatelink-portal.md)
- [Skapa en egen privat länk för Azure Database for PostgreSQL-enskild server med CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Skapa en egen privat länk för Azure Database for MySQL med hjälp av portalen](../mysql/howto-configure-privatelink-portal.md)
- [Skapa en egen privat länk för Azure Database for MySQL med CLI](../mysql/howto-configure-privatelink-cli.md)
- [Skapa en egen privat länk för Azure Database for MariaDB med hjälp av portalen](../mariadb/howto-configure-privatelink-portal.md)
- [Skapa en egen privat länk för Azure Database for MariaDB med CLI](../mariadb/howto-configure-privatelink-cli.md)