---
title: Felsöka Azure SQL Data Warehouse | Microsoft Docs
description: Felsöka Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 290753b866f15e09a52572fdd7a43a60fc2812d6
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575542"
---
# <a name="troubleshooting-connectivity-issues"></a>Felsöka anslutnings problem

Den här artikeln innehåller vanliga fel söknings tekniker för att ansluta till din SQL Data Warehouse.
- [Kontrol lera tjänstens tillgänglighet](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Sök efter pausad eller skalnings åtgärd](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Kontrol lera brand Väggs inställningarna](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Kontrol lera inställningarna för VNet/service-slutpunkt](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Sök efter de senaste driv rutinerna](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Kontrol lera anslutnings strängen](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Tillfälliga anslutnings problem](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Vanliga felmeddelanden](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Kontrol lera tjänstens tillgänglighet

Kontrol lera om tjänsten är tillgänglig. I Azure Portal går du till SQL Data Warehouse som du försöker ansluta. Klicka på **diagnostisera och lös problem**i den vänstra panelen i innehålls förteckningen.

![Välj resurs hälsa](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Status för SQL Data Warehouse visas här. Om tjänsten inte visas som **tillgänglig**kontrollerar du ytterligare steg.

![Tjänsten är tillgänglig](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Om din resurs hälsa visar att informations lagret har pausats eller skalas, följer du anvisningarna för att återuppta ditt informations lager.

![Tjänsten har pausats](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) ytterligare information om Resource Health finns här.

## <a name="check-for-paused-or-scaling-operation"></a>Sök efter pausad eller skalnings åtgärd

Kontrol lera portalen för att se om din SQL Data Warehouse har pausats eller skalats.

![Tjänsten har pausats](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Om du ser att tjänsten har pausats eller skalas kan du kontrol lera att den inte finns under ditt underhålls schema. I portalen för din SQL Data Warehouse *Översikt*visas schemat för valt underhåll.

![Översikt över underhålls schema](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Annars kan du kontakta IT-administratören för att kontrol lera att det här underhållet inte är en schemalagd händelse. Om du vill återuppta SQL Data Warehouse följer du stegen som beskrivs [här](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Kontrol lera brand Väggs inställningarna

SQL Database Warehouse kommunicerar via port 1433.   Om du försöker ansluta inifrån ett företagsnätverk kan det hända att utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-Server om inte din IT-avdelning öppnar port 1433. Ytterligare information om brand Väggs konfigurationer finns [här](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Kontrol lera inställningarna för VNet/service-slutpunkt

Om du får fel 40914 och 40615, se [fel beskrivning och lösning här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Sök efter de senaste driv rutinerna

### <a name="software"></a>Programvara

Kontrol lera att du använder de senaste verktygen för att ansluta till din SQL Data Warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Drivrutiner

Kontrol lera att du använder de senaste driv rutins versionerna.  Om du använder en äldre version av driv rutinerna kan det leda till oväntade beteenden eftersom de äldre driv rutinerna kanske inte stöder nya funktioner.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Kontrol lera anslutnings strängen

Kontrol lera att anslutnings strängarna är korrekt inställda.  Nedan visas några exempel.  Du hittar ytterligare information kring [anslutnings strängarna här](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

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

## <a name="intermittent-connection-issues"></a>Tillfälliga anslutnings problem

Kontrol lera om du är hårt belastad på servern med ett stort antal köade begär Anden. Du kan behöva skala upp data lagret för ytterligare resurser.

## <a name="common-error-messages"></a>Vanliga felmeddelanden

Fel 40914 och 40615, se [fel beskrivningen och lösningen här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Har du fortfarande problem med anslutningen?
Skapa ett [support ärende](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) så att teknik teamet kan stödja dig.
