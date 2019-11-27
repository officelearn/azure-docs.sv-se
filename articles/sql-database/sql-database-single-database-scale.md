---
title: Skala enkla databas resurser
description: Den här artikeln beskriver hur du skalar beräknings-och lagrings resurserna som är tillgängliga för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 8d4917bb8956185e0cb557368fbb0c64343c0ac6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422539"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skala enkla databas resurser i Azure SQL Database

Den här artikeln beskriver hur du skalar beräknings-och lagrings resurserna som är tillgängliga för en Azure SQL Database i den allokerade beräknings nivån. Alternativt tillhandahåller Server lös beräknings [nivån](sql-database-serverless.md) beräkning av automatisk skalning och räkningar per sekund för beräkning som används.

## <a name="change-compute-size-vcores-or-dtus"></a>Ändra beräknings storlek (virtuella kärnor eller DTU: er)

När du har valt antalet virtuella kärnor eller DTU: er kan du skala upp eller ned en enkel databas dynamiskt baserat på den faktiska upplevelsen med hjälp av [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API ](https://docs.microsoft.com/rest/api/sql/databases/update).

Följande video visar dynamiskt ändring av tjänst nivå och beräknings storlek för att öka tillgänglig DTU: er för en enskild databas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [Hantera fil utrymme i Azure SQL Database](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effekt av att ändra tjänst nivå eller skala om beräknings storlek

Att ändra tjänst nivå eller beräknings storlek i huvudsak inbegriper tjänsten som utför följande steg:

1. Skapa en ny beräknings instans för databasen  

    En ny beräknings instans skapas med den begärda tjänst nivån och beräknings storleken. För vissa kombinationer av tjänst nivå och beräknings storlek måste en replik av databasen skapas i den nya beräknings instansen som inbegriper kopiering av data och kan starkt påverka den totala svars tiden. Oavsett, är databasen online under det här steget, och anslutningar fortsätter att dirigeras till databasen i den ursprungliga beräknings instansen.

2. Växla routning av anslutningar till en ny beräknings instans

    Befintliga anslutningar till databasen i den ursprungliga beräknings instansen tas bort. Alla nya anslutningar upprättas till databasen i den nya beräknings instansen. För vissa kombinationer av tjänst nivå och beräknings storlek, kopplas databasfilerna bort och återkopplas under växeln.  Oavsett vilket kan växeln resultera i ett kort avbrott när databasen inte är tillgänglig generellt i mindre än 30 sekunder och ofta i några sekunder. Om det finns tids krävande transaktioner som körs när anslutningar släpps, kan varaktigheten för det här steget ta längre tid för att återställa avbrutna transaktioner. [Accelererad databas återställning](sql-database-accelerated-database-recovery.md) kan minska påverkan från att avbryta tids krävande transaktioner.

> [!IMPORTANT]
> Inga data förloras under något steg i arbets flödet. Kontrol lera att du har implementerat vissa [omprövnings logik](sql-database-connectivity-issues.md) i de program och komponenter som använder Azure SQL Database medan tjänst nivån ändras.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Svars tid för ändring av tjänst nivå eller skalning av beräknings storlek

Den uppskattade svars tiden för att ändra tjänst nivån eller skala om beräknings storleken för en enskild databas eller elastisk pool är parameterstyrda enligt följande:

|Tjänstenivå|Enkel databas,</br>Standard (S0-S1)|Basic elastisk pool,</br>Standard (S2-S12) </br>Hyperskala </br>Generell användning enskild databas eller elastisk pool|Premium-eller Affärskritisk enkel databas eller elastisk pool|
|:---|:---|:---|:---|
|**Enkel databas,</br> standard (S0-S1)**|&bull; &nbsp;tidssvars tid för konstant som är oberoende av använt utrymme</br>&bull; &nbsp;vanligt vis mindre än 5 minuter|&bull; &nbsp;svars tid som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull; &nbsp;vanligt vis mindre än 1 minut per GB använt utrymme|&bull; &nbsp;svars tid som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull; &nbsp;vanligt vis mindre än 1 minut per GB använt utrymme|
|**Basic elastisk pool, </br>standard (S2-S12), </br>storskalig </br>Generell användning enskild databas eller elastisk pool**|&bull; &nbsp;svars tid som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull; &nbsp;vanligt vis mindre än 1 minut per GB använt utrymme|&bull; &nbsp;tidssvars tid för konstant som är oberoende av använt utrymme</br>&bull; &nbsp;vanligt vis mindre än 5 minuter|&bull; &nbsp;svars tid som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull; &nbsp;vanligt vis mindre än 1 minut per GB använt utrymme|
|**Premium-eller Affärskritisk enkel databas eller elastisk pool**|&bull; &nbsp;svars tid som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull; &nbsp;vanligt vis mindre än 1 minut per GB använt utrymme|&bull; &nbsp;svars tid som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull; &nbsp;vanligt vis mindre än 1 minut per GB använt utrymme|&bull; &nbsp;svars tid som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull; &nbsp;vanligt vis mindre än 1 minut per GB använt utrymme|

> [!TIP]
> Information om hur du övervakar pågående åtgärder finns i: [Hantera åtgärder med hjälp av SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Hantera åtgärder med CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [ Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Avbryter ändringar av tjänst nivå eller beräkning av omskalning

En ändring eller skalnings åtgärd för en tjänst nivå kan avbrytas.

#### <a name="azure-portal"></a>Azure Portal

I bladet databas översikt navigerar du till **meddelanden** och klickar på panelen som visar att det finns en pågående åtgärd:

![Pågående åtgärd](media/sql-database-single-database-scale/ongoing-operations.png)

Klicka sedan på knappen **Avbryt den här åtgärden**.

![Avbryt pågående åtgärd](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Ange `$resourceGroupName`, `$serverName`och `$databaseName`från en PowerShell-kommandotolk och kör sedan följande kommando:

```powershell
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Ytterligare överväganden vid ändring av tjänst nivå eller skalning av beräknings storlek

- Om du uppgraderar till en högre tjänst nivå eller beräknings storlek, ökar inte databasens maximala storlek om du inte uttryckligen anger en större storlek (MaxSize).
- För att nedgradera en databas måste det databas utrymme som används vara mindre än den största tillåtna storleken för mål tjänst nivån och beräknings storleken.
- Vid nedgradering från **Premium** till **standard** -nivån gäller en extra lagrings kostnad om både (1) databasens Max storlek stöds i mål beräknings storleken och (2) Max storleken överskrider den mängd lagrings storlek som används för mål beräknings storleken. Om t. ex. en P1-databas med en Max storlek på 500 GB är downsized till S3, gäller en extra lagrings kostnad eftersom S3 har stöd för en Max storlek på 1 TB och den inkluderade lagrings mängden är bara 250 GB. Det extra lagrings utrymmet är 500 GB – 250 GB = 250 GB. Prissättning av extra lagrings utrymme finns [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än den mängd lagrings mängd som används, kan den här extra kostnaden undvikas genom att max storleken för databasen minskas till den inkluderade mängden.
- När du uppgraderar en databas med [geo-replikering](sql-database-geo-replication-portal.md) aktive rad uppgraderar du dess sekundära databaser till önskad tjänste nivå och beräknings storlek innan du uppgraderar den primära databasen (allmän vägledning för bästa prestanda). När du uppgraderar till en annan krävs en uppgradering av den sekundära databasen först.
- Vid nedgradering av en databas med [geo-replikering](sql-database-geo-replication-portal.md) aktiverat, nedgradera dess primära databaser till önskad tjänste nivå och beräknings storlek innan du degraderar den sekundära databasen (allmän vägledning för bästa prestanda). Vid nedgradering till en annan utgåva krävs en nedgradering av den primära databasen först.
- Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du nedgraderar nivån **Basic** finns det en lägre kvarhållningsperiod för säkerhets kopior. Se [Azure SQL Database säkerhets kopieringar](sql-database-automated-backups.md).
- De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

### <a name="billing-during-compute-rescaling"></a>Fakturering under beräknings omskalning

Du debiteras för varje timme som det finns en databas med den högsta tjänst nivån och den beräknings storlek som tillämpas under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme. Om du till exempel skapar en enskild databas och tar bort den fem minuter senare, motsvarar din faktura en avgift för en databas timme.

## <a name="change-storage-size"></a>Ändra lagrings storlek

### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

- Lagrings utrymmet kan allokeras till max storleks gränsen med 1 GB steg. Det minsta konfigurerbara data lagrings utrymmet är 5 GB
- Lagring för en enskild databas kan tillhandahållas genom att öka eller minska dess Max storlek med [Azure Portal](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- SQL Database automatiskt allokerar 30% av ytterligare lagrings utrymme för loggfilerna och 32 GB per vCore för TempDB, men inte överskrida 384GB. TempDB finns på en ansluten SSD på alla tjänst nivåer.
- Priset för lagring för en enskild databas är summan av data lagring och logg lagrings mängd multiplicerat med tjänst nivåns lagrings enhets pris. Kostnaden för TempDB ingår i vCore-priset. Mer information om priset för extra lagring finns [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [Hantera fil utrymme i Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-baserad inköps modell

- DTU-priset för en enskild databas omfattar en viss mängd lagring utan extra kostnad. Extra lagring utöver den mängd som ingår kan tillhandahållas för ytterligare kostnad upp till den maximala storleks gränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB utöver 1 TB. För inkluderade lagrings mängder och Max storleks gränser, se [enskild databas: lagrings storlekar och beräknings storlekar](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Extra lagrings utrymme för en enskild databas kan tillhandahållas genom att öka Max storleken med Azure Portal, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [POWERSHELL](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Priset för extra lagring för en enskild databas är det extra lagrings beloppet multiplicerat med det extra lagrings enhets priset för tjänst nivån. Mer information om priset för extra lagring finns [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [Hantera fil utrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11-och p15-begränsningar när Max storleken är större än 1 TB

Mer än 1 TB lagrings utrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala, Tyskland nordöstra, västra centrala USA, US DoD regioner och USA, centrala. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB. Följande överväganden och begränsningar gäller för p11-och p15-databaser med en maximal storlek som är större än 1 TB:

- Om den maximala storleken för en p11-eller p15-databas någonsin har angetts till ett värde som är större än 1 TB, kan den bara återställas eller kopieras till en p11-eller p15-databas.  Därefter kan databasen skalas om till en annan beräknings storlek förutsatt att mängden utrymme som allokerats vid tidpunkten för skalnings åtgärden inte överskrider max storleks gränsen för den nya beräknings storleken.
- För aktiva scenarier för geo-replikering:
  - Konfigurera en relation för geo-replikering: om den primära databasen är p11 eller p15 måste de sekundära (får) också vara p11 eller p15. lägre beräknings storlek avvisas som sekundära eftersom de inte har stöd för mer än 1 TB.
  - Uppgradera den primära databasen i en geo-replikeringsrelation-relation: om du ändrar den maximala storleken till mer än 1 TB på en primär databas utlöses samma ändring på den sekundära databasen. Båda uppgraderingarna måste lyckas för att ändringen på den primära ska börja gälla. Region begränsningar för mer än 1 TB-alternativet gäller. Om den sekundära finns i en region som inte har stöd för mer än 1 TB, uppgraderas inte den primära.
- Det går inte att använda import/export-tjänsten för att läsa in p11/p15-databaser med mer än 1 TB. Använd SqlPackage. exe för att [Importera](sql-database-import.md) och [Exportera](sql-database-export.md) data.

## <a name="next-steps"></a>Nästa steg

För övergripande resurs gränser, se [SQL Database vCore resurs gränser – enkla databaser](sql-database-vcore-resource-limits-single-databases.md) och [SQL Database DTU-baserade resurs gränser-elastiska pooler](sql-database-dtu-resource-limits-single-databases.md).
