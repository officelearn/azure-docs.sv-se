---
title: Felsöka anslutning
description: Felsöka anslutningen i SQL Analytics.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 003366a6d88e018090475b6fb22d9042a97af823
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192259"
---
# <a name="troubleshooting-connectivity-issues"></a>Felsöka anslutnings problem

Den här artikeln innehåller vanliga fel söknings tekniker för att ansluta till SQL Analytics-databasen.
- [Kontrol lera tjänstens tillgänglighet](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Kontrollera om tjänsten är pausad eller håller på att skalas om](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Kontrollera brandväggsinställningarna](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Kontrollera inställningarna för det virtuella nätverket/tjänstslutpunkten](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Kontrollera om det finns nya drivrutiner](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Kontrollera anslutningssträngen](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Tillfälliga anslutningsproblem](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Vanliga felmeddelanden](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Kontrol lera tjänstens tillgänglighet

Kontrol lera om tjänsten är tillgänglig. I Azure Portal går du till SQL Analytics-databasen som du försöker ansluta. Klicka på **diagnostisera och lös problem**i den vänstra panelen i innehålls förteckningen.

![Välj resurs hälsa](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

SQL Analytics-status visas här. Om tjänsten inte visas som **tillgänglig**kontrollerar du ytterligare steg.

![Tjänsten är tillgänglig](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Om din resurs hälsa visar att SQL Analytics-instansen har pausats eller skalats, följer du anvisningarna för att återuppta instansen.

![tjänsten har pausats](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) ytterligare information om Resource Health finns här.

## <a name="check-for-paused-or-scaling-operation"></a>Sök efter pausad eller skalnings åtgärd

Kontrol lera portalen för att se om SQL Analytics-instansen har pausats eller skalats.

![Tjänsten har pausats](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Om du ser att tjänsten har pausats eller skalas kan du kontrol lera att den inte finns under ditt underhålls schema. I portalen för din SQL Analytics- *Översikt*visas schemat för valt underhåll.

![Översikt över underhålls schema](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Annars kan du kontakta IT-administratören för att kontrol lera att det här underhållet inte är en schemalagd händelse. Om du vill återuppta SQL Analytics-instansen följer du stegen som beskrivs [här](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Kontrol lera brand Väggs inställningarna

SQL Analytics-databasen kommunicerar via port 1433.   Om du försöker ansluta inifrån ett företags nätverk kanske utgående trafik via port 1433 inte tillåts av nätverkets brand vägg. I så fall kan du inte ansluta till Azure SQL Database-servern såvida inte IT-avdelningen öppnar port 1433. Ytterligare information om brand Väggs konfigurationer finns [här](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Kontrol lera inställningarna för VNet/service-slutpunkt

Om du får fel 40914 och 40615, se [fel beskrivning och lösning här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Sök efter de senaste driv rutinerna

### <a name="software"></a>Programvara

Kontrol lera att du använder de senaste verktygen för att ansluta till SQL Analytics-databasen:

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

Se till att anslutningssträngarna är inställda på rätt sätt.  Nedan visas några exempel.  Du kan läsa mer om [anslutningssträngar här](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

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

Kontrollera om servern är hårt belastad med ett stort antal köade förfrågningar. Du kan behöva skala upp SQL Analytics-instansen för ytterligare resurser.

## <a name="common-error-messages"></a>Vanliga felmeddelanden

Fel 40914 och 40615, se [fel beskrivningen och lösningen här](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Har du fortfarande problem med anslutningen?
Skapa ett [support ärende](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) så att teknik teamet kan stödja dig.
