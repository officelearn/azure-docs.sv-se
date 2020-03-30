---
title: Hantera autentiseringsuppgifter i klientbiblioteket för elastisk databas
description: Så här ställer du in rätt nivå av autentiseringsuppgifter, admin till skrivskyddad, för elastiska databasappar
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91689a32a128584aade8081905e3d1aa3ecb0a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823568"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Autentiseringsuppgifter som används för att komma åt klientbiblioteket för elastisk databas

[Klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md) använder tre olika typer av autentiseringsuppgifter för att komma åt [fragmentkarthanteraren](sql-database-elastic-scale-shard-map-management.md). Beroende på behovet använder du autentiseringsuppgifterna med lägsta möjliga åtkomstnivå.

* **Hanteringsreferenser**: för att skapa eller manipulera en fragmentkarthanterare. (Se [ordlistan](sql-database-elastic-scale-glossary.md).)
* **Åtkomstautentiseringsuppgifter**: för att komma åt en befintlig shard map manager för att få information om shards.
* **Anslutningsuppgifter:** för att ansluta till shards.

Se även [Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Om autentiseringsuppgifter för hantering

Hanteringsautentiseringsuppgifter används för att skapa ett **ShardMapManager-objekt** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) för program som manipulerar fragmentmappningar. (Till exempel, [Lägga till en shard med hjälp av elastiska databasverktyg](sql-database-elastic-scale-add-a-shard.md) och [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)). Användaren av klientbiblioteket för elastisk skala skapar SQL-användare och SQL-inloggningar och ser till att var och en beviljas läs-/skrivbehörigheten på den globala fragmentkartdatabasen och alla fragmentdatabaser också. Dessa autentiseringsuppgifter används för att underhålla den globala fragmentkartan och de lokala fragmentmappningarna när ändringar i fragmentmappningen utförs. Använd till exempel hanteringsautentiseringsuppgifterna för att skapa objektet shard map manager (med **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Variabeln **smmAdminConnectionString** är en anslutningssträng som innehåller hanteringsautentiseringsuppgifterna. Användar-ID och lösenord ger läs-/skrivåtkomst till både fragmentkartdatabasen och enskilda shards. Anslutningssträngen för hantering innehåller även servernamnet och databasnamnet för att identifiera den globala fragmentkartdatabasen. Här är en typisk anslutning sträng för detta ändamål:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Använd inte värden i formusername@serverav " "– i stället bara använda "användarnamn" värde.  Detta beror på att autentiseringsuppgifter måste fungera mot både fragmentkarthanterarens databas och enskilda shards, som kan finnas på olika servrar.

## <a name="access-credentials"></a>Autentiseringsuppgifter för åtkomst

När du skapar en fragmentkarthanterare i ett program som inte administrerar fragmentmappningar använder du autentiseringsuppgifter som har skrivskyddade behörigheter på den globala fragmentkartan. Informationen som hämtas från den globala fragmentmappningen under dessa autentiseringsuppgifter används för [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) och för att fylla i fragmentkartcachen på klienten. Autentiseringsuppgifterna tillhandahålls via samma anropsmönster till **GetSqlShardMapManager:**

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Observera att användningen av **smmReadOnlyConnectionString** för att återspegla användningen av olika autentiseringsuppgifter för den här åtkomsten för användare som **inte är administratörer:** dessa autentiseringsuppgifter bör inte ge skrivbehörighet på den globala fragmentkartan.

## <a name="connection-credentials"></a>Autentiseringsuppgifter för anslutning

Ytterligare autentiseringsuppgifter behövs när du använder metoden **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) för att komma åt en shard som är associerad med en sharding-nyckel. Dessa autentiseringsuppgifter måste ge behörighet för skrivskyddad åtkomst till de lokala fragmentmappningstabeller som finns på fragmentet. Detta krävs för att utföra anslutningsverifiering för databeroende routning på shard. Det här kodavsnittet tillåter dataåtkomst i samband med databeroende routning:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

I det här exemplet innehåller **smmUserConnectionString** anslutningssträngen för användarautentiseringsuppgifterna. För Azure SQL DB är här en typisk anslutningssträng för användarautentiseringsuppgifter:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Som med administratörsautentiseringsuppgifterna ska du inteusername@serveranvända värden i form av " ". Använd istället "användarnamn".  Observera också att anslutningssträngen inte innehåller något servernamn och databasnamn. Det beror på att **OpenConnectionForKey-anropet** automatiskt dirigerar anslutningen till rätt fragment baserat på nyckeln. Därför anges inte databasnamnet och servernamnet.

## <a name="see-also"></a>Se även

[Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md)

[Säkra din SQL Database](sql-database-security-overview.md)

[Elastiska databasjobb](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
