---
title: Azure SQL Database connectivity-arkitektur | Microsoft Docs
description: Det här dokumentet förklarar Azure SQLDB anslutning arkitekturen från Azure eller från utanför Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 01/24/2018
ms.author: carlrab
ms.openlocfilehash: 76af1ead671a6d6b279202eaf7886463d4cd8bb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="azure-sql-database-connectivity-architecture"></a>Azure SQL Database Connectivity-arkitektur 

Den här artikeln beskriver arkitekturen för Azure SQL Database-anslutningen och förklarar hur de olika komponenterna fungerar för att dirigera trafik till din Azure SQL Database-instans. Dessa Azure SQL Database connectivity komponenter funktion för att dirigera nätverkstrafik till Azure-databas med klienter som ansluter från i Azure och anslutande klienter från utanför Azure. Den här artikeln innehåller även skriptexempel att ändra hur anslutningen sker och överväganden som rör ändra standardinställningar för anslutningen. 

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt över arkitekturen för Azure SQL Database-anslutningen.

![arkitektur, översikt](./media/sql-database-connectivity-architecture/architecture-overview.png)


Följande steg beskriver hur upprätta en anslutning till en Azure SQL database på Azure SQL Database-programbelastningsutjämnaren (SLB) och Azure SQL Database-gateway.

- Klienter i Azure eller utanför Azure ansluter till SLB som har en offentlig IP-adress och lyssnar på port 1433.
- SLB dirigerar trafik till Azure SQL Database-gateway.
- Gatewayen dirigerar trafik till rätt proxy mellanprogram.
- Proxy-mellanprogram omdirigerar trafiken till lämplig Azure SQL-databas.

> [!IMPORTANT]
> Var och en av dessa komponenter har distribuerats för tjänsten (DDoS) skydd inbyggda på nätverket och app-lagret.
>

## <a name="connectivity-from-within-azure"></a>Om du använder i Azure

Om du ansluter från i Azure anslutningarna har en princip för **omdirigera** som standard. En princip av **omdirigera** innebär att anslutningar när TCP-sessionen har upprättats till Azure SQL-databasen klientsessionen sedan omdirigeras till proxy mellanprogram med en ändring av den virtuella mål-IP från för Azure SQL Database-gateway som proxy mellanprogram. Därefter alla efterföljande paket som flödar direkt via proxy mellanprogram, vilket kringgår Azure SQL Database-gateway. Följande diagram illustrerar flödet i nätverkstrafiken.

![arkitektur, översikt](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutningen från utanför Azure

Om du ansluter från utanför Azure anslutningarna har en princip för **Proxy** som standard. En princip av **Proxy** betyder att TCP-sessionen har upprättats via Azure SQL Database-gatewayen och alla efterföljande paket flöda via gatewayen. Följande diagram illustrerar flödet i nätverkstrafiken.

![arkitektur, översikt](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database gateway IP-adresser

Om du vill ansluta till en Azure SQL database från lokala resurser, måste du tillåta utgående trafik till Azure SQL Database-gateway för din Azure-region. Anslutningar kan bara gå via gatewayen vid anslutning i proxyläge, vilket är standard när du ansluter från lokala resurser.

I följande tabell visas de primära och sekundära IPs för Azure SQL Database-gateway för alla dataområden. Det finns två IP-adresser för vissa regioner. I dessa regioner primära IP-adressen är den aktuella IP-adressen för gateway och den andra IP-adressen är en IP-adress för redundans. Failover-adressen är den adress som som vi kan flytta servern till att hålla den hög tillgängligheten för tjänst. För dessa regioner rekommenderar vi att du tillåter att utgående båda IP-adresser. Den andra IP-adressen ägs av Microsoft och lyssnar inte på några tjänster förrän den aktiveras av Azure SQL Database för att acceptera anslutningar.

| Regionsnamn | Primära IP-adress | Sekundär IP-adress |
| --- | --- |--- |
| Östra Australien | 191.238.66.109 | 13.75.149.87 |
| Sydöstra Australien | 191.239.192.109 | 13.73.109.251 |
| Södra Brasilien | 104.41.11.5 | |
| Centrala Kanada | 40.85.224.249 | |
| Östra Kanada | 40.86.226.166 | |
| Centrala USA | 23.99.160.139 | 13.67.215.62 |
| Östasien | 191.234.2.139 | 52.175.33.150 |
| Östra USA 1 | 191.238.6.43 | 40.121.158.30 |
| Östra USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Centrala Indien | 104.211.96.159  | |
| Södra Indien | 104.211.224.146  | |
| Västra Indien | 104.211.160.80 | |
| Östra Japan | 191.237.240.43 | 13.78.61.196 |
| Västra Japan | 191.238.68.11 | 104.214.148.156 |
| Centrala Korea | 52.231.32.42 | |
| Sydkorea | 52.231.200.86 |  |
| Norra centrala USA | 23.98.55.75 | 23.96.178.199 |
| Norra Europa | 191.235.193.75 | 40.113.93.91 |
| Södra centrala USA | 23.98.162.75 | 13.66.62.124 |
| Sydostasien | 23.100.117.95 | 104.43.15.0 |
| Storbritannien, norra | 13.87.97.210 | |
| Storbritannien, Syd 1 | 51.140.184.11 | |
| Storbritannien, södra 2 | 13.87.34.7 | |
| Storbritannien, västra | 51.141.8.11  | |
| Västra centrala USA | 13.78.145.25 | |
| Västra Europa | 191.237.232.75 | 40.68.37.158 |
| USA, västra 1 | 23.99.34.75 | 104.42.238.205 |
| Västra USA 2 | 13.66.226.202  | |
||||

\* **Obs:** *östra USA 2* har också en tertiär IP-adressen för `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Ändra principen för Azure SQL Database-anslutning

Om du vill ändra principen för Azure SQL Database för en Azure SQL Database-server, använder den [ansluten princip](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) kommando.

- Om din anslutningsprincip har angetts till **Proxy**, alla nätverksenheter paket flöde via Azure SQL Database-gateway. Du måste tillåta utgående till bara Azure SQL Database gateway IP-Adressen för den här inställningen. Med hjälp av inställningen **Proxy** har flera svarstider än inställningen **omdirigera**.
- Om din anslutning principinställningen **omdirigera**, alla nätverksenheter paket flödet direkt till mellanprogram proxy. Du måste tillåta utgående till flera IP-adresser för den här inställningen.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript för att ändra anslutningsinställningar via PowerShell

> [!IMPORTANT]
> Skriptet kräver den [Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).
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

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Skript för att ändra anslutningsinställningar via Azure CLI 2.0

> [!IMPORTANT]
> Skriptet kräver den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

CLI följande skript visar hur du ändrar principen.

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

- Information om hur du ändrar principen för Azure SQL Database för en Azure SQL Database-server finns i [ansluten princip](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Mer information om Azure SQL Database-anslutningen för klienter som använder ADO.NET 4.5 eller senare finns [portar utöver 1433 ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Allmänt program development översikt översiktsinformation finns i [SQL Database Development Programöversikt](sql-database-develop-overview.md).
