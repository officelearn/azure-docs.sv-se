---
title: Kopiera data från Azure Data Factory till Azure Datautforskaren
description: I den här artikeln får du lära dig hur du matar in data i Azure Datautforskaren med hjälp av verktyget Azure Data Factory kopiering.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300589"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Kopiera data till Azure Datautforskaren med Azure Data Factory 

Azure Datautforskaren är en snabb, fullständigt hanterad tjänst för data analys. Det erbjuder real tids analys av stora mängder data som strömmas från många källor, till exempel program, webbplatser och IoT-enheter. Med Azure Datautforskaren kan du iterativt utforska data och identifiera mönster och avvikelser för att förbättra produkter, förbättra kund upplevelser, övervaka enheter och öka driften. Det hjälper dig att utforska nya frågor och få svar på några minuter. 

Azure Data Factory är en fullständigt hanterad, molnbaserad tjänst för data integrering. Du kan använda den för att fylla i Azure Datautforskaren-databasen med data från ditt befintliga system. Det kan hjälpa dig att spara tid när du skapar analys lösningar.

När du läser in data i Azure Datautforskaren ger Data Factory följande fördelar:

* **Enkel installation**: Få en intuitiv, fem-stegs guide utan skript krävs.
* **Stöd för omfattande data lager**: Få inbyggt stöd för en omfattande uppsättning lokala och molnbaserade data lager. En detaljerad lista finns i tabellen över [data lager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Säkert och kompatibelt**: Data överförs via HTTPS eller Azure ExpressRoute. Med den globala tjänstens närvaro ser du till att dina data aldrig lämnar den geografiska gräns.
* **Hög prestanda**: Data inläsnings hastigheten är upp till 1 GB per sekund (GBps) i Azure Datautforskaren. Mer information finns i [Kopiera aktivitets prestanda](/azure/data-factory/copy-activity-performance).

I den här artikeln använder du verktyget Data Factory Kopiera data för att läsa in data från Amazon Simple Storage Service (S3) i Azure Datautforskaren. Du kan följa samma process för att kopiera data från andra data lager, till exempel:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Filsystem](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och en databas](create-cluster-database-portal.md).
* En data källa.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure Portal](https://ms.portal.azure.com).

1. I det vänstra fönstret väljer du **skapa en resurs** > **analys** > **Data Factory**.

   ![Skapa en data fabrik i Azure Portal](media/data-factory-load-data/create-adf.png)

1. I fönstret **ny data fabrik** anger du värden för fälten i följande tabell:

   ![Fönstret ny data fabrik](media/data-factory-load-data/my-new-data-factory.png)  

   | Inställning  | Värde att ange  |
   |---|---|
   | **Namn** | I rutan anger du ett globalt unikt namn för din data fabrik. Om du får ett fel meddelande om att *data \"fabriks namnet LoadADXDemo\" inte är tillgängligt*anger du ett annat namn för data fabriken. Regler om namngivning av Data Factory artefakter finns i [Data Factory namngivnings regler](/azure/data-factory/naming-rules).|
   | **Prenumeration** | Välj den Azure-prenumeration i list rutan som du vill skapa data fabriken i. |
   | **Resursgrupp** | Välj **Skapa ny**och ange sedan namnet på en ny resurs grupp. Om du redan har en resurs grupp väljer du **Använd befintlig**. |
   | **Version** | Välj **v2**i list rutan. |  
   | **Location** | Välj plats för data fabriken i list rutan. Endast platser som stöds visas i listan. De data lager som används av data fabriken kan finnas på andra platser eller regioner. |

1. Välj **Skapa**.

1. Välj **meddelanden** i verktygsfältet för att övervaka skapande processen. När du har skapat data fabriken väljer du den.
   
   Fönstret **Data Factory** öppnas.

   ![Fönstret Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Öppna programmet i ett separat fönster genom att välja panelen **författare & Monitor** .

## <a name="load-data-into-azure-data-explorer"></a>Läs in data i Azure Datautforskaren

Du kan läsa in data från många typer av [data lager](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) till Azure datautforskaren. Den här artikeln beskriver hur du läser in data från Amazon S3.

Du kan läsa in dina data på något av följande sätt:

* I det Azure Data Factory användar gränssnittet i det vänstra fönstret väljer du ikonen **författare** , som du ser i avsnittet "skapa en data fabrik" i [skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* I Azure Data Factory Kopiera data-verktyget, som du ser i [använd kopiera data-verktyget för att kopiera data](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Kopiera data från Amazon S3 (källa)

1. I fönstret **Låt oss komma igång** öppnar du kopiera data-verktyget genom att välja **Kopiera data**.

   ![Knappen Kopiera data-verktyget](media/data-factory-load-data/copy-data-tool-tile.png)

1. I fönstret **Egenskaper** , i rutan **uppgifts namn** , anger du ett namn och väljer sedan **Nästa**.

    ![Fönstret Kopiera data egenskaper](media/data-factory-load-data/copy-from-source.png)

1. I fönstret **käll data lager** väljer du **Skapa ny anslutning**.

    ![Rutan Kopiera data käll data lager](media/data-factory-load-data/source-create-connection.png)

1. Välj **Amazon S3**och välj sedan **Fortsätt**.

    ![Fönstret ny länkad tjänst](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. I fönstret **ny länkad tjänst (Amazon S3)** gör du följande:

    ![Ange länkad Amazon S3-tjänst](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. I rutan **namn** anger du namnet på den nya länkade tjänsten.

    b. Välj värdet i list rutan **Anslut via integration runtime** .

    c. I rutan **åtkomst nyckel-ID** anger du värdet.
    
    > [!NOTE]
    > I Amazon S3 kan du leta upp din åtkomst nyckel genom att välja ditt Amazon-användarnamn i navigerings fältet och sedan välja **Mina säkerhets uppgifter**.
    
    d. I rutan **hemlig åtkomst nyckel** anger du ett värde.

    e. Om du vill testa den länkade tjänst anslutningen som du skapade väljer du **Testa anslutning**.

    f. Välj **Slutför**.
    
      I fönstret **källdata** visas den nya AmazonS31-anslutningen. 

1. Välj **Nästa**.

   ![Käll data lager skapad anslutning](media/data-factory-load-data/source-data-store-created-connection.png)

1. I fönstret **Välj indatafil eller mapp** gör du följande:

    a. Bläddra till den fil eller mapp som du vill kopiera och markera den.

    b. Välj det kopierings beteende som du vill använda. Kontrol lera att kryss rutan **binär kopia** är avmarkerad.

    c. Välj **Nästa**.

    ![Välj indatafil eller mapp](media/data-factory-load-data/source-choose-input-file.png)

1. I fönstret **fil formats inställningar** väljer du relevanta inställningar för filen. och välj sedan **Nästa**.

   ![Fönstret fil formats inställningar](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiera data till Azure Datautforskaren (mål)

Den nya Azure Datautforskaren länkade tjänsten skapas för att kopiera data till Azure Datautforskaren mål tabellen (handfat) som anges i det här avsnittet.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Skapa den länkade tjänsten Azure Datautforskaren

Så här skapar du en länkad Azure Datautforskaren-tjänst:

1. Om du vill använda en befintlig data lager anslutning eller ange ett nytt data lager går du till fönstret **mål data lager** och väljer **Skapa ny anslutning**.

    ![Fönstret mål data lager](media/data-factory-load-data/destination-create-connection.png)

1. I fönstret **ny länkad tjänst** väljer du **Azure datautforskaren**och väljer sedan **Fortsätt**.

    ![Fönstret ny länkad tjänst](media/data-factory-load-data/adx-select-new-linked-service.png)

1. I fönstret **ny länkad tjänst (Azure datautforskaren)** gör du följande:

    ![Fönstret Azure Datautforskaren ny länkad tjänst](media/data-factory-load-data/adx-new-linked-service.png)

   a. I rutan **namn** anger du ett namn för den länkade Azure datautforskaren-tjänsten.

   b. Gör något av följande under **Val av konto**: 

    * Välj **från Azure-prenumeration** och välj sedan din **Azure-prenumeration** och ditt **kluster**i list rutorna. 

        > [!NOTE]
        > List rutan **kluster** visar bara kluster som är associerade med din prenumeration.

    * Välj **ange manuellt**och ange sedan **slut punkten**.

   c. I rutan **innehavare** anger du klient namnet.

   d. I rutan **tjänstens huvud namn-ID** anger du tjänstens huvud namn-ID.

   e. Välj **tjänstens huvud nyckel** och i rutan **tjänstens huvud nyckel** anger du värdet för nyckeln.

   f. I list rutan **databas** väljer du ditt databas namn. Du kan också markera kryss rutan **Redigera** och ange namnet på databasen.

   g. Om du vill testa den länkade tjänst anslutningen som du skapade väljer du **Testa anslutning**. Om du kan ansluta till den länkade tjänsten visas en grön bock markering i fönstret och ett meddelande om att **anslutningen är klar** .

   h. Klicka på **Slutför** om du vill slutföra skapandet av den länkade tjänsten.

    > [!NOTE]
    > Tjänstens huvud namn används av Azure Data Factory för att få åtkomst till Azure Datautforskaren-tjänsten. Om du vill skapa ett huvud namn för tjänsten går du till [skapa en Azure Active Directory (Azure AD) tjänstens huvud namn](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Använd inte metoden Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurera Azure Datautforskaren data anslutning

När du har skapat den länkade tjänst anslutningen öppnas fönstret **mål data lager** och den anslutning som du har skapat är tillgänglig för användning. Gör så här för att konfigurera anslutningen:

1. Välj **Nästa**.

    ![Fönstret mål data lager för Azure Datautforskaren](media/data-factory-load-data/destination-data-store.png)

1. I fönstret **tabell mappning** anger du namnet på mål tabellen och väljer sedan **Nästa**.

    ![Rutan tabell mappning för mål data uppsättning](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. I fönstret **kolumn mappning** sker följande mappningar:

    a. Den första mappningen utförs av Azure Data Factory enligt [schema mappningen för Azure Data Factory](/azure/data-factory/copy-activity-schema-and-type-mapping). Gör följande:

    * Ange **kolumn mappningar** för Azure Data Factory mål tabellen. Standard mappningen visas från källan till Azure Data Factory mål tabellen.

    * Avbryt valet av kolumner som du inte behöver definiera kolumn mappningen för.

    b. Den andra mappningen sker när dessa tabell data matas in i Azure Datautforskaren. Mappning utförs enligt CSV- [mappnings regler](/azure/kusto/management/mappings#csv-mapping). Även om käll informationen inte är i CSV-format, Azure Data Factory konvertera data till tabell format. Därför är CSV-mappning den enda relevanta mappningen i det här skedet. Gör följande:

    * Valfritt Under **Egenskaper för mottagare av Azure datautforskaren (Kusto)** lägger du till relevant inmatnings **mappnings namn** så att du kan använda kolumn mappning.

    * Om du inte anger namnet på inmatnings **mappningen** används mappnings ordningen *per namn* som definieras i avsnittet **kolumn mappningar** . Om mappningen Miss lyckas försöker Azure datautforskaren mata in data i en *kolumn positions* ordning (dvs. den mappas efter position som standard).

    * Välj **Nästa**.

    ![Kolumn mappnings fönstret för mål data uppsättningen](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. I fönstret **Inställningar** gör du följande:

    a. Under **fel tolerans inställningar**anger du relevanta inställningar.

    b. **Aktivera mellanlagring** gäller inte under **prestanda inställningar**och **Avancerade inställningar** innehåller kostnads överväganden. Lämna inställningarna som de är om du inte har några särskilda krav.

    c. Välj **Nästa**.

    ![Fönstret Kopiera data inställningar](media/data-factory-load-data/copy-data-settings.png)

1. I fönstret **Sammanfattning** granskar du inställningarna och väljer sedan **Nästa**.

    ![Fönstret Kopiera data Sammanfattning](media/data-factory-load-data/copy-data-summary.png)

1. I fönstret **distribution slutfört** gör du följande:

    a. Om du vill växla till fliken **övervakare** och Visa status för pipelinen (det vill säga, förlopp, fel och data flöde) väljer du **övervaka**.

    b. Om du vill redigera länkade tjänster, data uppsättningar och pipeliner väljer du **Redigera pipeline**.

    c. Slutför uppgiften kopiera data genom att klicka på **Slutför** .

    ![Fönstret "distributionen har slutförts"](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure datautforskaren-anslutningen](/azure/data-factory/connector-azure-data-explorer) i Azure Data Factory.
* Läs mer om hur du redigerar länkade tjänster, data uppsättningar och pipeliner i [Data Factory användar gränssnitt](/azure/data-factory/quickstart-create-data-factory-portal).
* Lär dig mer om [Azure datautforskaren frågor](/azure/data-explorer/web-query-data) för data frågor.
