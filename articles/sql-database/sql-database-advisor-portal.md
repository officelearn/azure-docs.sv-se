---
title: Tillämpa prestandarekommendationer
description: Använd Azure-portalen för att hitta prestandarekommendationer som kan optimera prestanda för din Azure SQL-databas.
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
ms.openlocfilehash: b0452d51dc472e100ef52536d8e3814ff395292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214168"
---
# <a name="find-and-apply-performance-recommendations"></a>Hitta och tillämpa prestandarekommendationer

Du kan använda Azure-portalen för att hitta prestandarekommendationer som kan optimera prestanda för din Azure SQL-databas eller för att korrigera vissa problem som identifierats i din arbetsbelastning. Med sidan **Prestandarekommendation** i Azure-portalen kan du hitta de bästa rekommendationerna baserat på deras potentiella inverkan.

## <a name="viewing-recommendations"></a>Visa rekommendationer

Om du vill visa och tillämpa prestandarekommendationer behöver du rätt [rollbaserade åtkomstkontrollbehörigheter](../role-based-access-control/overview.md) i Azure. **Läsare,** **SQL DB Contributor-behörigheter** krävs för att visa rekommendationer och **ägare,** SQL DB **Contributor-behörigheter** krävs för att utföra alla åtgärder. skapa eller släppa index och avbryta indexskapande.

Så här hittar du prestandarekommendationer på Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till **Alla tjänster** > **SQL-databaser**och välj din databas.
3. Navigera till **prestandarekommendation** om du vill visa tillgängliga rekommendationer för den valda databasen.

Prestandarekommendationer visas i tabellen som liknar den som visas på följande bild:

![Rekommendationer](./media/sql-database-advisor-portal/recommendations.png)

Rekommendationerna sorteras efter deras potentiella inverkan på prestanda i följande kategorier:

| Påverkan | Beskrivning |
|:--- |:--- |
| Hög |Rekommendationer med stor effekt bör ge den mest betydande prestandaeffekten. |
| Medel |Rekommendationer med medelhög påverkan bör förbättra prestanda, men inte väsentligt. |
| Låg |Rekommendationer med låg påverkan bör ge bättre prestanda än utan, men förbättringar kanske inte är betydande. |

> [!NOTE]
> Azure SQL Database måste övervaka aktiviteter åtminstone för en dag för att identifiera några rekommendationer. Azure SQL Database kan lättare optimera för konsekventa frågemönster än det kan för slumpmässiga ojämna utbrott av aktivitet. Om rekommendationer inte är tillgängliga för närvarande innehåller sidan **Prestandarekommendation** ett meddelande som förklarar varför.

Du kan också visa status för de historiska operationerna. Välj en rekommendation eller status om du vill se mer information.

Här är ett exempel på rekommendationen "Skapa index" i Azure-portalen.

![Skapa index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Tillämpa rekommendationer

Azure SQL Database ger dig full kontroll över hur rekommendationer är aktiverade med något av följande tre alternativ:

* Tillämpa individuella rekommendationer en i taget.
* Aktivera den automatiska justeringen för att automatiskt tillämpa rekommendationer.
* Om du vill implementera en rekommendation manuellt kör du det rekommenderade T-SQL-skriptet mot databasen.

Välj en rekommendation om du vill visa dess information och klicka sedan på **Visa skript** för att granska exakt information om hur rekommendationen skapas.

Databasen är online medan rekommendationen tillämpas – med hjälp av prestandarekommendation eller automatisk justering tar aldrig en databas offline.

### <a name="apply-an-individual-recommendation"></a>Tillämpa en enskild rekommendation

Du kan granska och acceptera rekommendationer en i taget.

1. Välj en rekommendation på sidan **Rekommendationer.**
2. Klicka på Knappen **Använd** på sidan **Information.**

   ![Tillämpa rekommendation](./media/sql-database-advisor-portal/apply.png)

Vald rekommendation tillämpas i databasen.

### <a name="removing-recommendations-from-the-list"></a>Ta bort rekommendationer från listan

Om listan med rekommendationer innehåller objekt som du vill ta bort från listan kan du ignorera rekommendationen:

1. Välj en rekommendation i listan med **rekommendationer** för att öppna informationen.
2. Klicka på **Ignorera** på sidan **Information.**

Om du vill kan du lägga till ignorerade objekt i listan **Rekommendationer** igen:

1. Klicka på Visa **ignorerad**på sidan **Rekommendationer.**
2. Välj ett ignorerat objekt i listan om du vill visa dess information.
3. Du kan också klicka på **Ångra ignorera** om du vill lägga till indexet i huvudlistan **med rekommendationer**.

> [!NOTE]
> Observera att om automatisk [inställning av](sql-database-automatic-tuning.md) SQL Database är aktiverad, och om du manuellt har ignorerat en rekommendation från listan, kommer en sådan rekommendation aldrig att tillämpas automatiskt. Att ignorera en rekommendation är ett praktiskt sätt för användare att ha automatisk justering aktiverad i fall när det kräver att en viss rekommendation inte ska tillämpas.
> Du kan återställa det här beteendet genom att lägga till ignorerade rekommendationer i listan Rekommendationer genom att välja alternativet Ångra ignorera.

### <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

Du kan ange att Azure SQL Database ska implementera rekommendationer automatiskt. När rekommendationer blir tillgängliga tillämpas de automatiskt. Som med alla rekommendationer som hanteras av tjänsten, om prestandapåverkan är negativ, återställs rekommendationen.

1. På sidan **Rekommendationer** klickar du på **Automatisera:**

   ![Inställningar för rådgivare](./media/sql-database-advisor-portal/settings.png)
2. Välj åtgärder som ska automatiseras:

   ![Rekommenderade index](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observera att **DROP_INDEX** alternativet för närvarande inte är kompatibelt med program som använder partitionsväxling och indextips.

När du har valt önskad konfiguration klickar du på Verkställ.

### <a name="manually-apply-recommendations-through-t-sql"></a>Tillämpa rekommendationer manuellt via T-SQL

Markera en rekommendation och klicka sedan på **Visa skript**. Kör skriptet mot databasen för att manuellt tillämpa rekommendationen.

*Index som körs manuellt övervakas och valideras inte för prestandapåverkan av tjänsten,* så det föreslås att du övervakar dessa index när de har skapats för att verifiera att de tillhandahåller prestandavinster och justerar eller tar bort dem om det behövs. Mer information om hur du skapar index finns i [SKAPA INDEX (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) Dessutom kommer manuellt tillämpade rekommendationer att förbli aktiva och visas i listan över rekommendationer för 24-48 timmar. innan systemet automatiskt drar tillbaka dem. Om du vill ta bort en rekommendation tidigare kan du ignorera den manuellt.

### <a name="canceling-recommendations"></a>Avbryta rekommendationer

Rekommendationer som finns i **statusen Väntande,** **Validera eller** **Lycka till** kan avbrytas. Rekommendationer med **statusen Körning** kan inte avbrytas.

1. Välj en rekommendation i området **Justeringshistorik** om du vill öppna **informationssidan för rekommendationer.**
2. Klicka på **Avbryt** om du vill avbryta processen med att tillämpa rekommendationen.

## <a name="monitoring-operations"></a>Övervakning av åtgärder

Att tillämpa en rekommendation kanske inte sker omedelbart. Portalen innehåller information om rekommendationens status. Följande är möjliga tillstånd som ett index kan finnas i:

| Status | Beskrivning |
|:--- |:--- |
| Väntande åtgärder |Kommandot Apply rekommendation har tagits emot och är schemalagt för körning. |
| Verkställande |Rekommendationen tillämpas. |
| Validera |Rekommendationen har tillämpats och tjänsten mäter fördelarna. |
| Lyckades |Rekommendationen tillämpades framgångsrikt och förmåner har mätts. |
| Fel |Ett fel uppstod under processen för att tillämpa rekommendationen. Detta kan vara ett tillfälligt problem, eller möjligen en schemaändring i tabellen och skriptet är inte längre giltigt. |
| Återgå |Rekommendationen tillämpades, men har bedömts vara icke-performant och håller på att återställas automatiskt. |
| Återgick |Rekommendationen återgick. |

Klicka på en rekommendation i processen från listan om du vill se mer information:

![Rekommenderade index](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Återställa en rekommendation

Om du använde prestandarekommendationerna för att tillämpa rekommendationen (vilket innebär att du inte körde T-SQL-skriptet manuellt) återställs ändringen automatiskt om prestandapåverkan är negativ. Om du av någon anledning bara vill återställa en rekommendation kan du göra följande:

1. Välj en rekommendation som har tillämpats i området **Justeringshistorik.**
2. Klicka på **Återställ** på **informationssidan för rekommendationsinformation.**

![Rekommenderade index](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Övervakning av indexrekommendationernas prestandapåverkan

När rekommendationerna har implementerats (för närvarande kan indexåtgärder och parameterisera frågerekommendationer endast) klicka på Frågestatistik på **rekommendationsinformationssidan** för att öppna [Frågeprestandastatistik](sql-database-query-performance.md) och se prestandapåverkan från dina vanligaste frågor.

![Övervaka prestandapåverkan](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Sammanfattning

Azure SQL Database innehåller rekommendationer för att förbättra SQL-databasprestanda. Genom att tillhandahålla T-SQL-skript får du hjälp med att optimera databasen och i slutändan förbättra frågeprestanda.

## <a name="next-steps"></a>Nästa steg

Övervaka dina rekommendationer och fortsätta att tillämpa dem för att förfina prestanda. Databasarbetsbelastningar är dynamiska och ändras kontinuerligt. Azure SQL Database fortsätter att övervaka och ge rekommendationer som potentiellt kan förbättra databasens prestanda.

* Se [Automatisk justering](sql-database-automatic-tuning.md) om du vill veta mer om den automatiska justeringen i Azure SQL Database.
* Se [Prestandarekommendationer](sql-database-advisor.md) för en översikt över prestandarekommendationer för Azure SQL Database.
* Se [Frågeprestandastatistik](sql-database-query-performance.md) om du vill veta mer om hur du visar prestandapåverkan för dina vanligaste frågor.

## <a name="additional-resources"></a>Ytterligare resurser

* [Fråga Store](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)
