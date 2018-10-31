---
title: Azure SQL elastisk skalning vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor om Azure SQL Database Elastic Scale.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 31501cb0cc66c9d8db646e3cf0c75cc3d8daf7e0
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242542"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Vanliga frågor (och svar FAQ) om elastiska Databasverktyg

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Om jag har en enda klient per shard och inga shardingnyckel, hur jag fylla shardingnyckel schemat information?

Info schemaobjekt används endast för att dela dokument scenarier. Om ett program är sin natur enda klient kan det kräver inte verktyget Dela sammanfoga och därför finns inget behov att fylla i information schemaobjekt.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Jag har etablerat en databas och jag har redan en Karthanteraren, hur registrerar jag mig den nya databasen som en shard?

Se  **[att lägga till en shard till ett program med hjälp av klientbiblioteket för elastiska databaser](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Hur mycket kostar elastiska Databasverktyg?

Med hjälp av klientbiblioteket för elastiska databaser leder inte till några kostnader. Kostnader tillkommer endast för Azure SQL-databaser som du använder för shards och Fragmentkartehanteraren, samt web/worker-roller som du etablerar för verktyget Dela sammanfoga.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Varför är min inloggningsinformation fungerar inte när jag lägger till en shard från en annan server?

Använd inte autentiseringsuppgifter i form av ”användar-ID =username@servername”, i stället använder bara ”användar-ID = username”.  Glöm inte att ”användarnamn” inloggningen har behörighet på fragmentet.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Behöver skapa en Karthanteraren och fylla i shards varje gång börjar jag mina program?

Nej – skapandet av Fragmentkartehanteraren (till exempel  **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) är en engångsåtgärd.  Ditt program bör använda anropet **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** på starttiden för programmet.  Det bör bara ett sådant anrop per programdomän.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Jag har frågor om hur du använder Verktyg för elastiska databaser, hur kan jag få dem besvarade?

Kontakta oss på den [Azure SQL Database-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>När jag får en databasanslutning med en shardingnyckel, men jag kan fortfarande köra frågor mot data för andra horisontell partitionering nycklar till samma fragment.  Är det avsiktligt?

Elastic Scale APIs ger en anslutning till rätt databas för din shardingnyckel men anger inte viktiga filtrering för horisontell partitionering.  Lägg till **där** satser i frågan för att begränsa omfånget till den angivna shardingnyckel om det behövs.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Kan jag använda en annan Azure Database-utgåva för varje fragment i min fragment set?

Ja, en shard är en individuell databas och en shard kan därför vara en Premium-versionen medan en annan vara en Standard-utgåva. Dessutom kan utgåvan av ett fragment skala upp eller ned flera gånger under livslängden för fragmentet.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Varken etablera för delnings-verktyget (eller ta bort) en databas under en delad tunnel eller merge-åtgärd?

Nej. För **dela** åtgärder, måldatabasen måste finnas med lämpligt schema och registreras med Fragmentkartehanteraren.  För **merge** åtgärder, måste du ta bort fragmentet från fragmentkartehanteraren och sedan ta bort databasen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]