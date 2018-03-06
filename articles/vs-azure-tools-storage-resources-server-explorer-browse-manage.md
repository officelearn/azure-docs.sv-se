---
title: "Bläddra bland och hantera lagringsresurser med hjälp av Server Explorer | Microsoft Docs"
description: "Bläddra och använder Server Explorer för att hantera lagringsresurser"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/24/2017
ms.author: kraigb
ms.openlocfilehash: ee91ca168acf2fa0d248e18cce64ac546740a2bd
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>Bläddra bland och hantera lagringsresurser med hjälp av Server Explorer

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Översikt

Om du har installerat Azure Tools för Microsoft Visual Studio, kan du visa blob, köer och tabelldata från dina lagringskonton för Azure. Azure **lagring** noden i Server Explorer visar data i ditt konto för lokal lagring emulatorn och andra Azure storage-konton.

Om du vill visa Server Explorer i Visual Studio på menyraden, Välj **visa** > **Server Explorer**. Den **lagring** noden visar alla lagringskonton som finns under varje Azure-prenumeration eller certifikat som du är ansluten till. Om ditt lagringskonto inte visas kan du lägga till den genom att följa anvisningarna [senare i den här artikeln](#add-storage-accounts-by-using-server-explorer).

Med början i Azure SDK 2.7 kan använda du också Cloud Explorer att visa och hantera Azure-resurser. Mer information finns i [hantera Azure-resurser med Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md).

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Visa och hantera lagringsresurser i Visual Studio

Server Explorer visar en lista över blobbar, köer och tabeller automatiskt i ditt lagringskonto i emulatorn. Storage-emulatorn kontot listas i Server Explorer under den **lagring** noden som den **Development** nod.

Om du vill se storage-emulatorn kontots resurser, expandera den **Development** nod. Om storage-emulatorn inte har startats när du expanderar den **Development** nod, startas automatiskt. Den här processen kan ta flera sekunder. Du kan fortsätta att arbeta med andra delar av Visual Studio medan storage-emulatorn startar.

Om du vill visa resurser i ett lagringskonto, expanderar du noden storage-konto i Server Explorer där du ser **Blobbar**, **köer**, och **tabeller** noder.

## <a name="work-with-blob-resources"></a>Arbeta med blob-resurser

Den **Blobbar** visar en lista över behållare för det valda lagringskontot. BLOB-behållare innehåller blob-filer och du kan ordna dessa blobbar i mappar och undermappar. Mer information finns i [använda Blob storage från .NET](storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="to-create-a-blob-container"></a>Så här skapar du en blob-behållare

1. Öppna snabbmenyn för den **Blobbar** och sedan väljer **skapa Blob-behållaren**.
1. I den **skapa Blob-behållaren** dialogrutan Ange namnet på den nya behållaren.  
1. Välj RETUR på tangentbordet eller du kan klicka eller tryck på utanför namnfältet spara blob-behållaren.

   > [!NOTE]
   > Blob behållarens namn måste börja med en siffra (0-9) eller en gemen bokstav (a-ö).

### <a name="to-delete-a-blob-container"></a>Ta bort en blobbehållaren

Öppna snabbmenyn för blob-behållaren som du vill ta bort och välj sedan **ta bort**.

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>Att visa en lista över objekt i en blob-behållare

Öppna snabbmenyn för namn på en blob-behållare i listan och välj sedan **öppna**.

När du visar innehållet i en blob-behållare, visas den på en flik kallas vyn blob-behållaren.

![BLOB-behållaren vy](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

Du kan utföra följande åtgärder på blobbar med hjälp av knapparna i övre högra hörnet av vyn blob-behållare:

* Ange ett filtervärde och använda den.
* Uppdatera listan över blobbar i behållaren.
* Överför en fil.
* Ta bort en blob. (Om du tar bort en fil från en blobbbehållare inte ta bort den underliggande filen. Det endast tar bort den från blob-behållaren.)
* Öppna en blob.
* Spara en blobb till den lokala datorn.

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Skapa en mapp eller undermapp i en blob-behållare

1. Välj blob-behållaren i Cloud Explorer. I fönstret behållare, Välj den **överför Blob** knappen.

1. I den **överför nya fil** väljer den **Bläddra** för att ange den fil som du vill ladda upp och ange sedan ett namn för mappen i den **mapp (valfritt)** rutan.

   ![Överför en fil till en blob-mapp](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

   Du kan lägga till undermappar i behållaren mappar genom att följa samma steg. Om du inte anger ett mappnamn, överföra filen till den översta nivån i blob-behållaren. Filen visas i den angivna mappen i behållaren.

   ![Mappen som lagts till i en blob-behållare](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Dubbelklicka på mappen eller välj RETUR för att visa innehållet i mappen. När du är i behållarens mapp går du tillbaka till roten i behållaren genom att välja den **öppna överordnade katalogen** (pilen).

### <a name="to-delete-a-container-folder"></a>Ta bort en mapp i behållaren

Ta bort alla filer i mappen.

Eftersom mappar i blob-behållare är virtuella mappar, kan du skapa en tom mapp. Du också kan inte ta bort en mapp för att ta bort innehållet i filen, men måste i stället att ta bort hela innehållet i en mapp för att ta bort mappen.

### <a name="to-filter-blobs-in-a-container"></a>Filtrera blobbar i en behållare

Du kan filtrera blobbar som visas genom att ange ett gemensamt prefix.

Om du anger prefixet som till exempel **hello** i filterrutan och välj sedan den **kör** (**!**) visas för knappen blobbar som börjar med ”hello”.

![Filter-textrutan](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

Filter-textrutan är skiftlägeskänsligt och stöder inte filtrering med jokertecken. Blobbar kan filtreras endast efter prefix. Prefixet kan innehålla avgränsare för en om du använder en avgränsare för att organisera blobbar i en virtuell hierarki. Till exempel filtrering på prefixet ”HelloFabric /” returnerar alla BLOB som börjar med strängen.

### <a name="to-download-blob-data"></a>Hämta blob-data

I Cloud Explorer använder du någon av följande metoder:

* Öppna snabbmenyn för en eller flera blobbar och välj sedan **öppna**.
* Välj blob-namn och välj sedan den **öppna** knappen.
* Dubbelklicka på blobbnamnet på.

Förlopp för en blob-hämtning visas i den **Azure-aktivitetsloggen** fönster.

Blob öppnas i standard-Redigeraren för filtypen. Om operativsystemet kan identifiera filtypen, öppnas filen i ett lokalt installerade program. I annat fall uppmanas du att välja ett program som är lämplig för filtypen för blob. Den lokala filen som skapas när du hämtar en blob har markerats som skrivskyddad.

BLOB-data cachelagras lokalt och kontrolleras mot den blob modifierades senast i Azure Blob storage. Om blob har uppdaterats sedan den senast hämtades, ned den igen. Annars blob har lästs in från den lokala disken.

Som standard hämtas en blobb till en tillfällig katalog. Om du vill ladda ned blobbar till en särskild katalog, öppna snabbmenyn för de valda blob-namn och välj **Spara som**. När du sparar en blobb i det här sättet blob-filen har inte öppnats och den lokala filen har skapats med attribut för läsning och skrivning.

### <a name="to-upload-blobs"></a>Att överföra blobbar

Om du vill överföra blobbar, Välj den **överför Blob** knappen när behållaren är öppen för visning i vyn blob-behållaren.

Du kan välja en eller flera filer som ska överföras och du kan ladda upp filer av valfri typ. Den **Azure-aktivitetsloggen** visar förloppet för överföringen. Mer information om hur du arbetar med blob-data finns [använda Azure Blob storage i .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Visa loggar som överförs till blobbar

Om du använder Azure-diagnostik för att logga data från ditt Azure-program och du har överfört loggar till ditt lagringskonto, visas behållare som Azure har skapat för de här loggarna. Visa dessa loggar i Server Explorer är ett enkelt sätt att identifiera problem med ditt program, särskilt om de har distribuerats till Azure.

Läs mer om Azure-diagnostik [samla in Data för loggning med hjälp av Azure-diagnostik](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Att hämta Webbadress för en blob

Öppna den blob snabbmenyn och välj sedan **Kopiera URL: en**.

### <a name="to-edit-a-blob"></a>Så här redigerar du en blob

Välj blob och välj sedan den **öppna Blob** knappen.

Filen laddas ned till en tillfällig plats och öppnas på den lokala datorn. Överför blob igen när du har gjort ändringar.

## <a name="work-with-queue-resources"></a>Arbeta med resurser för kön

Storage services köer finns i ett Azure storage-konto. Du kan använda dem för att tillåta ditt moln service roller att kommunicera med varandra och med andra tjänster med en mekanism för överföring av meddelanden. Du kan komma åt kön programmässigt via en molnbaserad tjänst och en webbtjänst för externa klienter. Du kan också komma åt kön direkt med hjälp av Server Explorer i Visual Studio.

När du utvecklar en molnbaserad tjänst som använder köer, kanske du vill använda Visual Studio för att skapa köer och arbeta med dem. interaktivt medan du utveckla och testa din kod.

I Server Explorer kan du visa köerna i ett lagringskonto, skapa och ta bort köer, öppna en kö om du vill visa meddelanden och lägga till meddelanden till en kö. Du kan visa enskilda meddelanden när du öppnar en kö för visning och du kan utföra följande åtgärder på kön med hjälp av knapparna i det övre vänstra hörnet:

* Uppdatera vyn i kön.
* Lägg till ett meddelande i kön.
* Översta meddelandet har status Created.
* Rensa hela kön.

Följande bild visar en kö som innehåller två meddelanden:

![Visa en kö](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

För mer information om storage services köer, se [komma igång med Azure Queue storage med hjälp av .NET](http://go.microsoft.com/fwlink/?LinkID=264702). Information om webbtjänsten för lagringstjänster köer, finns [kötjänst-koncept](http://go.microsoft.com/fwlink/?LinkId=264788). Information om hur du skickar meddelanden till en kö för storage-tjänster med hjälp av Visual Studio finns [skicka meddelanden till en kö för Storage Services](https://msdn.microsoft.com/library/azure/jj649344.aspx).

> [!NOTE]
> Tjänster lagringsköer skiljer sig från Azure Service Bus-köer. Läs mer om Service Bus-köer, [Service Bus-köer, ämnen och prenumerationer](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-queues-topics-subscriptions).

## <a name="work-with-table-resources"></a>Arbeta med resurser för tabellen

Azure Table Storage lagrar stora mängder strukturerade data. Tjänsten är en NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

### <a name="to-create-a-table"></a>Att skapa en tabell

1. I Cloud Explorer väljer du den **tabeller** nod i lagringskontot och välj sedan **Create Table**.
1. I den **Create Table** dialogrutan Ange ett namn för tabellen.

### <a name="to-view-table-data"></a>Visa tabelldata

1. I Cloud Explorer öppnar den **Azure** noden och sedan öppna den **lagring** nod.
1. Öppna noden storage-konto som du är intresserad av och sedan öppna den **tabeller** noden för att se en lista över tabeller för lagringskontot.
1. Öppna snabbmenyn för en tabell och välj sedan **visa tabellen**.

    ![En Azure-tabellen i Solution Explorer](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

Tabellen är ordnad efter enheter (visas i rader) och egenskaper (visas i kolumner). Bilden nedan visar exempelvis enheter som anges i tabelldesignern.

### <a name="to-edit-table-data"></a>Så här redigerar du tabelldata

Öppna snabbmenyn för en enhet (en enda rad) eller en egenskap (en cell) i tabelldesignern, och välj sedan **redigera**.

    ![Add or edit a table entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

Entiteter i en enskild tabell inte ha samma uppsättning egenskaper (kolumner). Tänk på följande begränsningar för visning och redigering tabelldata:

* Du kan inte visa eller Redigera binärdata (`type byte[]`), men du kan lagra i en tabell.
* Du kan inte redigera den **PartitionKey** eller **RowKey** värden, eftersom Table storage i Azure inte stöder åtgärden.
* Du kan inte skapa en egenskap som kallas **tidsstämpel**. Azure storage-tjänster kan du använda en egenskap med det namnet.
* Om du anger en **DateTime** värde, måste du följa ett format som är lämpligt för inställningar och språkinställningar i datorn (t.ex, åååå-MM-DD: mm: ss [AM | PM] för amerikansk engelska).

### <a name="to-add-entities"></a>Att lägga till enheter

1. I tabelldesignern, Välj den **lägga till entiteten** knappen.

    ![Entiteten knappen Lägg till](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. I den **lägga till entiteten** dialogrutan Ange värdena för den **PartitionKey** och **RowKey** egenskaper.

    ![Entiteten dialogrutan Lägg till](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Ange värdena noggrant. Du kan inte ändra dem när du har stängt dialogrutan om du inte ta bort entiteten och lägga till den igen.

### <a name="to-filter-entities"></a>Filtrera entiteter

Du kan anpassa de entiteter som visas i en tabell om du använder Frågeverktyget.

1. Öppna Frågeverktyget genom att öppna en tabell för visning.
1. Välj den **frågebyggaren** tabellvyn i verktygsfältet.

    Den **frågebyggaren** dialogrutan visas. Följande bild visar en fråga som skapas i Frågeverktyget.

    ![Frågebyggaren](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
1. När du är klar skapar frågan, Stäng dialogrutan. Formuläret text för frågan visas som ett filter för WCF Data Services i en textruta.
1. Välj ikonen grön triangel om du vill köra frågan.

Du kan också filtrera entitetsdata som visas i tabelldesignern om du anger en WCF-datatjänster Filtersträngen direkt i filter-textrutan. Den här typen av strängen liknar en SQL WHERE-sats men skickas till servern som en HTTP-begäran. Information om hur du skapar filtersträngar finns [filtret konstruerar strängar för tabelldesign](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Följande bild visar ett exempel på ett giltigt filter-sträng:

![Filtersträngen](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>Uppdatera storage-data

Om Server Explorer ansluter till eller hämtar data från ett lagringskonto, kan åtgärden ta upp till en minut att slutföra. Om Server Explorer inte kan ansluta kan Tidsgränsen nåddes för åtgärden. När data hämtas, kan du fortsätta att arbeta i andra delar av Visual Studio. Om du vill avbryta åtgärden om det tar för lång, Välj den **Stoppa uppdatering** i Server Explorer-verktygsfältet.

### <a name="to-refresh-blob-container-data"></a>Uppdatera data i blob-behållare

* Välj den **Blobbar** nod under **lagring**, och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.
* Om du vill uppdatera listan över blobbar som visas markerar du den **Execute** knappen.

### <a name="to-refresh-table-data"></a>Uppdatera tabelldata

* Välj den **tabeller** nod under **lagring**, och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.
* Om du vill uppdatera listan över enheter som visas i tabelldesignern, Välj den **Execute** knappen i tabelldesignern.

### <a name="to-refresh-queue-data"></a>Uppdatera data i kön

Välj den **köer** nod under **lagring**, och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.

### <a name="to-refresh-all-items-in-a-storage-account"></a>Uppdatera alla objekt i ett lagringskonto

Välj namnet på kontot och välj sedan den **uppdatera** i Server Explorer-verktygsfältet.

## <a name="add-storage-accounts-by-using-server-explorer"></a>Lägg till storage-konton med hjälp av Server Explorer

Det finns två sätt att lägga till storage-konton med hjälp av Server Explorer. Du kan skapa ett lagringskonto i din Azure-prenumeration eller du kan koppla ett befintligt lagringskonto.

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>Skapa ett lagringskonto med hjälp av Server Explorer

1. I Server Explorer, öppna snabbmenyn för den **lagring** och sedan väljer **skapa Lagringskonto**.

1. I den **skapa Lagringskonto** dialogrutan Välj eller ange följande information:

   * Azure-prenumerationen som du vill lägga till lagringskontot.
   * Namnet som du vill använda för det nya lagringskontot.
   * Region eller affinitetsgrupp (till exempel västra USA eller Östasien).
   * Typ av replikering som du vill använda för storage-konto som lokalt redundant.

   ![Skapa ett Azure-lagringskonto](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Välj **Skapa**.

Det nya lagringskontot som visas i den **lagring** lista i Solution Explorer.

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Att koppla ett befintligt lagringskonto med hjälp av Server Explorer

1. Öppna snabbmenyn för Azure i Server Explorer **lagring** och sedan väljer **Anslut extern lagring**.

    ![Lägga till ett befintligt lagringskonto](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
1. I den **skapa Lagringskonto** dialogrutan Välj eller ange följande information:

   * Namnet på befintliga lagringskonto som du vill bifoga.
   * Nyckeln för det valda lagringskontot. Det här värdet anges vanligtvis för dig när du väljer ett lagringskonto. Om du vill ha Visual Studio för att komma ihåg lagringskontots åtkomstnyckel väljer du den **Kom ihåg kontonyckel** kryssrutan.
   * Protokoll för att ansluta till lagringskontot, till exempel HTTP, HTTPS eller en anpassad slutpunkt. Mer information om anpassade slutpunkter finns [hur du konfigurerar anslutningssträngar](https://msdn.microsoft.com/library/azure/ee758697.aspx).

### <a name="to-view-the-secondary-endpoints"></a>Att visa de sekundära slutpunkterna

Om du har skapat ett lagringskonto med hjälp av den **läsbehörighet Geo-Redundant** replikeringsalternativet, du kan visa dess sekundär slutpunkter genom att öppna snabbmenyn för namnet på kontot och välj sedan **egenskaper**.

![Sekundär lagring-slutpunkter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Ta bort ett lagringskonto från Server Explorer

I Server Explorer, öppna snabbmenyn för namnet på kontot och välj sedan **ta bort**. 

Om du tar bort ett lagringskonto tas alla sparade viktig information för det kontot också bort.

Om du tar bort ett lagringskonto från Server Explorer påverkar det inte ditt lagringskonto eller data som den innehåller. Det tar bara bort referensen från Server Explorer. Permanent ta bort ett lagringskonto genom att använda den [Azure-portalen](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure-lagringstjänster finns [åtkomst till Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/ee405490.aspx).
