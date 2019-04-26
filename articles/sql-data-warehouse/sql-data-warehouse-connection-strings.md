---
title: Anslutningssträngar för Azure SQL Data Warehouse | Microsoft Docs
description: Anslutningssträngar för SQL Data Warehouse
services: sql-data-warehouse
author: rockboyfor
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 057e412857feb39069c55bb9905c83dcea7cbd4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402657"
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Anslutningssträngar för Azure SQL Data Warehouse
Du kan ansluta till SQL Data Warehouse med flera olika programprotokoll som, [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP] [ PHP] och [JDBC][JDBC]. Nedan visas några exempel på anslutningssträngar för varje protokoll.  Du kan också använda Azure-portalen för att bygga anslutningssträngen.  För att bygga anslutningssträngen med hjälp av Azure-portalen, navigera till ditt databas-blad under *Essentials* klickar du på *visa databasanslutningssträngar*.

## <a name="sample-adonet-connection-string"></a>Exemplet ADO.NET-anslutningssträng
```csharp
Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Exemplet ODBC-anslutningssträng
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Exempel PHP-anslutningssträng
```PHP
Server: {your_server}.database.chinacloudapi.cn,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.chinacloudapi.cn,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.chinacloudapi.cn,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Exemplet JDBC-anslutningssträng
```Java
jdbc:sqlserver://yourserver.database.chinacloudapi.cn:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.chinacloudapi.cn;loginTimeout=30;
```

> [!NOTE]
> Överväg att ange en tidsgräns på 300 sekunder för att tillåta anslutning till bryts vid korta perioder av inaktivitet.
> 
> 

## <a name="next-steps"></a>Nästa steg
Om du vill börja ställa frågor till ditt data warehouse med Visual Studio och andra program, se [fråga med Visual Studio][Query with Visual Studio].

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
<!-- Update_Description: update meta properties, wording update -->