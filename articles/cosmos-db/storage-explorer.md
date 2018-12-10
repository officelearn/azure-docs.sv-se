---
title: Hantera Azure Cosmos DB-resurser med Azure Storage Explorer
description: Lär dig hur du ansluter till Azure Cosmos DB och hantera dess resurser med hjälp av Azure Storage Explorer.
author: Jejiang
tags: Azure Cosmos DB
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jejiang
ms.custom: seodec18
ms.openlocfilehash: 1ce483a88c1f57912dfe30efa98f46335e97c01c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138134"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-storage-explorer"></a>Hantera Azure Cosmos DB-resurser med Azure Storage Explorer

Med hjälp av Azure Cosmos DB i Azure Storage Explorer kan du hantera Azure Cosmos DB-entiteter, manipulera data och uppdatera lagrade procedurer och utlösare, och även andra Azure-entiteter som lagringsblobar och köer. Nu kan du använda samma verktyg för att hantera olika Azure-entiteter på ett och samma ställe. För tillfället stöder Azure Storage Explorer SQL-, MongoDB-, Graph- och Table-konton.


## <a name="prerequisites"></a>Förutsättningar

Ett Azure Cosmos DB-konto för SQL-API:et<!--or MongoDB API-->. Om du inte har ett konto kan du skapa en i Azure-portalen, enligt beskrivningen i [Azure Cosmos DB: Skapa en SQL API-webbapp med .NET och Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Installation

Installera den senaste versionen av Azure Storage Explorer här: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Nu finns det versioner för Windows, Linux och MAC.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. När du har installerat **Azure Storage Explorer**, klickar du på **plugin**-ikonen till vänster som det visas i följande bild:
       
   ![Plugin-ikon](./media/storage-explorer/plug-in-icon.png)
 
2. Välj **Lägg till ett Azure-konto** och klicka sedan på **Logga in**.

   ![Ansluta till Azure-prenumerationen](./media/storage-explorer/connect-to-azure-subscription.png)

2. Välj **Logga in** i dialogrutan **Azure-inloggning** och ange dina autentiseringsuppgifter för Azure.

    ![Logga in](./media/storage-explorer/sign-in.png)

3. Välj din prenumeration i listan och klicka sedan på **Använd**.

    ![Använd](./media/storage-explorer/apply-subscription.png)

    Explorer-fönstret uppdateras och visar konton i den valda prenumerationen.

    ![Kontolista](./media/storage-explorer/account-list.png)

    Du har anslutit ditt **Cosmos DB-konto** till din Azure-prenumeration.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng

Ett alternativt sätt att ansluta till en Azure Cosmos DB är att använda en anslutningssträng. Följ stegen nedan för att ansluta med en anslutningssträng.

1. Hitta **Lokala och anslutna** i det vänstra trädet, högerklicka på **Cosmos DB-konton** och välj **Anslut till Cosmos DB...**

    ![Ansluta till Cosmos DB med hjälp av en anslutningssträng](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Stöder endast SQL- och Table-API för tillfället. Välj API, klistra in **Anslutningssträng**, fyll i **Kontoetikett**, klicka på **Nästa** för att kontrollera sammanfattningen och klicka sedan på **Anslut** för att ansluta Azure Cosmos DB-kontot. Information om hur du hämtar anslutningssträngen finns i [Hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Anslutningssträng](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Anslut till Azure Cosmos DB med en lokal emulator

Använd följande steg för att ansluta till en Azure Cosmos DB med en Emulator, stöder enbart SQL-konto för tillfället.

1. Installera Emulatorn och starta. Information om hur du installerar emulatorn finns i [Cosmos DB-emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Hitta **Lokala och anslutna** i det vänstra trädet, högerklicka på **Cosmos DB-konton** och välj **Anslut till Cosmos DB-emulator...**

    ![Anslut till Cosmos DB med emulator](./media/storage-explorer/emulator-entry.png)

3. Stöder endast SQL-API för tillfället. Klistra in **Anslutningssträng**, fyll i **Kontoetikett**, klicka på **Nästa** för att kontrollera sammanfattningen och klicka sedan på **Anslut** för att ansluta Azure Cosmos DB-kontot. Information om hur du hämtar anslutningssträngen finns i [Hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Dialogrutan Anslut till Cosmos DB med Emulator](./media/storage-explorer/emulator-dialog.png)


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

     ![Öppna i portalen](./media/storage-explorer/open-in-portal.png)

* Du kan också lägga till Azure Cosmos DB-konto, databas och samling i **Snabbåtkomst**.
* **Sök härifrån** aktiverar nyckelordssökning under den valda sökvägen.

    ![sök härifrån](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Databas- och samlingshantering
#### <a name="create-a-database"></a>Skapa en databas 
-   Högerklicka Azure Cosmos DB-kontot, välj **Skapa databas** ange databasnamnet och slutför genom att trycka på **Retur**.
       
    ![Skapa databas](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Ta bort en databas
- Högerklicka på databasen, klicka på **Ta bort databas** och klicka på **Ja** i popup-fönstret. Databasnoden tas bort och Azure Cosmos DB-konto uppdateras automatiskt.

    ![Ta bort databas1](./media/storage-explorer/delete-database1.png)  

    ![Ta bort databas2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Skapa en samling
1. Högerklicka på databasen, välj **Skapa samling** och ange sedan följande information som **Samlings-ID**, **Lagringskapacitet** o.s.v. Klicka på **OK** för att slutföra. 

    ![Skapa samling1](./media/storage-explorer/create-collection.png)

    ![Skapa samling2](./media/storage-explorer/create-collection2.png) 

2. Välj **obegränsad** för att kunna ange partitionsnyckel och klicka sedan på **OK** för att slutföra.

    Om en partitionsnyckel används när du skapar en samling kan partitionsnyckelvärdet inte ändras i samlingen när den väl har skapats.

    ![Partitionsnyckeln](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Ta bort en samling
- Högerklicka på samlingen, klicka på **Ta bort samling** och klicka sedan på **Ja** i popup-fönstret. 

    Samlingsnoden tas bort och databasen uppdateras automatiskt.

    ![Ta bort samling](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Dokumenthantering

#### <a name="create-and-modify-documents"></a>Skapa och ändra dokument
- Du kan skapa ett nytt dokument genom att öppna **Documents** (Dokument) i det vänstra fönstret, klicka på **New Document** (Nytt dokument), redigera innehållet i den högra rutan och sedan klicka på **Save** (Spara). Du kan också uppdatera ett befintligt dokument och sedan klicka på **Save**. Ändringar kan ignoreras genom att klicka på **Discard** (Ignorera).

    ![Dokument](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Ta bort ett dokument
- Klicka på knappen **Delete** (Ta bort) om du vill ta bort det valda dokumentet.

#### <a name="query-for-documents"></a>Fråga för dokument
- Redigera dokumentfilter genom att ange en [SQL-fråga](how-to-sql-query.md) och sedan klicka på **Apply** (Tillämpa).

    ![Dokumentfilter](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Graph-hantering

#### <a name="create-and-modify-vertex"></a>Skapa och ändra brytpunkt
1. Om du vill skapa en ny brytpunkt, öppnar du **Graph** från det vänstra fönstret, klickar på **Ny brytpunkt**, redigerar innehållet och klickar sedan på **OK**.    
2. Om du vill ändra en befintlig brytpunkt, klickar du på pennikonen i den högra rutan.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Ta bort en graf
- Om du vill ta bort en brytpunkt, klickar du på papperskorgsikonen bredvid brytpunktens namn.

#### <a name="filter-for-graph"></a>Filter för graf
- Redigera graffiltret genom att ange en [gremlin-fråga](gremlin-support.md) och sedan klicka på **Tillämpa filter**.

    ![Graffilter](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Tabellhantering

#### <a name="create-and-modify-table"></a>Skapa och ändra tabell
1. Om du vill skapa en ny tabell, öppnar du **entiteter** från det vänstra fönstret, klickar på **Lägg till**, redigera innehållet i dialogrutan **Lägg till entitet**, lägg till egenskapen genom att klicka på knappen **Lägg till egenskap** och klicka sedan på **Infoga**.
2. Om du vill ändra en tabell, klickar du på **Redigera**, ändrar innehållet och klickar sedan på **Uppdatera**.

    ![Tabell](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importera och exportera tabell
1. Om du vill importera, klickar du på **Importera**-knappen och väljer en befintlig tabell.
2. Om du vill exportera, klickar du på **Exportera**-knappen och väljer ett mål.

    ![Import och export av tabell](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Ta bort entiteter
- Välj entiteterna och klicka på knappen **Ta bort**.

    ![Ta bort tabell](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Frågetabell
- Klicka på **Fråga**-knappen, ange frågevillkoren och klicka sedan på knappen **Kör fråga**. Stäng frågefönstret genom att klicka på knappen **Stäng fråga**.

    ![Tabellfråga](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Hantera lagrade procedurer, utlösare och UDF:er
* Skapa en lagrad procedur genom att i vänster träd högerklicka på **Lagrad procedur**, välj **Skapa lagrad procedur**, ange ett namn i vänstra fönstret, ange skripten för den lagrade proceduren i det högra fönstret och klicka sedan på **Skapa**. 
* Du kan också redigera befintliga, lagrade procedurer genom att dubbelklicka, göra uppdateringen och sedan klicka på **Uppdatera** om du vill spara eller på **Ignorera** om du vill avbryta ändringen.

    ![Lagrad procedur](./media/storage-explorer/stored-procedure.png)
* Åtgärderna för **Utlösare** och **UDF** liknar dem för **Lagrade procedurer**.

## <a name="troubleshooting"></a>Felsökning

[Microsoft Azure Cosmos DB i Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) är en fristående app som gör det möjligt att ansluta till Azure Cosmos DB-konton som finns på Azure och i nationella moln från Windows, macOS eller Linux. Det gör det möjligt för dig att hantera Microsoft Azure Cosmos DB-entiteter, manipulera data och uppdatera lagrade procedurer och utlösare, och även andra Azure-entiteter som lagringsblobar och köer.

Det finns lösningar på vanliga problem för Azure Cosmos DB i Storage Explorer.

### <a name="sign-in-issues"></a>Inloggningsproblem

Försök att starta om programmet innan du går vidare och se om problemen kan åtgärdas.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Självsignerat certifikat i certifikatkedjan

Det finns några skäl till att det här felet visas, de två vanligaste är:

+ Du är bakom en *transparent proxy*, vilket innebär att någon (till exempel din IT-avdelning) avlyssnar HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.

+ Du kör programvara, till exempel antivirusprogram, som infogar ett självsignerat SSL-certifikat till de HTTPS-meddelanden du får.

När Storage Explorer påträffar ett sådant ”självsignerade certifikat” kan den inte längre veta om HTTPS-meddelandet den får har manipulerats eller inte. Om du har en kopia av det självsignerade certifikatet kan du berätta för Storage Explorer att lita på det. Om du är osäker på vem som infogat certifikatet kan du försöka att hitta det själv genom att göra följande:

1. Installera öppen SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (någon av de enklare versionerna är ok)
     - Mac och Linux: ska ingå i ditt operativsystem
2. Kör öppen SSL
    - Windows: Gå till installationskatalogen, sedan **/bin/**, dubbelklicka på **openssl.exe**.
    - Mac och Linux: kör **openssl** från en terminal
3. Kör `s_client -showcerts -connect microsoft.com:443`
4. Leta efter självsignerade certifikat. Om du är osäker på vilka som är självsignerade, leta då överallt efter om ämnet (”s:”) och utfärdaren (”i:”) är samma.
5.  När du har hittat självsignerade certifikat, kopiera och klistra in allt från och med **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** till en ny .cer-fil för varje certifikat.
6.  Öppna Storage Explorer och gå sedan till **Redigera** > **SSL-certifikat** > **Importera certifikat**. Med filväljaren, hitta, markera och öppna de CER-filerna som du skapade.

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

![konsol](./media/storage-explorer/console.png)

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
  - /Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer för Mac
  - ~/.config/StorageExplorer för Linux
  - **Du måste ange dina autentiseringsuppgifter igen** om du tar bort dessa filer


### <a name="httphttps-proxy-issue"></a>HTTP-/HYYPS-proxyproblem

Du kan inte visa Microsoft Azure Cosmos DB-noder i det vänstra trädet när du konfigurerar HTTP-/HTTPS-proxy i ASE. Det är ett känt problem och korrigeras i nästa version. Du kan använda Microsoft Azure Cosmos DB-datautforskare i Azure Portal som en temporär lösning för tillfället. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Problem med ”Utvecklingsnod” under ”Lokala och kopplade” noder

Du får inget svar när du klickar på noden ”utveckling” under ”lokala och kopplade” noder i vänster träd.  Det här beteendet är förväntat. Microsoft Azure Cosmos DB lokala emulatorn stöds i nästa version.

![Utvecklingsnod](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Nodfel vid koppling av Microsoft Azure Cosmos DB-konto i ”Lokala och kopplade”

Om du ser nedanstående fel när du har kopplat Microsoft Azure DB som Cosmos-kontot i ”Lokala och kopplade” noder, kontrollerar du om du använder rätt anslutningssträng.

![Fel vid fästning av Microsoft Azure Cosmos DB i Lokala och kopplade](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Nodfel vid expandera Microsoft Azure Cosmos DB

Du kan se nedanstående fel vid försök att expandera noderna i vänster träd. 

![Expanderingsfel](./media/storage-explorer/expand-error.png)

Prova följande rekommendationer:

- Kontrollera om Microsoft Azure Cosmos DB-kontot håller på att skapas och försök igen när kontot har skapats.
- Om kontot är under noden ”Snabbåtkomst” eller ”Lokala och kopplade” noder måste du kontrollera om kontot har tagits bort. I så fall måste du manuellt ta bort noden.

## <a name="contact-us"></a>Kontakta oss

Om ingen av lösningarna fungerar, skicka ett e-postmeddelande till Microsoft Azure Cosmos DB Dev Tooling Team ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) med information om felet för att åtgärda problemen.

## <a name="next-steps"></a>Nästa steg

* Titta på följande videoklipp om du vill se hur du använder Azure Cosmos DB i Azure Storage Explorer: [Använda Azure Cosmos-DB i Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Läs mer om Storage Explorer och om att ansluta fler tjänster i [Kom igång med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

