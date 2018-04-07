---
title: Azure SQL elastisk skalbarhet vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor om Azure SQL Database-elastisk skalbarhet.
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 57a5ef07aa8ca6d35085f9b2425cbc1c08c23b36
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="elastic-database-tools-faq"></a>Elastisk Databasverktyg vanliga frågor och svar
#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Om jag har en enskild klient per Fragmentera och ingen nyckel för horisontell partitionering, hur jag fylla nyckeln horisontell partitionering schemat information?
Info schemaobjekt används bara för att dela dokument scenarier. Om ett program är natur single-klient, behöver inte dela sammanfoga verktyget och därför det finns ingen anledning att fylla i info schemaobjekt.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Jag har etablerats en databas och jag har redan en Fragmentera kartan Manager, hur registrerar jag den nya databasen som en Fragmentera?
Se  **[att lägga till en Fragmentera i ett program med hjälp av klientbiblioteket för elastisk databas](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Hur mycket kostar elastisk Databasverktyg?
Med hjälp av klientbiblioteket för elastisk databas medför inte några kostnader. Kostnader påförs bara för Azure SQL-databaser som du använder för delar och Fragmentera kartan Manager samt web/worker-roller som du etablerar för verktyget Dela sammanfoga.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Varför fungerar inte mina autentiseringsuppgifter när jag lägger till en Fragmentera från en annan server?
Använd inte autentiseringsuppgifterna i formatet ”användar-ID =username@servername”, i stället helt enkelt använda ”användar-ID = username”.  Glöm inte att ”användarnamn” inloggningen har behörighet för Fragmentera.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Behöver skapa en karta Fragmentera-hanteraren och fylla shards varje gång startas Mina program?
Nej – skapandet av Fragmentera kartan Manager (till exempel  **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) är en engångsåtgärd.  Ditt program bör använda anropet **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** vid starttiden för programmet.  Det bör bara ett sådant anrop per domän.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Jag har frågor om hur du använder elastisk Databasverktyg, hur skaffar jag dem besvarade?
Kontakta oss på den [Azure SQL Database-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>När jag får en databasanslutning med en nyckel för horisontell partitionering kan jag fortfarande fråga data för andra horisontell partitionering nycklar på samma Fragmentera.  Är detta?
Elastic Scale API: er ger dig en anslutning till rätt databas för horisontell partitionering-nyckel, men innehåller delning viktiga filtrering.  Lägg till **där** satser till din frågan för att begränsa omfånget till den angivna horisontell partitionering nyckeln om det behövs.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Kan jag använda en annan utgåva för Azure-databas för varje Fragmentera i min Fragmentera uppsättningen?
Ja, en Fragmentera är en individuell databas och därmed en Fragmentera kan vara en Premium edition medan en annan vara Standard edition. Dessutom kan utgåvan av ett Fragmentera skala upp eller ned flera gånger under Fragmentera livstid.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Innehåller delning Merge tool etablera (eller ta bort) en databas under en delade eller merge-åtgärd?
Nej. För **dela** åtgärder, måldatabasen måste finnas med lämpligt schema och registreras med hanteraren Fragmentera kartan.  För **merge** åtgärder, du måste ta bort Fragmentera från Fragmentera kartan manager och sedan ta bort databasen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

