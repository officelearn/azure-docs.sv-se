---
title: Ansluta till Synapse SQL-pool
description: Anslut till SQL-poolen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 70dd1ae883ee1f44672dccb802c0e770d7676440
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619087"
---
# <a name="connect-to-synapse-sql-pool"></a>Ansluta till Synapse SQL-pool
Anslut till SQL-poolen.

## <a name="find-your-server-name"></a>Hitta servernamnet
Servernamnet i följande exempel är sqlpoolservername.database.windows.net. För att hitta det fullständigt kvalificerade servernamnet:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Klicka på **Azure Synapse Analytics**.
3. Klicka på den SQL-pool som du vill ansluta till.
4. Leta upp det fullständiga servernamnet.
   
    ![Fullständigt servernamn](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Drivrutiner och anslutningssträngar som stöds
SQL-poolen stöder [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)och [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). För att hitta den senaste versionen och dokumentationen, klicka på en av de föregående drivrutinerna. 

Om du vill generera anslutningssträngen automatiskt för drivrutinen som du använder från Azure-portalen klickar du på **anslutningssträngarna Visa databas** från föregående exempel. Nedan visas några exempel på hur en anslutningssträng kan se ut för respektive drivrutin.

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
SQL-poolen standardiserar vissa inställningar när anslutningen skapas och objekt skapas. Dessa inställningar kan inte åsidosättas. Det gäller exempelvis:

| Databasinställning | Värde |
|:--- |:--- |
| [ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx) |ON |
| [QUOTED_IDENTIFIERS](https://msdn.microsoft.com/library/ms174393.aspx) |ON |
| [DATEFORMAT](https://msdn.microsoft.com/library/ms189491.aspx) |mdy |
| [DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx) |7 |

## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter och ställer frågor med Visual Studio finns i [Fråga med Visual Studio](sql-data-warehouse-query-visual-studio.md). Mer information om autentiseringsalternativ finns i [Autentisering till Azure Synapse Analytics](sql-data-warehouse-authentication.md).

