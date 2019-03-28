---
title: Felsöka Azure SQL Data Warehouse | Microsoft Docs
description: Felsöka Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 03/27/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 5115ffbc3568c87c37bae4a3e65c37f8504f1fb8
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541136"
---
# <a name="troubleshooting-connectivity-issues"></a>Felsökning av problem med nätverksanslutningen

Den här artikeln innehåller vanliga felsökningstekniker runt ansluter till SQL Data Warehouse.
- [Kontrollera tjänsttillgänglighet för](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Sök efter pausad eller skalning](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Kontrollera dina brandväggsinställningar](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Kontrollera inställningarna för virtuellt nätverk/tjänstens slutpunkt](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Sök efter de senaste drivrutinerna](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Kontrollera anslutningssträngen](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Tillfälliga anslutningsproblem](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Vanliga felmeddelanden](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Kontrollera tjänsttillgänglighet för

Kontrollera om tjänsten är tillgänglig. I Azure-portalen går du till SQL data warehouse som du försöker ansluta. I den vänstra innehållsförteckning-panelen, klickar du på **diagnostisera och lösa problem**.

![Välj Resource health](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Status för SQL data warehouse visas här. Om tjänsten inte visas som **tillgänglig**, kontrollera ytterligare steg.

![Tjänsten är tillgänglig](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Om Resource health visar att ditt informationslager är pausat eller skalning, följer du anvisningarna för att återuppta ditt informationslager.

![Tjänsten pausas](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) ytterligare information om Resource Health finns här.

## <a name="check-for-paused-or-scaling-operation"></a>Sök efter pausad eller skalning

Kontrollera portalen och se om ditt SQL data warehouse är pausad eller skalar.

![Tjänsten pausas](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Om du se att tjänsten är pausad eller skalning, kontrollera att det inte under ditt underhållsschema. På portalen för din SQL data warehouse *översikt*, utvalda underhållsschemat visas.

![Översikt över underhållsschema](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

I annat fall söker med IT-administratören att kontrollera att det här underhållet inte är en schemalagd händelse. Om du vill återuppta SQL data warehouse, följer du stegen som [här](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Kontrollera dina brandväggsinställningar

SQL Database Warehouse kommunicerar via port 1433.   Om du försöker ansluta inifrån ett företagsnätverk kan det hända att utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 1433. Mer information om brandväggskonfigurationer finns [här](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json#manage-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Kontrollera inställningarna för virtuellt nätverk/tjänstens slutpunkt

Om du har fått fel 40914 och 40615, se [felbeskrivning och matchning här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Sök efter de senaste drivrutinerna

### <a name="software"></a>Programvara

Kontrollera att du använder de senaste verktygen för att ansluta till SQL data warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Drivrutiner

Kontrollera att du använder de senaste drivrutinsversionerna.  Använder en äldre version av drivrutinerna kan resultera i oväntade beteenden som inte kanske äldre drivrutiner har stöd för nya funktioner.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Kontrollera anslutningssträngen

Kontrollera att anslutningssträngarna är korrekt inställda.  Nedan visas några exempel.  Du hittar mer information kring [anslutning strängar här](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

ADO.NET-anslutningssträng

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC-anslutningssträng

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP-anslutningssträng

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-anslutningssträng

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Tillfälliga anslutningsproblem

Kontrollera om du upplever hård belastning på servern med ett stort antal begäranden i kö. Du kan behöva skala upp ditt informationslager för ytterligare resurser.

## <a name="common-error-messages"></a>Vanliga felmeddelanden

Fel 40914 och 40615, finns i den [felbeskrivning och matchning här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Har du fortfarande problem med anslutningen?
Skapa en [supportärende](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) så att du har stöd för det tekniska teamet.
