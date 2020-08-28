---
title: Hantera Azure Cosmos DB resurser med Azure Storage Explorer
description: Lär dig hur du ansluter till Azure Cosmos DB och hanterar dess resurser med Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1948ae186662c7f60f4d49c19a4d48b424a38f7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047487"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Arbeta med data med hjälp av Azure Storage Explorer

Med hjälp av Azure Cosmos DB i Azure Storage Explorer kan du hantera Azure Cosmos DB-entiteter, manipulera data och uppdatera lagrade procedurer och utlösare, och även andra Azure-entiteter som lagringsblobar och köer. Nu kan du använda samma verktyg för att hantera olika Azure-entiteter på ett och samma ställe. För tillfället stöder Azure Storage Explorer Cosmos-konton som kon figurer ATS för SQL-, MongoDB-, Graph-och table-API: er.


## <a name="prerequisites"></a>Krav

Ett Cosmos-konto med SQL API eller Azure Cosmos DB s API för MongoDB. Om du inte har ett konto kan du skapa en i Azure-portalen, enligt beskrivningen i [Azure Cosmos DB: Skapa en SQL API-webbapp med .NET och Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Installation

Installera den senaste versionen av Azure Storage Explorer här: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Nu finns det versioner för Windows, Linux och MAC.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. När du har installerat **Azure Storage Explorer**väljer du **plugin-** ikonen till vänster som visas i följande bild:

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Välj plugin-ikonen för att ansluta":::

2. Välj **Lägg till ett Azure-konto** och välj sedan **Logga in**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Anslut till den nödvändiga Azure-prenumerationen":::

2. I dialog rutan **Azure-inloggning** väljer du logga in och anger sedan dina autentiseringsuppgifter **för**Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Logga in på din Azure-prenumeration":::

3. Välj din prenumeration i listan och välj sedan **Använd**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Välj ett prenumerations-ID från listan att filtrera":::

    Explorer-fönstret uppdateras och visar konton i den valda prenumerationen.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Välj ett Azure Cosmos DB konto i listan tillgängliga":::

    Du har anslutit ditt **Cosmos DB-konto** till din Azure-prenumeration.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng

Ett alternativt sätt att ansluta till en Azure Cosmos DB är att använda en anslutningssträng. Följ stegen nedan för att ansluta med en anslutningssträng.

1. Hitta **Lokala och anslutna** i det vänstra trädet, högerklicka på **Cosmos DB-konton** och välj **Anslut till Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng":::

2. Stöder endast SQL- och Table-API för tillfället. Välj API, klistra in **anslutnings sträng**, **etikett**för ingående konto, Välj **Nästa** för att kontrol lera sammanfattningen och välj sedan **Anslut** för att ansluta Azure Cosmos DB konto. Information om hur du hämtar den primära anslutnings strängen finns i [Hämta anslutnings strängen](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Ange din anslutnings sträng":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Anslut till Azure Cosmos DB med en lokal emulator

Använd följande steg för att ansluta till en Azure Cosmos DB med en Emulator, stöder enbart SQL-konto för tillfället.

1. Installera Emulatorn och starta. Information om hur du installerar emulatorn finns i [Cosmos DB-emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Hitta **Lokala och anslutna** i det vänstra trädet, högerklicka på **Cosmos DB-konton** och välj **Anslut till Cosmos DB-emulator...**

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Ansluta till Azure Cosmos DB från emulatorn":::

3. Stöder endast SQL-API för tillfället. Klistra in **anslutnings sträng**, **etikett**för ingående konto, Välj **Nästa** för att kontrol lera sammanfattningen och välj sedan **Anslut** för att ansluta Azure Cosmos DB konto. Information om hur du hämtar den primära anslutnings strängen finns i [Hämta anslutnings strängen](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Ansluta till Cosmos DB från emulator-dialog rutan":::


## <a name="azure-cosmos-db-resource-management"></a>Resurshantering för Azure Cosmos DB

Du kan hantera ett Azure Cosmos DB-konto med följande åtgärder:
* Öppna kontot i Azure-portalen
* Lägg till resursen i listan Snabbåtkomst
* Sök och uppdatera resurser
* Skapa och ta bort databaser
* Skapa och ta bort samlingar
* Skapa, redigera, ta bort och filtrera dokument
* Hantera lagrade procedurer, utlösare och användardefinierade funktioner

### <a name="quick-access-tasks"></a>Uppgifter för snabbåtkomst

Genom att högerklicka på en prenumeration i Explorer-fönstret kan du utföra många snabbåtgärder:

* Högerklicka på ett Azure Cosmos DB-konto eller en databas så kan du välja alternativet för att **öppna i portalen** och hantera resursen i Azure-portalen i webbläsaren.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Öppna i portalen":::

* Du kan också lägga till Azure Cosmos DB-konto, databas och samling i **Snabbåtkomst**.
* **Sök härifrån** aktiverar nyckelordssökning under den valda sökvägen.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="sök härifrån":::

### <a name="database-and-collection-management"></a>Databas- och samlingshantering

#### <a name="create-a-database"></a>Skapa en databas

- Högerklicka Azure Cosmos DB-kontot, välj **Skapa databas** ange databasnamnet och slutför genom att trycka på **Retur**.

  :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Skapa en databas i ditt Azure Cosmos-konto":::

#### <a name="delete-a-database"></a>Ta bort en databas

- Högerklicka på databasen, Välj **ta bort databas**och välj **Ja** i popup-fönstret. Databasnoden tas bort och Azure Cosmos DB-konto uppdateras automatiskt.

  :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Ta bort den första databasen":::

  :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Ta bort de andra databaserna":::

#### <a name="create-a-collection"></a>Skapa en samling

1. Högerklicka på din databas, Välj **skapa samling**och ange sedan följande information, t. ex. **samlings-ID**, **lagrings kapacitet**osv. Klicka på **OK** för att slutföra.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Skapa första samlingen i databasen":::

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Skapa en andra samling i databasen":::

2. Välj **obegränsad** för att kunna ange partitionsnyckel och välj sedan **OK** för att slutföra.

    Om en partitionsnyckel används när du skapar en samling kan partitionsnyckelvärdet inte ändras i samlingen när den väl har skapats.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Konfigurera en partitionsnyckel":::

#### <a name="delete-a-collection"></a>Ta bort en samling

- Högerklicka på samlingen, Välj **ta bort samling**och välj sedan **Ja** i popup-fönstret.

    Samlingsnoden tas bort och databasen uppdateras automatiskt.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Ta bort en av samlingarna":::

### <a name="document-management"></a>Dokumenthantering

#### <a name="create-and-modify-documents"></a>Skapa och ändra dokument

- Om du vill skapa ett nytt dokument öppnar du **dokument** i det vänstra fönstret, väljer **nytt dokument**, redigerar innehållet i den högra rutan och väljer sedan **Spara**. Du kan också uppdatera ett befintligt dokument och sedan välja **Spara**. Ändringar kan ignoreras genom att klicka på **Discard** (Ignorera).

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Skapa ett nytt dokument":::

#### <a name="delete-a-document"></a>Ta bort ett dokument

- Klicka på knappen **Delete** (Ta bort) om du vill ta bort det valda dokumentet.

#### <a name="query-for-documents"></a>Fråga för dokument

- Redigera dokument filtret genom att ange en [SQL-fråga](how-to-sql-query.md) och välj sedan **Använd**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Fråga efter vissa dokument":::

### <a name="graph-management"></a>Graph-hantering

#### <a name="create-and-modify-vertex"></a>Skapa och ändra brytpunkt

1. Om du vill skapa ett nytt hörn öppnar du **Graph** från det vänstra fönstret, väljer **nytt hörn**, redigerar innehållet och väljer **OK**.
2. Om du vill ändra en befintlig topp markerar du Penn ikonen i den högra rutan.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Ändra ett diagrams hörn":::

#### <a name="delete-a-graph"></a>Ta bort en graf

- Om du vill ta bort ett formhörn väljer du pappers korgs ikonen bredvid namnet på hörn platsen.

#### <a name="filter-for-graph"></a>Filter för graf

- Redigera graf-filtret genom att ange en [Gremlin-fråga](gremlin-support.md) och välj sedan **Använd filter**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Köra en graf-fråga":::

### <a name="table-management"></a>Tabellhantering

#### <a name="create-and-modify-table"></a>Skapa och ändra tabell

1. För att skapa en ny tabell, öppna **entiteter** i det vänstra fönstret, Välj **Lägg till**, redigera innehållet i dialog rutan **Lägg till entitet** , Lägg till egenskap genom att klicka på knappen **Lägg till egenskap**och välj sedan **Infoga**.
2. Om du vill ändra en tabell väljer du **Redigera**, ändrar innehållet och väljer sedan **Uppdatera**.

   :::image type="content" source="./media/storage-explorer/table.png" alt-text="Skapa och ändra en tabell":::

#### <a name="import-and-export-table"></a>Importera och exportera tabell

1. Importera genom att välja knappen **Importera** och välja en befintlig tabell.
2. Om du vill exportera väljer du knappen **Exportera** och väljer ett mål.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Importera eller exportera en tabell":::

#### <a name="delete-entities"></a>Ta bort entiteter

- Välj entiteterna och välj **ta bort**.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Ta bort en tabell":::

#### <a name="query-table"></a>Frågetabell

- Klicka på knappen **fråga** , villkor för inmatad fråga och välj sedan **Kör fråga** . Stäng frågefönstret genom att klicka på knappen **Stäng fråga**.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Fråga efter data från tabellen":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Hantera lagrade procedurer, utlösare och UDF:er

* Om du vill skapa en lagrad procedur högerklickar du på **lagrad procedur**i det vänstra trädet, väljer **skapa lagrad procedur**, anger ett namn till vänster, skriver skripten för lagrade procedurer i det högra fönstret och väljer sedan **skapa**.
* Du kan också redigera befintliga lagrade procedurer genom att dubbelklicka, göra uppdateringen och sedan klicka på **Uppdatera** för att spara eller välja **Ignorera** för att avbryta ändringen.

  :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Skapa och hantera lagrade procedurer":::

* Åtgärderna för **Utlösare** och **UDF** liknar dem för **Lagrade procedurer**.

## <a name="troubleshooting"></a>Felsökning

[Microsoft Azure Cosmos DB i Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) är en fristående app som gör det möjligt att ansluta till Azure Cosmos DB-konton som finns på Azure och i nationella moln från Windows, macOS eller Linux. Det gör det möjligt för dig att hantera Microsoft Azure Cosmos DB-entiteter, manipulera data och uppdatera lagrade procedurer och utlösare, och även andra Azure-entiteter som lagringsblobar och köer.

Det finns lösningar på vanliga problem för Azure Cosmos DB i Storage Explorer.

### <a name="sign-in-issues"></a>Inloggningsproblem

Försök att starta om programmet innan du går vidare och se om problemen kan åtgärdas.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Självsignerat certifikat i certifikatkedjan

Det finns några skäl till att det här felet visas, de två vanligaste är:

+ Du är bakom en *transparent proxy*, vilket innebär att någon (till exempel din IT-avdelning) fångar upp HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.

+ Du kör program vara, t. ex. antivirus program, som matar in ett självsignerat TLS/SSL-certifikat i de HTTPS-meddelanden som du får.

När Storage Explorer påträffar ett sådant ”självsignerade certifikat” kan den inte längre veta om HTTPS-meddelandet den får har manipulerats eller inte. Om du har en kopia av det självsignerade certifikatet kan du berätta för Storage Explorer att lita på det. Om du är osäker på vem som infogat certifikatet kan du försöka att hitta det själv genom att göra följande:

1. Installera OpenSSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (någon av de enklare versionerna är ok)
     - Mac och Linux: ska ingå i ditt operativsystem
2. Kör OpenSSL
    - Windows: Gå till installationskatalogen, sedan **/bin/**, dubbelklicka på **openssl.exe**.
    - Mac och Linux: kör **openssl** från en terminal
3. Kör `s_client -showcerts -connect microsoft.com:443`
4. Leta efter självsignerade certifikat. Om du är osäker på vilka som är självsignerade, leta då överallt efter om ämnet (”s:”) och utfärdaren (”i:”) är samma.
5.  När du har hittat självsignerade certifikat, kopiera och klistra in allt från och med **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** till en ny .cer-fil för varje certifikat.
6.  Öppna Storage Explorer och gå sedan till **Redigera**  >  **SSL-certifikat**  >  **Importera certifikat**. Med filväljaren, hitta, markera och öppna de CER-filerna som du skapade.

Om det inte går att hitta något självsignerat certifikat med hjälp av stegen ovan kan du skicka feedback för mer hjälp.

#### <a name="unable-to-retrieve-subscriptions"></a>Det gick inte att hämta prenumerationer

Om det inte går att hämta dina prenumerationer när du har loggat in:

- Verifiera att ditt konto har åtkomst till prenumerationerna genom att logga in på [Azure Portal](https://portal.azure.com/)
- Kontrollera att du har loggat in med rätt miljö ([Azure](https://portal.azure.com/), [Azure Kina](https://portal.azure.cn/), [Azure Tyskland](https://portal.microsoftazure.de/), [Azure som tillhör amerikanska myndigheter](https://portal.azure.us/), eller Anpassad miljö/Azure Stack)
- Om du är bakom en proxyserver, se till att du har konfigurerat Storage Explorer-proxyservern korrekt
- Försök att ta bort och lägga till kontot igen
- Försök att ta bort följande filer från arbetskatalogen (exempel: C:\Users\ContosoUser), och lägg sedan till kontot igen:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Titta på utvecklingsverktygskonsolen (f12) när du loggar in för eventuella felmeddelanden

:::image type="content" source="./media/storage-explorer/console.png" alt-text="Kontrol lera om det finns några fel i konsolen för utvecklarverktyg":::

#### <a name="unable-to-see-the-authentication-page"></a>Det gick inte att visa autentiseringssidan

Om du inte kan se autentiseringssidan:

- Beroende på anslutningen kan det ta ett tag för inloggningssidan att läsas in, vänta minst en minut innan du stänger dialogrutan för autentisering
- Om du är bakom en proxyserver, se till att du har konfigurerat Storage Explorer-proxyservern korrekt
- Visa utvecklingskonsolen genom att trycka på F12. Titta på svar från utvecklarkonsolen och se om du hittar en ledtråd till varför autentiseringen inte fungerar

#### <a name="cannot-remove-account"></a>Det går inte att ta bort kontot

Om du inte kan ta bort ett konto, eller återautentiseringslänken inte gör något

- Försök att ta bort följande filer från arbetskatalogen och lägg sedan till kontot igen:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Om du vill ta bort SAS-kopplade lagringsresurser, ta bort:
  - %AppData%/StorageExplorer-mapp för Windows
  - /Users/<your_name>/Library/Application SUpport/StorageExplorer för Mac
  - ~/.config/StorageExplorer för Linux
  - **Du måste ange dina autentiseringsuppgifter igen** om du tar bort dessa filer


### <a name="httphttps-proxy-issue"></a>HTTP-/HYYPS-proxyproblem

Du kan inte visa Microsoft Azure Cosmos DB-noder i det vänstra trädet när du konfigurerar HTTP-/HTTPS-proxy i ASE. Du kan använda Microsoft Azure Cosmos DB-datautforskare i Azure Portal som en temporär lösning för tillfället.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problem med ”Utvecklingsnod” under ”Lokala och kopplade” noder

Det finns inget svar när du har valt noden "utveckling" under noden "lokal och kopplad" i det vänstra trädet.  Det här beteendet är förväntat. Microsoft Azure Cosmos DB lokala emulatorn stöds i nästa version.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Utvecklingsnod":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Nodfel vid koppling av Microsoft Azure Cosmos DB-konto i ”Lokala och kopplade”

Om du ser nedanstående fel när du har kopplat Microsoft Azure DB som Cosmos-kontot i ”Lokala och kopplade” noder, kontrollerar du om du använder rätt anslutningssträng.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Fel vid fästning av Microsoft Azure Cosmos DB i Lokala och kopplade":::

### <a name="expand-azure-cosmos-db-node-error"></a>Nodfel vid expandera Microsoft Azure Cosmos DB

Du kan se nedanstående fel vid försök att expandera noderna i vänster träd.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Nodfel vid expandera Microsoft Azure Cosmos DB":::

Prova följande rekommendationer:

- Kontrollera om Microsoft Azure Cosmos DB-kontot håller på att skapas och försök igen när kontot har skapats.
- Om kontot är under noden ”Snabbåtkomst” eller ”Lokala och kopplade” noder måste du kontrollera om kontot har tagits bort. I så fall måste du manuellt ta bort noden.

## <a name="next-steps"></a>Nästa steg

* Titta på följande videoklipp om du vill se hur du använder Azure Cosmos DB i Azure Storage Explorer: [Använda Azure Cosmos-DB i Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Läs mer om Storage Explorer och om att ansluta fler tjänster i [Kom igång med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
