---
title: Felsöka anslutning
description: Felsöka anslutning i SQL Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 689a2e549c2627c607b6549f164e55a73318f63e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350049"
---
# <a name="troubleshooting-connectivity-issues"></a>Felsökning av anslutningsproblem

I den här artikeln visas vanliga felsökningstekniker för anslutning till SQL Analytics-databasen.
- [Kontrollera tjänstens tillgänglighet](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Kontrollera om tjänsten är pausad eller håller på att skalas om](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Kontrollera brandväggsinställningarna](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Kontrollera inställningarna för det virtuella nätverket/tjänstslutpunkten](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Kontrollera om det finns nya drivrutiner](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Kontrollera anslutningssträngen](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Tillfälliga anslutningsproblem](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Vanliga felmeddelanden](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Kontrollera tjänstens tillgänglighet

Kontrollera om tjänsten är tillgänglig. Gå till den SQL Analytics-databas som du försöker ansluta i Azure-portalen. I den vänstra TOC-panelen klickar du på **Diagnostisera och lösa problem**.

![Välj resurshälsa](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Statusen för din SQL Analytics visas här. Om tjänsten inte visas som **Tillgänglig**kontrollerar du ytterligare steg.

![Tjänsten är tillgänglig](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Om din resurshälsa visar att SQL Analytics-instansen är pausad eller skalning följer du anvisningarna för att återuppta din instans.

![Pausad](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) tjänst Ytterligare information om Resurshälsa finns här.

## <a name="check-for-paused-or-scaling-operation"></a>Kontrollera om tjänsten är pausad eller håller på att skalas om

Kontrollera portalen för att se om SQL Analytics-instansen är pausad eller skalning.

![Tjänsten pausad](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Om du ser att tjänsten är pausad eller skalning kontrollerar du att den inte finns under underhållsschemat. På portalen för din SQL Analytics *Översikt*ser du det valda underhållsschemat.

![Översikt Underhåll Schema](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Annars kontaktar du IT-administratören för att kontrollera att underhållet inte är en schemalagd händelse. Om du vill återuppta SQL Analytics-instansen följer du stegen som beskrivs [här](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Kontrollera brandväggsinställningarna

SQL Analytics-databasen kommunicerar via port 1433.Om du försöker ansluta inifrån ett företagsnätverk kan det hända att utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till Azure SQL Database-servern såvida inte IT-avdelningen öppnar port 1433. Ytterligare information om brandväggskonfigurationer finns [här](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Kontrollera inställningarna för det virtuella nätverket/tjänstslutpunkten

Om du får fel 40914 och 40615 läser du [felbeskrivning och lösning här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Kontrollera om det finns nya drivrutiner

### <a name="software"></a>Programvara

Kontrollera att du använder de senaste verktygen för att ansluta till SQL Analytics-databasen:

* SSMS
* Azure Data Studio
* SQL Server-dataverktyg (Visual Studio)

### <a name="drivers"></a>Drivrutiner

Kontrollera att du använder de senaste drivrutinsversionerna.Om du använder en äldre version av drivrutinerna kan det leda till oväntade beteenden eftersom de äldre drivrutinerna kanske inte stöder nya funktioner.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [Php](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Kontrollera anslutningssträngen

Se till att anslutningssträngarna är inställda på rätt sätt.  Nedan följer några prover.  Du kan läsa mer om [anslutningssträngar här](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

ADO.NET-anslutningssträng

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

OdBC-anslutningssträng

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

Kontrollera om servern är hårt belastad med ett stort antal köade förfrågningar. Du kan behöva skala upp SQL Analytics-instansen för ytterligare resurser.

## <a name="common-error-messages"></a>Vanliga felmeddelanden

Fel 40914 och 40615, se [felbeskrivning och lösning här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Har du fortfarande anslutningsproblem?
Skapa en [supportbiljett](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) så att ingenjörsteamet kan stödja dig.
