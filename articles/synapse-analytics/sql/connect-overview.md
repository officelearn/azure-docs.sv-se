---
title: Ansluta till Synapse SQL
description: Anslut dig till Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 9748b0354ce09752296fb7d736e09af716f19351
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424610"
---
# <a name="connect-to-synapse-sql"></a>Ansluta till Synapse SQL
Anslut dig till Synapse SQL-funktionen i Azure Synapse Analytics.

## <a name="supported-tools-for-sql-on-demand-preview"></a>Verktyg som stöds för SQL på begäran (förhandsgranskning)

Det verktyg som stöds fullt ut är Azure Data Studio (förhandsversion).

SQL Server Management Studio stöds delvis från version 18.4. Det finns begränsade funktioner som anslutning och frågor.

## <a name="find-your-server-name"></a>Hitta servernamnet

Servernamnet för SQL Pool i följande exempel är: showdemoweu.sql.azuresynapse.net.
Servernamnet för SQL on-demand i följande exempel är: showdemoweu-ondemand.sql.azuresynapse.net.

För att hitta det fullständigt kvalificerade servernamnet:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Klicka på **Synapse arbetsytor**.
3. Klicka på arbetsytan som du vill ansluta till.
4. Gå till översikt.
5. Leta upp det fullständiga servernamnet.

## <a name="sql-pool"></a>**SQL-pool**

![Fullständigt servernamn](./media/connect-overview/server-connect-example.png)

## <a name="sql-on-demand"></a>**SQL på begäran**

![Fullständigt servernamn SQL på begäran](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Drivrutiner och anslutningssträngar som stöds
Synapse SQL stöder [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)och [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). För att hitta den senaste versionen och dokumentationen, klicka på en av de föregående drivrutinerna. Om du vill generera anslutningssträngen automatiskt för drivrutinen som du använder från Azure-portalen klickar du på **anslutningssträngarna Visa databas** från föregående exempel. Nedan visas några exempel på hur en anslutningssträng kan se ut för respektive drivrutin.

> [!NOTE]
> Det kan vara bra att ange en tidsgräns på 300 sekunder för anslutningen så att den inte bryts vid korta perioder av inaktivitet.

### <a name="adonet-connection-string-example"></a>Exempel på ADO.NET-anslutningssträng

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exempel på ODBC-anslutningssträng

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exempel på PHP-anslutningssträng

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exempel på JDBC-anslutningssträng

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Inställningar för anslutning
Synapse SQL standardiserar vissa inställningar när anslutningen och objekt skapas. Dessa inställningar kan inte åsidosättas. Det gäller exempelvis:

| Databasinställning | Värde |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="recommendations"></a>Rekommendationer

För att köra **SQL on-demand-frågor** är rekommenderade verktyg [Azure Data Studio](get-started-azure-data-studio.md) och Azure Synapse Studio.

## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter och ställer frågor med Visual Studio finns i [Fråga med Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Mer information om autentiseringsalternativ finns i [Autentisering till Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).