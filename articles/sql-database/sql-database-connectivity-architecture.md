---
title: Azure SQL Database connectivity-arkitektur | Microsoft Docs
description: Det här dokumentet beskriver den Azure-SQLDB anslutningsarkitektur från Azure eller från utanför Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DhruvMsft
ms.author: dhruv
ms.reviewer: carlrab
manager: craigg
ms.date: 01/24/2018
ms.openlocfilehash: 66f558db713ab951864fe694f27f2e60d52e875a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064160"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Azure SQL Database Connectivity-arkitektur 

Den här artikeln beskriver arkitekturen för Azure SQL Database-anslutning och förklarar hur de olika komponenterna fungerar för att dirigera trafik till din Azure SQL Database-instans. Azure SQL Database connectivity komponenter fungerar att dirigera nätverkstrafik till Azure-databasen med klienter som ansluter från i Azure och klienter som ansluter från utanför Azure. Den här artikeln innehåller också skriptexempel för att ändra hur anslutning sker, samt den information som rör ändra standardinställningar för anslutningen. 

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt över arkitekturen för Azure SQL Database-anslutning.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/architecture-overview.png)


Följande steg beskriver hur upprättas en anslutning till en Azure SQL database via Azure SQL Database-belastningsutjämnaren för programvara (SLB) och Azure SQL Database-gateway.

- Klienterna i Azure eller utanför Azure ansluter till SLB, som har en offentlig IP-adress och lyssnar på port 1433.
- SLB dirigerar trafik till Azure SQL Database-gateway.
- Gatewayen dirigerar trafiken till rätt proxy mellanprogram.
- Proxy-mellanprogram dirigerar trafiken till lämplig Azure SQL-databasen.

> [!IMPORTANT]
> Var och en av dessa komponenter har distribuerats med DOS-(DDoS) skydd inbyggda på nätverket och appnivån.
>

## <a name="connectivity-from-within-azure"></a>Anslutningen från i Azure

Om du ansluter från inom Azure, dina anslutningar har en princip för **omdirigera** som standard. En princip av **omdirigera** innebär att anslutningar när TCP-sessionen har upprättats till Azure SQL-databas klientsessionen sedan omdirigeras till proxy mellanprogram en ändring av den virtuella mål-IP från den för Azure SQL Database-gateway med proxy mellanprogram. Alla efterföljande paket som flödar därefter direkt via proxy-middleware, vilket kringgår Azure SQL Database-gateway. Följande diagram illustrerar det här flödet i nätverkstrafiken.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutningen från utanför Azure

Om du ansluter från platser utanför Azure, dina anslutningar har en princip för **Proxy** som standard. En princip av **Proxy** innebär att TCP-sessionen har upprättats via gatewayen för Azure SQL-databasen och alla efterföljande paket som flödar via gatewayen. Följande diagram illustrerar det här flödet i nätverkstrafiken.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

> [!IMPORTANT]
> När du använder Tjänsteslutpunkter med Azure SQL Database principen är **Proxy** som standard. Om du vill aktivera anslutningen från i det virtuella nätverket måste du tillåta utgående anslutningar till Azure SQL Database Gateway IP-adresser som anges i listan nedan. När du använder Tjänsteslutpunkter vi rekommenderar starkt att ändra anslutningsprincipen till **omdirigera** för att förbättra prestanda. Om du ändrar din anslutningsprincip till **omdirigera** det inte blir tillräckliga för att tillåta utgående på din NSG till Azure-SQLDB-gateway IP-adresser som anges nedan, måste du tillåta utgående trafik till alla SQLDB IP-adresser för Azure. Detta kan åstadkommas med hjälp av Tjänsttaggar för NSG (Nätverkssäkerhetsgrupper). Mer information finns i [Tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database gateway IP-adresser

Om du vill ansluta till en Azure SQL database från lokala resurser, som du vill tillåta utgående trafik till Azure SQL Database-gatewayen för din Azure-region. Dina anslutningar kan bara gå via gatewayen när du ansluter i Proxy-läge, vilket är standard när du ansluter från lokala resurser.

I följande tabell visas de primära och sekundära IP-adresserna för Azure SQL Database-gateway för alla dataområden. Det finns två IP-adresser för vissa regioner. Den primära IP-adressen är den aktuella IP-adressen till gatewayen i dessa regioner och den andra IP-adressen är en IP-adress för redundans. Redundans-adressen är den adress som vi kan också flytta din server för att hålla hög tjänsternas tillgänglighet. För dessa regioner rekommenderar vi att du tillåter utgående trafik till båda IP-adresserna. Den andra IP-adressen ägs av Microsoft och lyssnar inte på alla tjänster förrän den aktiveras genom Azure SQL Database för att acceptera anslutningar.

> [!IMPORTANT]
> Om du ansluter från inom Azure anslutningsprincipen blir **omdirigera** som standard (utom om du använder Tjänsteslutpunkter). Det räcker inte att tillåta följande IP-adresser. Du måste tillåta alla Azure SQL Database IP-adresser. Om du ansluter från inom ett virtuellt nätverk, kan detta åstadkommas med hjälp av Tjänsttaggar för NSG (Nätverkssäkerhetsgrupper). Mer information finns i [Tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

| Regionsnamn | Primär IP-adress | Sekundär IP-adress |
| --- | --- |--- |
| Östra Australien | 191.238.66.109 | 13.75.149.87 |
| Sydöstra Australien | 191.239.192.109 | 13.73.109.251 |
| Södra Brasilien | 104.41.11.5 | |
| Centrala Kanada | 40.85.224.249 | |
| Östra Kanada | 40.86.226.166 | |
| Centrala USA | 23.99.160.139 | 13.67.215.62 |
| Östasien | 191.234.2.139 | 52.175.33.150 |
| Östra USA 1 | 191.238.6.43 | 40.121.158.30 |
| USA, östra 2 | 191.239.224.107 | 40.79.84.180 * |
| Centrala Indien | 104.211.96.159  | |
| Södra Indien | 104.211.224.146  | |
| Västra Indien | 104.211.160.80 | |
| Östra Japan | 191.237.240.43 | 13.78.61.196 |
| Västra Japan | 191.238.68.11 | 104.214.148.156 |
| Sydkorea, centrala | 52.231.32.42 | |
| Sydkorea, södra | 52.231.200.86 |  |
| Norra centrala USA | 23.98.55.75 | 23.96.178.199 |
| Norra Europa | 191.235.193.75 | 40.113.93.91 |
| Södra centrala USA | 23.98.162.75 | 13.66.62.124 |
| Sydostasien | 23.100.117.95 | 104.43.15.0 |
| Storbritannien, norra | 13.87.97.210 | |
| Storbritannien, södra 1 | 51.140.184.11 | |
| Storbritannien, södra 2 | 13.87.34.7 | |
| Storbritannien, västra | 51.141.8.11  | |
| Västra centrala USA | 13.78.145.25 | |
| Västra Europa | 191.237.232.75 | 40.68.37.158 |
| Västra USA 1 | 23.99.34.75 | 104.42.238.205 |
| Västra USA 2 | 13.66.226.202  | |
||||

\* **Obs:** *östra USA 2* har också en tertiär IP-adressen för `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Ändra principen för Azure SQL Database-anslutning

Du kan ändra principen för Azure SQL Database för en Azure SQL Database-server med den [an-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) kommando.

- Om din princip har angetts till **Proxy**, alla nätverksanslutningar paket flow via Azure SQL Database-gateway. Du måste tillåta utgående trafik till endast Azure SQL Database gateway-IP för den här inställningen. Med hjälp av en inställning av **Proxy** har flera svarstider än inställningen **omdirigera**.
- Om din anslutning principinställningen **omdirigera**, alla nätverksanslutningar paket flow direkt till proxyn mellanprogram. Du måste tillåta utgående trafik till flera IP-adresser för den här inställningen.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript för att ändra anslutningsinställningar via PowerShell

> [!IMPORTANT]
> Det här skriptet kräver den [Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).
>

Följande PowerShell-skript visar hur du ändrar principen.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript för att ändra anslutningsinställningar via Azure CLI

> [!IMPORTANT]
> Det här skriptet kräver den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

Följande CLI-skript visar hur du ändrar principen.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar principen för Azure SQL Database för en Azure SQL Database-server finns i [an-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Information om Azure SQL Database-anslutningsbeteendet för klienter som använder ADO.NET 4.5 eller senare finns i [portar utöver 1433 för ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Allmän utveckling översiktlig information finns i [översikt över SQL Database Application Development](sql-database-develop-overview.md).
