---
title: 'Snabb start: skapa en färdigheter i Azure Portal'
titleSuffix: Azure Cognitive Search
description: Använd guiden Importera data för att lägga till kognitiva kunskaper i en indexerings pipeline. Kognitiva kunskaper är OCR (optisk tecken läsning) och bearbetning av naturligt språk.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 2280b718fe949384bb67b1b606ab143ddca8e077
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113363"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Snabb start: skapa en färdigheter för Azure Kognitiv sökning kognitivt i Azure Portal

En färdigheter är en AI-funktion som extraherar information och struktur från stora, ej differentierade text-eller bildfiler och gör det lätt att indexera och söka efter fullständiga texts öknings frågor i Azure Kognitiv sökning. 

I den här snabb starten ska du kombinera tjänster och data i Azure-molnet för att skapa färdigheter. När allt är på plats kör du guiden **Importera data** i portalen för att hämta den tillsammans. Slut resultatet är ett sökbart index som är ifyllt med data som skapats av AI-bearbetning som du kan fråga i portalen ([Sök Utforskaren](search-explorer.md)).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-services-and-load-data"></a>Skapa tjänster och läsa in data

I den här snabb starten används Azure Kognitiv sökning Azure Blob Storage och [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. 

Eftersom arbets belastningen är så liten är Cognitive Services i bakgrunden för att tillhandahålla kostnads fri bearbetning för upp till 20 transaktioner dagligen när de anropas från Azure Kognitiv sökning. Så länge du använder de exempel data som vi tillhandahåller kan du hoppa över att skapa eller bifoga en Cognitive Services-resurs.

1. [Ladda ned exempeldata](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) som består av en liten filuppsättning med olika typer av data. Zippa upp filerna.

1. [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) under din aktuella prenumeration. 

   Välj samma region som Azure Kognitiv sökning. Välj konto typen StorageV2 (General Purpose v2) om du vill testa kunskaps lagrings funktionen senare i en annan genom gång. Annars väljer du vilken typ som helst.

1. Öppna BLOB Services-sidorna och skapa en behållare. Du kan använda standard nivån för offentlig åtkomst. 

1. I behållare klickar du på **överför** för att ladda upp exempelfilerna som du laddade ned i det första steget. Observera att du har ett brett utbud av innehålls typer, inklusive bilder och programfiler som inte är full text sökbar i deras egna format.

   ![Källfiler i Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under samma prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

Nu kan du gå vidare till guiden Importera data.

## <a name="run-the-import-data-wizard"></a>Kör guiden Importera data

På översikts sidan för Search-tjänsten klickar du på **Importera data** i kommando fältet för att ställa in kognitiv berikning i fyra steg.

  ![Kommandot Importera data](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

1. I **Anslut till dina data**väljer du **Azure Blob Storage**, väljer det lagrings konto och den behållare som du skapade. Namnge datakällan och lämna standardvärdena för resten av inställningarna. 

   ![Konfiguration av Azure-blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Fortsätt till nästa sida.

### <a name="step-2-add-cognitive-skills"></a>Steg 2: Lägga till kognitiva kunskaper

Lägg sedan till kognitiva kunskaper för att anropa naturlig språk bearbetning. Exempel data består av 12 filer, så den kostnads fria tilldelningen av 20 transaktioner på Cognitive Services räcker för den här snabb starten. Eftersom vi inte använder OCR, kommer endast icke-bildfiler att räknas, knäckas och användas i den här processen.

1. I den här snabb starten använder vi den **kostnads fria** Cognitive Services resursen.

   ![Bifoga Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Utöka **Lägg till kunskaper** och välj kunskaper som utför bearbetning av naturligt språk. I den här snabbstarten väljer du entitetsigenkänning för personer, organisationer och platser.

   ![Bifoga Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

1. Godkänn standard käll fältet: `content`. Detta kan verka som ett litet mål, men för Azure-blobbar innehåller `content` fältet de flesta av BLOB-dokumenten (till exempel ett Word-dokument eller en PowerPoint-kortlek), vilket gör det till en bra kandidat.

1. Fortsätt till nästa sida.

> [!NOTE]
> Kunskaper för bearbetning av naturligt språk körs på textinnehåll i exempeldatamängden. Eftersom vi inte valde OCR-alternativet bearbetas inte JPEG- och PNG-filerna som finns i exempeldatauppsättningen i den här snabbstarten. 

### <a name="step-3-configure-the-index"></a>Steg 3: Konfigurera indexet

I Azure Kognitiv sökning, innehåller ett index ditt sökbara innehåll och guiden **Importera data** kan vanligt vis skapa schemat åt dig genom att sampla data källan. I det här steget ska du granska det genererade schemat och eventuellt ändra inställningarna. Nedan visas standardschemat som skapas för demo-BLOB-datauppsättningen.

I den här snabbstarten passar guidens standardinställningar bra: 

+ Standardnamnet är *azureblob-index* baserat på datakällans typ. 

+ Standard fält baseras på det ursprungliga käll data fältet (`content`) plus de utdatakolumner (`people`, `organizations`och `locations`) som skapats av kognitiva färdigheter. Standarddatatyperna härleds från metadata och datasampling.

+ Standard dokument nyckeln är *metadata_storage_path* (markerad eftersom fältet innehåller unika värden).

+ Standardattributen är **Hämtningsbart** och **Sökbart** i dessa fält. **Sökbar** anger att det går att söka i ett fält. **Hämtbar** betyder att det kan returneras i resultat. Guiden förutsätter att du vill att dessa fält ska vara hämtningsbara och sökbara, eftersom du har skapat dem via en kompetensuppsättning.

  ![Indexfält](media/cognitive-search-quickstart-blob/index-fields.png)

Observera genomstrykningen och frågetecknet i attributet **Hämtningsbart** i fältet `content`. I textbaserade blobbdokument innehåller fältet `content` den största delen av filen, som skulle kunna köras som tusentals rader. Om du vill skicka filens innehåll till klientkod, ser du till att **Hämtningsbart** förblir markerat. Annars kan du ta bort attributet i `content` om de extraherade elementen (`people`, `organizations` och `locations`) är tillräckliga för dina syften.

Att ett fält markeras som **Hämtningsbart** innebär inte att fältet *måste* finnas i sökresultaten. Du kan detaljstyra sammansättningen av sökresultat med hjälp av frågeparametern **$select** om du vill ange vilka fält som ska inkluderas. I textintensiva fält som `content`, är parametern **$select** din lösning för att dina programanvändare ska få hanterbara sökresultat, samtidigt som du säkerställer att klientkoden har åtkomst till all information som behövs via attributet **Hämtningsbart**.
  
Fortsätt till nästa sida.

### <a name="step-4-configure-the-indexer"></a>Steg 4: Konfigurera indexeraren

Indexeraren är en övergripande resurs som styr indexeringen. Indexeraren definierar datakällans namn, ett målindex och körningsfrekvensen. Guiden **Importera data** skapar flera objekt, och av dem är det alltid en indexerare som du kan köra upprepade gånger.

1. På sidan **indexerare** kan du acceptera standard namnet och klicka på schema alternativet **när** du vill köra det direkt. 

   ![Definition av indexerare](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Klicka på **Skicka** för att skapa och köra indexeraren samtidigt.

## <a name="monitor-status"></a>Övervaka status

Indexering av kognitiva kunskaper tar längre tid än vanlig text baserad indexering. Du övervakar förloppet genom att gå till sidan Översikt och klicka på **indexerare** mitt på sidan.

Varningen beror på att JPG-och PNG-bildfilerna finns i data källan och att vi utelämnade OCR-kunskaper från denna pipeline. Du hittar också trunkering av meddelanden. Extraktionen är begränsad till 32 000 tecken på den kostnads fria nivån.

  ![Meddelande om Azure-Kognitiv sökning](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Eftersom indexeringen och berikningen kan ta tid rekommenderar vi att du börjar med mindre datamängder. 

I Azure Portal kan du också övervaka aktivitets loggen för meddelanden som länkar till en klicknings bara **Azure kognitiv sökning meddelande** status. Det kan ta flera minuter att slutföra körningen.

## <a name="query-in-search-explorer"></a>Fråga i Sökutforskaren

När ett index har skapats kan du skicka frågor för att returnera dokument från indexet. Använd **Sökutforskaren** i portalen till att köra frågor och visa resultat. 

1. Klicka på **Sökutforskaren** i kommandofältet på söktjänstens instrumentpanelsida.

1. Välj **Ändra index** längst upp och välj det index som du skapade.

1. Ange en söksträng för att fråga indexet, till exempel `search=Microsoft&searchFields=Organizations`.

Resultatet returneras i JSON, som kan vara relativt utförligt och svårläst, särskilt i stora dokument som kommer från Azure-blobar. Om det är svårt att överblicka resultatet använder du CTRL-F för att söka i dokument. För den här frågan kan du söka i JSON för specifika villkor. 

Du kan också använda CTRL-F för att se hur många dokument det finns i en viss resultatuppsättning. För Azure-blobar väljer portalen ”metadata_storage_path” som nyckel eftersom varje värde är unikt för dokumentet. Använd CTRL-F och sök efter ”metadata_storage_path” för att se antalet dokument. 

  ![Exempel med Sökutforskaren](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Lärdomar

Nu har du skapat din första färdigheter och lärt dig viktiga koncept som är användbara för prototyp av en omfattande Sök lösning med hjälp av dina egna data.

Bland de viktigaste lärdomarna som vi hoppas att du tar med dig är beroendet av Azure-datakällor. En färdigheter är kopplad till en indexerare och indexerarna är Azure och projektspecifika. I den här snabbstarten används Azure Blob Storage, men det går att använda andra Azure-datakällor. Mer information finns i [indexerare i Azure kognitiv sökning](search-indexer-overview.md).

En annan viktig aspekt är att kunskaper körs på indatafält. På portalen måste du välja ett enda fält för alla kunskaper. I koden kan indata vara andra fält, eller utdata från en överordnad kunskap.

Utdata dirigeras till ett sökindex och det finns en mappning mellan namn-värdepar som skapas vid indexering och enskilda fält i indexet. Internt konfigurerar portalen [anteckningar](cognitive-search-concept-annotations-syntax.md) och definierar en [kunskapsuppsättning](cognitive-search-defining-skillset.md), som definierar ordningen på åtgärder och det allmänna flödet. Dessa steg är dolda på portalen, men när du börjar skriva kod blir dessa begrepp viktiga.

Slutligen har du lärt dig att kunna verifiera innehållet genom att fråga indexet. I slutet av den här artikeln är Azure Kognitiv sökning ett sökbart index, som du kan fråga med antingen den [enkla](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) eller [helt utökade frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Ett index som innehåller berikade fält är precis som andra fält. Om du vill lägga till standard-eller [Anpassade analyser](search-analyzers.md), kan du göra det genom att använda [bedömnings profiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonymer](search-synonyms.md), [fasettiska filter](search-filters-facets.md), geo-sökning eller någon annan Azure kognitiv sökning-funktion.

## <a name="clean-up"></a>Rensa

När du arbetar med din egen prenumeration är det en bra idé i slutet av ett projekt för att identifiera om du fortfarande behöver de resurser som du har skapat. Resurser som har lämnats igång kostar dig pengar. Du kan ta bort resurser individuellt eller ta bort resurs gruppen för att ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Du kan skapa färdighetsuppsättningar med hjälp av portalen, .NET SDK eller REST API. Om du vill veta mer om din kunskap kan du prova REST API med Postman och fler exempel data.

> [!div class="nextstepaction"]
> [Självstudie: Lägg till struktur i "ostrukturerat innehåll" med AI-anrikning](cognitive-search-tutorial-blob.md)

> [!Tip]
> Ta bort indexeraren i portalen om du vill upprepa den här övningen eller testa en annan AI-anrikning. Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren tillbaka till noll för Cognitive Services bearbetning.