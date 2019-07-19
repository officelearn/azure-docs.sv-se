---
title: Azure SQL Database-Läs frågor om repliker | Microsoft Docs
description: Azure SQL Database ger möjlighet att belastningsutjämna skrivskyddade arbets belastningar med hjälp av kapaciteten hos skrivskyddade repliker – som kallas Läs utskalning.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: 3d9da312f86128dc738b367371016d03da2c1629
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228237"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Använd skrivskyddade repliker för att belastningsutjämna skrivskyddade arbets belastningar för frågor

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Som en del av [arkitekturen för hög tillgänglighet](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)etablerades varje databas i Premium-, affärskritisk-eller hög nivå tjänst nivå automatiskt med en primär replik och flera sekundära repliker. De sekundära replikerna är etablerade med samma beräknings storlek som den primära repliken. Med funktionen **Läs skalbarhet** kan du belastningsutjämna SQL Database skrivskyddade arbets belastningar med kapaciteten hos en av de skrivskyddade replikerna i stället för att dela Skriv-och skriv repliken. Det här gör att den skrivskyddade arbetsbelastningen isoleras från den huvudsakliga läs/skriv-arbetsbelastningen och påverkar inte dess prestanda. Funktionen är avsedd för program som innehåller logiskt åtskilda skrivskyddade arbets belastningar, till exempel analys. De kan få prestanda för delar med den här extra kapaciteten utan extra kostnad.

Följande diagram illustrerar det med hjälp av en Affärskritisk databas.

![Skrivskyddade repliker](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Funktionen Läs skalning är aktive rad som standard på nya Premium-, Affärskritisk-och storskaliga databaser. Om SQL-anslutningssträngen har kon figurer `ApplicationIntent=ReadOnly`ATS med kommer programmet att omdirigeras av gatewayen till en skrivskyddad replik av databasen. Information om hur du använder `ApplicationIntent` egenskapen finns i [Ange program avsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Om du vill säkerställa att programmet ansluter till den primära repliken oavsett `ApplicationIntent` inställningen i SQL-anslutningssträngen, måste du explicit inaktivera Läs-och utskalning när du skapar databasen eller när du ändrar konfigurationen. Om du till exempel uppgraderar din databas från standard-eller Generell användning-nivån till Premium, Affärskritisk eller nivån på den storskaliga nivån och vill se till att alla dina anslutningar fortsätter att gå till den primära repliken inaktiverar du Läs-och utskalning. Mer information om hur du inaktiverar det finns i [Aktivera och inaktivera Läs skalning](#enable-and-disable-read-scale-out).

> [!NOTE]
> Fråga efter data lager, utökade händelser, SQL profiler och gransknings funktioner stöds inte på skrivskyddade repliker. 

## <a name="data-consistency"></a>Datakonsekvens

En av fördelarna med repliker är att replikerna alltid är i ett transaktions konsekvent tillstånd, men vid olika tidpunkter kan det uppstå en liten fördröjning mellan olika repliker. Läs skalbarhet stöder konsekvens på sessionsläge. Det innebär att om den skrivskyddade sessionen återansluter efter ett anslutnings fel på grund av otillgänglig replik, kan den omdirigeras till en replik som inte är 100% uppdaterad med den skrivskyddade repliken. Om ett program skriver data med hjälp av en Read-Write-session och läser den direkt med en skrivskyddad session, är det möjligt att de senaste uppdateringarna inte visas direkt på repliken. Svars tiden orsakas av en åtgärd för att göra en asynkron transaktions logg.

> [!NOTE]
> Replikeringsfördröjning i regionen är låga och den här situationen är sällsynt.

## <a name="connect-to-a-read-only-replica"></a>Anslut till en skrivskyddad replik

När du aktiverar Läs skalning för en databas, anger det `ApplicationIntent` alternativ i anslutnings strängen som klienten tillhandahåller om anslutningen dirigeras till Skriv repliken eller till en skrivskyddad replik. Om `ApplicationIntent` värdet är `ReadWrite` (standardvärdet) dirigeras anslutningen till databasens Skriv-och skriv replik. Detta är identiskt med det befintliga beteendet. Om värdet är `ReadOnly`dirigeras anslutningen till en skrivskyddad replik. `ApplicationIntent`

Följande anslutnings sträng ansluter till exempel klienten till en skrivskyddad replik (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Någon av följande anslutnings strängar ansluter klienten till en skrivskyddad replik (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Kontrol lera att en anslutning är till en skrivskyddad replik

Du kan kontrol lera om du är ansluten till en skrivskyddad replik genom att köra följande fråga. Den returnerar READ_ONLY när du är ansluten till en skrivskyddad replik.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Endast en av AlwaysON-replikerna kan nås av de skrivskyddade sessionerna.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Övervakning och fel sökning av skrivskyddad replik

När du är ansluten till en skrivskyddad replik kan du komma åt prestanda måtten med hjälp `sys.dm_db_resource_stats` av DMV. Använd `sys.dm_exec_query_stats`- `sys.dm_exec_query_plan` och DMV:erförattfååtkomsttillstatistikförfrågeplan.`sys.dm_exec_sql_text`

> [!NOTE]
> DMV `sys.resource_stats` i den logiska huvud databasen returnerar CPU-användning och lagrings data för den primära repliken.


## <a name="enable-and-disable-read-scale-out"></a>Aktivera och inaktivera Läs skalning

Läs skalning är aktiverat som standard på Premium-, Affärskritisk-och Scale service-nivåer. Läs-och utskalning kan inte aktive ras på tjänst nivåerna Basic, standard eller Generell användning. Läs skalning är inaktiverat automatiskt på storskaliga databaser som kon figurer ATS med 0 repliker. 

Du kan inaktivera och återaktivera Läs skala på enskilda databaser och elastiska pooler i Premium-eller Affärskritisk tjänst nivå med hjälp av följande metoder.

> [!NOTE]
> Möjligheten att inaktivera Läs skalning tillhandahålls för bakåtkompatibilitet.

### <a name="azure-portal"></a>Azure Portal

Du kan hantera inställningen för Läs skalning på bladet **Konfigurera** databas. 

### <a name="powershell"></a>PowerShell

Hantering av Läs-och utskalning i Azure PowerShell kräver Azure PowerShells versionen december 2016 eller senare. Den senaste PowerShell-versionen finns i [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Du kan inaktivera eller återaktivera Läs-och utskalning i Azure PowerShell genom att anropa cmdleten [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) och skicka in det önskade värdet – `Enabled` eller `Disabled` --för `-ReadScale` parametern. 

Så här inaktiverar du Läs skala ut i en befintlig databas (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Så här inaktiverar du Läs skala i en ny databas (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Så här återaktiverar du Läs skalan för en befintlig databas (ersätter objekten inom vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST-API

Om du vill skapa en databas med Läs skala inaktive rad, eller ändra inställningen för en befintlig databas, använder du följande metod med `readScale` egenskapen inställd på `Enabled` eller `Disabled` som i nedanstående exempel förfrågan.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Mer information finns i [databaser-skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Använda TempDB på skrivskyddad replik

TempDB-databasen replikeras inte till skrivskyddade repliker. Varje replik har en egen version av TempDB-databasen som skapas när repliken skapas. Det säkerställer att TempDB kan uppdateras och kan ändras under frågekörningen. Om din skrivskyddade arbets belastning är beroende av att använda TempDB-objekt bör du skapa dessa objekt som en del av frågans skript. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Använda Läs-och utskalning med geo-replikerade databaser

Om du använder Läs utskalning för att belastningsutjämna skrivskyddade arbets belastningar i en databas som är geo-replikerad (till exempel som medlem i en grupp för växling vid fel) ser du till att Läs utskalning är aktiverat på både den primära och den geo-replikerade sekundära databasen. Med den här konfigurationen kan du se till att samma belastnings Utjämnings upplevelse fortsätter när programmet ansluter till den nya primära efter redundansväxlingen. Om du ansluter till den geo-replikerade sekundära databasen med Läs skala aktive rad dirigeras sessionerna `ApplicationIntent=ReadOnly` till en av replikerna på samma sätt som vi dirigerar anslutningar till den primära databasen.  Sessionerna utan `ApplicationIntent=ReadOnly` kommer att dirigeras till den primära repliken av den geo-replikerade sekundära, som också är skrivskyddad. Eftersom den geo-replikerade sekundära databasen har en annan slut punkt än den primära databasen krävdes `ApplicationIntent=ReadOnly`tidigare för att komma åt den sekundära databasen. För att säkerställa bakåtkompatibilitet, `sys.geo_replication_links` visar `secondary_allow_connections=2` DMV (alla klient anslutningar tillåts).

> [!NOTE]
> Resursallokering (Round-Robin) och annan belastningsutjämnad routning mellan de lokala replikerna av den sekundära databasen stöds inte.

## <a name="next-steps"></a>Nästa steg

- Mer information om SQL Database skalnings erbjudande finns i [storskalig Service Tier](./sql-database-service-tier-hyperscale.md).
