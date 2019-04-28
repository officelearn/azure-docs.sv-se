---
title: Tillämpa prestandarekommendationer – Azure SQL Database | Microsoft Docs
description: Använd Azure-portalen för att hitta prestandarekommendationer som kan optimera prestanda för din Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: d80581aae56fc9d65d6f24d21f2c582cb74b3f2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420377"
---
# <a name="find-and-apply-performance-recommendations"></a>Hitta och tillämpa prestandarekommendationer

Du kan använda Azure-portalen för att hitta prestandarekommendationer som kan optimera prestanda för din Azure SQL Database eller åtgärda vissa problem som identifierats i din arbetsbelastning. **Prestanda rekommendation** sidan på Azure portal kan du hitta de övre rekommendationer baserat på deras potentiella inverkan. 

## <a name="viewing-recommendations"></a>Visa rekommendationer

Om du vill visa och tillämpa prestandarekommendationer, behöver du rätt [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) behörigheter i Azure. **Läsare**, **SQL DB-deltagare** behörigheter som krävs för att visa rekommendationer, och **ägare**, **SQL DB-deltagare** behörigheter krävs för att utföra några åtgärder; Skapa eller ta bort index och avbryta skapandet av index.

Använd följande steg för att hitta prestandarekommendationer på Azure-portalen:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till **alla tjänster** > **SQL-databaser**, och välj din databas.
3. Gå till **prestanda rekommendation** att visa tillgängliga rekommendationer för den valda databasen.

Prestandarekommendationer visas i tabellen som liknar den som visas på följande bild:

![Rekommendationer](./media/sql-database-advisor-portal/recommendations.png)

Rekommendationer sorteras efter deras inverkan på prestanda i följande kategorier:

| Påverkan | Beskrivning |
|:--- |:--- |
| Hög |Rekommendationer för hög påverkan bör ge mest betydande prestandapåverkan. |
| Medel |Medelstor inverkan rekommendationer bör förbättra prestanda, men inte avsevärt. |
| Låg |Låg inverkan rekommendationer bör ge bättre prestanda än utan, men förbättringar är eventuellt inte märkbar. |


> [!NOTE]
> Azure SQL-databasen måste övervaka aktiviteter på minst en dag för att identifiera några rekommendationer. Azure SQL Database kan enklare att optimera för konsekvent frågemönster än den kan för slumpmässiga spotty ökningar av aktivitet. Om rekommendationer inte är tillgängliga, den **prestanda rekommendation** sidan innehåller ett meddelande som förklarar varför.
> 

Du kan också visa status för tidigare åtgärder. Välj en rekommendation eller status som du vill se mer information.

Här är ett exempel på ”Skapa index” rekommendationen i Azure-portalen.

![Skapa index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Tillämpa rekommendationer
Azure SQL Database ger dig fullständig kontroll över hur rekommendationerna är aktiverat med någon av följande tre alternativ: 

* Använda enskilda rekommendationer en i taget.
* Aktivera den automatiska justeringskonfigurationen för att automatiskt tillämpa rekommendationer.
* För att implementera en rekommendation manuellt, kör du det rekommenderade T-SQL-skriptet mot din databas.

Välj en rekommendation att visa dess egenskaper och klicka sedan på **Visa skript** att granska information om hur rekommendationen skapas.

Databasen förblir online medan rekommendationen tillämpas – med hjälp av prestanda rekommendation eller automatisk justering aldrig tar en databas offline.

### <a name="apply-an-individual-recommendation"></a>Tillämpa en enskild rekommendation
Du kan granska och acceptera rekommendationer en i taget.

1. På den **rekommendationer** väljer du en rekommendation.
2. På den **information** klickar du på **tillämpa** knappen.
   
    ![Tillämpa rekommendation](./media/sql-database-advisor-portal/apply.png)

Valda rekommendationen tillämpas på databasen.

### <a name="removing-recommendations-from-the-list"></a>Tar bort rekommendationer från listan

Om din lista över rekommendationer innehåller objekt som du vill ta bort från listan kan ignorera du rekommendationen:

1. Välj en rekommendation i listan över **rekommendationer** att öppna informationen.
2. Klicka på **Ignorera** på den **information** sidan.

Om du vill kan du lägga till borttagna objekten tillbaka till den **rekommendationer** lista:

1. På den **rekommendationer** klickar du på **Visa ignorerade**.
2. Välj ett borttagna objekt från listan för att visa dess egenskaper.
3. Du kan också klicka på **ångra ta bort** att lägga till indexet huvudlistan av **rekommendationer**.

> [!NOTE]
> Observera att om SQL Database [automatisk justering](sql-database-automatic-tuning.md) aktiveras, och om du har manuellt bort en rekommendation i listan, sådana rekommendation kommer aldrig att tillämpas automatiskt. Tar bort en rekommendation är ett praktiskt sätt för användare att ha automatisk justering aktiverad i fall när kräver att en specifik rekommendation inte bör användas.
> Du kan återställa det här beteendet genom att lägga till ignorerade rekommendationer tillbaka till listan över rekommendationer genom att välja alternativet Ångra Ta bort.
> 

### <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning
Du kan ange Azure SQL Database att implementera rekommendationer automatiskt. När rekommendationerna blir tillgängliga, tillämpas de automatiskt. Precis som med alla rekommendationer som hanteras av tjänsten, om denna påverkan inte är negativt, är rekommendationen återställd.

1. På den **rekommendationer** klickar du på **automatisera**:
   
    ![Advisor-inställningar](./media/sql-database-advisor-portal/settings.png)
2. Välj åtgärder för att automatisera:
   
    ![Rekommenderat index](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Tänk på att **DROP_INDEX** alternativet är för närvarande inte kompatibelt med program som använder partition växla och index-tips. 
>

När du har valt önskade konfigurationen kan du klicka på Verkställ.

### <a name="manually-apply-recommendations-through-t-sql"></a>Tillämpa manuellt rekommendationer via T-SQL

Välj valfri rekommendation och klicka sedan på **Visa skript**. Kör skriptet mot databasen för att manuellt tillämpat rekommendationen.

*Index som körs manuellt inte övervakas och verifierat att prestanda påverkas av tjänsten* så vi rekommenderar att du övervaka dessa index när du har skapat för att verifiera de ge prestandavinster och justera eller ta bort dem om det behövs. Mer information om hur du skapar index finns i [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Dessutom förblir manuellt applicerade rekommendationer aktiv och visas i listan över rekommendationer för 24 – 48 timmar. innan systemet automatiskt återkallar dem. Om du vill ta bort en rekommendation tidigare kan du manuellt ta bort den.

### <a name="canceling-recommendations"></a>Avbryter rekommendationer

Rekommendationer som finns i en **väntande**, **verifierar**, eller **lyckades** status kan avbrytas. Rekommendationer med statusen **kör** kan inte annulleras.

1. Välj en rekommendation i den **justering historik** område för att öppna den **rekommendationsdetaljer** sidan.
2. Klicka på **Avbryt** att avbryta processen med Tillämpandet av rekommendationen.

## <a name="monitoring-operations"></a>Övervakningsåtgärder

Tillämpa en rekommendation kanske inte omedelbart sker. Portalen innehåller information om status för rekommendation. Följande är möjliga tillstånd som ett index kan vara:

| Status | Beskrivning |
|:--- |:--- |
| Väntande åtgärder |Tillämpa rekommendation kommandot har tagits emot och är schemalagd för utförande. |
| Kör |Rekommendationen tillämpas. |
| Verifierar |Rekommendationen har tillämpats och tjänsten mäter fördelarna. |
| Lyckades |Rekommendationen har tillämpats och har varit mäts fördelar. |
| Fel |Det uppstod ett fel i samband med tillämpandet av rekommendationen. Detta kan vara ett övergående problem eller eventuellt ett schema ändra i tabellen och skriptet är inte längre giltig. |
| Återkallande |Rekommendationen tillämpades, men har bedömts vara icke-högpresterande och återställs automatiskt. |
| Återkallat |Rekommendationen har återställts. |

Klicka på ett pågående rekommendation från listan över vill se mer information:

![Rekommenderat index](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Återställa en rekommendation
Om du tillämpat rekommendationen prestandarekommendationer återgår (vilket innebär att du inte manuellt köra T-SQL-skript), automatiskt den ändringen om den hittar prestandapåverkan vara negativ. Om du av någon anledning du bara vill återställa en rekommendation, kan du göra följande:

1. Välj en rekommendation som har tillämpats på den **Justeringshistorik** området.
2. Klicka på **Revert** på den **information om rekommendationen** sidan.

![Rekommenderat index](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Övervakning av prestanda påverkas av indexrekommendationer
När rekommendationerna har implementerats (för närvarande, index-åtgärder och Parameterisera frågorna rekommendationer endast), kan du klicka på **Frågeinsikter** på rekommendation sidan information om du vill öppna [fråga Prestandainsikter](sql-database-query-performance.md) och se prestandapåverkan från återställning av dina viktigaste frågor.

![Övervaka prestanda påverkas](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Sammanfattning
Azure SQL Database innehåller rekommendationer för att förbättra prestanda för SQL-databasen. Genom att tillhandahålla T-SQL-skript kan få du hjälp i genom att optimera din databas och slutligen förbättra frågeprestanda.

## <a name="next-steps"></a>Nästa steg
Övervaka dina rekommendationer och fortsätta att använda dem för att förbättra prestanda. Databasarbetsbelastningar är dynamisk och ändras kontinuerligt. Azure SQL Database fortsätter att övervaka och rekommendationer som kan förbättra databasens prestanda. 

* Se [automatisk justering](sql-database-automatic-tuning.md) mer information om automatisk justering i Azure SQL Database.
* Se [prestandarekommendationer](sql-database-advisor.md) en översikt över Azure SQL Database prestandarekommendationer.
* Se [fråga Prestandainsikter](sql-database-query-performance.md) att lära dig om hur du visar prestandapåverkan från återställning av dina viktigaste frågor.

## <a name="additional-resources"></a>Ytterligare resurser
* [Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
* [SKAPA INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)

