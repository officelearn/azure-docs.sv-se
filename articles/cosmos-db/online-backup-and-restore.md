---
title: Säkerhets kopiering online och data återställning på begäran i Azure Cosmos DB
description: Den här artikeln beskriver hur automatisk säkerhets kopiering, data återställning på begäran fungerar, hur du konfigurerar säkerhets kopierings intervall och kvarhållning i Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 0db34a615c9d92401e760c702feb0dbbf13ce01d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803882"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Säkerhets kopiering online och data återställning på begäran i Azure Cosmos DB

Azure Cosmos DB automatiskt tar säkerhets kopior av dina data med jämna mellanrum. Automatiska säkerhets kopieringar tas utan att påverka databas åtgärdernas prestanda eller tillgänglighet. Alla säkerhets kopior lagras separat i en lagrings tjänst och de säkerhets kopiorna replikeras globalt för återhämtning mot regionala haverier. Automatiska säkerhets kopieringar är användbara i scenarier när du oavsiktligt tar bort eller uppdaterar ditt Azure Cosmos-konto, din databas eller behållare och senare behöver data återställningen.

## <a name="automatic-and-online-backups"></a>Automatisk säkerhets kopiering och onlinesäkerhetskopiering

Med Azure Cosmos DB, inte bara för dina data, utan även säkerhets kopior av dina data är mycket redundanta och elastiska för regional katastrofer. Följande steg visar hur Azure Cosmos DB utför säkerhets kopiering av data:

* Azure Cosmos DB automatiskt tar en fullständig säkerhets kopia av databasen var fjärde timme och när som helst, så lagras bara de senaste två säkerhets kopiorna som standard. Om standard intervallen inte räcker för dina arbets belastningar kan du ändra säkerhets kopierings intervallet och kvarhållningsperioden från Azure Portal. Du kan ändra konfigurationen för säkerhets kopiering under eller efter att Azure Cosmos-kontot har skapats. Om behållaren eller databasen tas bort behåller Azure Cosmos DB befintliga ögonblicks bilder av en specifik behållare eller databas i 30 dagar.

* Azure Cosmos DB lagrar dessa säkerhets kopior i Azure Blob Storage, medan faktiska data finns lokalt inom Azure Cosmos DB.

* För att garantera låg latens lagras ögonblicks bilden av säkerhets kopian i Azure Blob Storage i samma region som den aktuella Skriv regionen (eller **någon** av de skrivskyddade regionerna, om du har en Skriv konfiguration i flera regioner). Som haveriberedskap replikeras alla ögonblicksbilder av säkerhetskopierade data i Azure Blob Storage till en annan region via geo-redundant lagring (GRS). Den region som säkerhetskopian replikeras till baseras på källregionen och det regionala par som källregionen är kopplad till. Mer information finns i [listan över geo-redundanta par av Azure-regioner](../best-practices-availability-paired-regions.md) . Du kan inte komma åt den här säkerhetskopian direkt. Azure Cosmos DB-teamet återställer din säkerhetskopia när du begär det via en supportbegäran.

   Följande bild visar hur en Azure Cosmos-behållare med alla de tre primära fysiska partitionerna i västra USA säkerhets kopie ras i ett fjärran slutet Azure Blob Storage-konto i USA och sedan replikeras till USA, östra:

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Regelbundna fullständiga säkerhets kopieringar av alla Cosmos DB entiteter i GRS Azure Storage" border="false":::

* Säkerhets kopiorna tas utan att påverka programmets prestanda eller tillgänglighet. Azure Cosmos DB utför säkerhets kopiering av data i bakgrunden utan att behöva använda ytterligare allokerat data flöde (ru: er) eller påverka databasens prestanda och tillgänglighet.

## <a name="options-to-manage-your-own-backups"></a>Alternativ för att hantera dina egna säkerhets kopior

Med Azure Cosmos DB SQL API-konton kan du även underhålla dina egna säkerhets kopior genom att använda någon av följande metoder:

* Använd [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta data regelbundet till valfri lagrings plats.

* Använd Azure Cosmos DB [ändra feed](change-feed.md) för att läsa data regelbundet för fullständiga säkerhets kopieringar eller för stegvisa ändringar och lagra dem i ditt eget lagrings utrymme.

## <a name="modify-the-backup-interval-and-retention-period"></a>Ändra intervallet för säkerhets kopiering och kvarhållningsperiod

Azure Cosmos DB automatiskt tar en fullständig säkerhets kopia av dina data varje 4 timme och när som helst, lagras de senaste två säkerhets kopiorna. Den här konfigurationen är standard alternativet och erbjuds utan extra kostnad. Du kan ändra standard intervallet för säkerhets kopiering och kvarhållningsperiod när du skapar ett Azure Cosmos-konto eller när kontot har skapats. Konfigurationen för säkerhetskopiering anges på kontonivå i Azure Cosmos och du måste ange inställningen för varje konto. När du har konfigurerat alternativ för säkerhets kopiering för ett konto tillämpas det på alla behållare i kontot. Du kan för närvarande bara ändra säkerhetskopieringsalternativ via Azure-portalen.

Om du av misstag har tagit bort eller skadat dina data, **innan du skapar en support förfrågan för att återställa data, måste du öka säkerhets kopians kvarhållning för ditt konto till minst sju dagar. Det är bäst att öka din kvarhållning inom 8 timmar efter den här händelsen.** På så sätt har Azure Cosmos DB-teamet tillräckligt med tid för att återställa ditt konto.

Använd följande steg för att ändra standard alternativ för säkerhets kopiering för ett befintligt Azure Cosmos-konto:

1. Logga in på [Azure Portal.](https://portal.azure.com/)
1. Gå till ditt Azure Cosmos-konto och öppna fönstret **säkerhetskopiera & återställning** . Uppdatera säkerhets kopierings intervallet och lagrings perioden för säkerhets kopior som krävs.

   * **Säkerhets kopierings intervall** – det är det intervall med vilket Azure Cosmos DB försöker säkerhetskopiera dina data. Säkerhets kopieringen tar en tids period som inte är noll och i vissa fall kan det potentiellt uppstå problem på grund av underordnade beroenden. Azure Cosmos DB försöker med att göra en säkerhets kopia vid det konfigurerade intervallet, men garanterar inte att säkerhets kopieringen har slutförts inom detta tidsintervall. Du kan konfigurera det här värdet i timmar eller minuter. Intervallet för säkerhets kopiering kan inte vara mindre än 1 timme och större än 24 timmar. När du ändrar intervallet börjar det nya intervallet gälla från och med den tidpunkt då den senaste säkerhets kopieringen utfördes.

   * **Kvarhållning av säkerhets kopior** – den representerar den period där varje säkerhets kopia behålls. Du kan konfigurera den i timmar eller dagar. Den minsta kvarhållningsperioden får inte vara mindre än två gånger intervallet för säkerhets kopiering (i timmar) och får inte vara större än 720 timmar.

   * **Kopior av data som behålls** – som standard erbjuds två säkerhets kopior av dina data utan kostnad. Du får ytterligare en avgift om du behöver fler än två kopior. Mer information om priset för ytterligare kopior finns i avsnittet Förbrukat lagringsutrymme på [sidan Prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="Regelbundna fullständiga säkerhets kopieringar av alla Cosmos DB entiteter i GRS Azure Storage" border="true":::

Om du konfigurerar säkerhets kopierings alternativ när kontot skapas kan du konfigurera **säkerhets kopierings principen**, som är antingen **Periodisk** eller **kontinuerlig**. Med den periodiska principen kan du konfigurera intervallet för säkerhets kopiering och kvarhållning av säkerhets kopior. Den kontinuerliga principen är för närvarande endast tillgänglig vid registrering. Azure Cosmos DBs teamet kommer att bedöma din arbets belastning och godkänna din begäran.

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="Regelbundna fullständiga säkerhets kopieringar av alla Cosmos DB entiteter i GRS Azure Storage" border="true":::

## <a name="restore-data-from-an-online-backup"></a>Återställa data från en säkerhets kopia online

Du kan oavsiktligt ta bort eller ändra dina data i något av följande scenarier:  

* Ta bort hela Azure Cosmos-kontot.

* Ta bort en eller flera Azure Cosmos-databaser.

* Ta bort en eller flera Azure Cosmos-behållare.

* Ta bort eller ändra Azure Cosmos-objekten (till exempel dokument) i en behållare. Detta specialfall kallas vanligt vis för skadade data.

* En delad erbjudande databas eller behållare i en delad erbjudande databas tas bort eller skadas.

Azure Cosmos DB kan återställa data i alla scenarier ovan. När du återställer skapas ett nytt Azure Cosmos-konto för att lagra återställda data. Namnet på det nya kontot, om det inte har angetts, kommer att ha formatet `<Azure_Cosmos_account_original_name>-restored1` . Den sista siffran ökar stegvis när flera återförsök görs. Du kan inte återställa data till ett redan skapat Azure Cosmos-konto.

När du råkar ta bort ett Azure Cosmos-konto kan vi återställa data till ett nytt konto med samma namn, förutsatt att konto namnet inte används. Därför rekommenderar vi att du inte skapar kontot igen när du har tagit bort det. Eftersom den inte bara förhindrar att återställda data använder samma namn, men även identifierar rätt konto för att återställa från svårt.

När du tar bort en Azure Cosmos-databas av misstag kan vi återställa hela databasen eller en delmängd av behållarna i databasen. Det går också att välja vissa behållare i databaser och återställa dem till ett nytt Azure Cosmos-konto.

När du oavsiktligt tar bort eller ändrar ett eller flera objekt i en behållare (skadade data) måste du ange hur lång tid som ska återställas. Tiden är viktig om data skadas. Eftersom behållaren är Live körs säkerhets kopieringen fortfarande, så om du väntar bortom kvarhållningsperioden (Standardvärdet är åtta timmar) kommer säkerhets kopiorna att skrivas över. **För att förhindra att säkerhets kopian skrivs över ökar du säkerhets kopians kvarhållning för ditt konto till minst sju dagar. Det är bäst att öka din kvarhållning inom 8 timmar från skadade data.**

Om du av misstag har tagit bort eller skadat dina data bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom 8 timmar så att Azure Cosmos DB-teamet kan hjälpa dig att återställa data från säkerhets kopiorna. På så sätt får Azure Cosmos DB support-teamet tillräckligt med tid för att återställa ditt konto.

> [!NOTE]
> När du har återställt data överförs inte alla käll funktioner eller inställningar till det återställda kontot. Följande inställningar överförs inte till det nya kontot:

> * Åtkomst kontrol listor för VNET
> * Lagrade procedurer, utlösare och användardefinierade funktioner
> * Inställningar för flera regioner  

Om du etablerar data flöde på databas nivå sker säkerhets kopierings-och återställnings processen i det här fallet på hela databas nivån och inte på de enskilda behållar nivåerna. I sådana fall kan du inte välja en delmängd av behållare som ska återställas.

## <a name="migrate-data-to-the-original-account"></a>Migrera data till det ursprungliga kontot

Det primära målet för data återställningen är att återställa de data som du av misstag har tagit bort eller ändrat. Vi rekommenderar därför att du först kontrollerar innehållet i de återställda data för att se till att det innehåller det du förväntar dig. Senare kan du migrera data tillbaka till det primära kontot. Även om det är möjligt att använda det återställda kontot som ditt nya aktiva konto, är det inte ett rekommenderat alternativ om du har produktions arbets belastningar.  

Följande är olika sätt att migrera data tillbaka till det ursprungliga Azure Cosmos-kontot:

* Använd [verktyget för migrering av Azure Cosmos db data](import-data.md).
* Använd [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Använd [ändra feed](change-feed.md) i Azure Cosmos dB.
* Du kan skriva en egen anpassad kod.

Se till att ta bort de återställda kontona så snart du har migrerat dina data, eftersom de kommer att medföra kontinuerliga kostnader.

## <a name="next-steps"></a>Nästa steg

Härnäst kan du lära dig hur du återställer data från ett Azure Cosmos-konto eller Lär dig hur du migrerar data till ett Azure Cosmos-konto

* Om du vill göra en återställnings förfrågan kontaktar du Azure-supporten, [File a Ticket från Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Så här återställer du data från ett Azure Cosmos-konto](how-to-backup-and-restore.md)
* [Använd Cosmos DB ändra feed](change-feed.md) för att flytta data till Azure Cosmos dB.
* [Använd Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta Data till Azure Cosmos dB.

