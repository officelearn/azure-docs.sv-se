---
title: Läsa frågor på repliker
description: Azure SQL Database ger möjlighet att läsa skrivskyddade arbetsbelastningar med skrivskyddad arbetsbelastning med hjälp av kapaciteten för skrivskyddade repliker - som kallas Lässkalning.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206953"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Använd skrivskyddade repliker för att belastningsutjämna skrivskyddade frågearbetsbelastningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Som en del av [arkitekturen Hög tillgänglighet](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)etableras varje databas på tjänstnivån Premium och Affärskritisk automatiskt med en primär replik och flera sekundära repliker. De sekundära replikerna etableras med samma beräkningsstorlek som den primära repliken. Med **funktionen Lässkalning** kan du läsa sql-databas-arbetsbelastningar med skrivskyddad med hjälp av kapaciteten för en av skrivskyddade repliker i stället för att dela läs-skriv-repliken. Det här gör att den skrivskyddade arbetsbelastningen isoleras från den huvudsakliga läs/skriv-arbetsbelastningen och påverkar inte dess prestanda. Funktionen är avsedd för program som innehåller logiskt separerade skrivskyddade arbetsbelastningar, till exempel analyser. På premium- och affärskritiska tjänstnivåer kan program få prestandafördelar med den här ytterligare kapaciteten utan extra kostnad.

**Funktionen Lässkalning** är också tillgänglig på tjänstnivån Hyperskala när minst en sekundär replik skapas. Flera sekundära repliker kan användas om skrivskyddade arbetsbelastningar kräver mer resurser än tillgängliga på en sekundär replik. Tjänstnivåerna Hög tillgänglighet för tjänstnivåer basic, standard och allmänt ändamål innehåller inga repliker. **Funktionen Lässkalning** är inte tillgänglig på dessa tjänstnivåer.

Följande diagram illustrerar det med hjälp av en affärskritisk databas.

![Readonly repliker](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Funktionen Lässkalning är aktiverad som standard på nya Premium-, affärskritiska och hyperskalade databaser. För Hyperscale skapas en sekundär replik som standard för nya databaser. Om SQL-anslutningssträngen `ApplicationIntent=ReadOnly`är konfigurerad med omdirigeras programmet av gatewayen till en skrivskyddad replik av databasen. Information om hur du `ApplicationIntent` använder egenskapen finns i [Ange programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Om du vill vara medveten om att programmet ansluter `ApplicationIntent` till den primära repliken oavsett inställningen i SQL-anslutningssträngen, måste du uttryckligen inaktivera lässkalning när du skapar databasen eller när du ändrar dess konfiguration. Om du till exempel uppgraderar databasen från standard- eller generell nivå till Premium-, affärskritisk- eller hyperskala-nivå och vill vara säker på att alla dina anslutningar fortsätter att gå till den primära repliken inaktiverar du Lässkalning. Mer information om hur du inaktiverar den finns i [Aktivera och inaktivera Lässkalning](#enable-and-disable-read-scale-out).

> [!NOTE]
> Frågedatalagring, utökade händelser och SQL Profiler-funktioner stöds inte på skrivskyddade repliker.

## <a name="data-consistency"></a>Datakonsekvens

En av fördelarna med repliker är att replikerna alltid är i transaktionskonsekvent tillstånd, men vid olika tidpunkter kan det finnas några små svarstid mellan olika repliker. Läs Scale-Out stöder konsekvens på sessionsnivå. Det innebär att om skrivskyddad session återansluter efter ett anslutningsfel som orsakas av repliken otillgänglighet, kan det omdirigeras till en replik som inte är 100% up-to-date med läs-skriva replika. På samma sätt, om ett program skriver data med hjälp av en läs-skriv-session och omedelbart läser den med hjälp av en skrivskyddad session, är det möjligt att de senaste uppdateringarna inte omedelbart visas på repliken. Svarstiden orsakas av en asynkron transaktionslogg.

> [!NOTE]
> Replikeringsfördydnadsligheter i regionen är låga och den här situationen är sällsynt.

## <a name="connect-to-a-read-only-replica"></a>Ansluta till en skrivskyddad replik

När du aktiverar Lässkalning för `ApplicationIntent` en databas avgör alternativet i anslutningssträngen som tillhandahålls av klienten om anslutningen dirigeras till skrivrepliken eller till en skrivskyddad replik. Om `ApplicationIntent` värdet är `ReadWrite` (standardvärdet) dirigeras anslutningen till databasens läs-skriv-replik. Detta är identiskt med befintligt beteende. Om `ApplicationIntent` värdet `ReadOnly`är dirigeras anslutningen till en skrivskyddad replik.

Följande anslutningssträng ansluter till exempel klienten till en skrivskyddad replik (ersätter objekten i vinkelparenteserna med rätt värden för din miljö och släpper vinkelparenteserna):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Någon av följande anslutningssträngar ansluter klienten till en skrivreplik (ersätter objekten i vinkelparenteserna med rätt värden för din miljö och släpper vinkelparenteserna):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Kontrollera att en anslutning är till en skrivskyddad replik

Du kan kontrollera om du är ansluten till en skrivskyddad replik genom att köra följande fråga. Den returneras READ_ONLY när den är ansluten till en skrivskyddad replik.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Vid varje given tidpunkt är endast en av AlwaysON-replikerna tillgänglig för ReadOnly-sessionerna.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Övervaka och felsöka skrivskyddad replik

När du är ansluten till en skrivskyddad replik kan du komma åt prestandamåtten `sys.dm_db_resource_stats` med hjälp av DMV. Använd data för frågeplan `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` för `sys.dm_exec_sql_text` att komma åt frågeplanstatistik.

> [!NOTE]
> DMV `sys.resource_stats` i den logiska huvuddatabasen returnerar CPU-användning och lagringsdata för den primära repliken.

## <a name="enable-and-disable-read-scale-out"></a>Aktivera och inaktivera lässkalning

Lässkalning är aktiverat som standard på tjänstnivåer för Premium, Affärskritiska och Hyperskala. Lässkalning kan inte aktiveras på tjänstnivåer för grundläggande, standard eller allmänt syfte. Lässkalning inaktiveras automatiskt på hyperskaladatabaser som konfigurerats med 0 repliker.

Du kan inaktivera och återaktivera lässkalning på enskilda databaser och elastiska pooldatabaser på premium- eller affärskritisk tjänstnivå med hjälp av följande metoder.

> [!NOTE]
> Möjligheten att inaktivera Lässkalning tillhandahålls för bakåtkompatibilitet.

### <a name="azure-portal"></a>Azure Portal

Du kan hantera inställningen För lässkalning på bladet **Konfigurera** databas.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

För att hantera lässkalning i Azure PowerShell krävs Azure PowerShell-versionen i december 2016 eller nyare. Den senaste PowerShell-versionen finns i [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Du kan inaktivera eller återaktivera Lässkalning i Azure PowerShell genom att anropa cmdleten [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) och skicka in önskat värde – `Enabled` eller `Disabled` – för `-ReadScale` parametern.

Så här inaktiverar du utskalning av avläsning på en befintlig databas (ersätter objekten i vinkelparenteserna med rätt värden för din miljö och släpper vinkelparenteserna):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Så här inaktiverar du utskalning av avläsning på en ny databas (ersätter objekten i vinkelparenteserna med rätt värden för din miljö och släpper vinkelparenteserna):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Så här aktiverar du om avläsningen i en befintlig databas (ersätter objekten i vinkelparenteserna med rätt värden för din miljö och släpper vinkelparenteserna):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Om du vill skapa en databas med inaktiverad utskalning av läsning eller `readScale` ändra inställningen `Enabled` `Disabled` för en befintlig databas använder du följande metod med egenskapen inställd på eller som i exempelbegäran nedan.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Mer information finns i [Databaser - Skapa eller Uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Använda TempDB på skrivskyddad replik

TempDB-databasen replikeras inte till skrivskyddade repliker. Varje replik har sin egen version av TempDB-databasen som skapas när repliken skapas. Det säkerställer att TempDB kan uppdateras och kan ändras under frågekörningen. Om din skrivskyddade arbetsbelastning är beroende av att använda TempDB-objekt bör du skapa dessa objekt som en del av frågeskriptet.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Använda Lässkalning med geo-replikerade databaser

Om du använder Lässkalning för att läsa skrivskyddade arbetsbelastningar för belastningsutjämnad i en databas som är georeplikerad (till exempel som medlem i en redundansgrupp) kontrollerar du att avläsningskalning är aktiverat på både de primära och de geo-replikerade sekundära databaserna. Den här konfigurationen säkerställer att samma belastningsutjämningsupplevelse fortsätter när ditt program ansluter till den nya primära efter redundansen. Om du ansluter till den geo-replikerade sekundära databasen med lässkala aktiverad dirigeras dina sessioner med `ApplicationIntent=ReadOnly` till en av replikerna på samma sätt som vi dirigerar anslutningar i den primära databasen.  Sessionerna `ApplicationIntent=ReadOnly` utan dirigeras till den primära repliken för den georepnad sekundära, som också är skrivskyddad. Eftersom geo-replikerad sekundär databas har en annan slutpunkt än den primära databasen, historiskt `ApplicationIntent=ReadOnly`för att komma åt den sekundära det inte var skyldig att ställa in . För att säkerställa `sys.geo_replication_links` bakåtkompatibilitet `secondary_allow_connections=2` visar DMV (alla klientanslutningar är tillåtna).

> [!NOTE]
> Round-robin eller någon annan belastningsbalanserad routning mellan de lokala replikerna i den sekundära databasen stöds inte.

## <a name="next-steps"></a>Nästa steg

- Information om SQL Database Hyperscale-erbjudande finns i [Hyperskala-tjänstnivå](./sql-database-service-tier-hyperscale.md).
