---
title: 'Självstudier: Söka i ostrukturerade data i Azure Blob Storage'
description: 'Självstudier: Söka i ostrukturerade data i Blob Storage med hjälp av Azure Search.'
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 42c67d73ee776488fbe932676f61cb7166c2984b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599850"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Självstudier: Sök efter ostrukturerade data i molnlagringen

I den här självstudien får du lära dig hur du söker i ostrukturerade data med hjälp av [Azure Search](../../search/search-what-is-azure-search.md) och data som lagras i Azure Blob Storage. Ostrukturerade data är data som inte är ordnade på ett fördefinierat sätt eller inte har en datamodell. Ett exempel är en .txt-fil.

Du behöver en Azure-prenumeration för den här självstudien. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp
> * skapar ett lagringskonto
> * Skapa en container
> * Ladda upp data till din container
> * Skapa en söktjänst via portalen
> * Ansluta en söktjänst till ett lagringskonto
> * Skapa en datakälla
> * Konfigurera indexet
> * Skapa en indexerare
> * Använda söktjänsten för att söka i en container

## <a name="prerequisites"></a>Nödvändiga komponenter

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. I den här självstudien skapas en ny resursgrupp.

Logga in på [Azure-portalen](http://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

En exempeldatamängd har förberetts så att du kan använda den i den här självstudien. Ladda ned [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) och extrahera den i dess egen mapp.

Exemplet består av textfiler som hämtats från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). I den här självstudien används de som exempeltextfiler som genomsöks med hjälp av Azure Search-tjänster.

## <a name="create-a-container"></a>Skapa en container

Container liknar mappar och används för att lagra blobar.

För den här självstudien använder du en enskild container att lagra de textfiler som hämtas från clinicaltrials.gov.

1. Gå till ditt lagringskonto i Azure-portalen.

2. Välj **Bläddra efter blobar** under **Blob-tjänst**.

3. Lägg till en ny container.

4. Ge containern namnet **data** och välj **Container** för offentlig åtkomstnivå.

5. Klicka på **OK** för att skapa containern.

  ![Ostrukturerad sökning](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Ladda upp exempeldata

Nu när du har en container kan du ladda upp dina exempeldata till den.

1. Välj din container och välj **Ladda upp**.

2. Välj den blå mappikon intill fältet **Filer** och bläddra till den lokala mapp där du extraherade exempeldata.

3. Markera alla de extraherade filerna och välj **Öppna**.

4. Välj **Ladda upp** för att påbörja uppladdningsprocessen.

  ![Ostrukturerad sökning](media/storage-unstructured-search/upload.png)

Uppladdningsprocessen kan ta en stund.

När den är klar går du tillbaka till datacontainern och bekräftar att textfilerna har laddats upp.

  ![Ostrukturerad sökning](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Skapa en söktjänst

Azure Search är en sökning-som-tjänst-molnlösning som ger utvecklare API:er och verktyg för att lägga till sökfunktioner för data.

För den här självstudien använder du en söktjänst för att söka i de textfiler som hämtas från clinicaltrials.gov.

1. Gå till ditt lagringskonto i Azure-portalen.

2. Rulla nedåt och välj **Lägg till Azure Search** under **BLOB SERVICE** (BLOBTJÄNST).

3. Under **Importera data** väljer du **Välj din tjänst**.

4. Välj **Klicka här om du vill skapa en ny söktjänst**.

5. Inuti **Ny söktjänst** anger du ett unikt namn för din söktjänst i fältet **URL**.

6. Under **Resursgrupp** väljer du **Använd befintlig** och väljer den resursgrupp som du skapade tidigare.

7. För **Prisnivå** väljer du nivån **Kostnadsfri** och klickar på **Välj**.

8. Välj **Skapa** för att skapa söktjänsten.

  ![Ostrukturerad sökning](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Ansluta din söktjänst till containern

Nu när du har en söktjänst kan du koppla den till ditt blob storage. Det här avsnittet vägleder dig genom processen för att välja en datakälla, skapa ett index och skapa en indexerare.

1. Gå till ditt lagringskonto.

2. Välj **Lägg till Azure Search** under **BLOB SERVICE** (BLOBTJÄNST).

3. Välj **Söktjänst** inuti **Importera data** och klicka sedan på den söktjänst som du skapade i föregående avsnitt. Då öppnas **Ny datakälla**.

### <a name="create-a-data-source"></a>Skapa en datakälla

  En datakälla anger vilka data som ska indexeras och hur data ska kommas åt. En datakälla kan användas flera gånger av samma söktjänst.

1. Ange ett namn för datakällan. Under **Data som ska extraheras** väljer du **Innehåll och metadata**. Datakällan anger vilka delar av bloben som indexeras.

2. Eftersom de blobar du använder är textfiler ställer du in **Parsningsläge** på **Text**.

  ![Ostrukturerad sökning](media/storage-unstructured-search/datasources.png)

3. Välj **Lagringscontainer** för att lista tillgängliga lagringskonton.

4. Välj ditt lagringskonto och välj sedan den container som du skapade tidigare.

  ![Ostrukturerad sökning](media/storage-unstructured-search/datacontainer.png)

5. Klicka på **Välj** för att återgå till **Ny datakälla** och välj **OK** för att fortsätta.

### <a name="configure-the-index"></a>Konfigurera indexet

  Ett index är en samling fält från din datakälla som kan sökas. Du anger och konfigurerar parametrar i de här fälten så att söktjänsten vet vilka sätt som ska användas för sökning i dina data.

1. I **Importera data** väljer du **Anpassa målindex**.

2. Ange ett namn för indexet i fältet **Indexnamn**.

3. Markera kryssrutan för attributet **Hämtningsbar** under **metadata_storage_name**.

  ![Ostrukturerad sökning](media/storage-unstructured-search/valuestoselect.png)

4. Välj **OK** så öppnas **Skapa en indexerare**.

Parametrarna för ditt index och de attribut som du ger de parametrarna är viktiga. Parametrarna anger *vilka* data som ska lagras, medan attributen anger *hur* dessa data ska lagras.

Kolumnen **FIELD NAME** (FÄLTNAMN) innehåller parametrarna. Följande tabell innehåller en lista över tillgängliga attribut och deras beskrivningar.

#### <a name="field-attributes"></a>Fältattribut

| Attribut | Beskrivning |
| --- | --- |
| *Nyckel* |En sträng som innehåller det unika ID:t för varje dokument och som används för att leta upp dokument. Alla index måste ha en nyckel. Endast ett fält kan vara nyckeln och dess typ måste anges till Edm.String. |
| *Hämtningsbar* |Anger om ett fält kan returneras i sökresultat. |
| *Filtrerbar* |Gör att fältet kan användas i filterfrågor. |
| *Sorterbar* |Gör att en fråga kan sortera sökresultat med hjälp av det här fältet. |
| *Fasettbar* |Gör att ett fält kan användas i en struktur för aspektbaserad navigering för filtrering av användaren. Oftast fungerar fält med upprepade värden som kan användas för att gruppera dokument (till exempel flera dokument som hör till samma varumärkes- eller tjänstkategori) bäst som aspekter. |
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

1. Gå till alla resurser och leta upp din nyligen skapade söktjänst.

  ![Ostrukturerad sökning](media/storage-unstructured-search/exampleurl.png)

2. Öppna indexet genom att välja det.

  ![Ostrukturerad sökning](media/storage-unstructured-search/overview.png)

3. Välj **Sökutforskaren** för att öppna sökutforskaren, där du kan köra live-frågor mot dina data.

  ![Ostrukturerad sökning](media/storage-unstructured-search/indexespane.png)

4. Välj **Sök** medan frågesträngsfältet är tomt. En tom fråga returnerar *alla* data från dina blobar.

  ![Ostrukturerad sökning](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>Utför en fulltextsökning

Ange **Myopia** (närsynthet) i fältet **Frågesträng** och välj **Sök**. Det här steget påbörjar en sökning av filernas innehåll och returnerar en delmängd av dem som innehåller ordet ”Myopia”.

  ![Ostrukturerad sökning](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>Utföra en sökning av systemegenskaper

Utöver fulltextsökningar kan du även skapa frågor som söker efter systemegenskaper med hjälp av parametern `$select`.

Ange `$select=metadata_storage_name` i frågesträngen och tryck på **Retur**. Detta leder till att endast det särskilda fältet returneras.

Frågesträngen ändrar URL:en direkt, och därför tillåts inte blanksteg. Om du vill söka i flera fält använder du ett kommatecken, till exempel: `$select=metadata_storage_name,metadata_storage_path`

Parametern `$select` kan bara användas med fält som är markerade som hämtningsbara när du definierar ditt index.

  ![Ostrukturerad sökning](media/storage-unstructured-search/metadatasearchunstructured.png)

Du har nu slutfört den här självstudien och har en sökbar uppsättning ostrukturerade data.

## <a name="clean-up-resources"></a>Rensa resurser

Det enklaste sättet att ta bort de resurser som du har skapat är att ta bort hela resursgruppen. Om du tar bort resursgruppen tas även alla resurser bort som ingår i gruppen. När du i följande exempel tar bort resursgruppen tas lagringskontot och själva resursgruppen bort.

1. I Azure-portalen går du till listan över resursgrupper i din prenumeration.
2. Välj den resursgrupp som du vill ta bort.
3. Välj den knappen **Ta bort resursgrupp** och ange namnet på resursgruppen i borttagningsfältet.
4. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Följ den här länken om du vill veta mer om hur du indexerar dokument med Azure Search:

> [!div class="nextstepaction"]
> [Indexera dokument i Azure Blob Storage med Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)
