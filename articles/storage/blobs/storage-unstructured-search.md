---
title: Söka efter ostrukturerade data i Azure-molnlagring
description: Söker efter ostrukturerade data med hjälp av Azure Search.
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: eba2ef280e60693cfd4402348fe61b122cdccdf6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399864"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Sök efter ostrukturerade data i molnlagringen

I den här självstudien får du lära dig hur du söker efter ostrukturerade data med [Azure Search](../../search/search-what-is-azure-search.md) med hjälp av data som lagras i Azure-blobar. Ostrukturerade data är data som inte är ordnade på ett fördefinierat sätt eller inte har en datamodell. Ett exempel är en .txt-fil.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp
> * skapar ett lagringskonto
> * Skapa en container
> * Ladda upp data till din container
> * Skapa en söktjänst via portalen
> * Använda söktjänsten för att söka i en container

## <a name="download-the-sample"></a>Hämta exemplet

En uppsättning exempeldata har förberetts för dig. **Ladda ned [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** och extrahera den i dess egen mapp.

Exemplet består av textfiler som hämtats från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Du kan använda dem som exempeltextfiler för att söka med hjälp av Azure.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett lagringskonto tillhandahåller en unik plats där du kan lagra och få åtkomst till dina Azure Storage-dataobjekt.

Det finns för närvarande två typer av lagringskonton, **Blob** och **General-purpose** (för generell användning). För den här självstudien skapar du ett lagringskonto **för generell användning**.

Om du inte är bekant med processen för att skapa ett lagringskonto för generell användning skapar du ett så här:

1. I den vänstra menyn väljer du **Lagringskonton** och sedan **Lägg till**.

2. Ange ett unikt namn för lagringskontot. 

3. Välj **Resource Manager** för din **distributionsmodell** och välj **General purpose** (Generell användning) från listrutan **Account kind** (Kontovariant).

4. Välj **Lokalt redundant lagring (LRS)** i listrutan **Replikering**.

5. Under **Resursgrupp** väljer du **Skapa ny** och anger ett unikt namn.

6. Välj en lämplig prenumeration.

7. Välj en plats och välj **Skapa**.

  ![Ostrukturerad sökning](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Skapa en container

Container liknar mappar och används för att lagra blobar.

För den här självstudien använder du en enskild container att lagra de textfiler som hämtas från clinicaltrials.gov.

1. Navigera till ditt lagringskonto i Azure-portalen.

2. Välj **Bläddra efter blobar** under **Blob-tjänst**.

3. Lägg till en ny container.

4. Ge containern namnet ”data” och välj **Container** för offentlig åtkomstnivå.

5. Klicka på **OK** för att skapa containern. 

  ![Ostrukturerad sökning](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Ladda upp exempeldata

Nu när du har en container kan du ladda upp dina exempeldata till den.

1. Välj din container och välj **Ladda upp**.

2. Välj den blå mappikon som visas intill fältet Filer och bläddra till den lokala mappen där du extraherade exempeldata.

3. Markera alla de extraherade filerna och välj **Öppna**

4. Välj **Ladda upp** för att påbörja uppladdningsprocessen.

  ![Ostrukturerad sökning](media/storage-unstructured-search/upload.png)

Uppladdningsprocessen kan ta en stund.

När den är klar går du tillbaka till datacontainern för att bekräfta att textfilerna har laddats upp.

  ![Ostrukturerad sökning](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Skapa en söktjänst

Azure Search är en sökning-som-tjänst-molnlösning som ger utvecklare API:er och verktyg för att lägga till omfattande sökfunktioner för data i webb-, mobil- och företagsprogram.

Om du inte är bekant med processen för att skapa en söktjänst skapar du en så här:

1. Navigera till ditt lagringskonto i Azure-portalen.

2. Rulla nedåt och klicka på **Lägg till Azure Search** under **BLOB SERVICE** (BLOBTJÄNST).

3. Under **Importera data** väljer du **Välj din tjänst**.

4. Välj **Klicka här om du vill skapa en ny söktjänst**.

5. Inuti **Ny söktjänst** anger du ett unikt namn för din söktjänst i fältet **URL**.

6. Under **Resursgrupp** väljer du **Använd befintlig** och väljer samma resursgrupp som du skapade tidigare.

7. För **Prisnivå** väljer du nivån **Kostnadsfri** och klickar på **Välj**.

8. Välj **Skapa** för att skapa söktjänsten.

  ![Ostrukturerad sökning](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Ansluta din söktjänst till containern

Nu när du har en söktjänst kan du koppla den till ditt blob storage. Det här avsnittet vägleder dig genom processen för att välja en datakälla, skapa ett index och skapa en indexerare.

1. Navigera till ditt lagringskonto.

2. Välj **Lägg till Azure Search** under **BLOB SERVICE** (BLOBTJÄNST).

3. Välj **Söktjänst** inuti **Importera data** och klicka sedan på den söktjänst som du skapade i föregående avsnitt. Detta öppnar **Ny datakälla**.

### <a name="new-data-source"></a>Ny datakälla

  En datakälla anger vilka data som ska indexeras och hur data ska kommas åt. En datakälla kan användas flera gånger av samma söktjänst.

1. Ange ett namn för datakällan. Under **Data som ska extraheras** väljer du **Innehåll och metadata**. Datakällan anger vilka delar av bloben som indexeras.
    
    a. I dina egna framtida scenarier kan du även välja **Lagra enbart metadata**. Du gör det valet om du vill begränsa de data som indexeras till standard-blob-egenskaper eller användardefinierade egenskaper.
    
    b. Du kan även välja **Alla metadata** för att hämta båda standard-blob-egenskaper och *alla* innehållstypspecifika metadata. 

2. Eftersom de blobar du använder är textfiler ställer du in **Parsningsläge** på **Text**.
    
    a. I dina egna framtida scenarier kan det vara bra att välja [andra parsningslägen](../../search/search-howto-indexing-azure-blob-storage.md) beroende på innehållet i dina blobar.

  ![Ostrukturerad sökning](media/storage-unstructured-search/datasources.png)

3. Välj **Lagringscontainer** för att lista tillgängliga lagringskonton.

4. Välj ditt lagringskonto och välj sedan den container som du skapade tidigare.

5. Klicka på **Välj** för att återgå till **Ny datakälla** och välj **OK** för att fortsätta.

  ![Ostrukturerad sökning](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Konfigurera indexet

  Ett index är en samling fält från din datakälla som kan sökas. Indexet är det sätt som din söktjänst använder för att veta på vilka sätt dina data ska sökas igenom.

1. I **Importera data** väljer du **Anpassa målindex**.
 
2. Ange ett namn för indexet i fältet **Indexnamn**.

3. Markera kryssrutan för attributet **Hämtningsbar** under **metadata_storage_name**.

  ![Ostrukturerad sökning](media/storage-unstructured-search/valuestoselect.png)

4. Klicka på **OK** så öppnas **Skapa en indexerare**.

Parametrarna för ditt index och de attribut som du ger de parametrarna är viktiga. Parametrarna anger *vilka* data som ska lagras, medan attributen anger *hur* dessa data ska lagras.

Kolumnen **FIELD NAME** (FÄLTNAMN) innehåller parametrarna. Följande tabell innehåller en lista över tillgängliga attribut och deras beskrivningar.

### <a name="field-attributes"></a>Fältattribut
| Attribut | Beskrivning |
| --- | --- |
| *Nyckel* |En sträng som innehåller det unika ID:t för varje dokument och som används för att leta upp dokument. Alla index måste ha en nyckel. Endast ett fält kan vara nyckeln och dess typ måste anges till Edm.String. |
| *Hämtningsbar* |Anger om ett fält kan returneras i sökresultat. |
| *Filtrerbar* |Gör att fältet kan användas i filterfrågor. |
| *Sorterbar* |Gör att en fråga kan sortera sökresultat med hjälp av det här fältet. |
| *Fasettbar* |Gör att ett fält kan användas i en struktur för aspektbaserad navigering för filtrering av användaren. Oftast fungerar fält med upprepade värden som kan användas för att gruppera flera dokument (till exempel flera dokument som hör till samma varumärkes- eller tjänstkategori) bäst som aspekter. |
| *Sökbar* |Markerar fältet som fulltextsökbart. |


### <a name="create-an-indexer"></a>Skapa en indexerare
    
  En indexerare ansluter en datakälla med ett sökindex, och innehåller ett schema för att indexera om dina data.

1. Ange ett namn i fältet **Namn** och välj **OK**.

  ![Ostrukturerad sökning](media/storage-unstructured-search/exindexer.png)

2. Du kommer tillbaka till **Importera data**. Välj **OK** för att slutföra anslutningsprocessen.

Du har nu anslutit din blob till din söktjänst. Det tar några minuter för portalen att visa att indexet har fyllts i. Söktjänsten börjar dock indexera direkt så att du kan börja söka på en gång.

## <a name="search-your-text-files"></a>Söka bland dina textfiler

Om du vill söka bland dina filer öppnar du sökutforskaren inuti indexet för din nyligen skapade söktjänst.

Följande steg visar var du hittar sökutforskaren och ger dig några exempelfrågor:

1. Navigera till alla resurser och leta upp din nyligen skapade söktjänst.

  ![Ostrukturerad sökning](media/storage-unstructured-search/exampleurl.png)

3. Välj ditt index för att öppna det. 

  ![Ostrukturerad sökning](media/storage-unstructured-search/overview.png)

4. Välj **Sökutforskaren** för att öppna sökutforskaren, där du kan köra live-frågor mot dina data.

  ![Ostrukturerad sökning](media/storage-unstructured-search/indexespane.png)

5. Välj **Sök** medan frågesträngsfältet är tomt. En tom fråga returnerar *alla* data från dina blobar.

  ![Ostrukturerad sökning](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Fulltextsökning 

Ange ”Myopia” i fältet **Frågesträng** och välj **Sök**. En sökning av innehållet i filerna startas och returnerar en delmängd av dem, som innehåller ordet ”Myopia”.

  ![Ostrukturerad sökning](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Sökning av systemegenskaper

Du kan även skapa frågor som söker efter systemegenskaper med hjälp av parametern `$select`. Ange `$select=metadata_storage_name` i frågesträngen och tryck på Retur, så returneras endast det särskilda fältet.
    
Frågesträngen ändrar URL:en direkt, och därför tillåts inte blanksteg. Om du vill söka i flera fält använder du ett kommatecken, till exempel: `$select=metadata_storage_name,metadata_storage_path`
    
Parametern `$select` kan bara användas med fält som är markerade som hämtningsbara när du definierar ditt index.

  ![Ostrukturerad sökning](media/storage-unstructured-search/metadatasearchunstructured.png) 

Du har nu slutfört den här självstudien och har en sökbar uppsättning ostrukturerade data.

## <a name="next-steps"></a>Nästa steg

I den här självstudien fick du lära dig hur du söker i ostrukturerade data med Azure Search, till exempel:

> [!div class="checklist"]
> * Skapa en resursgrupp
> * skapar ett lagringskonto
> * Skapa en container
> * Ladda upp data till din container
> * Skapa en söktjänst
> * Använda söktjänsten för att söka i en container

Följ den här länken om du vill veta mer om hur du indexerar dokument med Azure Search.

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage med Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)