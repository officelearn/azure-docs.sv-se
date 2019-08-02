---
title: Använd prestanda rekommendationer – Azure SQL Database | Microsoft Docs
description: Använd Azure Portal för att hitta prestanda rekommendationer som kan optimera prestanda för dina Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b6bcb2650cd11ed97b6e81fda6aaafe7fca61202
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569598"
---
# <a name="find-and-apply-performance-recommendations"></a>Hitta och Använd prestanda rekommendationer

Du kan använda Azure Portal för att hitta prestanda rekommendationer som kan optimera prestanda för dina Azure SQL Database eller för att åtgärda problem som identifieras i din arbets belastning. Med sidan **prestanda rekommendation** i Azure Portal kan du hitta de bästa rekommendationerna utifrån deras potentiella påverkan. 

## <a name="viewing-recommendations"></a>Visa rekommendationer

Om du vill visa och tillämpa prestanda rekommendationer behöver du rätt [rollbaserad åtkomst kontroll](../role-based-access-control/overview.md) behörighet i Azure. Du måste ha behörighet för **SQL DB-deltagare** för att kunna visa rekommendationer och **ägare**, behörigheter för **SQL DB-deltagare** krävs för att utföra åtgärder. Skapa eller släpp index och Avbryt skapandet av index.

Använd följande steg för att hitta prestanda rekommendationer för Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till **alla Services** > **SQL-databaser**och välj din databas.
3. Gå till **prestanda rekommendation** om du vill visa tillgängliga rekommendationer för den valda databasen.

Prestanda rekommendationer visas i tabellen som liknar den som visas på följande bild:

![Rekommendationer](./media/sql-database-advisor-portal/recommendations.png)

Rekommendationerna sorteras efter deras potentiella påverkan på prestanda i följande kategorier:

| Påverkan | Beskrivning |
|:--- |:--- |
| Hög |Rekommendationer för hög påverkan bör ge den mest betydande prestanda påverkan. |
| Medel |Rekommendationer för medelhög påverkan bör förbättra prestanda, men inte i stor utsträckning. |
| Låg |Rekommendationer för låg påverkan bör ge bättre prestanda än utan, men förbättringar kan vara betydande. |


> [!NOTE]
> Azure SQL Database behöver övervaka aktiviteter minst under en dag för att kunna identifiera några rekommendationer. Azure SQL Database kan enklare optimeras för konsekventa frågeuttryck än den kan för slumpmässiga Spotty-burst-aktiviteter. Om inga rekommendationer för närvarande är tillgängliga, visar sidan **prestanda rekommendation** ett meddelande som förklarar varför.
> 

Du kan också visa status för de historiska åtgärderna. Välj en rekommendation eller status om du vill se mer information.

Här är ett exempel på rekommendationen "skapa index" i Azure Portal.

![Skapa index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Använda rekommendationer
Azure SQL Database ger dig fullständig kontroll över hur rekommendationerna aktive ras med något av följande tre alternativ: 

* Tillämpa enskilda rekommendationer en i taget.
* Aktivera automatisk justering för att automatiskt tillämpa rekommendationer.
* Om du vill implementera en rekommendation manuellt kör du det rekommenderade T-SQL-skriptet mot databasen.

Välj en rekommendation om du vill visa information om den och klicka sedan på **Visa skript** för att granska exakt information om hur rekommendationen skapas.

Databasen är online medan rekommendationen tillämpas – användning av prestanda rekommendation eller automatisk justering tar aldrig en databas offline.

### <a name="apply-an-individual-recommendation"></a>Tillämpa en individuell rekommendation
Du kan granska och godkänna rekommendationer en i taget.

1. På sidan **rekommendationer** väljer du en rekommendation.
2. Klicka på knappen **Använd** på sidan **information** .
   
    ![Använd rekommendation](./media/sql-database-advisor-portal/apply.png)

Den valda rekommendationen har tillämpats på databasen.

### <a name="removing-recommendations-from-the-list"></a>Ta bort rekommendationer från listan

Om listan över rekommendationer innehåller objekt som du vill ta bort från listan kan du ignorera rekommendationen:

1. Välj en rekommendation i listan med **rekommendationer** för att öppna informationen.
2. Klicka på **Ignorera** på **informations** sidan.

Om du vill kan du lägga tillbaka borttagna objekt till listan **rekommendationer** :

1. Klicka på **Visa ignorerade**på sidan **rekommendationer** .
2. Välj ett borttaget objekt i listan om du vill visa information om det.
3. Du kan också klicka på **Ångra ignorera** för att lägga till indexet i huvud listan över **rekommendationer**.

> [!NOTE]
> Observera att om SQL Database [Automatisk justering](sql-database-automatic-tuning.md) är aktive rad och om du har avvisat en rekommendation manuellt från listan, kommer sådan rekommendation aldrig att tillämpas automatiskt. Att ta bort en rekommendation är ett praktiskt sätt för användarna att automatiskt aktivera automatisk justering i fall när det krävs att en speciell rekommendation inte bör tillämpas.
> Du kan återställa det här beteendet genom att lägga till ignorerade rekommendationer tillbaka till listan rekommendationer genom att välja alternativet ångra borttagning.
> 

### <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning
Du kan ställa in Azure SQL Database att implementera rekommendationer automatiskt. När rekommendationerna blir tillgängliga tillämpas de automatiskt. Som med alla rekommendationer som hanteras av tjänsten, om prestanda påverkan är negativ, återställs rekommendationen.

1. På sidan **rekommendationer** klickar du på **Automatisera**:
   
    ![Advisor-inställningar](./media/sql-database-advisor-portal/settings.png)
2. Välj åtgärder som ska automatiseras:
   
    ![Rekommenderade index](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observera att alternativet **DROP_INDEX** inte är kompatibelt med program som använder partitions växlings-och index tips. 
>

När du har valt önskad konfiguration klickar du på Använd.

### <a name="manually-apply-recommendations-through-t-sql"></a>Tillämpa rekommendationer manuellt via T-SQL

Välj en rekommendation och klicka sedan på **Visa skript**. Kör skriptet mot databasen för att manuellt tillämpa rekommendationen.

*Index som körs manuellt övervakas och verifieras inte för prestanda som påverkas av tjänsten* , så vi rekommenderar att du övervakar dessa index när du har skapat dem för att kontrol lera att de ger prestanda vinster och justerar eller tar bort dem vid behov. Mer information om hur du skapar index finns i [create index (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Dessutom förblir manuellt tillämpade rekommendationer aktiva och visas i listan över rekommendationer för 24-48 timmar. innan systemet återkallar dem automatiskt. Om du vill ta bort en rekommendation tidigare kan du ta bort den manuellt.

### <a name="canceling-recommendations"></a>Avbryta rekommendationer

Rekommendationer som har statusen **väntar**, **verifiering**eller **lyckad** kan avbrytas. Det går inte att avbryta rekommendationer med statusen **körs** inte.

1. Välj en rekommendation i avsnittet **Justera historik** för att öppna sidan **rekommendationer** .
2. Klicka på **Avbryt** om du vill avbryta processen med att tillämpa rekommendationen.

## <a name="monitoring-operations"></a>Övervakningsåtgärder

Att tillämpa en rekommendation kanske inte sker omedelbart. Portalen innehåller information om rekommendationens status. Följande är möjliga lägen som ett index kan vara i:

| Status | Beskrivning |
|:--- |:--- |
| Väntande |Kommandot Använd rekommendation har mottagits och är schemalagt för körning. |
| Kör |Rekommendationen har tillämpats. |
| Verifierar |Rekommendationen har tillämpats och tjänsten mäter fördelarna. |
| Klart |Rekommendationen har tillämpats och fördelarna har mätts. |
| Fel |Det uppstod ett fel i samband med tillämpandet av rekommendationen. Detta kan vara ett tillfälligt problem eller en schema ändring i tabellen och skriptet är inte längre giltigt. |
| Återkallande |Rekommendationen har tillämpats, men har bedömts vara icke-utfört och återställs automatiskt. |
| Återkallat |Rekommendationen har återställts. |

Klicka på en rekommendation i processen i listan om du vill se mer information:

![Rekommenderade index](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Återställa en rekommendation
Om du har använt prestanda rekommendationerna för att tillämpa rekommendationen (vilket innebär att du inte manuellt körde T-SQL-skriptet), återställer det automatiskt ändringen om det hittar prestanda påverkan som negativ. Om du av någon anledning vill återställa en rekommendation kan du göra följande:

1. Välj en rekommendation som har tillämpats i område för **justerings historik** .
2. Klicka på **Återställ** på sidan med **rekommendations information** .

![Rekommenderade index](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Övervaka prestanda påverkan av index rekommendationer
När rekommendationerna har implementerats (för närvarande, index åtgärder och Parameterisera fråge rekommendationer) kan du klicka på **frågor** och svar på rekommendations informations sidan för att öppna [frågor prestanda](sql-database-query-performance.md) insikter och se de vanligaste frågornas prestanda påverkan.

![Övervaka prestanda påverkan](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Sammanfattning
Azure SQL Database ger rekommendationer för att förbättra prestanda för SQL Database. Genom att tillhandahålla T-SQL-skript kan du få hjälp med att optimera databasen och slutligen förbättra frågans prestanda.

## <a name="next-steps"></a>Nästa steg
Övervaka dina rekommendationer och fortsätt att tillämpa dem för att förbättra prestanda. Databas arbets belastningar är dynamiska och ändras kontinuerligt. Azure SQL Database fortsätter att övervaka och tillhandahålla rekommendationer som kan förbättra databasens prestanda. 

* Se [Automatisk justering](sql-database-automatic-tuning.md) för att lära dig mer om den automatiska justeringen i Azure SQL Database.
* Se [prestanda rekommendationer](sql-database-advisor.md) för en översikt över Azure SQL Database prestanda rekommendationer.
* Mer information om hur du visar prestanda påverkan för dina vanligaste frågor finns i [fråga prestanda](sql-database-query-performance.md) insikter.

## <a name="additional-resources"></a>Ytterligare resurser
* [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
* [SKAPA INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)

