---
title: "Hantera autentiseringsuppgifter i klientbibliotek för elastisk databas | Microsoft Docs"
description: "Hur du anger rätt nivå av autentiseringsuppgifter, admin till skrivskyddat läge, för appar för elastisk databas"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 72e0edaf-795e-4856-84a5-6594f735fb7e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 46908be2846062a0520d21e06db3091a4d711b0b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Autentiseringsuppgifter som används för att komma åt klientbibliotek för elastisk databas
Den [klientbibliotek för elastisk databas](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) använder tre olika typer av autentiseringsuppgifter för åtkomst till den [Fragmentera kartan manager](sql-database-elastic-scale-shard-map-management.md). Beroende på behov, använda autentiseringsuppgifter med den lägsta nivån av åtkomst som möjligt.

* **Hantering av autentiseringsuppgifter**: för att skapa eller ändra en Fragmentera kartan manager. (Se den [ordlista](sql-database-elastic-scale-glossary.md).) 
* **Åtkomst till autentiseringsuppgifterna för**: åtkomst till en befintlig Fragmentera kartan manager för att få information om hur du delar.
* **Autentiseringsuppgifter för anslutning**: att ansluta till delar. 

Se även [hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md). 

## <a name="about-management-credentials"></a>Om hantering av autentiseringsuppgifter
Hantering av autentiseringsuppgifter används för att skapa en [ **ShardMapManager** ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) objekt för program som hanterar Fragmentera maps. (Till exempel se [att lägga till en Fragmentera med elastiska Databasverktyg](sql-database-elastic-scale-add-a-shard.md) och [Data beroende routning](sql-database-elastic-scale-data-dependent-routing.md)) användaren av klientbiblioteket elastisk skalbarhet skapar SQL-användare och SQL-inloggningar och ser till att varje beviljas Läs-/ skrivbehörigheter på globala Fragmentera kartan databasen och alla Fragmentera databaser samt. Dessa autentiseringsuppgifter används för att underhålla globala Fragmentera kartan och lokala Fragmentera maps när ändringar i kartan Fragmentera utförs. Till exempel använda autentiseringsuppgifter hantering för att skapa Fragmentera kartan manager-objekt (med hjälp av [ **GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

Variabeln **smmAdminConnectionString** är en anslutningssträng som innehåller autentiseringsuppgifter för hantering. Användar-ID och lösenord ger läsning och skrivning åtkomst till både Fragmentera kartan databas och enskilda delar. Management-anslutningssträngen innehåller också servernamnet och databasnamnet att identifiera globala Fragmentera kartan databasen. Här är en typisk anslutningssträng för detta ändamål:

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Använd inte värden i form av ”username@server”, i stället använda värdet ”användarnamn”.  Det beror på att autentiseringsuppgifter måste arbeta mot både Fragmentera kartan manager-databasen och enskilda delar som kan vara på olika servrar.

## <a name="access-credentials"></a>Autentiseringsuppgifter
När du skapar en Fragmentera kartan manager i ett program som inte administrerar Fragmentera maps Använd autentiseringsuppgifter som har skrivskyddad behörighet på kartan globala Fragmentera. Informationen som hämtas från global Fragmentera kartan under dessa autentiseringsuppgifter används för [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md) och för att fylla i Fragmentera kartan cachen på klienten. Autentiseringsuppgifterna som tillhandahålls via samma mönster för anrop till **GetSqlShardMapManager** som ovan: 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Observera användningen av den **smmReadOnlyConnectionString** återspeglar användningen av olika autentiseringsuppgifter för åtkomst på uppdrag av **icke-administratörer** användare: autentiseringsuppgifterna bör inte ge skrivbehörighet för globala Fragmentera kartan. 

## <a name="connection-credentials"></a>Autentiseringsuppgifter för anslutning
Ytterligare autentiseringsuppgifter behövs när du använder den [ **OpenConnectionForKey** ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) metod för att komma åt en Fragmentera som är associerade med en nyckel för horisontell partitionering. Dessa autentiseringsuppgifter måste du ange behörigheter för skrivskyddad åtkomst till lokala Fragmentera kartan tabellerna som finns på Fragmentera. Detta behövs för att utföra anslutningsverifiering för omdirigering av data beroende på Fragmentera. Det här kodstycket tillåter åtkomst till data i samband med data beroende routning: 

    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

I det här exemplet **smmUserConnectionString** innehåller anslutningssträngen för autentiseringsuppgifter. Här är en typisk anslutningssträng för autentiseringsuppgifter för Azure SQL DB: 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Precis som med administratörsautentiseringsuppgifter, inte värden i form av ”username@server”. Använd ”användarnamn” istället bara.  Observera också att anslutningssträngen inte innehåller ett servernamn och databasnamn. Det beror på den **OpenConnectionForKey** anropet kommer automatiskt att dirigera anslutningen till rätt Fragmentera baserat på nyckeln. Därför tillhandahålls inte databasnamnet och namn på servern. 

## <a name="see-also"></a>Se även
[Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md)

[Säkra din SQL Database](sql-database-security-overview.md)

[Komma igång med jobb för elastisk databas](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

