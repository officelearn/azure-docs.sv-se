---
title: 'Snabbstart: Skapa en kompetens i Azure-portalen'
titleSuffix: Azure Cognitive Search
description: I den här portalens snabbstart kan du lära dig hur du använder guiden Importera data för att lägga till kognitiva kunskaper i en indexeringspipeline i Azure Cognitive Search. Färdigheter inkluderar optisk teckenigenkänning (OCR) och bearbetning av naturligt språk.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472425"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Snabbstart: Skapa en cognitive skillset för Azure Cognitive Search i Azure-portalen

En kompetens är en AI-funktion som extraherar information och struktur från stora odifferentierade text- eller bildfiler och gör den indexerbar och sökbar för fulltextsökningsfrågor i Azure Cognitive Search. 

I den här snabbstarten kombinerar du tjänster och data i Azure-molnet för att skapa kompetensen. När allt är på plats kör du guiden **Importera data** i portalen för att samla allt. Slutresultatet är ett sökbart index som fylls med data som skapats av AI-bearbetning som du kan fråga i portalen ([Sök explorer](search-explorer.md)).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-services-and-load-data"></a>Skapa tjänster och läsa in data

Den här snabbstarten använder Azure Cognitive Search, [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/)och [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. 

Eftersom arbetsbelastningen är så liten utnyttjas Cognitive Services bakom kulisserna för att tillhandahålla kostnadsfri bearbetning för upp till 20 transaktioner. För en så liten datauppsättning kan du hoppa över att skapa eller koppla en Cognitive Services-resurs.

1. [Ladda ned exempeldata](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) som består av en liten filuppsättning med olika typer av data. Packa upp filerna.

1. [Skapa ett Azure-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Välj samma region som Azure Cognitive Search för att undvika bandbreddsavgifter. 
   
   Välj kontotypen StorageV2 (allmänt ändamål V2) om du vill prova funktionen kunskapslager senare, i en annan genomgång. Annars väljer du vilken typ som helst.

1. Öppna sidorna Blob-tjänster och skapa en behållare. Du kan använda standardnivån för offentlig åtkomst. 

1. I behållaren klickar du på **Ladda** upp för att ladda upp exempelfilerna som du hämtade i det första steget. Observera att du har ett brett utbud av innehållstyper, inklusive bilder och programfiler som inte är fulltextsökbara i sina ursprungliga format.

   ![Källfiler i Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnadsfri tjänst för den här snabbstarten.

Du är nu redo att gå vidare med guiden Importera data.

## <a name="run-the-import-data-wizard"></a>Köra guiden Importera data

På sidan Översikt över söktjänsten klickar du på **Importera data** i kommandofältet för att ställa in kognitiv anrikning i fyra steg.

  ![Kommandot Importera data](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Steg 1 - Skapa en datakälla

1. I **Anslut till dina data**väljer du Azure **Blob-lagring**, väljer lagringskontot och behållaren som du skapade. Namnge datakällan och lämna standardvärdena för resten av inställningarna. 

   ![Konfiguration av Azure-blob](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Fortsätt till nästa sida.

### <a name="step-2---add-cognitive-skills"></a>Steg 2 - Lägg till kognitiva färdigheter

Konfigurera sedan AI-anrikning för att anropa OCR, bildanalys och bearbetning av naturligt språk. 

1. För den här snabbstarten använder vi resursen **Free** Cognitive Services. Exempeldata består av 14 filer, så den kostnadsfria tilldelningen av 20-transaktionen på Cognitive Services är tillräcklig för den här snabbstarten. 

   ![Bifoga Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Expandera **Lägg till anrikningar** och gör fyra val. 

   Aktivera OCR för att lägga till bildanalyskunskaper på guidesidan.

   Ange granularitet till sidor för att dela upp text i mindre segment. Flera textkunskaper är begränsade till 5 KB-indata.

   Välj entitetsigenkänning (personer, organisationer, platser) och bildanalys.

   ![Bifoga Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Fortsätt till nästa sida.

### <a name="step-3---configure-the-index"></a>Steg 3 - Konfigurera indexet

Ett index innehåller ditt sökbara innehåll och guiden **Importera data** kan vanligtvis skapa schemat åt dig genom att prova datakällan. I det här steget granskar du det genererade schemat och kan ändra eventuella inställningar. Nedan visas standardschemat som skapats för demonstrations-Blob-datauppsättningen.

I den här snabbstarten passar guidens standardinställningar bra:  

+ Standardfält baseras på egenskaper för befintliga blobbar plus nya fält `people`som `organizations` `locations`ska innehålla anrikningsutdata (till exempel , , ). Datatyper härleds från metadata och dataprovtagning.

+ Standarddokumentnyckeln *är metadata_storage_path* (markerad eftersom fältet innehåller unika värden).

+ Standardattribut är **Hämtningsbara** och **sökbara**. **Sökbar** tillåter fulltextsökning ett fält. **Hämtningsbara** innebär att fältvärden kan returneras i resultat. Guiden förutsätter att du vill att dessa fält ska vara hämtningsbara och sökbara, eftersom du har skapat dem via en kompetensuppsättning.

  ![Indexfält](media/cognitive-search-quickstart-blob/index-fields.png)

Observera genomstrykningen och frågetecknet i attributet **Hämtningsbart** i fältet `content`. I textbaserade blobbdokument innehåller fältet `content` den största delen av filen, som skulle kunna köras som tusentals rader. Ett fält som detta är otympligt i sökresultaten och du bör utesluta det för den här demon. 

Om du behöver skicka filinnehåll till klientkod kontrollerar du dock att **hämtningsbara** förblir markerat. Annars kan du avmarkera `content` attributet på om `people`de `organizations` `locations`extraherade elementen (till exempel , , och så vidare) är tillräckliga.

Att ett fält markeras som **Hämtningsbart** innebär inte att fältet *måste* finnas i sökresultaten. Du kan detaljstyra sammansättningen av sökresultat med hjälp av frågeparametern **$select** om du vill ange vilka fält som ska inkluderas. I textintensiva fält som `content`, är parametern **$select** din lösning för att dina programanvändare ska få hanterbara sökresultat, samtidigt som du säkerställer att klientkoden har åtkomst till all information som behövs via attributet **Hämtningsbart**.
  
Fortsätt till nästa sida.

### <a name="step-4---configure-the-indexer"></a>Steg 4 - Konfigurera indexeraren

Indexeraren är en övergripande resurs som styr indexeringen. Indexeraren definierar datakällans namn, ett målindex och körningsfrekvensen. Guiden **Importera data** skapar flera objekt, och av dem är alltid en indexerare som du kan köra flera gånger.

1. På sidan **Indexerare** kan du acceptera standardnamnet och klicka på alternativet **En gång** schema för att köra det omedelbart. 

   ![Definition av indexerare](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Klicka på **Skicka** för att skapa och köra indexeraren samtidigt.

## <a name="monitor-status"></a>Övervaka status

Kognitiv kompetensindexering tar längre tid att slutföra än typisk textbaserad indexering, särskilt OCR och bildanalys. Om du vill övervaka förloppet går du till sidan Översikt och klickar på **Indexerare** mitt på sidan.

  ![Azure Cognitive Search-meddelande](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Varningar är normala med tanke på det stora utbudet av innehållstyper. Vissa innehållstyper är inte giltiga för vissa kunskaper och på lägre nivåer är det vanligt att stöta på [indexergränser](search-limits-quotas-capacity.md#indexer-limits). Trunkeringsmeddelanden på 32 000 tecken är till exempel en indexeringsgräns på den kostnadsfria nivån. Om du körde den här demon på en högre nivå, skulle många trunkering varningar försvinna.

Om du vill kontrollera varningar eller fel klickar du på varningsstatusen i indexerarlistan för att öppna sidan Körningshistorik.

På den sidan klickar du på Varningsstatus igen om du vill visa listan med varningar som liknar den som visas nedan. 

  ![Varningslista för Indexerare](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Detaljer visas när du klickar på en viss statusrad. Denna varning säger att sammanslagningen stoppas efter att ha nått en maximal tröskel (denna PDF är stor).

  ![Varningsinformation](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Fråga i Sökutforskaren

När ett index har skapats kan du köra frågor för att returnera resultat. Använd **Sökutforskaren** för den här uppgiften i portalen. 

1. Klicka på **Sökutforskaren** i kommandofältet på söktjänstens instrumentpanelsida.

1. Välj **Ändra index** längst upp och välj det index som du skapade.

1. Ange en söksträng för att fråga indexet, till exempel `search=Microsoft&$select=people,organizations,locations,imageTags`.

Resultaten returneras som JSON, som kan vara utförliga och svåra att läsa, särskilt i stora dokument som kommer från Azure-blobbar. Några tips för att söka i det här verktyget är följande tekniker:

+ Lägg `$select` till för att ange vilka fält som ska inkluderas i resultat. 
+ Använd CTRL-F för att söka i JSON efter specifika egenskaper eller termer.

Frågesträngar är skiftlägeskänsliga så om du får ett meddelande om "okänt fält" kontrollerar du **Fält** eller **Indexdefinition (JSON)** för att verifiera namn och ärende. 

  ![Exempel med Sökutforskaren](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Lärdomar

Du har nu skapat din första kompetens och lärt dig viktiga begrepp som är användbara för att skapa en berikad söklösning med dina egna data.

Bland de viktigaste lärdomarna som vi hoppas att du tar med dig är beroendet av Azure-datakällor. En kompetens är bunden till en indexerare och indexerare är Azure och källspecifika. I den här snabbstarten används Azure Blob Storage, men det går att använda andra Azure-datakällor. Mer information finns [i Indexerare i Azure Cognitive Search](search-indexer-overview.md). 

Ett annat viktigt begrepp är att färdigheter fungerar över innehållstyper, och när du arbetar med heterogent innehåll kommer vissa indata att hoppas över. Dessutom kan stora filer eller fält överskrida indexeringsgränserna för din tjänstnivå. Det är normalt att se varningar när dessa händelser inträffar. 

Utdata dirigeras till ett sökindex och det finns en mappning mellan namnvärdespar som skapas under indexeringen och enskilda fält i indexet. Internt konfigurerar portalen [anteckningar](cognitive-search-concept-annotations-syntax.md) och definierar en [kunskapsuppsättning](cognitive-search-defining-skillset.md), som definierar ordningen på åtgärder och det allmänna flödet. Dessa steg är dolda på portalen, men när du börjar skriva kod blir dessa begrepp viktiga.

Slutligen lärde du dig att kunna verifiera innehåll genom att fråga efter indexet. I slutändan är vad Azure Cognitive Search ger ett sökbart index, som du kan fråga med hjälp av antingen [enkel](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) eller [helt utökad frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Ett index som innehåller berikade fält är precis som andra fält. Om du vill införliva standard- eller [anpassade analysatorer](search-analyzers.md), [poängprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonymer,](search-synonyms.md) [fasterade filter,](search-filters-facets.md)geo-sökning eller någon annan Azure Cognitive Search-funktion, kan du säkert göra det.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Du kan skapa skillsets med hjälp av portalen,.NET SDK eller REST API. Om du vill ha mer kunskap kan du prova REST API med Postman och mer exempeldata.

> [!div class="nextstepaction"]
> [Självstudiekurs: Extrahera text och struktur från JSON-blobbar med REST-API:er](cognitive-search-tutorial-blob.md)

> [!Tip]
> Om du vill upprepa den här övningen eller prova en annan GENOMGÅNG AV AI-anrikning tar du bort indexeraren i portalen. Om du tar bort indexeraren återställs den kostnadsfria dagliga transaktionsräknaren tillbaka till noll för bearbetning av Cognitive Services.