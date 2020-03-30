---
title: Vanliga frågor om elastisk skala
description: Vanliga frågor och svar om Azure SQL Database Elastic Scale.
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
ms.openlocfilehash: 3eedfb1e9ec59fbe12ee94a65d3702a7ef8ca95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823646"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Elastiska databasverktyg vanliga frågor (FAQ)

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Om jag har en enda klient per shard och ingen sharding-nyckel, hur fyller jag i fragmenteringsnyckeln för schemainformationen

Schemainformationsobjektet används endast för att dela scenarier för koppling. Om ett program är en enda klient, kräver det inte verktyget Dela koppling och därför finns det ingen anledning att fylla i schemainformationsobjektet.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Jag har etablerat en databas och jag har redan en Shard Map Manager, hur registrerar jag den här nya databasen som en fragment

Se [Lägga till en shard i ett program med hjälp av klientbiblioteket](sql-database-elastic-scale-add-a-shard.md)för elastisk databas .

## <a name="how-much-do-elastic-database-tools-cost"></a>Hur mycket kostar elastiska databasverktyg

Att använda klientbiblioteket för elastisk databas medför inga kostnader. Kostnader ackumuleras endast för Azure SQL-databaser som du använder för shards och Shard Map Manager, samt webb/arbetsroller som du etablerar för verktyget Dela koppling.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Varför fungerar mina autentiseringsuppgifter inte när jag lägger till en fragment från en annan server

Använd inte autentiseringsuppgifter i form avusername@servername"User ID= ", istället helt enkelt använda "User ID = användarnamn".  Se också till att inloggningen för användarnamn har behörigheter för shard.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Måste jag skapa en Shard Map Manager och fylla shards varje gång jag startar mina program

Nej – skapandet av Shard Map Manager (till exempel [ShardMapManagerFactory.CreateSqlShardMapManager)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)är en engångsåtgärd.  Programmet ska använda anropet [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) vid starttiden för programmet.  Det bör bara ett sådant samtal per programdomän.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Jag har frågor om hur du använder elastiska databasverktyg, hur får jag dem besvarade

Vänligen nå ut till oss på [SQL Database forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>När jag får en databasanslutning med hjälp av en sharding-nyckel kan jag fortfarande fråga efter data efter andra sharding-nycklar på samma fragment.  Är detta avsiktligt

Api:erna för elastisk skala ger dig en anslutning till rätt databas för sharding-tangenten, men ger inte sharding-tangentfiltrering.  Lägg till **WHERE-satser** i frågan för att begränsa omfånget till den angivna sharding-nyckeln om det behövs.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Kan jag använda en annan SQL Database-utgåva för varje fragment i fragmentuppsättningen

Ja, en shard är en enskild databas, och därför kan en shard vara en Premium-utgåva medan en annan är en Standard-utgåva. Vidare kan utgåvan av en shard skalas upp eller ned flera gånger under shardns livstid.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Etablerar eller tar du bort en databas för att koppla samman en databas under en delnings- eller sammanfogningsåtgärd

Nej. För **delade** åtgärder måste måldatabasen finnas med rätt schema och registreras med Shard Map Manager.  För **kopplingsåtgärder** måste du ta bort fragmentet från fragmentkarthanteraren och sedan ta bort databasen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]