---
title: Skala enkla databasresurser
description: I den här artikeln beskrivs hur du skalar beräknings- och lagringsresurser som är tillgängliga för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061603"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skala enskilda databasresurser i Azure SQL Database

I den här artikeln beskrivs hur du skalar beräknings- och lagringsresurser som är tillgängliga för en Azure SQL-databas på den etablerade beräkningsnivån. Alternativt ger den [serverlösa beräkningsnivån](sql-database-serverless.md) automatisk skalning och räkningar per sekund för beräkning som används.

När du först har plockat antalet virtuella kärnor eller DU:er kan du skala en enskild databas upp eller ned dynamiskt baserat på faktisk upplevelse med hjälp av [Azure-portalen,](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server) [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

Följande video visar dynamiskt ändra tjänstnivån och beräkningsstorlek för att öka tillgängliga DU:er för en enskild databas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="impact"></a>Påverkan

Att ändra tjänstnivån eller beräkningsstorleken för huvudsakligen innebär att tjänsten utför följande steg:

1. Skapa ny beräkningsinstans för databasen  

    En ny beräkningsinstans skapas med den begärda tjänstnivån och beräkningsstorleken. För vissa kombinationer av tjänstnivå och ändring av beräkningsstorlek måste en replik av databasen skapas i den nya beräkningsinstansen som innebär att du kopierar data och kan starkt påverka den övergripande svarstiden. Oavsett förblir databasen online under det här steget och anslutningar fortsätter att dirigeras till databasen i den ursprungliga beräkningsinstansen.

2. Växla routning av anslutningar till ny beräkningsinstans

    Befintliga anslutningar till databasen i den ursprungliga beräkningsinstansen tas bort. Alla nya anslutningar upprättas till databasen i den nya beräkningsinstansen. För vissa kombinationer av tjänstnivå och ändringar av beräkningsstorlek tas databasfiler bort och återansluts under växeln.  Oavsett kan växeln resultera i ett kort avbrott i tjänsten när databasen inte är tillgänglig i allmänhet i mindre än 30 sekunder och ofta bara några sekunder. Om det finns tidskrävande transaktioner som körs när anslutningar tas bort kan varaktigheten för det här steget ta längre tid för att återställa avbrutna transaktioner. [Snabbare databasåterställning](sql-database-accelerated-database-recovery.md) kan minska effekten från att avbryta långvariga transaktioner.

> [!IMPORTANT]
> Inga data går förlorade under något steg i arbetsflödet. Kontrollera att du har implementerat en logik [för återförsök](sql-database-connectivity-issues.md) i de program och komponenter som använder Azure SQL Database medan tjänstnivån ändras.

## <a name="latency"></a>Svarstid 

Den uppskattade svarstiden för att ändra tjänstnivån eller skala om beräkningsstorleken för en enskild databas eller elastisk pool är parameteriserad enligt följande:

|Tjänstenivå|Grundläggande enskild databas,</br>Standard (S0-S1)|Grundläggande elastisk pool,</br>Standard (S2-S12), </br>Hyperskala, </br>Allmän databas för allmänt ändamål eller elastisk pool|Premium eller affärskritisk enda databas eller elastisk pool|
|:---|:---|:---|:---|
|**Grundläggande enskild</br> databas, Standard (S0-S1)**|&bull;&nbsp;Konstant tidsfördröjning oberoende av det utrymme som används</br>&bull;&nbsp;Normalt är mindre än 5 minuter|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|
|**Grundläggande elastisk </br>pool, Standard (S2-S12), </br>Hyperskala, </br>allmän databas eller elastisk pool**|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Konstant tidsfördröjning oberoende av det utrymme som används</br>&bull;&nbsp;Normalt är mindre än 5 minuter|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|
|**Premium eller affärskritisk enda databas eller elastisk pool**|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|

> [!TIP]
> Om du vill övervaka pågående åtgärder läser du: [Hantera åtgärder med SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), Hantera åtgärder med [CLI](/cli/azure/sql/db/op), Övervaka åtgärder [med T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Avbryta ändringar

En ändrings- eller beräkningsändringsåtgärd för tjänstnivå kan avbrytas.

#### <a name="azure-portal"></a>Azure Portal

I databasöversiktsbladet navigerar du till **Meddelanden** och klickar på panelen som anger att det pågår en åtgärd:

![Pågående drift](media/sql-database-single-database-scale/ongoing-operations.png)

Klicka sedan på knappen **Avbryt den här åtgärden**.

![Avbryt pågående åtgärd](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Från en PowerShell-kommandotolk `$serverName`ställer `$databaseName`du in `$resourceGroupName`, och kör sedan följande kommando:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Annat som är bra att tänka på

- Om du uppgraderar till en högre tjänstnivå eller beräkningsstorlek ökar inte databasens maxstorlek om du inte uttryckligen anger en större storlek (maxsize).
- Om du vill nedgradera en databas måste det databasbaserade utrymmet vara mindre än den högsta tillåtna storleken på måltjänstnivån och beräkningsstorleken.
- Vid nedgradering från **Premium** till **standardnivån** gäller en extra lagringskostnad om både (1) databasens maxstorlek stöds i målberäkningsstorleken och (2) maxstorleken överstiger den medföljande lagringsmängden för målberäkningsstorleken. Om till exempel en P1-databas med en maxstorlek på 500 GB är nedstämd till S3, gäller en extra lagringskostnad eftersom S3 stöder en maxstorlek på 1 TB och dess medföljande lagringsmängd endast är 250 GB. Så är den extra lagringsmängden 500 GB - 250 GB = 250 GB. För prissättning av extra lagringsutrymme, se [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än det inkluderade lagringsbeloppet, kan den här extra kostnaden undvikas genom att minska databasens maxstorlek till det medföljande beloppet.
- När du uppgraderar en databas med [geo-replikering](sql-database-geo-replication-portal.md) aktiverad uppgraderar du dess sekundära databaser till önskad tjänstnivå och beräkningsstorlek innan den primära databasen uppgraderas (allmän vägledning för bästa prestanda). När du uppgraderar till en annan utgåva är det ett krav att den sekundära databasen uppgraderas först.
- När du nedgraderar en databas med [georeplikering](sql-database-geo-replication-portal.md) aktiverad nedgraderar du dess primära databaser till önskad tjänstnivå och beräkningsstorlek innan den sekundära databasen nedgraderas (allmän vägledning för bästa prestanda). När du nedgraderar till en annan utgåva är det ett krav att den primära databasen nedgraderas först.
- Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du nedgraderar till **basic-nivån** finns det en lägre lagringsperiod för säkerhetskopiering. Se [Azure SQL Database Backups](sql-database-automated-backups.md).
- De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

## <a name="billing"></a>Fakturering 

Du debiteras för varje timme som en databas finns med den högsta tjänstnivån + beräkningsstorleken som tillämpades under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme. Om du till exempel skapar en enskild databas och tar bort den fem minuter senare visar fakturan en debitering för en databastimme.

## <a name="change-storage-size"></a>Ändra lagringsstorlek

### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

- Lagring kan etableras upp till maxstorleksgränsen för datalagring med 1 GB steg. Den minsta konfigurerbara datalagringen är 1 GB. Se resursgränsdokumentationssidor för [enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) för maxstorleksgränser för datalagring i varje tjänstmål.
- Datalagring för en enskild databas kan etableras genom att öka eller minska dess maxstorlek med hjälp av [Azure-portalen,](https://portal.azure.com) [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/databases/update). Om maxstorleksvärdet anges i byte måste det vara en multipel på 1 GB (1073741824 byte).
- Mängden data som kan lagras i datafilerna i en databas begränsas av den konfigurerade datalagringens maxstorlek. Utöver det lagringsutrymmet allokerar SQL Database automatiskt 30 % mer lagringsutrymme som ska användas för transaktionsloggen.
- SQL Database allokerar automatiskt 32 `tempdb` GB per vCore för databasen. `tempdb`finns på den lokala SSD-lagringen på alla tjänstnivåer.
- Priset på lagring för en enskild databas eller en elastisk pool är summan av datalagrings- och transaktionslogglagringsbelopp multiplicerat med lagringsenhetspriset för tjänstnivån. Kostnaden för `tempdb` ingår i priset. Mer information om lagringspris finns i [SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-baserad inköpsmodell

- DTU-priset för en enskild databas innehåller en viss mängd lagringsutrymme utan extra kostnad. Extra lagringsutrymme utöver det medföljande beloppet kan etableras för en extra kostnad upp till maxstorleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB längre än 1 TB. För inkluderade lagringsbelopp och maxstorleksgränser finns i [En databas: Lagringsstorlekar och beräkningsstorlekar](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Extra lagringsutrymme för en enskild databas kan etableras genom att öka dess maxstorlek med hjälp av [Azure-portalen, Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Priset för extra lagringsutrymme för en enskild databas är det extra lagringsbeloppet multiplicerat med det extra lagringsenhetspriset för tjänstnivån. Mer information om priset på extra lagringsutrymme finns i [SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

### <a name="geo-replicated-database"></a>Geo-replikerad databas

Om du vill ändra databasstorleken för en replikerad sekundär databas ändrar du storleken på den primära databasen. Den här ändringen replikeras och implementeras även i den sekundära databasen. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11- och P15-begränsningar när maxstorlek är större än 1 TB

Mer än 1 TB lagringsutrymme i Premium-nivån är för närvarande tillgängligt i alla regioner utom: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD-regioner och US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB. Följande överväganden och begränsningar gäller för P11- och P15-databaser med en maximal storlek som är större än 1 TB:

- Om den maximala storleken för en P11- eller P15-databas någonsin har ställts in på ett värde som är större än 1 TB, kan den bara återställas eller kopieras till en P11- eller P15-databas.  Därefter kan databasen skalas om till en annan beräkningsstorlek förutsatt att mängden utrymme som allokerats vid tidpunkten för omskalningen inte överskrider maxstorleksgränser för den nya beräkningsstorleken.
- För aktiva geo-replikeringsscenarier:
  - Ställa in en geo-replikeringsrelation: Om den primära databasen är P11 eller P15 måste sekundären också vara P11 eller P15. lägre beräkningsstorlek avvisas som sekundärer eftersom de inte kan stödja mer än 1 TB.
  - Om du uppgraderar den primära databasen i en georeplikeringsrelation: Om du ändrar den maximala storleken till mer än 1 TB i en primär databas utlöses samma ändring i den sekundära databasen. Båda uppgraderingarna måste lyckas för att ändringen på den primära ska börja gälla. Regionbegränsningar för alternativet mer än 1 TB gäller. Om den sekundära är i en region som inte stöder mer än 1 TB, uppgraderas inte den primära.
- Det går inte att använda tjänsten Import/Export för inläsning av P11/P15-databaser med mer än 1 TB. Använd SqlPackage.exe för att [importera](sql-database-import.md) och [exportera](sql-database-export.md) data.

## <a name="next-steps"></a>Nästa steg

Övergripande resursgränser finns i [SQL Database vCore-baserade resursgränser – enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [DTU-baserade SQL Database-baserade resursgränser – elastiska pooler](sql-database-dtu-resource-limits-single-databases.md).
