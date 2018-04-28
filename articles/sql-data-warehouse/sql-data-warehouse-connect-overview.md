---
title: Anslut till Azure SQL Data Warehouse | Microsoft Docs
description: Anslut till Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2aff8379667191017830bf441ee101e773d2dfc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Anslut till Azure SQL Data Warehouse
Anslut till Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Hitta servernamnet
Servernamnet i följande exempel är samplesvr.database.windows.net. För att hitta det fullständigt kvalificerade servernamnet:

1. Gå till [Azure Portal][Azure portal].
2. Klicka på **SQL-informationslager**.
3. Klicka på det informationslager som du vill ansluta till.
4. Leta upp det fullständiga servernamnet.
   
    ![Fullständigt servernamn][1]

## <a name="supported-drivers-and-connection-strings"></a>Drivrutiner och anslutningssträngar som stöds
Azure SQL Data Warehouse stöder [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] och [JDBC][JDBC]. För att hitta den senaste versionen och dokumentation, klickar du på någon av de föregående drivrutinerna. För att automatiskt generera anslutningssträngen för den drivrutin som du använder från Azure portal, klicka på den **visa databasanslutningssträngar** från föregående exempel. Nedan visas några exempel på hur en anslutningssträng kan se ut för respektive drivrutin.

> [!NOTE]
> Det kan vara bra att ange en tidsgräns på 300 sekunder för anslutningen så att den inte bryts vid korta perioder av inaktivitet.
> 
> 

### <a name="adonet-connection-string-example"></a>Exempel på ADO.NET-anslutningssträng
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exempel på ODBC-anslutningssträng
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exempel på PHP-anslutningssträng
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exempel på JDBC-anslutningssträng
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Inställningar för anslutning
SQL Data Warehouse standardiserar några inställningar under anslutning och objektskapande. Dessa inställningar kan inte åsidosättas. Det gäller exempelvis:

| Databasinställning | Värde |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ON |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ON |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter och ställer frågor med Visual Studio finns i [Fråga med Visual Studio][Query with Visual Studio]. Läs mer om autentiseringsalternativ i [Autentisera till Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


