---
title: Kopiera data från Azure Data Lake Storage Gen1 till Gen2 med Azure Data Factory
description: Använd Azure Data Factory för att kopiera data från Azure Data Lake Storage Gen1 till Gen2
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068980"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopiera data från Azure Data Lake Storage Gen1 till Gen2 med Azure Data Factory

Azure Data Lake Storage Gen2 är en uppsättning funktioner för analys av stordata som är inbyggd i [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Du kan använda den för att samverka med dina data med hjälp av paradigm för lagring av system- och båda fil.

Om du använder Azure Data Lake Storage Gen1, kan du utvärdera Azure Data Lake Storage Gen2 genom att kopiera data från Data Lake Storage Gen1 till Gen2 med hjälp av Azure Data Factory.

Azure Data Factory är en fullständigt hanterad molnbaserad dataintegreringstjänst. Du kan använda tjänsten för att fylla i sjön med data från ett stort utbud av lokala och molnbaserade datalager och spara tid när du skapar Analyslösningar. En lista över kopplingar som stöds finns i tabellen med [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory är en lösning för flytt av skalbara, hanterade data. På grund av skalbar arkitektur för Data Factory, kan det mata in data med ett högt dataflöde. Mer information finns i [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder Data Factory kopiera data för att kopiera data från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Data Lake Storage Gen1-konto med data i den.
* Azure Storage-konto med Data Lake Storage Gen2 aktiverat. Om du inte har ett lagringskonto [skapa ett konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På menyn till vänster väljer **skapa en resurs** > **Data och analys** > **Data Factory**.
   
   ![Valet data Factory i den nya rutan](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På den **ny datafabrik** anger värden för fälten som visas i följande bild: 
      
   ![Sida för ny datafabrik](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Namn på**: Ange ett globalt unikt namn för din Azure data factory. Om du får felet ”datafabriksnamnet \"LoadADLSDemo\" är inte tillgänglig”, ange ett annat namn för data factory. Använd till exempel namnet _**dittnamn**_ **ADFTutorialDataFactory**. Skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data factory. 
    * **Resursgrupp**: Välj en befintlig resursgrupp från den nedrullningsbara listan. Du kan också välja den **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md). 
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används i datafabriken kan finnas på andra platser och regioner. 

3. Välj **Skapa**.
4. När datafabriken har skapats går du till din datafabrik. Du ser den **Data Factory** startsida, enligt följande bild: 
   
   ![Datafabrikens startsida](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Välj den **författare och Övervakare** att starta programmet för dataintegrering i en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läs in data till Azure Data Lake Storage Gen2

1. På den **börjar** väljer den **kopieringsdata** att starta verktyget kopiera data. 

   ![Panel för verktyget kopiera data](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. På den **egenskaper** anger **CopyFromADLSGen1ToGen2** för den **aktivitetsnamn** fält. Välj **Nästa**.

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. På den **källdatalagret** väljer **+ Skapa ny anslutning**.

    ![Sidan Källdatalager](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Välj **Azure Data Lake Storage Gen1** i galleriet för anslutningen och välj **Fortsätt**.
    
    ![Azure Data Lake Storage Gen1 sidan källans datalager](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. På den **ange Azure Data Lake Storage Gen1 anslutning** utför de här stegen:

   a. Välj Data Lake Storage Gen1 för namnet på kontot och ange eller verifiera den **klient**.
  
   b. Välj **Testanslutning** att verifiera inställningarna. Välj sedan **Slutför**.
  
   c. Du kan se att en ny anslutning skapades. Välj **Nästa**.
   
   > [!IMPORTANT]
   > I den här genomgången använder du en hanterad identitet för Azure-resurser för att autentisera din Azure Data Lake Storage Gen1. Om du vill bevilja den hanterade identitet i Azure Data Lake Storage Gen1 rätt behörigheter, Följ [instruktionerna](connector-azure-data-lake-store.md#managed-identity).
   
   ![Ange konto för Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. På den **Välj indatafil eller mapp** sidan, bläddra till mappen och filen som du vill kopiera över. Välj mappen eller filen och välj **Välj**.

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Ange kopieringsbeteendet genom att välja den **kopiera filer rekursivt** och **binär kopia** alternativ. Välj **Nästa**.

    ![Ange Utdatamappen](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. På den **måldatalager** väljer **+ Skapa ny anslutning** > **Azure Data Lake Storage Gen2**  >   **Fortsätta**.

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. På den **ange Azure Data Lake Storage Gen2 anslutning** utför de här stegen:

   a. Välj Data Lake Storage Gen2 kan kontot från de **lagringskontonamn** listrutan.
   
   b. Välj **Slutför** att skapa anslutningen. Välj sedan **Nästa**.
   
   ![Ange konto för Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. På den **Välj utdatafil eller mapp** anger **copyfromadlsgen1** som utdata mappnamn och välj **nästa**. Data Factory skapar motsvarande Azure Data Lake Storage Gen2 filsystemet och undermappar vid kopiering om de inte finns.

    ![Ange Utdatamappen](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. På den **inställningar** väljer **nästa** att använda standardinställningarna.

12. På den **sammanfattning** , granskar du inställningarna och välj **nästa**.

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. På den **distributionssida**väljer **övervakaren** att övervaka pipelinen.

    ![Distributionssida](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **Åtgärder** finns länkar som visar information om aktivitetskörningen och för att köra pipelinen igen.

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningen, väljer den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Växla tillbaka till vyn med pipelinekörningar, Välj den **Pipelines** länken längst upp. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. För att övervaka körning-information för varje kopieringsaktiviteten, Välj den **information** länken (glasögonbilden) under **åtgärder** i övervakningsvyn-aktivitet. Du kan övervaka information som mängden data som kopieras från källan till mottagare, dataflöde, utförande med motsvarande tid och används konfigurationer.

    ![Övervaka aktivitetskörningsinformation](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Kontrollera att data har kopierats till ditt konto för Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Bästa praxis

Om du vill utvärdera uppgradera från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2 i allmänhet, se [uppgradera din big data analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). I följande avsnitt beskrivs bästa praxis för att använda Data Factory för en datauppgradering från Data Lake Storage Gen1 till Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Datapartition för kopiering av historiska data

- Om din totala mängden data i Data Lake Storage Gen1 är mindre än 30 TB och antalet filer är mindre än 1 miljon, kan du kopiera alla data i en enda kopieringsaktivitetskörning.
- Om du har en större mängd data som ska kopieras, eller om du vill ha flexibiliteten att hantera migrering av data i batchar och göra dem klar inom en specifik tidsgräns, partitionera data. Partitionering minskar också risken för eventuella oväntade problem.

Använd ett Konceptbevis för att kontrollera slutpunkt till slutpunkt-lösningen och testa kopia dataflödet i din miljö. Viktigaste proof of concept-stegen: 

1. Skapa en Data Factory-pipeline med en enda Kopieringsaktivitet som kopierar flera TB data från Data Lake Storage Gen1 till Data Lake Storage Gen2 att hämta en kopia baslinje för prestanda. Börja med [integrering enheter (DIUs)](copy-activity-performance.md#data-integration-units) som 128. 
2. Baserat på Kopiera dataflödet som du får i steg 1, beräkna den beräknade tiden som krävs för migrering av hela data. 
3. (Valfritt) Skapa en tabell med kontroll och definiera filtret om du vill partitionera på filerna så att migreras. Sättet att partitionera filerna är att: 

    - Partitionen av mapp- eller mappnamn med jokertecken-filtret. Vi rekommenderar den här metoden.
    - Partitionen av en fil är senast ändrad.

### <a name="network-bandwidth-and-storage-io"></a>Nätverk i/o med bandbredd och lagring 

Du kan styra samtidighet av Data Factory kopia jobb som läser data från Data Lake Storage Gen1 och skriva data till Data Lake Storage Gen2. På så sätt kan hantera du användningen på lagringssystemet i/o för att undvika påverkar företagets normala arbete på Data Lake Storage Gen1 under migreringen.

### <a name="permissions"></a>Behörigheter 

I Data Factory den [Data Lake Storage Gen1 connector](connector-azure-data-lake-store.md) stöder tjänsten huvudnamn och hanterad identitet för Azure-resurs-autentiseringar. Den [Data Lake Storage Gen2 connector](connector-azure-data-lake-storage.md) stöder konto nyckel, tjänstens huvudnamn och hanterad identitet för Azure-resurs-autentiseringar. Att göra Data Factory kan navigera och kopierar alla filer eller åtkomstkontrollistor (ACL) du behöver, beviljar hög tillräcklig behörighet för det konto som du anger för att komma åt, läsa, eller skriva alla filer och ange ACL: er om du vill. Bevilja en roll för en användare eller ägare under migreringsperioden. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Bevara ACL: er från Data Lake Storage Gen1

Om du vill replikera ACL: er tillsammans med filer när du uppgraderar från Data Lake Storage Gen1 till Data Lake Storage Gen2 [bevara ACL: er från Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Inkrementell kopia 

Du kan använda flera metoder för att läsa in bara nya eller uppdaterade filer från Data Lake Storage Gen1:

- Läsa in nya eller uppdaterade filer efter tid partitionerade mapp eller fil namn. Ett exempel är/2019/05/13 / *.
- Läsa in nya eller uppdaterade filer enligt LastModifiedDate.
- Identifiera nya eller uppdaterade filer av några verktyg från tredje part eller lösningen. Skicka sedan filen eller mappen namnet till Data Factory-pipeline via parametern eller en tabell eller fil. 

Rätt frekvensen göra stegvis inläsning är beroende av det totala antalet filer i Azure Data Lake Storage Gen1 och de nya eller uppdaterade filer som ska läsas in varje gång. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
> [connector för Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 connector](connector-azure-data-lake-storage.md)