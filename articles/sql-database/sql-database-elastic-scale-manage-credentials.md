---
title: Hantera autentiseringsuppgifter i klientbiblioteket för elastiska databaser | Microsoft Docs
description: Hur du ställer in rätt nivå av autentiseringsuppgifter för administratören att skrivskyddade för appar för elastisk databas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: e6c7d682d9b16266208baeeff14168b3da157251
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186631"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Autentiseringsuppgifter som används för att få åtkomst till klientbiblioteket för elastiska databaser

Den [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md) använder tre olika typer av autentiseringsuppgifter för åtkomst till den [karthanteraren](sql-database-elastic-scale-shard-map-management.md). Beroende på behov, använda autentiseringsuppgifter med den lägsta nivån av åtkomst som möjligt.

* **Autentiseringsuppgifter för hantering**: för att skapa eller ändra en karthanteraren. (Se den [ordlista](sql-database-elastic-scale-glossary.md).)
* **Autentiseringsuppgifter för åtkomst**: åtkomst till en befintlig karthanteraren för att få information om shards.
* **Autentiseringsuppgifter för anslutning**: att ansluta till shards.

Se även [hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Om autentiseringsuppgifter för hantering

Autentiseringsuppgifter för hantering som används för att skapa en **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager))-objekt för program som manipulerar fragmentkartor. (Till exempel se [att lägga till en shard med elastiska Databasverktyg](sql-database-elastic-scale-add-a-shard.md) och [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)). Användaren av Elastiskt skalat klientbibliotek skapar SQL-användare och SQL-inloggningar och ser till var och en har beviljats Läs/Skriv-behörigheter på fragmentkartan kartan databasen och alla fragment databaser samt. Dessa autentiseringsuppgifter används för att underhålla globala fragmentkartan och lokala shard-kartor när ändringar i fragmentkartan utförs. Till exempel använda autentiseringsuppgifter för hantering för att skapa fragment manager Kartobjekt (med hjälp av **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Variabeln **smmAdminConnectionString** är en anslutningssträng som innehåller autentiseringsuppgifterna för hantering. Ger läsåtkomst till både fragment kartan databas och enskilda shards användar-ID och lösenord. Anslutningssträngen management innehåller även servernamnet och databasnamnet för att identifiera fragmentkartan kartan databasen. Här är en typisk anslutningssträng för detta ändamål:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Använd inte värden i form av ”username@server” – i stället använda värdet ”användarnamn”.  Det beror på att autentiseringsuppgifter måste arbeta mot både databas och enskilda shards, vilket kan vara på olika servrar.

## <a name="access-credentials"></a>Autentiseringsuppgifter för åtkomst

När du skapar ett fragment kartan manager i ett program som inte administrerar fragmentkartor Använd autentiseringsuppgifter som har skrivskyddad behörighet på den globala fragmentkartan. Den information som hämtas från global fragmentkartan under dessa autentiseringsuppgifter används för [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) och att fylla i fragment kartan cachen på klienten. Autentiseringsuppgifterna som tillhandahålls via samma mönster för anrop till **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Observera användningen av den **smmReadOnlyConnectionString** så att användningen av olika autentiseringsuppgifter för den här åtkomsten för **icke-administratörer** användare: de här autentiseringsuppgifterna ska inte ger skrivbehörighet på global fragmentkartan.

## <a name="connection-credentials"></a>Autentiseringsuppgifter för anslutning

Ytterligare autentiseringsuppgifter behövs när du använder den **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) metoden för att få åtkomst till en shard som är associerade med en shardingnyckel. Dessa autentiseringsuppgifter måste du ange behörigheter för skrivskyddad åtkomst till lokala fragment kartan tabeller som finns på fragmentet. Detta behövs för att utföra anslutningsverifiering för databeroende routning på fragmentet. Det här kodfragmentet tillåter åtkomst till data i samband med databeroende routning:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

I det här exemplet **smmUserConnectionString** innehåller anslutningssträngen för autentiseringsuppgifter. Här är en typisk anslutningssträng för användarens autentiseringsuppgifter för Azure SQL DB:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Som med administratörsautentiseringsuppgifter och Använd inte värden i form av ”username@server”. Använd ”användarnamn” i stället bara.  Observera också att anslutningssträngen inte innehåller en servernamnet och databasnamnet. Det beror på den **OpenConnectionForKey** anrop dirigerar automatiskt anslutningen till rätt fragment baserat på nyckeln. Därför tillhandahålls inte databasnamnet och servernamn.

## <a name="see-also"></a>Se också

[Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md)

[Säkra din SQL Database](sql-database-security-overview.md)

[Komma igång med Elastic Database-jobb](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
