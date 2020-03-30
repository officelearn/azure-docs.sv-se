---
title: Återställa Azure Cosmos DB-data från en säkerhetskopia
description: I den här artikeln beskrivs hur du återställer Azure Cosmos DB-data från en säkerhetskopia, hur du kontaktar Azure-stöd för att återställa data, åtgärder som ska vidtas när data har återställts.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70240771"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Återställa data från en säkerhetskopia i Azure Cosmos DB 

Om du av misstag tar bort databasen eller en behållare kan du [lämna in en supportbiljett]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eller [ringa Azure-stöd]( https://azure.microsoft.com/support/options/) för att återställa data från automatiska onlinesäkerhetskopior. Azure-support är endast tillgänglig för valda planer, till exempel **Standard,** **Utvecklare**och planer som är högre än dem. Azure-support är **Basic** inte tillgänglig med Basic-planen. Mer information om olika supportplaner finns på sidan [Azure-supportplaner.](https://azure.microsoft.com/support/plans/) 

För att återställa en specifik ögonblicksbild av säkerhetskopian kräver Azure Cosmos DB att data är tillgängliga under hela säkerhetskopieringscykeln för den ögonblicksbilden.

## <a name="request-a-restore"></a>Begär en återställning

Du bör ha följande information innan du begär en återställning:

* Ha ditt prenumerations-ID klart.

* Baserat på hur dina data togs bort eller ändrades av misstag bör du förbereda dig för att få ytterligare information. Det rekommenderas att du har den information som finns tillgänglig framåt för att minimera fram och tillbaka som kan vara skadligt i vissa tidskänsliga fall.

* Om hela Azure Cosmos DB-kontot tas bort måste du ange namnet på det borttagna kontot. Om du skapar ett annat konto med samma namn som det borttagna kontot delar du det med supportteamet eftersom det hjälper till att avgöra rätt konto att välja. Vi rekommenderar att du lämnar in olika supportärenden för varje borttaget konto eftersom det minimerar förvirringen i återställningens tillstånd.

* Om en eller flera databaser tas bort bör du ange Azure Cosmos-kontot, samt Azure Cosmos-databasnamnen och ange om det finns en ny databas med samma namn.

* Om en eller flera behållare tas bort bör du ange Azure Cosmos-kontonamnet, databasnamnen och behållarnamnen. Och ange om det finns en behållare med samma namn.

* Om du av misstag har tagit bort eller skadat dina data bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom 8 timmar så att Azure Cosmos DB-teamet kan hjälpa dig att återställa data från säkerhetskopiorna.
  
  * Om du av misstag har tagit bort databasen eller behållaren öppnar du ett Sev B- eller Sev C Azure-supportärende. 
  * Om du av misstag har tagit bort eller skadat vissa dokument i behållaren öppnar du ett Sev A-supportärende. 

När data skadas och om dokumenten i en behållare ändras eller tas **bort, tar behållaren bort så snart som möjligt**. Genom att ta bort behållaren kan du undvika att Azure Cosmos DB skriver över säkerhetskopiorna. Om det av någon anledning inte är möjligt att ta bort den, bör du lämna in en biljett så snart som möjligt. Förutom Azure Cosmos-kontonamn, databasnamn, behållarnamn bör du ange den tidpunkt som data kan återställas till. Det är viktigt att vara så exakt som möjligt för att hjälpa oss att avgöra de bästa tillgängliga säkerhetskopiorna vid den tidpunkten. Det är också viktigt att ange tiden i UTC. 

Följande skärmbild illustrerar hur du skapar en supportbegäran för en behållare(insamling/diagram/tabell) för att återställa data med hjälp av Azure-portalen. Ange ytterligare information som typ av data, syftet med återställningen, tid då data togs bort för att hjälpa oss att prioritera begäran.

![Skapa en supportbegäran för säkerhetskopiering med Azure-portalen](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Åtgärder efter återställning

När du har återställt data får du ett meddelande om namnet på `<original-name>-restored1`det nya kontot (det är vanligtvis i formatet) och den tidpunkt då kontot återställdes till. Det återställda kontot har samma etablerade dataflöde, indexeringsprinciper och ligger i samma region som det ursprungliga kontot. En användare som är prenumerationsadministratör eller en coadmin kan se det återställda kontot.

När data har återställts bör du granska och validera data i det återställda kontot och se till att de innehåller den version som du förväntar dig. Om allt ser bra ut bör du migrera tillbaka data till ditt ursprungliga konto med hjälp av [Azure Cosmos DB-ändringsflöde](change-feed.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Det rekommenderas att du tar bort behållaren eller databasen omedelbart efter att du migrerat data. Om du inte tar bort de återställda databaserna eller behållarna medför de kostnader för begärandeenheter, lagring och utgående.

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig mer om hur du migrerar tillbaka data till ditt ursprungliga konto med hjälp av följande artiklar:

* Om du vill göra en återställningsbegäran kontaktar du Azure Support, [lämnar in en biljett från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Använd Cosmos DB ändra foder](change-feed.md) för att flytta data till Azure Cosmos DB.

* [Använd Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta data till Azure Cosmos DB.
