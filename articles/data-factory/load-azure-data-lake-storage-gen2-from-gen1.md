---
title: Kopiera data från Azure Data Lake Storage Gen1 till Gen2
description: Använda Azure Data Factory för att kopiera data från Azure Data Lake Storage Gen1 till Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 81f8577592f1d53627bc09a2f9ace8c060ad4660
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668857"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopiera data från Azure Data Lake Storage Gen1 till Gen2 med Azure Data Factory

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stordataanalys som är inbyggda i [Azure Blob-lagring](../storage/blobs/storage-blobs-introduction.md). Du kan använda den för att samverka med dina data med hjälp av både filsystem och objektlagringsparadigm.

Om du för närvarande använder Azure Data Lake Storage Gen1 kan du utvärdera Azure Data Lake Storage Gen2 genom att kopiera data från Data Lake Storage Gen1 till Gen2 med hjälp av Azure Data Factory.

Azure Data Factory är en fullständigt hanterad molnbaserad dataintegrationstjänst. Du kan använda tjänsten för att fylla sjön med data från en omfattande uppsättning lokala och molnbaserade datalager och spara tid när du bygger dina analyslösningar. En lista över kopplingar som stöds finns i tabellen [över datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory erbjuder en skala ut, hanterad dataförflyttningslösning. På grund av utskalningsarkitekturen i Data Factory kan den inta data med högt dataflöde. Mer information finns i [Kopiera aktivitetsprestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder datafabrikens kopieringsdataverktyg för att kopiera data från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* Azure Data Lake Storage Gen1-konto med data i det.
* Azure Storage-konto med Data Lake Storage Gen2 aktiverat. Om du inte har ett lagringskonto [skapar du ett konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resursData** > **+ Analytics** > **Data Factory**.
   
   ![Data Fabriksval i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du värden för de fält som visas i följande bild: 
      
   ![Ny fabrikssida för data](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure-datafabrik. Om felet "Data fabriksnamn \"LoadADLSDemo\" inte är tillgängligt" anger du ett annat namn för datafabriken. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa datafabriken. 
    * **Resursgrupp:** Välj en befintlig resursgrupp i listrutan. Du kan också välja alternativet **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren som används av datafabriken kan finnas på andra platser och i andra regioner. 

3. Välj **Skapa**.
4. När skapandet är klart går du till datafabriken. Startsidan för **Data Factory** visas i följande bild: 
   
   ![Datafabrikens startsida](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Välj panelen **Författare & Övervakare** om du vill starta programmet Dataintegrering på en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läsa in data i Azure Data Lake Storage Gen2

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta kopieringsdataverktyget. 

   ![Kopiera dataverktygspanel](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. På sidan **Egenskaper** anger du **CopyFromADLSGen1ToGen2** för fältet **Aktivitetsnamn.** Välj **Nästa**.

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. På sidan **Källdatalager** väljer du **+ Skapa ny anslutning**.

    ![Sidan Källdatalager](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Välj **Azure Data Lake Storage Gen1** i galleriet med anslutningsappar och välj **Fortsätt**.
    
    ![Källa datalager Azure Data Lake Storage Gen1 sida](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Gör så här på **sidan Ange Azure Data Lake Storage Gen1:An ange azure data lake storage gen1-anslutningssida:**

   a. Välj din DataSjölagringsgend1 för kontonamnet och ange eller validera **klienten**.
  
   b. Välj **Testa anslutning** för att validera inställningarna. Välj sedan **Slutför**.
  
   c. Du ser att en ny anslutning har skapats. Välj **Nästa**.
   
   > [!IMPORTANT]
   > I den här genomsprföringen använder du en hanterad identitet för Azure-resurser för att autentisera din Azure Data Lake Storage Gen1. Om du vill ge den hanterade identiteten rätt behörigheter i Azure Data Lake Storage Gen1 följer du [dessa instruktioner](connector-azure-data-lake-store.md#managed-identity).
   
   ![Ange Azure Data Lake Storage Gen1-konto](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Bläddra till mappen och filen som du vill kopiera över på sidan **Välj indatafil eller mapp.** Markera mappen eller filen och välj **Välj**.

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Ange kopieringsbeteendet genom att välja alternativen **Kopiera filer rekursivt** och **binärt.** Välj **Nästa**.

    ![Ange utdatamapp](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. På sidan **Måldatalager** väljer du **+ Skapa ny anslutning** > **Azure Data Lake Storage Gen2** > **Continue**.

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Gör så här på **sidan Ange Azure Data Lake Storage Gen2:a:**

   a. Välj ditt datasjölagringsgenm2-kompatibla konto i listrutan **Lagringskontonamn.**
   
   b. Välj **Slutför** för att skapa anslutningen. Välj sedan **Nästa**.
   
   ![Ange Azure Data Lake Storage Gen2-konto](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. På sidan **Välj utdatafil eller mapp** anger du **copyfromadlsgen1** som utdatamappnamn och väljer **Nästa**. Data Factory skapar motsvarande Azure Data Lake Storage Gen2-filsystem och undermappar under kopiering om de inte finns.

    ![Ange utdatamapp](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. På sidan **Inställningar** väljer du **Nästa** för att använda standardinställningarna.

12. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa**.

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. På **sidan Distribution**väljer du **Övervaka** för att övervaka pipelinen.

    ![Distributionssida](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **Åtgärder** finns länkar som visar information om aktivitetskörningen och för att köra pipelinen igen.

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningen väljer du länken **Visa aktivitetskörningar** i kolumnen **Åtgärder.** Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipelinekörningar väljer du länken **Pipelines** högst upp. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). 

    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Om du vill övervaka körningsinformationen för varje kopieringsaktivitet väljer du länken **Information** (glasögonbild) under **Åtgärder** i aktivitetsövervakningsvyn. Du kan övervaka information som mängden data som kopieras från källan till diskhon, datadataflöde, körningssteg med motsvarande varaktighet och använda konfigurationer.

    ![Information om övervakning av aktivitetskörning](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Kontrollera att data kopieras till ditt Azure Data Lake Storage Gen2-konto.

## <a name="best-practices"></a>Bästa praxis

Information om hur du utvärderar uppgradering från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2 i allmänhet läser [du Uppgradera dina lösningar för stordataanalys från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). I följande avsnitt beskrivs metodtips för att använda Data Factory för en datauppgradering från Data Lake Storage Gen1 till Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Datapartition för historisk datakopiering

- Om den totala datastorleken i Data Lake Storage Gen1 är mindre än 30 TB och antalet filer är mindre än 1 miljon kan du kopiera alla data i en enda kopieringsaktivitetskörning.
- Om du har en större mängd data att kopiera, eller om du vill ha flexibiliteten att hantera datamigrering i batchar och göra var och en av dem komplett inom en viss tidsram, partitionera data. Partitionering minskar också risken för oväntade problem.

Använd ett proof of concept för att verifiera lösningen från slutna till slutna mål och testa kopieringsdataflödet i din miljö. Större proof-of-concept steg: 

1. Skapa en Data Factory-pipeline med en enda kopieringsaktivitet för att kopiera flera datapunkter från Data Lake Storage Gen1 till Data Lake Storage Gen2 för att få en databasprestandabaslinje. Börja med [dataintegrationsenheter som](copy-activity-performance-features.md#data-integration-units) 128. 
2. Baserat på kopieringsdataflödet som du får i steg 1 beräknar du den beräknade tid som krävs för hela datamigrering. 
3. (Valfritt) Skapa en kontrolltabell och definiera filfiltret för att partitionera de filer som ska migreras. Sättet att partitionera filerna är att: 

    - Partition efter mappnamn eller mappnamn med ett jokerteckenfilter. Vi rekommenderar den här metoden.
    - Partition av en fils senaste ändrade tid.

### <a name="network-bandwidth-and-storage-io"></a>I/O för nätverksbandbredd och lagring 

Du kan styra samtidigheten i datafabrikskopieringsjobb som läser data från Data Lake Storage Gen1 och skriver data till Data Lake Storage Gen2. På så sätt kan du hantera användningen på den lagrings-I/O för att undvika att påverka det normala affärsarbetet på Data Lake Storage Gen1 under migreringen.

### <a name="permissions"></a>Behörigheter 

I Data Factory stöder [Data Lake Storage Gen1-anslutningen](connector-azure-data-lake-store.md) tjänstens huvudnamn och hanterade identitet för Azure-resursautentiseringar. [Data Lake Storage Gen2-anslutningsappen](connector-azure-data-lake-storage.md) stöder kontonyckel, tjänsthuvudnamn och hanterad identitet för Azure-resursautentiseringar. Om du vill att Data Factory ska kunna navigera och kopiera alla filer eller åtkomstkontrollistor (ACL: er) som du behöver, ger du tillräckligt hög behörighet för det konto som du anger för att komma åt, läsa eller skriva alla filer och ange ACL:er om du väljer det. Ge den en superanvändar- eller ägarroll under migreringsperioden. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Bevara ACL:er från DataSjölagring gen1

Om du vill replikera ACL:erna tillsammans med datafiler när du uppgraderar från Data Lake Storage Gen1 till Data Lake Storage Gen2 läser [du Bevara ACL:er från DataSjölagring gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Inkrementell kopia 

Du kan använda flera metoder för att läsa in endast nya eller uppdaterade filer från Data Lake Storage Gen1:

- Ladda nya eller uppdaterade filer efter tidspartitionerad mapp eller filnamn. Ett exempel är /2019/05/13/*.
- Ladda nya eller uppdaterade filer efter LastModifiedDate.
- Identifiera nya eller uppdaterade filer med ett verktyg eller en lösning från tredje part. Skicka sedan fil- eller mappnamnet till datafabrikspipelinen via parametern eller en tabell eller fil. 

Rätt frekvens för inkrementell belastning beror på det totala antalet filer i Azure Data Lake Storage Gen1 och volymen av nya eller uppdaterade filer som ska läsas in varje gång. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kopiera aktivitetsöversikt](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1-anslutning](connector-azure-data-lake-store.md)
> Azure Data Lake Storage[Gen2-anslutning](connector-azure-data-lake-storage.md)