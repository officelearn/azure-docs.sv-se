---
title: Kopiera data från Azure Data Factory till Azure Data Explorer
description: I den här artikeln får du lära dig hur du inbelastar (läser in) data i Azure Data Explorer med hjälp av kopieringsverktyget för Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 8e17a004ff866f3915000fb72b6770757062cf83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422903"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Kopiera data till Azure Data Explorer med hjälp av Azure Data Factory 

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst. Det erbjuder realtidsanalys på stora mängder data som strömmas från många källor, till exempel program, webbplatser och IoT-enheter. Med Azure Data Explorer kan du iterativt utforska data och identifiera mönster och avvikelser för att förbättra produkter, förbättra kundupplevelser, övervaka enheter och öka verksamheten. Det hjälper dig att utforska nya frågor och få svar på några minuter. 

Azure Data Factory är en fullständigt hanterad, molnbaserad dataintegrationstjänst. Du kan använda den för att fylla i din Azure Data Explorer-databas med data från ditt befintliga system. Det kan hjälpa dig att spara tid när du bygger analyslösningar.

När du läser in data i Azure Data Explorer ger Data Factory följande fördelar:

* **Enkel installation:** Få en intuitiv, femstegsguide utan skript som krävs.
* **Omfattande stöd för datalager**: Få inbyggt stöd för en omfattande uppsättning lokala och molnbaserade datalager. En detaljerad lista finns i tabellen [över datalager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Säker och kompatibel**: Data överförs via HTTPS eller Azure ExpressRoute. Den globala tjänstnärvaron säkerställer att dina data aldrig lämnar den geografiska gränsen.
* **Hög prestanda**: Datainläsningshastigheten är upp till 1 gigabyte per sekund (GBps) i Azure Data Explorer. Mer information finns i [Kopiera aktivitetsprestanda](/azure/data-factory/copy-activity-performance).

I den här artikeln använder du datafabrikskopieringsdataverktyget för att läsa in data från Amazon Simple Storage Service (S3) i Azure Data Explorer. Du kan följa en liknande process för att kopiera data från andra datalager, till exempel:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL-datalager](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Filsystem](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och en databas](create-cluster-database-portal.md).
* En datakälla.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://ms.portal.azure.com).

1. Välj Skapa en **resurs** > **Analytics** > **Data Factory**i den vänstra rutan .

   ![Skapa en datafabrik i Azure-portalen](media/data-factory-load-data/create-adf.png)

1. Ange värden för fälten i följande tabell i fönstret **Nytt datafabrik:**

   ![Fönstret "Ny datafabrik"](media/data-factory-load-data/my-new-data-factory.png)  

   | Inställning  | Värde att ange  |
   |---|---|
   | **Namn** | Ange ett globalt unikt namn för datafabriken i rutan. Om du får ett felmeddelande *anger Datafabriksnamn \"\" LoadADXDemo inte*ett annat namn för datafabriken. Regler om namngivning av datafabriksartefakter finns i [Namngivningsregler för Data Factory](/azure/data-factory/naming-rules).|
   | **Prenumeration** | I listrutan väljer du den Azure-prenumeration som du vill skapa datafabriken i. |
   | **Resursgrupp** | Välj **Skapa ny**och ange sedan namnet på en ny resursgrupp. Om du redan har en resursgrupp väljer du **Använd befintlig**. |
   | **Version** | Välj **V2**i listrutan . |    
   | **Location** | Välj plats för datafabriken i listrutan. Endast platser som stöds visas i listan. De datalager som används av datafabriken kan finnas på andra platser eller regioner. |

1. Välj **Skapa**.

1. Om du vill övervaka skapandeprocessen väljer du **Meddelanden** i verktygsfältet. När du har skapat datafabriken väljer du den.
   
   Fönstret **Datafabrik** öppnas.

   ![Fönstret Datafabrik](media/data-factory-load-data/data-factory-home-page.png)

1. Om du vill öppna programmet i ett separat fönster markerar du panelen **Författare & Bildskärm.**

## <a name="load-data-into-azure-data-explorer"></a>Läsa in data i Azure Data Explorer

Du kan läsa in data från många typer av [datalager](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) i Azure Data Explorer. I den här artikeln beskrivs hur du läser in data från Amazon S3.

Du kan läsa in dina data på något av följande sätt:

* Välj ikonen **Författare** i det vänstra fönstret i azure data factory. Detta visas i avsnittet "Skapa en datafabrik" i [Skapa en datafabrik med hjälp av Azure Data Factory UI](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* I azure data fabrikskopieringsdata, som visas i [Använd verktyget Kopiera data för att kopiera data](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Kopiera data från Amazon S3 (källa)

1. Öppna verktyget Kopiera data i fönstret **Kom igång** genom att välja **Kopiera data**.

   ![Knappen Kopiera data](media/data-factory-load-data/copy-data-tool-tile.png)

1. Ange ett namn i rutan **Aktivitetsnamn** i fönstret **Egenskaper** och välj sedan **Nästa**.

    ![Egenskapsfönstret Kopiera data](media/data-factory-load-data/copy-from-source.png)

1. Välj **Skapa ny anslutning**i fönstret **Källdatalager** .

    ![Fönstret Kopiera data "Källdatalager"](media/data-factory-load-data/source-create-connection.png)

1. Välj **Amazon S3**och välj sedan **Fortsätt**.

    ![Fönstret Ny länkad tjänst](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Gör följande i fönstret **Ny länkad tjänst (Amazon S3):**

    ![Ange Amazon S3-länkad tjänst](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Ange namnet på den nya länkade tjänsten i rutan **Namn.**

    b. Välj värdet i listrutan **Anslut via integreringskörning.**

    c. Ange värdet i rutan **Åtkomstnyckel-ID.**
    
    > [!NOTE]
    > I Amazon S3, för att hitta din åtkomstnyckel, välj din Amazon användarnamn på navigeringsfältet, och välj sedan **Mina säkerhetsreferenser**.
    
    d. Ange ett värde i rutan **Hemlig åtkomstnyckel.**

    e. Om du vill testa den länkade tjänstanslutning som du skapade väljer du **Testa anslutning**.

    f. Välj **Slutför**.
    
      Fönstret **Källdatalager** visar din nya AmazonS31-anslutning. 

1. Välj **Nästa**.

   ![Källa datalager skapade anslutning](media/data-factory-load-data/source-data-store-created-connection.png)

1. Gör följande i fönstret **Välj indatafil eller mapp:**

    a. Bläddra till den fil eller mapp som du vill kopiera och markera den sedan.

    b. Markera önskat kopieringsbeteende. Kontrollera att kryssrutan **Binär kopia** är avmarkerad.

    c. Välj **Nästa**.

    ![Välj indatafil eller mapp](media/data-factory-load-data/source-choose-input-file.png)

1. Välj relevanta inställningar för filen i fönstret Inställningar för **filformat.** och välj sedan **Nästa**.

   ![Fönstret "Inställningar för filformat"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiera data till Azure Data Explorer (mål)

Den nya Azure Data Explorer-länkade tjänsten skapas för att kopiera data till måltabellen för Azure Data Explorer (sink) som anges i det här avsnittet.

> [!NOTE]
> Använd [kommandoaktiviteten i Azure Data Factory för att köra Azure Data Explorer-kontrollkommandon](data-factory-command-activity.md) `.set-or-replace`och använda något av [de intag från frågekommandona,](/azure/kusto/management/data-ingestion/ingest-from-query)till exempel .

#### <a name="create-the-azure-data-explorer-linked-service"></a>Skapa den länkade azure data explorer-tjänsten

Så här skapar du den länkade Azure Data Explorer-tjänsten:

1. Om du vill använda en befintlig datalageranslutning eller ange ett nytt datalager väljer du **Skapa ny anslutning**i fönstret **Måldatalager** .

    ![Fönstret Måldatalager](media/data-factory-load-data/destination-create-connection.png)

1. I fönstret **Ny länkad tjänst** väljer du **Azure Data Explorer**och väljer sedan **Fortsätt**.

    ![Fönstret Nytt länkat tjänst](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Gör följande i fönstret **Ny länkad tjänst (Azure Data Explorer):**

    ![Fönstret Ny länkad tjänst i Azure Data Explorer](media/data-factory-load-data/adx-new-linked-service.png)

   a. Ange ett namn för den länkade Azure Data Explorer-tjänsten i rutan **Namn.**

   b. Under **Kontovalsmetod**väljer du något av följande alternativ: 

    * Välj **Från Azure-prenumeration** och välj sedan din **Azure-prenumeration** och ditt **kluster**i listrutorna. 

        > [!NOTE]
        > Listkontrollen **Kluster** list list listar bara kluster som är associerade med din prenumeration.

    * Välj **Ange manuellt**och ange sedan **slutpunkten**.

   c. Ange klientnamnet i rutan **Klient.**

   d. Ange tjänstens huvud-ID i rutan **Tjänstens huvud-ID.**

   e. Välj **Huvudnyckel** för Tjänsten och ange sedan värdet för nyckeln i **nyckelrutan Tjänstens huvudnamn.**

   f. Välj databasnamnet i listrutan **Databas.** Du kan också markera kryssrutan **Redigera** och sedan ange databasnamnet.

   g. Om du vill testa den länkade tjänstanslutning som du skapade väljer du **Testa anslutning**. Om du kan ansluta till den länkade tjänsten visas en grön bock och ett meddelande **om anslutning.**

   h. Välj **Slutför** om du vill slutföra skapandet av den länkade tjänsten.

    > [!NOTE]
    > Tjänstens huvudnamn används av Azure Data Factory för att komma åt Azure Data Explorer-tjänsten. Om du vill skapa ett huvudnamn för tjänsten går du till skapa [ett huvudnamn för Azure Active Directory (Azure AD).](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Använd inte azure key vault-metoden.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurera dataanslutningen för Azure Data Explorer

När du har skapat den länkade tjänstanslutningen öppnas fönstret **Måldatalager** och anslutningen som du skapade är tillgänglig för användning. Så här konfigurerar du anslutningen:

1. Välj **Nästa**.

    ![Fönstret "Måldatalager" i Azure Data Explorer](media/data-factory-load-data/destination-data-store.png)

1. I fönstret **Tabellmappning** anger du måltabellens namn och väljer sedan **Nästa**.

    ![Fönstret Måldatauppsättning "Tabellmappning"](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. I **fönstret Kolumnmappning** sker följande mappningar:

    a. Den första mappningen utförs av Azure Data Factory enligt [azure data factory-schemamappningen](/azure/data-factory/copy-activity-schema-and-type-mapping). Gör följande:

    * Ange **kolumnmappningar** för måltabellen för Azure Data Factory. Standardmappningen visas från källa till måltabellen för Azure Data Factory.

    * Avbryt markeringen av de kolumner som du inte behöver definiera kolumnmappningen.

    b. Den andra mappningen sker när dessa tabelldata förtärs i Azure Data Explorer. Mappning utförs enligt [CSV-mappningsregler](/azure/kusto/management/mappings#csv-mapping). Även om källdata inte är i CSV-format konverterar Azure Data Factory data till ett tabellformat. Därför är CSV-mappning den enda relevanta mappningen i det här skedet. Gör följande:

    * (Valfritt) Lägg till relevant **inmatningsmappningsnamn** under **Azure Data Explorer (Kusto) sink-egenskaper**så att kolumnmappning kan användas.

    * Om **mappningsnamnet** för inmatning inte anges används den mappningsordning *för namn* som definieras i avsnittet **Kolumnmappningar.** Om *mappningen av by-name* misslyckas försöker Azure Data Explorer att inta data i en *positionsordning efter kolumn* (det vill ha mappning efter position som standard).

    * Välj **Nästa**.

    ![Fönstret Måldatauppsättning "Kolumnmappning"](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Gör följande i fönstret **Inställningar:**

    a. Ange relevanta inställningar under **Inställningar för feltolerans.**

    b. Under **Prestandainställningar**gäller inte **Aktivera mellanlagring** och **Avancerade inställningar** innehåller kostnadsöverväganden. Om du inte har några specifika krav lämnar du dessa inställningar som de är.

    c. Välj **Nästa**.

    ![Fönstret "Inställningar" för kopieringsdata](media/data-factory-load-data/copy-data-settings.png)

1. Granska inställningarna i **fönstret Sammanfattning** och välj sedan **Nästa**.

    ![Fönstret "Sammanfattning" i kopieringsdata](media/data-factory-load-data/copy-data-summary.png)

1. Gör följande i fönstret **Distributionen:**

    a. Om du vill växla till fliken **Övervakare** och visa pipelinens status (d.v.s. förlopp, fel och dataflöde) väljer du **Övervaka**.

    b. Om du vill redigera länkade tjänster, datauppsättningar och pipelines väljer du **Redigera pipeline**.

    c. Välj **Slutför** om du vill slutföra kopieringsdatauppgiften.

    ![Fönstret "Distributionen klar"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure Data Explorer-anslutningen](/azure/data-factory/connector-azure-data-explorer) i Azure Data Factory.
* Läs mer om hur du redigerar länkade tjänster, datauppsättningar och pipelines i [datafabrikens användargränssnitt](/azure/data-factory/quickstart-create-data-factory-portal).
* Lär dig mer om [Azure Data Explorer-frågor](/azure/data-explorer/web-query-data) för datafrågor.
