---
title: Skapa en ny elastisk pool med Azure-portalen | Microsoft Docs
description: "Så här lägger du till en skalbar elastisk databaspool till din SQL-databaskonfiguration för enklare administration och resursdelning över flera databaser."
keywords: skalbar databas, databaskonfiguration
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: 
ms.assetid: bf12594b-d258-40e6-a9fc-d8a8710c2d65
ms.service: sql-database
ms.devlang: NA
ms.date: 07/20/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f83e1aa30cfee86137c13c3a15c0e989558c0df8


---
# <a name="create-a-new-elastic-database-pool-with-the-azure-portal"></a>Skapa en ny elastisk databaspool med Azure-portalen
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
> 
> 

Den här artikeln visar hur du skapar en skalbar [elastisk databaspool](sql-database-elastic-pool.md) med [Azure-portalen](https://portal.azure.com/). Det finns två sätt att skapa en pool. Du kan göra det från grunden om du vet vilken typ av pool du vill ha, eller så kan du börja med en rekommendation från tjänsten. SQL Database har inbyggd intelligens som rekommenderar en pool-installationen om det är mer kostnadseffektivt baserat på den senaste användningstelemetrin för dina databaser.

Du kan lägga till flera pooler till en server, men du kan inte lägga till databaser från olika servrar i samma pool. För att skapa en pool, behöver du minst en databas på en V12-server. Om du inte har någon, kan du [Skapa din första Azure SQL-databas](sql-database-get-started.md). Du kan skapa en pool med bara en databas, men pooler är bara kostnadseffektiva med flera databaser. Se [pris- och prestandaöverväganden för en elastisk databaspool](sql-database-elastic-pool-guidance.md).

> [!NOTE]
> Elastiska pooler är allmänt tillgängliga (GA) i alla Azure-regioner utom Västra Indien där de genomgår förhandsgranskning.  GA för elastiska pooler i den här regionen inträffar så snart som möjligt. 
> 
> 

## <a name="step-1-create-a-new-pool"></a>Steg 1: Skapa en ny pool
Den här artikeln visar hur du skapar en ny pool på ett befintligt **serverblad** i portalen, vilket är det enklaste sättet för att flytta befintliga databaser till en pool. 

> [!NOTE]
> Oavsett om du redan har en server eller inte, kan du också skapa en ny pool på bladet **Elastiska SQL-pooler** (under listan till vänster i portalen klickar du på **Bläddra** **>** **Elastiska SQL-pooler**). Klicka på **+ Lägg till** på bladet **Elastiska SQL-pooler** för att skapa en ny server under arbetsflödet för pooldistribution.
> 
> 

1. I [Azure Portal](http://portal.azure.com/) nedanför listan till vänster, klickar du på **Bläddra** **>** **SQL-servrar** och klickar sedan på den server som innehåller databaserna som du vill lägga till i en poolen.
2. Klicka på **Ny pool**.
   
    ![Lägg till poolen till en server](./media/sql-database-elastic-pool-create-portal/new-pool.png)
   
    **-ELLER-**
   
    Så kan du få ett meddelande om att det finns rekommenderade elastiska databaspooler för servern (enbart V12). Klicka på meddelandet för att se de rekommenderade poolerna baserat på historisk telemetri över databasanvändningen och klicka på nivån för att visa mer information och anpassa poolen. Se [Förstå pool-rekommendationer](#understand-pool-recommendations) lite senare i ämnet för hur rekommendationen görs.
   
    ![rekommenderad pool](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)
   
    Bladet **Elastisk databaspool** visas och det är där du ställer in din pool. Om du klickade på **Ny pool** i det föregående steget, väljer portalen en **Standardpool** under **Prisnivå**, ett unikt **Namn** för poolen och en standardkonfiguration för poolen. Om du valt en rekommenderad pool så är rekommenderad nivå och konfiguration redan valt, men du kan ändå ändra dem.
   
    ![Konfigurera elastisk pool](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)
3. Ange ett namn för den elastiska poolen, eller lämna den som standard.

## <a name="step-2-choose-a-pricing-tier"></a>Steg 2: Välj prisnivå.
Prisnivån för poolen avgör vilka funktioner som finns tillgängliga för de elastiska databaserna i poolen och maximala antalet eDTU:er (eDTU MAX) och lagringsutrymme (GB) som finns tillgängliga för varje databas. Mer information finns i tjänstnivåer.

Om du vill ändra prisnivå för poolen klickar du på **Prisnivå**, klickar på den prisnivå du vill ha och klickar sedan på **Välj**.

> [!IMPORTANT]
> När du valt prisnivå och bekräftat ditt val genom att klicka på **OK** i det sista steget, kommer du inte kunna ändra prisnivå för poolen igen. För att ändra prisnivå för en befintlig elastisk pool, kan du skapa en ny elastisk pool i den önskade prisnivån och sedan migrera de elastiska databaserna till den nya poolen.
> 
> 

![Välj en prisnivå](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## <a name="step-3-configure-the-pool"></a>Steg 3: Konfigurera poolen
Efter att du ställt in prisnivå, klickar du på Konfigurera pool, där du lägger till databaser, ställer in pool-eDTU:er och lagrings (pool-GB) och ställer in min och mas eDTU:er för de elastiska databaserna i poolen.

1. Klicka på **Konfigurera pool**
2. Välj de databaser som du vill lägga till i poolen. Det här steget är valfritt medan du skapar poolen. Databaser kan läggas till efter att poolen har skapats.
    För att lägga till databaser, klickar du på **Lägg till databas**, klickar på de databaser som du vill lägga till och klickar sedan på knappen **Välj**.
   
    ![Lägg till databaser](./media/sql-database-elastic-pool-create-portal/add-databases.png)
   
    Om de databaser du arbetar med har tillräckligt med historisk användningstelemetri, kommer stapeldiagrammen **Beräknad eDTU- och GB-användning** och **Faktisk eDTU-användning** att uppdateras för att hjälpa dig att fatta konfigurationsbeslut. Tjänsten kan också ge dig ett rekommendationsmeddelande för att hjälpa dig få rätt storlek på poolen. Se [Dynamiska rekommendationer](#dynamic-recommendations).
3. Använd kontrollerna på sidan **Konfigurera pool** för att kolla igenom inställningarna och konfigurera din pool. Se [Begränsningar för elastiska pooler](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) för mer information om begränsningarna för varje tjänstnivå och se [Pris- och prestandaöverväganden för elastiska databaspooler](sql-database-elastic-pool-guidance.md) för detaljerad information om rätt storlek för en pool. För mer information om poolinställningar kan du se [Egenskaper för elastiska databaspooler](sql-database-elastic-pool.md#elastic-database-pool-properties).
   
    ![Konfigurera elastisk pool](./media/sql-database-elastic-pool-create-portal/configure-performance.png)
4. Klicka på **Välj** på bladet **Konfigurera pool**, efter att du har ändrat inställningarna.
5. Klicka på **OK** för att skapa poolen.

## <a name="understand-pool-recommendations"></a>Förstå poolrekommendationer
SQL Database-tjänsten utvärderar användningshistorik och rekommenderar en eller flera pooler när det är mer kostnadseffektivt än att använda enskilda databaser. Varje rekommendation är konfigurerad med en unik delmängd av serverns databaser som bäst passar i poolen.

![rekommenderad pool](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

Pool-rekommendationerna omfattar:

* En prisnivå för poolen (Basic, Standard eller Premium)
* Lämpliga **POOL-eDTU:er** (kallas även Max eDTU:er per pool)
* **eDTU MAX** och **eDTU Min** per databas
* Listan över rekommenderade databaser för poolen

Tjänsten tar hänsyn till de senaste 30 dagarnas telemetri vid rekommendation av pooler. För att en databas ska anses som en kandidat för en elastisk databaspool, måste den ha funnits i minst 7 dagar. Databaser som redan finns i en elastisk databaspool anses inte vara kandidater för elastiska databaspool-rekommendationer.

Tjänsten utvärderar resursbehov och kostnadseffektivitet vid flytt av de enskilda databaserna i varje tjänstnivå till pooler inom samma nivå. Alla standard-databaser på servern utvärderas exempelvis för hur de skulle passa in i en standard elastisk pool. Det innebär att tjänsten inte göra rekommendationer mellan olika nivåer, som att flytta en standard-databas till en premium-pool.

### <a name="dynamic-recommendations"></a>Dynamiska rekommendationer
Efter att databaserna lagts till i poolen, kommer rekommendationer att genereras dynamiskt baserat på historisk användning av databaserna du har valt. De här rekommendationerna kommer att visas i användningsdiagrammen eDTU och GB samt i en rekommendations-banderoll överst på **Konfigurera pool**-bladet. De här rekommendationerna är avsedda att hjälpa dig att skapa en pool som är optimerad för dina specifika databaser.

![dynamiska rekommendationer](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="additional-resources"></a>Ytterligare resurser
* [Hantera en SQL Database-elastisk pool med portalen](sql-database-elastic-pool-manage-portal.md)
* [Hantera en SQL Database-elastisk pool med PowerShell](sql-database-elastic-pool-manage-powershell.md)
* [Hantera en SQL Database-elastisk pool med C#](sql-database-elastic-pool-manage-csharp.md)
* [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md) 




<!--HONumber=Nov16_HO2-->


