---
title: Hantera autentiseringsuppgifter i klient biblioteket för Elastic Database | Microsoft Docs
description: Ange rätt nivå för autentiseringsuppgifter, admin till skrivskyddad, för Elastic Database-appar
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
ms.openlocfilehash: d89e83092775828016c2c47a96164319f5474c1e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568427"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Autentiseringsuppgifter som används för att komma åt Elastic Database klient biblioteket

I [Elastic Database klient biblioteket](sql-database-elastic-database-client-library.md) används tre olika typer av autentiseringsuppgifter för att få åtkomst till [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md). Beroende på behov använder du autentiseringsuppgiften med den lägsta möjliga åtkomst nivån.

* **Autentiseringsuppgifter för hantering**: för att skapa eller ändra en Shard Map Manager. (Se [ord](sql-database-elastic-scale-glossary.md)listan.)
* **Autentiseringsuppgifter för åtkomst**: för att få åtkomst till en befintlig Shard Map Manager för att hämta information om Shards.
* **Autentiseringsuppgifter för anslutning**: för att ansluta till Shards.

Se även [Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Om autentiseringsuppgifter för hantering

Hanterings autentiseringsuppgifter används för att skapa ett **ShardMapManager** -objekt ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) för program som manipulerar Shard-kartor. (Se till exempel [lägga till en Shard med hjälp av Elastic Database verktyg](sql-database-elastic-scale-add-a-shard.md) och [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md)). Användaren av klient biblioteket för elastisk skalning skapar SQL-användare och SQL-inloggningar och ser till att varje beviljas Läs-/Skriv behörighet för den globala Shard Map-databasen och alla Shard-databaser. Dessa autentiseringsuppgifter används för att underhålla den globala Shard-kartan och de lokala Shard-kartorna när ändringar i Shard-mappningen utförs. Använd till exempel hanterings uppgifter för att skapa Shard Map Manager-objektet (med **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Variabeln **smmAdminConnectionString** är en anslutnings sträng som innehåller autentiseringsuppgifterna för hantering. Användar-ID och lösen ord ger Läs-/Skriv behörighet till både Shard Map-databas och enskilda Shards. Hanterings anslutnings strängen innehåller även Server namnet och databas namnet för att identifiera den globala Shard Map-databasen. Här är en typisk anslutnings sträng för det syftet:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Använd inte värden i formatet "username@server", istället för att använda värdet "username".  Detta beror på att autentiseringsuppgifterna måste fungera mot både Shard Map Manager-databasen och enskilda Shards, vilket kan finnas på olika servrar.

## <a name="access-credentials"></a>Autentiseringsuppgifter för åtkomst

När du skapar en Shard Map Manager i ett program som inte administrerar Shard Maps, använder du autentiseringsuppgifter som har Läs behörighet för den globala Shard-kartan. Informationen som hämtas från den globala Shard-kartan under dessa autentiseringsuppgifter används för [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md) och för att fylla i Shard Map-cachen på klienten. Autentiseringsuppgifterna tillhandahålls via samma anrops mönster till **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Observera användningen av **smmReadOnlyConnectionString** för att återspegla användningen av olika autentiseringsuppgifter för den här åtkomsten för användare som **inte är administratörer** : dessa autentiseringsuppgifter ska inte ge Skriv behörighet till den globala Shard-kartan.

## <a name="connection-credentials"></a>Autentiseringsuppgifter för anslutning

Ytterligare autentiseringsuppgifter krävs när du använder metoden **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) för att få åtkomst till en Shard som är associerad med en horisontell partitionering-nyckel. Dessa autentiseringsuppgifter måste ge behörighet för skrivskyddad åtkomst till de lokala Shard kart tabeller som finns på Shard. Detta krävs för att utföra anslutnings validering för data beroende routning på Shard. Det här kodfragmentet ger åtkomst till data i kontexten för data beroende Routning:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

I det här exemplet innehåller **smmUserConnectionString** anslutnings strängen för användarens autentiseringsuppgifter. För Azure SQL DB är här en typisk anslutnings sträng för användarautentiseringsuppgifter:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Använd inte värden i formatet "username@server" som med administratörs behörighet. Använd istället "username".  Observera också att anslutnings strängen inte innehåller ett server namn och ett databas namn. Det beror på att **OpenConnectionForKey** -anropet automatiskt dirigerar anslutningen till rätt Shard baserat på nyckeln. Därför anges inte databas namnet och Server namnet.

## <a name="see-also"></a>Se också

[Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md)

[Säkra din SQL Database](sql-database-security-overview.md)

[Elastic Database-jobb](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
