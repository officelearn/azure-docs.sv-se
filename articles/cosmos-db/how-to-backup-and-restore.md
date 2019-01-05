---
title: Hur du återställer Azure Cosmos DB-data från en säkerhetskopia
description: Den här artikeln beskrivs hur du återställer Azure Cosmos DB-data från en säkerhetskopia, så kontakta Azure-supporten om du vill återställa data, steg för att ta när data har återställts.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e21a3ae23ca6a856c9524e52e80f33362f53cae1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040854"
---
# <a name="how-to-restore-azure-cosmos-db-data-from-a-backup"></a>Hur du återställer Azure Cosmos DB-data från en säkerhetskopia

Om du råkar ta bort din databas eller en behållare kan du [öppna ett supportärende]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eller [anropa Azure-supporten]( https://azure.microsoft.com/support/options/) att återställa data från automatiska säkerhetskopior online. Azure-support är tillgängligt för planerna i de valda endast som **Standard**, ** Developer och planer som är högre än dem. Azure-support är inte tillgänglig med **grundläggande** plan. Läs mer om olika supportplaner, i den [supportavtal](https://azure.microsoft.com/support/plans/) sidan. 

Om du vill återställa en specifik ögonblicksbild av säkerhetskopian, kräver Azure Cosmos DB att data är tillgängliga under hela säkerhetskopieringscykel för denna ögonblicksbild.

## <a name="request-a-restore"></a>Begär en återställning

Du bör ha följande information innan du begär en återställning:

* Har prenumerations-ID är redo.

* Baserat på hur dina data råkar ta bort eller ändras, bör du förbereda ha ytterligare information. Det rekommenderas att du har uppgifterna vidare att minimera den fram och tillbaka som kan vara skadligt ibland tiden känslig.

* Om hela Azure Cosmos DB-kontot tas bort, måste du ange namnet på kontot. Om du skapar ett annat konto med samma namn som kontot kan du dela som med supporten eftersom det hjälper dig för att fastställa rätt typ av konto du väljer. Det rekommenderas att filen olika supportärenden för varje konto som har tagits bort eftersom den minimerar förvirring av tillståndet för återställningen.

* Om en eller flera databaser tas bort, bör du tillhandahåller Azure Cosmos-konto, samt Azure Cosmos-databasnamn och anger om det finns en ny databas med samma namn.

* Om en eller flera behållare är borttagna kan bör du ange namnet på Azure Cosmos-kontot, databasnamn och behållare namnen. Och ange om det finns en behållare med samma namn.

När data skadas och om dokumenten i en behållare har ändrats eller tagits bort, **ta bort behållaren så snart som möjligt**. Du kan undvika Azure Cosmos DB skriver över säkerhetskopiorna genom att ta bort behållaren. Om du av någon anledning att borttagningen inte är möjligt, du bör registrera en supportbegäran så snart som möjligt. Utöver Azure Cosmos-kontonamn, databasnamn, samlingsnamn, bör du ange punkten i tiden som data kan återställas till. Det är viktigt att vara så exakt som möjligt för att hjälpa oss att avgöra de bästa tillgängliga säkerhetskopiorna vid den tidpunkten. Det är också viktigt att ange hur lång tid i UTC. 

Följande skärmbild illustrerar hur du skapar en begäran om en container(collection/graph/table) återställa data med hjälp av Azure-portalen. Ange ytterligare information, till exempel typ av data, syftet med återställningen tid när data skedde som hjälper oss att prioritera begäran.

![Skapa en säkerhetskopiering supportbegäran med hjälp av Azure portal](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Efter återställning åtgärder

När du återställer data kan du få ett meddelande om namnet på det nya kontot (det är vanligtvis i formatet `<original-name>-restored1`) och hur lång tid när kontot har återställts till. Återställda kontot har samma etablerat dataflöde indexeringsprinciper och den är i samma region som det ursprungliga kontot. En användare som är prenumerationsadministratör eller en coadmin kan se det återställda kontot.

När data har återställts, bör du granska och verifiera data i det återställda kontot och kontrollera att den innehåller den version som du förväntar dig. Om allt ser bra ut, bör du migrera data tillbaka till din ursprungliga kontot med [Azure Cosmos DB-ändringsflödet](change-feed.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Det rekommenderas att du tar bort behållare eller databasen omedelbart när du har migrerat data. Om du inte tar bort den återställda databaser eller behållare, kommer de resultera i kostnader för programbegäran, lagring och utgående trafik.

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig om hur du migrerar data tillbaka till ditt ursprungliga konto med följande artiklar:

* Att göra en återställning för begäran, kontakta Azure-supporten [lämna in en biljett från Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Använd Cosmos DB-ändringsflödet](change-feed.md) att flytta data till Azure Cosmos DB.

* [Använda Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) att flytta data till Azure Cosmos DB.
