---
title: Söka efter och hantera lagringsresurser genom att använda Server Explorer | Microsoft Docs
description: Hur du hittar och använder Server Explorer för att hantera lagringsresurser
services: visual-studio-online
author: ghogen
manager: douge
assetId: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/24/2017
ms.author: ghogen
ms.openlocfilehash: ece9865ad442edc27fd0f495940fd0945d0529ab
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969267"
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Söka efter och hantera lagringsresurser genom att använda Server Explorer

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Översikt

Om du har installerat Azure Tools för Microsoft Visual Studio, kan du visa blob, queue och tabelldata från lagringskonton för Azure. Azure **Storage** noden i Server Explorer visar data i ditt lokala emulatorn-lagringskonto och dina andra Azure storage-konton.

Om du vill visa Server Explorer i Visual Studio på menyraden, Välj **visa** > **Server Explorer**. Den **Storage** noden visar alla lagringskonton som finns under varje Azure-prenumeration eller certifikat som du är ansluten till. Om ditt lagringskonto inte visas, kan du lägga till den genom att följa anvisningarna [senare i den här artikeln](#add-storage-accounts-by-using-server-explorer).

Från och med Azure SDK 2.7 kan använda du också Cloud Explorer att visa och hantera dina Azure-resurser. Mer information finns i [hantera Azure-resurser med Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Visa och hantera lagringsresurser i Visual Studio

Server Explorer visar en lista över blobbar, köer och tabeller automatiskt i ditt storage-emulatorn-konto. Storage-emulatorn kontot finns i Server Explorer under den **Storage** noden som den **utveckling** noden.

Om du vill se emulatorn lagringskontots resurser, expandera den **utveckling** noden. Om storage-emulatorn inte har startats när du har expanderat den **utveckling** nod, startas automatiskt. Den här processen kan ta flera sekunder. Du kan fortsätta att arbeta i andra delar av Visual Studio medan storage-emulatorn startar.

Om du vill visa resurser i ett lagringskonto, expandera lagringskontots nod i Server Explorer där du kan se **Blobar**, **köer**, och **tabeller** noder.

## <a name="work-with-blob-resources"></a>Arbeta med blob-resurser

Den **Blobar** nod visar en lista över behållare för det valda lagringskontot. BLOB-behållare som innehåller blobfiler och du kan ordna dessa blobar i mappar och undermappar. Mer information finns i [använda Blob storage från .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>Att skapa en blobbehållare

1. Öppna snabbmenyn för den **Blobar** noden och välj sedan **skapa Blobbehållare**.
1. I den **skapa Blobbehållare** dialogrutan anger du namnet på den nya behållaren.  
1. Trycka på RETUR på tangentbordet eller du kan klicka eller tryck utanför namnfältet att spara blob-behållaren.

   > [!NOTE]
   > Blob-behållarnamn måste börja med en siffra (0-9) eller en gemen bokstav (a-z).

### <a name="to-delete-a-blob-container"></a>Att ta bort en blob-behållare

Öppna snabbmenyn för blob-behållaren som du vill ta bort och välj sedan **ta bort**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Att visa en lista över hur objekten i en blobbehållare

Öppna snabbmenyn för ett blob-behållarnamn i listan och välj sedan **öppna**.

När du visar innehållet i en blobbehållare, visas det på en flik kallas vyn blob-behållare.

![Visa för BLOB-behållare](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

Du kan utföra följande åtgärder för BLOB-objekt med hjälp av knapparna i det övre högra hörnet av vyn blob-behållare:

* Ange ett filtervärde och tillämpar den.
* Uppdatera listan över blobar i behållaren.
* Överför en fil.
* Ta bort en blob. (Tar bort en fil från en blobbehållare bort inte den underliggande filen. Det bara tar bort den från blob-behållaren.)
* Öppna en blob.
* Spara en blob till den lokala datorn.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Skapa en mapp eller undermapp i en blob-behållare

1. Välj blob-behållaren i Cloud Explorer. I behållarfönstret väljer du den **ladda upp Blob** knappen.

1. I den **ladda upp en ny fil** dialogrutan den **Bläddra** för att ange den fil som du vill ladda upp och ange sedan ett mappnamn i den **mapp (valfritt)** box.

   ![Ladda upp en fil till en blobbmapp](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Du kan lägga till undermappar i behållaren mappar genom att följa samma steg. Om du inte anger ett mappnamn, överföra filen till den översta nivån i blob-behållaren. Filen visas i den angivna mappen i behållaren.

   ![Mappen som lagts till i en blob-behållare](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Dubbelklicka på mappen eller tryck på RETUR för att se innehållet i mappen. När du är i behållarens mapp går du tillbaka till roten i behållaren genom att välja den **öppna överordnade katalogen** (pilen)-knappen.

### <a name="to-delete-a-container-folder"></a>Ta bort en mapp för behållare

Ta bort alla filer i mappen.

Eftersom mappar i blob-behållare är virtuella mappar, kan du inte skapa en tom mapp. Du också kan inte ta bort en mapp för att ta bort innehållet i filen, men måste i stället att ta bort hela innehållet i en mapp att ta bort mappen.

### <a name="to-filter-blobs-in-a-container"></a>Filtrera blobarna i en behållare

Du kan filtrera de blobbar som visas genom att ange ett Allmänt prefix.

Exempel: Om du anger prefixet **hello** i textrutan filter och välj sedan den **kör** (**!**) knapp, endast de blobbar som börjar med ”hello” visas.

![Filtertextruta](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

Textrutan filter är skiftlägeskänslig och stöder inte filtrering med jokertecken. Blobar kan filtreras endast efter prefix. Prefixet kan innehålla en avgränsare om du använder en avgränsare för att organisera blobarna i en virtuell hierarki. Till exempel filtrera på prefixet ”HelloFabric /” returnerar alla blobar som börjar med strängen.

### <a name="to-download-blob-data"></a>Ladda ned blob-data

I Cloud Explorer använder du någon av följande metoder:

* Öppna snabbmenyn för en eller flera blobbar och välj sedan **öppna**.
* Välj blobnamn och välj sedan den **öppna** knappen.
* Dubbelklicka på blobnamnet.

Förloppet för en blob download som visas i den **Azure-aktivitetsloggen** fönster.

Blobben öppnas i standard redigeringsprogram för filtypen. Om operativsystemet kan identifiera filtypen, öppnas filen i ett lokalt installerat program. I annat fall uppmanas du att välja ett program som är lämplig för filtypen för blobben. Den lokala filen som skapas när du laddar ned en blob har markerats som skrivskyddade.

BLOB-data cachelagras lokalt och kontrolleras mot blobens tid för senaste ändring i Azure Blob storage. Om bloben har uppdaterats sedan den senast hämtades, hämtas den igen. I annat fall bloben har lästs in från den lokala disken.

Som standard hämtas en blob till en tillfällig katalog. Om du vill ladda ned blobar till en viss katalog, öppna snabbmenyn för valda blobnamn och välj **Spara som**. När du sparar en blob i det här sättet, blobbfilen öppnas inte och den lokala filen skapas med Läs/Skriv-attribut.

### <a name="to-upload-blobs"></a>Du överför blobbar

Om du vill ladda upp blobar, Välj den **ladda upp Blob** knappen när behållaren har öppnats för visning i vyn för blob-behållare.

Du kan välja en eller flera filer att ladda upp och du kan ladda upp filer av valfri typ. Den **Azure-aktivitetsloggen** visar förloppet för överföringen. Mer information om hur du arbetar med blob-data finns i [använda Azure Blob storage i .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Att visa loggar överförs till blobar

Om du använder Azure-diagnostik för att logga data från dina Azure-program och du har överfört loggar till ditt lagringskonto, visas behållare som Azure skapade för de här loggarna. Visa dessa loggar i Server Explorer är ett enkelt sätt att identifiera problem med ditt program, särskilt om det har distribuerats till Azure.

Läs mer om Azure Diagnostics [samla in Data för loggning av med hjälp av Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Att hämta URL: en för en blob

Öppna blobens snabbmenyn och välj sedan **Kopiera URL: en**.

### <a name="to-edit-a-blob"></a>Så här redigerar du en blob

Välj blob och välj sedan den **öppna Blob** knappen.

Filen laddas ned till en tillfällig plats och öppnas på den lokala datorn. Ladda upp blob igen när du har gjort ändringar.

## <a name="work-with-queue-resources"></a>Arbeta med resurser för kö

Storage services-köer finns i ett Azure storage-konto. Du kan använda dem för att tillåta din cloud service-roller att kommunicera med varandra och med andra tjänster med en mekanism för överföring av meddelanden. Du kan komma åt kön programmässigt via en molnbaserad tjänst och en webbtjänst för externa klienter. Du kan också komma åt kön direkt från Server Explorer i Visual Studio.

När du utvecklar en molnbaserad tjänst som använder köer kan du använda Visual Studio för att skapa köer och arbeta med dem interaktivt när du utvecklar och testa din kod.

I Server Explorer kan du visa köer i ett lagringskonto, skapa och ta bort köer, öppna en kö om du vill visa meddelanden och lägga till meddelanden i en kö. När du öppnar en kö för visning, du kan visa enskilda meddelanden och du kan utföra följande åtgärder för kön med hjälp av knapparna i det övre vänstra hörnet:

* Uppdatera vyn för kön.
* Lägg till ett meddelande i kön.
* Ta bort från kön översta meddelandet.
* Rensa hela kön.

Följande bild visar en kö som innehåller två meddelanden:

![Visa en kö](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Mer information om storage services köer finns i [Kom igång med Azure Queue storage med hjälp av .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Information om webbtjänsten storage services köer finns i [kötjänst-koncept](http://go.microsoft.com/fwlink/?LinkId=264788). Information om hur du skickar meddelanden till en lagringskö för tjänster med hjälp av Visual Studio finns i [skicka meddelanden till en Lagringskö tjänster](https://docs.microsoft.com/azure/visual-studio/vs-storage-cloud-services-getting-started-queues).

> [!NOTE]
> Lagringsköer tjänster skiljer sig från Azure Service Bus-köer. Läs mer om Service Bus-köer, [Service Bus-köer, ämnen och prenumerationer](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>Arbeta med tabell-resurser

Azure Table Storage lagrar stora mängder strukturerade data. Tjänsten är en NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

### <a name="to-create-a-table"></a>Att skapa en tabell

1. I Cloud Explorer väljer du den **tabeller** nod i storage-konto och välj sedan **Create Table**.
1. I den **Create Table** dialogrutan, ange ett namn för tabellen.

### <a name="to-view-table-data"></a>Visa tabelldata

1. I Cloud Explorer öppnar du den **Azure** noden och öppna den **Storage** noden.
1. Öppna noden storage-konto som du är intresserad av och öppna sedan den **tabeller** noden för att se en lista över tabeller för lagringskontot.
1. Öppna snabbmenyn för en tabell och välj sedan **visa tabellen**.

    ![En Azure-tabell i Solution Explorer](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

Tabellen är ordnad efter entiteter (visas i rader) och egenskaper (visas i kolumner). I nästa bild visas till exempel entiteter som anges i tabelldesignern.

### <a name="to-edit-table-data"></a>Så här redigerar du tabelldata

Öppna snabbmenyn för en entitet (en enskild rad) eller en egenskap (en cell) i tabelldesignern, och välj sedan **redigera**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

Entiteter i en enda tabell är inte måste ha samma uppsättning egenskaper (kolumner). Tänk på följande begränsningar för att visa och redigera tabelldata:

* Du kan inte visa eller redigera binära data (`type byte[]`), men du kan lagra det i en tabell.
* Du kan inte redigera den **PartitionKey** eller **RowKey** värden, eftersom tabellagring i Azure inte stöder åtgärden.
* Du kan inte skapa en egenskap som kallas **tidsstämpel**. Azure storage-tjänster kan du använda en egenskap med det namnet.
* Om du anger en **DateTime** värde, måste du följa ett format som är lämpligt att inställningar och språkinställningar i datorn (till exempel ÅÅÅÅ-MM-DD: mm: ss [AM | PM] för amerikansk engelska).

### <a name="to-add-entities"></a>Att lägga till entiteter

1. I tabelldesignern, Välj den **Lägg till entitet** knappen.

    ![Lägg till entitet knapp](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. I den **Lägg till entitet** dialogrutan anger du värdena för den **PartitionKey** och **RowKey** egenskaper.

    ![Entiteten dialogrutan Lägg till](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Ange värden noggrant. Du kan inte ändra dem när du har stängt dialogrutan om du inte ta bort entiteten och lägga till den igen.

### <a name="to-filter-entities"></a>Filtrera enheter

Du kan anpassa uppsättningen med entiteter som visas i en tabell om du använder Frågeverktyget.

1. Öppna Frågeverktyget genom att öppna en tabell för visning.
1. Välj den **frågebyggaren** tabellvyn i verktygsfältet.

    Den **frågebyggaren** dialogrutan visas. Följande bild visar en fråga som håller på att skapas i Frågeverktyget.

    ![Frågebyggaren](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. När du är klar att skapa frågan, Stäng dialogrutan. Formuläret text av frågan visas som ett filter för WCF-datatjänster i en textruta.
1. Välj grön triangel om du vill köra frågan.

Du kan också filtrera entitetsdata som visas i tabelldesignern om du anger en WCF-datatjänster Filtersträngen direkt i textrutan filter. Den här typen av strängen liknar en SQL WHERE-sats, men skickas till servern som en HTTP-begäran. Information om hur du skapar filtersträngar finns i [Constructing filtersträngar för tabelldesign](https://docs.microsoft.com/azure/vs-azure-tools-table-designer-construct-filter-strings).

Följande bild visar ett exempel på en giltig Filtersträngen:

![Filtersträngen](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Uppdatera lagringsdata

När Server Explorer ansluter till eller hämtar data från ett lagringskonto, kan åtgärden ta upp till en minut att slutföra. Om Server Explorer inte kan ansluta, kan åtgärden timeout. Medan data hämtas, kan du fortsätta att arbeta i andra delar av Visual Studio. Om du vill avbryta åtgärden om det tar för lång, Välj den **Avbryt uppdatering** i Server Explorer-verktygsfältet.

### <a name="to-refresh-blob-container-data"></a>Uppdatera data i blob-behållare

* Välj den **Blobar** nod under **Storage**, och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.
* Om du vill uppdatera listan över blobar som visas, Välj den **kör** knappen.

### <a name="to-refresh-table-data"></a>Uppdatera tabelldata

* Välj den **tabeller** nod under **Storage**, och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.
* Om du vill uppdatera listan över entiteter som visas i tabelldesignern, Välj den **kör** knappen i tabelldesignern.

### <a name="to-refresh-queue-data"></a>Uppdatera data i kö

Välj den **köer** nod under **Storage**, och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Uppdatera alla objekt i ett lagringskonto

Välj namnet på kontot och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Lägga till lagringskonton med hjälp av Server Explorer

Det finns två sätt att lägga till lagringskonton med hjälp av Server Explorer. Du kan skapa ett lagringskonto i din Azure-prenumeration eller du kan koppla ett befintligt lagringskonto.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Skapa ett lagringskonto med hjälp av Server Explorer

1. I Server Explorer, öppna snabbmenyn för den **Storage** noden och välj sedan **skapa Lagringskonto**.

1. I den **skapa Lagringskonto** dialogrutan Välj eller ange följande information:

   * Den Azure-prenumeration som du vill lägga till lagringskontot.
   * Namnet som du vill använda för det nya lagringskontot.
   * Den region eller tillhörighetsgrupp (till exempel USA, västra eller Östasien).
   * Vilken typ av replikering som du vill använda för storage-konto som lokalt redundant.

   ![Skapa ett Azure-lagringskonto](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Välj **Skapa**.

Det nya lagringskontot visas i den **Storage** lista i Solution Explorer.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Att koppla ett befintligt lagringskonto genom att använda Server Explorer

1. Öppna snabbmenyn i Server Explorer för Azure **Storage** noden och välj sedan **Anslut extern lagring**.

    ![Att lägga till ett befintligt lagringskonto](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. I den **skapa Lagringskonto** dialogrutan Välj eller ange följande information:

   * Namnet på det befintliga lagringskontot som du vill bifoga.
   * Nyckeln för det valda lagringskontot. Det här värdet anges vanligtvis åt dig när du väljer ett lagringskonto. Om du vill att Visual Studio för att komma ihåg lagringskontonyckeln väljer den **Kom ihåg kontonyckel** markerar du kryssrutan.
   * Protokollet som du använder för att ansluta till lagringskontot, till exempel HTTP, HTTPS eller en anpassad slutpunkt. Läs mer om anpassade slutpunkter [hur du konfigurerar anslutningssträngar](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>Att visa de sekundära slutpunkterna

Om du har skapat ett lagringskonto med hjälp av den **Read-Access Geo Redundant** replikeringsalternativet, du kan visa dess sekundära slutpunkter genom att öppna snabbmenyn för kontonamnet och välj sedan **egenskaper**.

![Storage sekundära slutpunkter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Ta bort ett lagringskonto från Server Explorer

Öppna snabbmenyn för kontonamnet i Server Explorer och välj sedan **ta bort**. 

Om du tar bort ett lagringskonto tas även alla sparade viktig information för det kontot.

Om du tar bort ett lagringskonto från Server Explorer påverkar det inte ditt storage-konto eller data som den innehåller. Det tar bara bort referensen från Server Explorer. Permanent ta bort ett lagringskonto genom att använda den [Azure-portalen](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure-lagringstjänster finns [åtkomst till Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/ee405490.aspx).
