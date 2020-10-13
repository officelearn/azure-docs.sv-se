---
title: Återställa Azure Cosmos DB data från en säkerhets kopia
description: Den här artikeln beskriver hur du återställer Azure Cosmos DB data från en säkerhets kopia, hur du kontaktar Azure-supporten för att återställa data, steg som ska vidtas när data har återställts.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9956ca0ca9c0957557e7ee74883a75c074ff22f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797980"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Återställa data från en säkerhets kopia i Azure Cosmos DB

Om du av misstag tar bort databasen eller en behållare kan du välja [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/) för att återställa data från automatiska säkerhets kopieringar online. Support för Azure är tillgängligt för valda planer, till exempel **standard**, **utvecklare**och planer som är högre än de. Azure-supporten är inte tillgänglig med **Basic** -planen. Mer information om olika support avtal finns på sidan [support](https://azure.microsoft.com/support/plans/) avtal för Azure.

För att återställa en speciell ögonblicks bild av säkerhets kopieringen kräver Azure Cosmos DB att data är tillgängliga under tiden för säkerhets kopierings cykeln för ögonblicks bilden.

## <a name="request-a-restore"></a>Begär en återställning

Du bör ha följande information innan du begär en återställning:

* Ha ditt prenumerations-ID klart.

* Utifrån hur dina data har tagits bort eller ändrats av misstag bör du förbereda dig för att ha ytterligare information. Vi rekommenderar att du har den information som är tillgänglig i förväg för att minimera den säkerhets kopiering som kan skadas i vissa tids känsliga fall.

* Om hela Azure Cosmos DBs kontot tas bort måste du ange namnet på det borttagna kontot. Om du skapar ett annat konto med samma namn som det borttagna kontot, kan du dela det med support teamet, eftersom det hjälper till att fastställa rätt konto för att välja. Vi rekommenderar att du skiljer olika support biljetter för varje borttaget konto eftersom det minimerar förvirringen för återställnings läget.

* Om en eller flera databaser tas bort bör du ange ett Azure Cosmos-konto, samt databas namnen för Azure Cosmos och ange om det finns en ny databas med samma namn.

* Om en eller flera behållare tas bort bör du ange konto namnet för Azure-Cosmos, databas namn och behållar namnen. Och ange om det finns en behållare med samma namn.

* Om du av misstag har tagit bort eller skadat dina data bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom 8 timmar så att Azure Cosmos DB-teamet kan hjälpa dig att återställa data från säkerhets kopiorna. **Innan du skapar en supportbegäran för att återställa data, se till att [öka säkerhets kopians kvarhållning](online-backup-and-restore.md) för ditt konto till minst sju dagar. Det är bäst att öka din kvarhållning inom 8 timmar efter den här händelsen.** På så sätt får Azure Cosmos DB support-teamet tillräckligt med tid för att återställa ditt konto.

Förutom namnet på Azure Cosmos-kontot, databas namn, behållar namn, bör du ange den tidpunkt som data kan återställas till. Det är viktigt att vara så exakt som möjligt för att hjälpa oss att fastställa de bästa tillgängliga säkerhets kopiorna. **Det är också viktigt att ange tiden i UTC.**

Följande skärm bild visar hur du skapar en support förfrågan för en behållare (samling/graf/tabell) för att återställa data med hjälp av Azure Portal. Ange ytterligare information, till exempel typ av data, syftet med återställningen, tid när data togs bort för att prioritera begäran.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Skapa en support förfrågan för säkerhets kopiering med Azure Portal":::

## <a name="post-restore-actions"></a>Åtgärder efter återställning

När du har återställt data får du ett meddelande om namnet på det nya kontot (vanligt vis i formatet `<original-name>-restored1` ) och tiden då kontot återställdes till. Det återställda kontot kommer att ha samma allokerade data flöde, indexerings principer och finns i samma region som det ursprungliga kontot. En användare som är prenumerations administratör eller en medadministratör kan se det återställda kontot.

När data har återställts bör du kontrol lera och verifiera data i det återställda kontot och se till att det innehåller den version som du förväntar dig. Om allting ser bra ut bör du migrera tillbaka data till det ursprungliga kontot med hjälp av [Azure Cosmos DB ändra feed](change-feed.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Vi rekommenderar att du tar bort behållaren eller databasen direkt efter att ha migrerat data. Om du inte tar bort de återställda databaserna eller behållarna kommer de att debiteras för enheter för programbegäran, lagring och utgående trafik.

## <a name="next-steps"></a>Nästa steg

Nu kan du lära dig hur du migrerar data tillbaka till det ursprungliga kontot med hjälp av följande artiklar:

* Om du vill göra en återställnings förfrågan kontaktar du Azure-supporten, [File a Ticket från Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Använd Cosmos DB ändra feed](change-feed.md) för att flytta data till Azure Cosmos dB.

* [Använd Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta Data till Azure Cosmos dB.
