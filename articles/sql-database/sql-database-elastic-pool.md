---
title: Hantera flera SQL-databaser med elastiska pooler Azure | Microsoft Docs
description: Hantera och skala flera databaser i SQL - hundratals och tusentalsavgränsare - med elastiska pooler. Ett pris för resurser som du kan distribuera om det behövs.
keywords: flera databaser, databasresurser, databasprestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/07/2018
ms.author: ninarn
ms.topic: conceptual
ms.openlocfilehash: 6e58d3ed84771cedda126511e868ad264db88606
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850499"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Hjälper dig att hantera och skala flera Azure SQL-databaser för elastiska pooler

SQL-databas elastiska pooler är en enkel och kostnadseffektiv lösning för att hantera och skala flera databaser som har olika och oförutsägbart krav för användning. Databaserna i en elastisk pool finns på en enda Azure SQL Database-server och dela ett visst antal resurser på ett fast pris. Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas.

## <a name="what-are-sql-elastic-pools"></a>Vad är SQL elastiska pooler?

SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta varierande och oförutsägbara användningsmönster, och det är svårt att förutse resursbehoven för varje enskild databasanvändare. Traditionellt har du två alternativ:

- Etablera över resurser baserat på högsta användningsnivå och över lön, eller
- Under etablera spara kostnaden på bekostnad av prestanda och nöjda kunder under toppar.

Elastiska pooler lösa detta problem genom att säkerställa att databaser får prestanda-resurser som de behöver, när de behöver den. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Elastiska pooler kan utvecklare att köpa resurser för en pool som delas av flera databaser för oväntade perioder med användning av enskilda databaser. Du kan konfigurera resurser för poolen baserat antingen på den [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md). Resurskrav för en pool bestäms av den sammanlagda användningen av databaserna. Mängden resurser som är tillgängliga för poolen styrs av utvecklare budget. Utvecklaren bara lägger till databaserna i poolen, anger de lägsta och högsta resurserna för databaserna (lägsta och högsta dtu: er eller lägsta eller högsta vCores beroende på ditt val av resourcing modellen), och anger resurserna från poolen baserat på sina budget. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.

I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Hårt belastad, kan en databas använda mer resurser för att uppfylla begäran. Databaser under lätta belastningar förbrukar mindre och databaser under inga belastningen använda några resurser. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom har en förutsägbar budget för poolen. Ytterligare resurser kan läggas till en befintlig adresspool utan avbrott i databasen, förutom att databaserna kan behöva flyttas att tillhandahålla ytterligare beräkningsresurser för ny eDTU-reservation. På liknande sätt, om extra resurser inte längre behövs de kan tas bort från en befintlig adresspool när som helst i tid. Och du kan lägga till eller ta bort databaser i poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>När bör du överväga en SQL Database-elastisk pool?

Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt programanvändningsmönster kan du få besparingar med så lite som två S3-databaser.

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster

Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/sql-database-elastic-pool/one-database.png)

Under femminutersperioden som visas har DB1 toppar med 90 DTU:er, men en genomsnittlig användning på mindre än 5 DTU:er. En S3-prestandanivå krävs för att köra den här arbetsbelastningen i en enkel databas, men det innebär att de flesta av resurserna inte används under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. I de två figurerna nedan användning av fyra databaser och 20 databaser lager till samma diagram som illustrerar den icke-överlappande strukturen på deras belastning över tid med DTU-baserade inköpsmodell:

   ![4 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/four-databases.png)

   ![20 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/twenty-databases.png)

Den sammanlagda DTU-användningen över alla 20 databaser illustreras av den svarta linjen i föregående bild. Detta visar att den sammanlagda DTU-användningen aldrig överskrider 100 DTU:er och indikerar att 20 databaser kan dela 100 eDTU:er under den här tidsperioden. Detta resulterar i 20 gånger färre DTU:er och 13 gånger lägre pris jämfört med om varje databas läggs till i S3-prestandanivåer för enskilda databaser.

Det här exemplet är idealisk av följande anledningar:

* Skillnaderna mellan användningen vid hög aktivitet och den genomsnittliga användningen per databas är stora.
* Belastningstopparna för varje databas inträffar vid olika tidpunkter.
* eDTU:er som delas mellan flera databaser.

Priset för en pool är associerat med poolens eDTU:er. Även om eDTU-enhetspriset för en pool är 1,5 gånger större än DTU-enhetspriset för en enkel databas, så kan **pool-DTU:erna delas av många databaser och det behövs färre eDTU:er**. Dessa skillnader i pris och eDTU-delning utgör grunden för de prisbesparingar som pooler kan medföra.

Genom att följa nedanstående tumregler för antalet databaser och databasanvändning kan du vara säker på att en pool ger mindre kostnader jämfört med användningen av prestandanivåer för enskilda databaser.

### <a name="minimum-number-of-databases"></a>Minsta antal databaser

Om summan av resurser för enskilda databaser är mer än 1,5 x de resurser som krävs för poolen, är det mer kostnadseffektivt med en elastisk pool.

***DTU-baserade köp modell-exempel***<br>
Minst två S3-databaser eller minst 15 S0-databaser behövs för att en pool med 100 eDTU:er ska vara mer kostnadseffektivt än användningen av prestandanivåer för enskilda databaser.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Högsta antal samtidigt databaser med aktivitetstoppar

Genom att dela resurser, kan inte alla databaser i poolen samtidigt använda resurser upp till gränsen som är tillgängliga för enskilda databaser. Färre databaserna som samtidigt högsta, Ju lägre pool-resurser kan ställas in och det mer kostnadseffektivt poolen blir. I allmänhet bör inte mer än 2/3 (eller 67%) för databaserna i poolen samtidigt högsta att begränsa deras resurser.

***DTU-baserade köp modell-exempel***<br>
För att minska kostnaderna för tre S3-databaser i en pool med 200 eDTU:er kan högst två av dessa databaser ha belastningstoppar samtidigt. Annars, om fler än två av dessa fyra S3-databaser har toppar samtidigt, skulle poolen behöva utökas till mer än 200 eDTU:er. Om poolen utökas till mer än 200 eDTU:er skulle fler S3-databaser behöva läggas till i poolen för att kostnaderna ska vara lägre än med prestandanivåer för enskilda databaser.

Observera att det här exemplet inte tar hänsyn till användningen av andra databaser i poolen. Om alla databaser har viss belastning vid en given tidpunkt kan mindre än 2/3 (eller 67 %) av databaserna ha aktivitetstoppar samtidigt.

### <a name="resource-utilization-per-database"></a>Resursutnyttjande per databas
En stor skillnad mellan topp- och genomsnittsanvändningen av en databas indikerar långa perioder med låg belastning och korta perioder med hög användning. Det här användningsmönstret är idealisk för delning av resurser mellan databaser. Du bör överväga att lägga till en databas i en pool om dess högsta användning är runt 1,5 gånger större än dess genomsnittliga användning.

***DTU-baserade köp modell-exempel***<br>
En S3-databas som behöver 100 DTU:er vid hög aktivitet och som har en genomsnittlig användning på 67 DTU:er eller mindre är lämplig för delning av eDTU:er i en pool. På motsvarande sätt är en S1-databas som använder 20 DTU:er vid hög belastning och som har en genomsnittsanvändning på 13 DTU:er inte lämplig för en pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hur väljer rätt poolstorleken?

Den rekommenderade storleken för en pool beror på de sammanställda resurser som krävs för alla databaser i poolen. Detta omfattar att fastställa följande:

* Maximal resurser som används av alla databaser i poolen (högsta dtu: er eller maximal vCores beroende på ditt val av resourcing modell).
* Högsta lagringsutrymme i byte som används av alla databaser i poolen.

Tillgängliga nivåer för varje resursmodell, finns det [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md).

SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Förutom rekommendationerna finns det en inbyggd beräkning som uppskattar eDTU-användningen för en anpassad grupp databaser på servern. Detta gör att du kan utföra en konsekvensanalys genom att interaktivt lägga till databaser i poolen och sedan ta bort dem för att visa en analys över resursanvändningen och storleksrekommendationer innan du genomför ändringarna. Mer information finns i [Monitor, manage, and size an elastic pool](#monitor-an-elastic-pool-and-its-databases) (Övervaka, hantera och ändra storlek på en elastisk pool).

I de fall då du inte kan använda verktygsuppsättningar kan du följa stegen nedan för att ta reda på om en pool är ett mer kostnadseffektivt alternativ än enskilda databaser:

1. Beräkna edtu: er eller vCores som behövs för poolen på följande sätt:

   För DTU-baserade inköpsmodell: MAX (<*Totalt antal DBs* X *genomsnittlig DTU-användning per DB*>,<br>
   <*Antal databaser som har aktivitetstoppar samtidigt* × *DTU-toppbelastning per databas*)

   För vCore-baserade inköpsmodell (förhandsversion): MAX (<*Totalt antal DBs* X *genomsnittlig vCore användning per DB*>,<br>
   <*Antal samtidigt peaking DBs* X *belastning vCore användning per DB*)

2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme.
3. DTU-baserade inköpsmodell ta för större eDTU uppskattningar från steg 1 och 2. Ta vCore uppskattning från steg 1 för vCore-baserade köp modellen (förhandsversion).
4. Finns det [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/) och hitta poolen minsta storlek som är större än uppskattningen från steg3.
5. Jämför poolpriset i steg 5 med priset för att använda lämpliga prestandanivåer för enskilda databaser.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Använda andra funktioner i SQL-databas med elastiska pooler

### <a name="elastic-jobs-and-elastic-pools"></a>Elastiska jobb och elastiska pooler

Med en pool förenklas hanteringsuppgifterna eftersom du kan köra skript i **[elastiska jobb](sql-database-elastic-jobs-overview.md)**. Ett elastiskt jobb underlättar hanteringen av stora antal databaser. För att komma igång kan du se [Kom igång med elastiska jobb](sql-database-elastic-jobs-getting-started.md).

Mer information om andra databasverktyg för att jobba med flera databaser, finns i [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Alternativ för verksamhetskontinuitet för databaser i en elastisk pool
Databaser i pool stöder generellt sett samma [funktioner för affärskontinuitet](sql-database-business-continuity.md) som finns tillgängliga för enskilda databaser.

- **Point-in-time-återställning**: Point-in-time-återställning använder automatiska säkerhetskopieringar för att återställa en databas i en pool till en specifik tidpunkt. Mer information finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore)

- **GEO-återställning**: Geo-återställning ger standardalternativet när en databas är inte tillgänglig på grund av en incident i den region där databasen finns. Mer information finns i avsnittet [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Återställa en Azure SQL-databas eller växla över till en sekundär databas)

- **Aktiv geo-replikering**: konfigurera för program som har mer aggressivt recovery krav än geo-återställning kan erbjuda [aktiv geo-replikering](sql-database-geo-replication-overview.md).

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Hantera elastiska pooler och databaser med hjälp av Azure portal

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Skapa en ny SQL Database-elastisk pool med Azure-portalen

Det finns två sätt att skapa en elastisk pool i Azure-portalen.
1. Du kan skapa en elastisk pool genom att söka **elastiska SQL-poolen** i den **Marketplace** eller klicka på **+ Lägg till** på SQL elastiska pooler bläddrar du bladet. Du kan ange en ny eller befintlig server via den här poolen etablering av arbetsflöde.
2. Du kan också skapa en elastisk pool genom att gå till en befintlig SQLServer och klicka **skapa poolen** att skapa en pool direkt till servern. Den enda skillnaden är att du hoppa över steg där du anger servern under poolen etablering av arbetsflöde.

> [!NOTE]
> Du kan skapa flera pooler på en server, men du kan inte lägga till databaser från olika servrar i samma pool.

Den pooltjänstnivå avgör vilka funktioner som är tillgängliga för elastics i poolen och maximala mängden resurser som är tillgängliga för varje databas. Mer information finns i gränserna för elastiska pooler i den [DTU modellen](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) och [vCore modellen](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Konfigurera resurserna och prisnivå för poolen, klickar du på **konfigurera pool**. Välj en tjänstnivå, lägga till databaser i poolen och konfigurera resurs-gränser för poolen och databaserna.

När du har slutfört konfigurationen poolen, kan du på Verkställ, namn i poolen, och klicka på 'OK' om du vill skapa poolen.

### <a name="monitor-an-elastic-pool-and-its-databases"></a>Övervaka en elastisk pool och databaserna

Du kan övervaka användningen av en elastisk pool och databaserna i poolen i Azure-portalen. Du kan också göra en uppsättning ändringar i den elastiska poolen och skicka alla ändringar på samma gång. De här förändringarna innefattar att lägga till eller ta bort databaser, ändra inställningarna elastisk pool eller ändra databasinställningarna.

Om du vill börja övervaka din elastisk pool, hitta och öppna en elastisk pool i portalen. En skärm som ger en översikt över statusen för den elastiska poolen visas först. Det här omfattar:

* Övervakning av diagram som visar resurser användning av den elastiska poolen
* Senaste aviseringarna och rekommendationer om de är tillgängliga för den elastiska poolen

Följande bild visar ett exempel elastisk pool:

![Pool-vy](./media/sql-database-elastic-pool-manage-portal/basic.png)

Om du vill ha mer information om poolen kan du klicka på någon av informationen i den här översikten. Klicka på den **resursutnyttjande** diagrammet tar dig till den Azure-övervakning vyn där du kan anpassa fönstret mått och tid som visas i diagrammet. Klicka på alla tillgängliga meddelanden att ta dig till ett blad som visar fullständig information om aviseringen eller rekommendation.

Om du vill övervaka databaser i din pool, kan du klicka på **databasen resursutnyttjande** i den **övervakning** resurs-menyn till vänster.

![Sidan databas resurs för användning](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

#### <a name="to-customize-the-chart-display"></a>Du kan anpassa diagram

Du kan redigera diagrammet och sidan mått om du vill visa andra mått som CPU-procent, data IO-procent och loggen IO-procent som används.

På den **redigera diagram** formuläret som du kan välja en fast tidpunkt intervall eller klicka på **anpassade** att välja valfritt 24-timmarsformat fönster under de senaste två veckorna och välj sedan resurserna som ska övervaka.

#### <a name="to-select-databases-to-monitor"></a>Att välja databaser för att övervaka

Som standard i diagrammet i den **databasen resursutnyttjande** bladet visar 5 viktigaste databaser genom DTU eller processor (beroende på din tjänstnivån). Du kan växla av databaserna i det här diagrammet genom att välja och unselecting databaser i listan under diagrammet via kryssrutorna till vänster.

Du kan också välja fler mått till vyn sida vid sida i den här databastabell för att få en mer komplett vy av dina databaser prestanda.

Mer information finns i [skapa SQL-databas aviseringar i Azure-portalen](sql-database-insights-alerts-portal.md).

### <a name="manage-an-elastic-pool-and-its-databases"></a>Hantera en elastisk pool och databaserna

Alla inställningar för programpool kan hittas på en plats: den **konfigurera pool** bladet. Hitta en elastisk pool i portalen och klicka på för att få här **konfigurera pool** högst upp på bladet eller resurs-menyn till vänster.

Du kan välja valfri kombination av följande ändringar och spara dem i en batch härifrån:
1. Ändra tjänstnivån för poolen
2. Skala prestanda (DTU eller vCores) och lagring upp eller ned
3. Lägg till eller ta bort databaser från poolen
4. Ange en minut (garanteras) samt högsta antal prestanda gränsen för databaserna i pooler
5. Granska sammanfattningen av kostnaden för att visa ändringar i fakturan på grund av nya alternativ

![Elastisk pool configuration bladet](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Hantera elastiska pooler och databaser med hjälp av PowerShell

Använd följande PowerShell-cmdlets för att skapa och hantera SQL-databas elastiska pooler med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Om du vill skapa och hantera databaser, servrar och brandväggsregler, se [skapa och hantera Azure SQL Database-servrar och databaser med hjälp av PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> PowerShell-exempelskript, se [skapa elastiska pooler och flytta databaser mellan pooler och från en pool med PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) och [Använd PowerShell för att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Skapar en elastisk databaspool på en logisk SQLServer.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Hämtar elastiska pooler och deras värden på en logisk SQLServer.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Ändrar egenskaper för en elastisk databaspool på en logisk SQLServer. Till exempel använda den **StorageMB** egenskapen att ändra maximal lagringskapacitet för en elastisk pool.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Tar bort en elastisk databaspool på en logisk SQLServer.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Hämtar status för åtgärder på en elastisk pool på en logisk SQLServer.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Skapar en ny databas i en befintlig adresspool eller som en enskild databas. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas i, slut på eller mellan elastiska pooler.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Tar bort en databas.|


> [!TIP]
> Skapandet av många databaser i en elastisk pool kan ta tid när det görs med portalen eller PowerShell-cmdletar som skapar bara en enskild databas åt gången. Om du vill automatisera skapande i en elastisk pool, se [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Hantera elastiska pooler och databaser med hjälp av Azure CLI

Skapa och hantera SQL-databas elastiska pooler med den [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> Azure CLI exempelskript finns [Använd CLI för att flytta en Azure SQL database i en elastisk pool SQL](scripts/sql-database-move-database-between-pools-cli.md) och [Använd Azure CLI för att skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql elastisk pool](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Skapar en elastisk pool.|
|[AZ sql elastisk pool lista](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Returnerar en lista över elastiska pooler i en server.|
|[AZ sql elastisk pool lista-databaser](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Returnerar en lista över databaser i en elastisk pool.|
|[AZ sql elastisk pool lista-versioner](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Även tillgängliga poolen DTU inställningar, Lagringsgränser och per databasinställningarna. För att minska detaljnivå ytterligare Lagringsgränser och per databas inställningar döljs som standard.|
|[AZ sql elastisk pool uppdateringen](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Uppdaterar en elastisk pool.|
|[ta bort AZ sql-elastisk pool](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Tar bort den elastiska poolen.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Hantera databaser i elastiska pooler med Transact-SQL

Skapa och flytta databaser i befintliga elastiska pooler eller att returnera information om en SQL Database-elastisk pool med Transact-SQL, Använd följande T-SQL-kommandon. Du kan skicka dessa kommandon med hjälp av Azure portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. Om du vill skapa och hantera databaser, servrar och brandväggsregler, se [skapa och hantera Azure SQL Database-servrar och databaser med hjälp av Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en Azure SQL Database-elastisk pool med Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool och du kan använda av DMV: er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[Skapa databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig adresspool eller som en enskild databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas i, slut på eller mellan elastiska pooler.|
|[Ta bort databasen (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar resurs användningsstatistik för elastiska databaspooler i en logisk server. För varje elastisk databaspool att det finns en rad för varje 15 sekunder reporting fönstret (fyra rader per minut). Detta inkluderar CPU, IO, Log, användningen av lagringsutrymme och samtidiga begäran-session användning av alla databaser i poolen.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar edition (tjänstnivån), tjänstmålet (prisnivån) och namn på elastisk pool, för en Azure SQL-databas eller ett Azure SQL Data Warehouse. Returnerar information om alla databaser om inloggad på master-databasen i en Azure SQL Database-server. För Azure SQL Data Warehouse, måste du vara ansluten till master-databasen.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Hantera elastiska pooler och databaser med hjälp av REST-API

Om du vill skapa och hantera SQL Database-elastisk använda pooler dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler – skapa eller uppdatera](/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler - ta bort](/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler - Get](/rest/api/sql/elasticpools/get)|Hämtar en elastisk pool.|
|[Elastiska pooler - lista av servern](/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista över elastiska pooler i en server.|
|[Elastiska pooler - uppdatering](/rest/api/sql/elasticpools/update)|Uppdaterar en befintlig elastisk pool.|
|[Rekommenderade elastiska pooler - Get](/rest/api/sql/recommendedelasticpools/get)|Hämtar en rekommenderad elastisk pool.|
|[Rekommenderade elastiska pooler - lista av servern](/rest/api/sql/recommendedelasticpools/listbyserver)|Returnerar rekommenderade elastiska pooler.|
|[Rekommenderade elastiska pooler - listan mått](/rest/api/sql/recommendedelasticpools/listmetrics)|Returnerar rekommenderad elastisk pool mått.|
|[Elastisk Pool aktiviteter](/rest/api/sql/elasticpoolactivities)|Returnerar elastisk pool aktiviteter.|
|[Databasaktiviteter elastisk Pool](/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet på databaser i en elastisk pool.|
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Get](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – få genom elastisk Pool](/rest/api/sql/databases/getbyelasticpool)|Hämtar en databas i en elastisk pool.|
|[Databaser – få genom rekommenderad elastisk Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Hämtar en databas i en rekommenderad elastisk pool.|
|[Databaser - listan efter elastisk Pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - listan efter rekommenderad elastisk Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Returnerar en lista över databaser i en rekommenderad elastisk pool.|
|[Databaser – lista av servern](/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser - uppdatering](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Se en video [Microsoft Virtual Academy video kursen på Azure SQL Database-elastisk funktioner](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Om du har en SaaS-självstudiekurs med elastiska pooler finns [introduktion till Wingtip SaaS-program](sql-database-wtp-overview.md).
