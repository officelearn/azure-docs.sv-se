---
title: "Söka efter Ostrukturerade data i Azure-molnlagring"
description: "Söker Ostrukturerade data med Azure search."
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Sök efter ostrukturerade data i molnlagringen

I kursen får du lära dig hur söka Ostrukturerade data med [Azure Search](../../search/search-what-is-azure-search.md) med data som lagras i Azure BLOB. Ostrukturerade data är data som inte är ordnade i en fördefinierad sätt eller inte har en datamodell. Ett exempel är en txt-fil.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp
> * skapar ett lagringskonto
> * Skapa en behållare
> * Ladda upp data till din behållare
> * Skapa en söktjänst via portalen
> * Använda search-tjänsten för att söka i behållaren

## <a name="download-the-sample"></a>Hämta exemplet

Data exempeldata har förberetts för dig. **Hämta [kliniska trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)**  och packa till sin egen mapp.

Exemplet består av textfiler som hämtats från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Du kan använda dem som exempel textfiler för att söka med Azure.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](http://portal.azure.com).

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett lagringskonto tillhandahåller en unik plats för att lagra och komma åt dina Azure Storage-dataobjekt.

Det finns för närvarande två typer av lagringskonton **Blob** och **allmänna**. Den här självstudiekursen skapar du en **allmänna** storage-konto.

Om du inte är bekant med att skapa ett allmänt lagringskonto är här hur man skapar en:

1. I den vänstra menyn, Välj **Lagringskonton**och välj **Lägg till**.

2. Ange ett unikt namn för ditt lagringskonto. 

3. Välj **Resource Manager** för din **distributionsmodellen** och välj **generella** från den **konto kind** listrutan.

4. Välj **lokalt redundant lagring (LRS)** från den **replikering** listrutan.

5. Under **resursgruppen**väljer **Skapa nytt** och ange ett unikt namn.

6. Välj en lämplig prenumeration.

7. Välj en plats och välj **skapa.**

  ![Ostrukturerade sökning](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Skapa en behållare

Behållare liknar mappar och används för lagring av BLOB.

För den här kursen använder du en enskild behållare för lagring av textfiler som hämtas från clinicaltrials.gov.

1. Navigera till ditt lagringskonto i Azure-portalen.

2. Välj **Bläddra blobbar** under **Blob-tjänsten.**

3. Lägg till en ny behållare.

4. Behållaren ”data” och välj **behållare** för offentliga åtkomstnivå.

5. Välj **OK** att skapa behållaren. 

  ![Ostrukturerade sökning](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Ladda upp exempeldata

Nu när du har en behållare kan du ladda upp exempeldata till den.

1. Välj din behållare och välj **överför**.

2. Välj ikonen blå mapp avbildas bredvid fältet filer och bläddra till den lokala mappen där du extraherade exempeldata.

3. Markera alla de extraherade filerna och välj **öppna**

4. Välj **överför** att påbörja överföringen.

  ![Ostrukturerade sökning](media/storage-unstructured-search/upload.png)

Överföringen kan ta en stund.

När den är klar kan du gå tillbaka till din databehållare bekräfta textfiler har överförts.

  ![Ostrukturerade sökning](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Skapa en söktjänst

Azure Search är en molnlösning för sökning som en tjänst som ger utvecklare API: er och verktyg för att lägga till en omfattande sökinställningar över dina data i webb-, mobil- och enterprise-program.

Om du inte är bekant med att skapa en söktjänst är här hur man skapar en:

1. Navigera till ditt lagringskonto i Azure-portalen.

2. Bläddra nedåt och klicka **lägga till Azure Search** under **BLOB-tjänsten**.

3. Under **importera Data**väljer **Välj tjänsten**.

4. Välj **Klicka här om du vill skapa en ny söktjänsten**.

5. I **nya söktjänsten** ange ett unikt namn för din söktjänst i den **URL** fältet.

6. Under **resursgruppen**väljer **använda befintliga** och välj den resursgrupp som du skapade tidigare.

7. För den **prisnivå**, Välj den **lediga** nivån och klicka på **Välj**.

8. Välj **skapa** att skapa search-tjänsten.

  ![Ostrukturerade sökning](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Ansluta din söktjänst till din behållaren

Nu när du har en söktjänst kan koppla du den till blob storage. Det här avsnittet vägleder dig genom processen för att välja en datakälla, skapa ett index och skapa en indexerare.

1. Navigera till ditt lagringskonto.

2. Välj **lägga till Azure Search** under **BLOB-tjänsten.**

3. Välj **söktjänsten** i **importera Data** och klicka sedan på search-tjänsten som du skapade i föregående avsnitt. Detta öppnar **ny datakälla**.

### <a name="new-data-source"></a>Ny datakälla

  En datakälla anger vilka data som ska index och hur du kommer åt data. En datakälla kan användas flera gånger med samma search-tjänsten.

1. Ange ett namn för datakällan. Under **Data att extrahera**väljer **innehåll och Metadata**. Datakällan anger vilka delar av blob som indexeras.
    
    a. I din egen framtida scenarier kan du också välja **lagring metadata**. Du kan göra detta val om du vill begränsa de data som är indexerad till standard blob användardefinierade egenskaper eller.
    
    b. Du kan också välja **alla metadata** att hämta egenskaper för både standard-blob och *alla* innehållstypen specifika metadata. 

2. Eftersom blobbar som du använder är textfiler, ange **parsning läge** till **Text**.
    
    a. I din egen framtida scenarier kan du markera [andra tolkning lägen](../../search/search-howto-indexing-azure-blob-storage.md) beroende på innehållet i dina blobbar.

  ![Ostrukturerade sökning](media/storage-unstructured-search/datasources.png)

3. Välj **lagringsbehållaren** att lista tillgängliga storage-konton.

4. Välj ditt lagringskonto och markera den behållare som du skapade tidigare.

5. Klicka på **Välj** att återgå till **ny datakälla** och välj **OK** att fortsätta.

  ![Ostrukturerade sökning](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Konfigurera indexet

  Ett index är en samling av fält i datakällan som kan söka efter. Indexet är hur din söktjänst vet på vilka sätt dina data ska genomsökas.

1. I **dataimport** Välj **anpassa målindexet**.
 
2. Ange ett namn för ditt index i den **Indexnamnet** fältet.

3. Välj den **Retrievable** attributets kryssrutan under **metadata_storage_name**.

  ![Ostrukturerade sökning](media/storage-unstructured-search/valuestoselect.png)

4. Klicka på **OK**, vilket öppnar **skapa en indexerare**.

Parametrarna för ditt index och attribut som du ger dessa parametrar är viktiga. Ange parametrarna *vad* data som lagras, ange attributen *hur* att lagra dessa data.

Den **fältnamn** kolumnen innehåller parametrar. Följande tabell innehåller en lista över tillgängliga attribut och deras beskrivningar.

### <a name="field-attributes"></a>Fältattribut
| Attribut | Beskrivning |
| --- | --- |
| *Nyckel* |En sträng som innehåller unikt ID för varje dokument som används för dokumentet sökning. Alla index måste ha en nyckel. Endast ett fält kan vara nyckeln och dess typ måste anges till Edm.String. |
| *Hämtningsbar* |Anger om ett fält kan returneras i sökresultat. |
| *Filtrerbar* |Gör att fältet kan användas i filterfrågor. |
| *Sorterbar* |Gör att en fråga kan sortera sökresultat med hjälp av det här fältet. |
| *Fasettbar* |Gör att ett fält som ska användas i en fasetterad navigeringsstruktur för användaren själv dirigerad filtrering. Oftast fungerar fält med upprepade värden som kan användas för att gruppera flera dokument (till exempel flera dokument som hör till samma varumärkes- eller tjänstkategori) bäst som aspekter. |
| *Sökbar* |Markerar fältet som fulltextsökbart. |


### <a name="create-an-indexer"></a>Skapa en indexerare
    
  En indexerare ansluter en datakälla med en sökindex och ger ett schema för att indexera om dina data.

1. Ange ett namn i den **namn** fältet och välj **OK**.

  ![Ostrukturerade sökning](media/storage-unstructured-search/exindexer.png)

2. Du kommer tillbaka till **importera Data**väljer **OK** att slutföra anslutningen.

Du har nu har anslutit din blob till din söktjänst. Det tar några minuter att visa att indexet fylls i portalen. Söktjänsten börjar dock omedelbart indexering så att du kan börja söka direkt.

## <a name="search-your-text-files"></a>Söka i textfiler

Öppna Sökutforskaren i index för nyskapade search-tjänsten för att söka efter dina filer.

Följande steg visar var du hittar Sökutforskaren och ger dig vissa exempelfrågor:

1. Navigera till alla resurser och hittar nyskapade search-tjänsten.

  ![Ostrukturerade sökning](media/storage-unstructured-search/exampleurl.png)

3. Välj ditt index öppnas. 

  ![Ostrukturerade sökning](media/storage-unstructured-search/overview.png)

4. Välj **Sök Explorer** Öppna Sök explorer, där du kan göra live frågor på dina data.

  ![Ostrukturerade sökning](media/storage-unstructured-search/indexespane.png)

5. Välj **Sök** medan frågesträngfältet är tom. Returnerar en tom frågan *alla* data från dina blobbar.

  ![Ostrukturerade sökning](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Fulltextsökning 

Ange ”Myopia” i den **frågesträng** fältet och välj **Sök**. Initierar en sökning av innehållet i filerna och returnerar en delmängd av dem, som innehåller ordet ”Myopia”.

  ![Ostrukturerade sökning](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>System egenskaper sökning

Du kan även skapa frågor som söka efter Systemegenskaper med hjälp av den `$select` parameter. Ange `$select=metadata_storage_name` i frågesträngen och trycker på Ange, returnerar endast att programmatiskt.
    
Frågesträngen är direkt ändra URL-Adressen, så blanksteg inte tillåts. Använd kommatecken, som om du vill söka flera fält:`$select=metadata_storage_name,metadata_storage_path`
    
Den `$select` parametern kan bara användas med fält som är markerade strängfält när du definierar ditt index.

  ![Ostrukturerade sökning](media/storage-unstructured-search/metadatasearchunstructured.png) 

Du har nu slutfört den här kursen och har en sökbar uppsättning Ostrukturerade data.

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om att söka Ostrukturerade data med Azure Search, till exempel att:

> [!div class="checklist"]
> * Skapa en resursgrupp
> * skapar ett lagringskonto
> * Skapa en behållare
> * Ladda upp data till din behållare
> * Skapa en söktjänst
> * Använda Search-tjänsten för att söka i behållaren

Den här länken om du vill veta mer om indexering dokument med Azure Search.

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage med Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)