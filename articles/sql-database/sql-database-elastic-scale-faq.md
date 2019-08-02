---
title: Vanliga frågor och svar om elastiska Azure SQL | Microsoft Docs
description: Vanliga frågor om Azure SQL Database elastisk skalning.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 2b101aebd048b94ac95e1dba0f6504446d6d6803
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568433"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Elastic Database-verktyg

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Om jag har en enda klient per Shard och ingen horisontell partitionering-nyckel, Hur fyller jag i horisontell partitionering-nyckeln för schema informationen

Objektet schema information används endast för att dela upp sammanfognings scenarier. Om ett program är enskilt med en enda klient behöver det inte verktyget Dela sammanslagning och därför behöver du inte fylla i schema information-objektet.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Jag har skapat en databas och har redan en Shard Map Manager, Hur registrerar jag den här nya databasen som en Shard

Se [lägga till en Shard till ett program med hjälp av klient biblioteket för Elastic Database](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Hur mycket kostar elastiska databas verktyg

Det kostar inget att använda klient biblioteket för Elastic Database. Kostnader kan bara användas för de Azure SQL-databaser som du använder för Shards och Shard Map Manager, samt de webb-och arbets roller som du etablerar för verktyget Dela sammanslagning.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Varför fungerar inte mina autentiseringsuppgifter när jag lägger till en Shard från en annan server

Använd inte autentiseringsuppgifter i formatet "User ID =username@servername", och Använd i stället bara "User ID = username".  Se också till att "username"-inloggningen har behörighet för Shard.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Måste jag skapa en Shard Map Manager och fylla i Shards varje gång jag startar mina program

Nej – skapandet av Shard Map Manager (till exempel [ShardMapManagerFactory. CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) är en engångs åtgärd.  Programmet bör använda anropet [ShardMapManagerFactory. TryGetSqlShardMapManager ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) vid programmets start tid.  Det ska bara finnas ett sådant anrop per program domän.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Jag har frågor om att använda elastiska databas verktyg, hur får jag svar på dem

Kontakta oss på [SQL Database-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>När jag får en databas anslutning med en horisontell partitionering-nyckel kan jag fortfarande fråga efter data för andra horisontell partitionering-nycklar på samma Shard.  Är detta enligt design

API: erna för elastisk skalning ger dig en anslutning till rätt databas för din horisontell partitionering-nyckel, men ger inte horisontell partitionering-nyckel filtrering.  Lägg till **WHERE** -satser i frågan för att begränsa omfattningen till den angivna horisontell partitionering-nyckeln, om det behövs.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Kan jag använda en annan SQL Database-utgåva för varje Shard i min Shard-uppsättning

Ja, en Shard är en enskild databas och därför kan en Shard vara en Premium version, medan en annan är en standard version. Dessutom kan versionen av en Shard skalas upp eller ned flera gånger under Shard livs längd.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Etablerar verktyget för delad sammanslagning (eller tar bort) en databas under en delnings-eller sammanslagnings åtgärd

Nej. För **Split** -åtgärder måste mål databasen finnas med lämpligt schema och registreras med Shard Map Manager.  För **sammanfognings** åtgärder måste du ta bort Shard från Shard Map Manager och sedan ta bort databasen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]