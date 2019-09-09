---
title: Kopiera data från Azure Data Factory till Azure Datautforskaren
description: I det här avsnittet får du lära dig att mata in data i Azure Datautforskaren med hjälp av Azure Data Factory kopierings verktyget
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b3bd9b800da4f096639d02c78b718216441621a9
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803982"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Kopiera data till Azure Datautforskaren med Azure Data Factory 

Azure Datautforskaren är en snabb, fullständigt hanterad tjänst för data analys för real tids analys av stora mängder data som strömmas från många källor, till exempel program, webbplatser och IoT-enheter. Utforska data upprepade gånger och identifiera mönster och avvikelser för att förbättra produkter, förbättra kund upplevelser, övervaka enheter och förstärknings åtgärder. Utforska nya frågor och få svar på några minuter. Azure Data Factory är en helt hanterad molnbaserad data integrerings tjänst. Du kan använda tjänsten för att fylla i Azure Datautforskaren-databasen med data från ditt befintliga system och spara tid när du skapar analys lösningarna.

Azure Data Factory erbjuder följande fördelar för att läsa in data i Azure Datautforskaren:

* **Enkelt att konfigurera**: En intuitiv fem stegs guide utan skript krävs.
* **Stöd för omfattande data lager**: Inbyggt stöd för en omfattande uppsättning lokala och molnbaserade data lager. En detaljerad lista finns i tabellen över [data lager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Säkert och kompatibelt**: Data överförs via HTTPS eller ExpressRoute. Med den globala tjänstens närvaro ser du till att dina data aldrig lämnar den geografiska gräns.
* **Hög prestanda**: Upp till 1 GB/s data inläsnings hastighet i Azure Datautforskaren. Mer information finns i [Kopiera aktivitets prestanda](/azure/data-factory/copy-activity-performance).

Den här artikeln visar hur du använder verktyget Data Factory Kopiera data för att läsa in data från Amazon S3 till Azure Datautforskaren. Du kan följa liknande steg för att kopiera data från andra data lager, till exempel [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle)och [fil system](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Datautforskaren-kluster och-databas](create-cluster-database-portal.md)
* Data källa.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Välj knappen **skapa en resurs** (+) i det övre vänstra hörnet i portalen > **Analytics** > **Data Factory**.

   ![Skapa en ny datafabrik](media/data-factory-load-data/create-adf.png)

1. På sidan **ny data fabrik** anger du värden för följande fält och väljer sedan **skapa**.

    ![Sida för ny datafabrik](media/data-factory-load-data/my-new-data-factory.png)

    **Inställning**  | **Fältbeskrivning**
    |---|---|
    | **Namn** | Ange ett globalt unikt namn för din data fabrik. Om du får felet *"Data Factory name \"LoadADXDemo\" är inte tillgängligt"* anger du ett annat namn på data fabriken. Information om namngivnings regler för Data Factory artefakter finns i [Data Factory namngivnings regler](/azure/data-factory/naming-rules).|
    | **Prenumeration** | Välj din Azure-prenumeration där du vill skapa data fabriken. |
    | **Resursgrupp** | Välj **Skapa nytt** och ange namnet på en ny resurs grupp. Välj **Använd befintlig**om du har en befintlig resurs grupp. |
    | **Version** | Välj **v2** |
    | **Location** | Välj plats för data fabriken. Endast platser som stöds visas i listrutan. De data lager som används av Data Factory kan finnas på andra platser eller regioner. |
    | | |

1. Välj meddelanden i verktygsfältet för att övervaka skapande processen. När du har skapat filen går du till den data fabrik du skapade. Start sidan för **Data Factory** öppnas.

   ![Datafabrikens startsida](media/data-factory-load-data/data-factory-home-page.png)

1. Välj panelen **författare & Monitor** för att starta programmet på en separat flik.

## <a name="load-data-into-azure-data-explorer"></a>Läs in data i Azure Datautforskaren

Data kan läsas in från många typer av [data lager](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) i Azure datautforskaren. I den här artikeln beskrivs inläsning av data från Amazon S3.

Det finns två sätt att läsa in data i Azure Datautforskaren med Azure Data Factory:

* Azure Data Factory användar gränssnitt – [fliken **författare**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **Kopiera data** verktyg](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) som används i den här artikeln.

### <a name="copy-data-from-amazon-s3-source"></a>Kopiera data från Amazon S3 (källa)

1. På sidan **nu sätter vi igång** väljer du panelen **Kopiera data** för att starta verktyget kopiera data.

   ![Verktygs panel för att kopiera data](media/data-factory-load-data/copy-data-tool-tile.png)

1. På sidan **Egenskaper** anger du **uppgifts namn** och väljer **Nästa**.

    ![Kopiera från käll egenskaper](media/data-factory-load-data/copy-from-source.png)

1. På sidan **käll data lager** klickar du på **+ Skapa ny anslutning**.

    ![Käll data skapa anslutning](media/data-factory-load-data/source-create-connection.png)

1. Välj **Amazon S3**och välj sedan **Fortsätt**

    ![Ny länkad tjänst](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. På sidan **ny länkad tjänst (Amazon S3)** gör du följande:

    ![Ange länkad Amazon S3-tjänst](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Ange **namnet** på den nya länkade tjänsten.
    * Välj **Anslut via integration runtime** -värdet från List rutan.
    * Ange **åtkomst nyckelns ID-** värde.
    * Ange nyckel värde för **hemlig åtkomst** .
    * Välj **test anslutning** för att testa den länkade tjänst anslutningen som du skapade.
    * Välj **Slutför**.

1. På sidan **käll data lager** visas den nya AmazonS31-anslutningen. Välj **Nästa**.

   ![Käll data lager skapad anslutning](media/data-factory-load-data/source-data-store-created-connection.png)

1. På sidan **Välj indatafil eller mapp** :

    1. Bläddra till mappen/filen som du vill kopiera. Välj mapp/fil.
    1. Välj kopierings beteendet efter behov. Behåll **binär kopia** avmarkerad.
    1. Välj **Nästa**.

    ![Välj indatafil eller mapp](media/data-factory-load-data/source-choose-input-file.png)

1. På sidan **fil format inställningar** väljer du relevanta inställningar för filen och klickar på **Nästa**.

   ![Välj indatafil eller mapp](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiera data till Azure Datautforskaren (mål)

Azure Datautforskaren ny länkad tjänst skapas för att kopiera data till Azure Datautforskaren mål tabellen (mottagare) som anges nedan.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Skapa den länkade tjänsten Azure Datautforskaren

1. På sidan **mål data lager** kan du använda en befintlig data lager anslutning eller ange ett nytt data lager genom att klicka på **+ Skapa ny anslutning**.

    ![Sidan Måldatalager](media/data-factory-load-data/destination-create-connection.png)

1. På sidan **ny länkad tjänst** väljer du **Azure datautforskaren**och väljer sedan **Fortsätt**

    ![Välj Azure-Datautforskaren-ny länkad tjänst](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Utför följande steg på sidan **ny länkad tjänst (Azure datautforskaren)** :

    ![ADX ny länkad tjänst](media/data-factory-load-data/adx-new-linked-service.png)

   * Välj **namn** för den länkade tjänsten Azure-datautforskaren.
   * I **Val av konto**: 
        * Välj alternativ knappen **från Azure-prenumerationen** och välj ditt **Azure-prenumerations** konto. Välj sedan **klustret**. Obs! List rutan visar bara kluster som tillhör användaren.
        * Alternativt kan du välja alternativ knappen **ange manuellt** och ange din **slut punkt**.
    * Ange **klient organisation**.
    * Ange **tjänstens huvud namns-ID**.
    * Välj **nyckel för tjänstens huvud namn** och ange **nyckel för tjänstens huvud namn**.
    * Välj din **databas** i list menyn. Du kan också välja **Redigera** kryss rutan och ange ditt databas namn.
    * Välj **test anslutning** för att testa den länkade tjänst anslutningen som du skapade. Om du kan ansluta till installationen visas en grön bock- **anslutning** som är klar.
    * Klicka på **Slutför** om du vill slutföra skapandet av länkade tjänster.

    > [!NOTE]
    > Tjänstens huvud namn används av Azure Data Factory för att få åtkomst till Azure Datautforskaren-tjänsten. För tjänstens huvud namn [skapar du ett Azure Active Directory (Azure AD)-tjänstens huvud namn](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Använd inte metoden **Azure Key Vault** .

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurera Azure Datautforskaren data anslutning

1. **Mål data lagret** öppnas. Den Azure Datautforskaren data anslutning som du har skapat är tillgänglig för användning. Välj **Nästa** för att konfigurera anslutningen.

    ![ADX mål data lager](media/data-factory-load-data/destination-data-store.png)

1. Ange mål tabellens namn i **tabell mappning**och välj **Nästa**.

    ![Tabell mappning för mål data uppsättning](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. På sidan **kolumn mappning** :
    * Den första mappningen utförs av ADF enligt [ADF schema mappning](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Ange **kolumn mappningar** för Azure Data Factory mål tabellen. Standard mappningen visas från källan till ADF mål tabellen.
        * Avmarkera de kolumner som du inte behöver definiera kolumn mappningen för.
    * Den andra mappningen sker när dessa tabell data matas in i Azure Datautforskaren. Mappning utförs enligt CSV- [mappnings regler](/azure/kusto/management/mappings#csv-mapping). Observera att även om källdata inte var i CSV-format, har ADF konverterat data till ett tabell format, vilket innebär att CSV-mappning är den enda relevanta mappningen i det här skedet.
        * Under **egenskaperna för mottagare av Azure datautforskaren (Kusto)** lägger du till relevant inmatnings **mappnings namn** (valfritt) så att kolumn mappning kan användas.
        * Om mappnings namnet för inmatnings **mappningen** inte anges sker mappnings ordningen "efter namn" som definierats i **kolumn mappnings** avsnittet. Om mappningen av "efter namn" Miss lyckas försöker Azure Datautforskaren att mata in data i en "efter kolumn position"-order (Maps-position som standard).
    * Välj **Nästa**

    ![Kolumn mappning för mål data uppsättning](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. På sidan **Inställningar**:
    * Ange de relevanta **fel tolerans inställningarna**.
    * **Prestanda inställningar**: Aktivering av mellanlagring är inte tillämpligt. **Avancerade inställningar** inkluderar kostnads överväganden. Låt stå kvar om inga speciella behov.
    * Välj **Nästa**.

    ![Kopiera data inställningar](media/data-factory-load-data/copy-data-settings.png)

1. Granska inställningarna i **Sammanfattning**och välj **Nästa**.

    ![Kopiera data Sammanfattning](media/data-factory-load-data/copy-data-summary.png)

1. På **sidan distribution**:
    * Välj **övervaka** för att växla till fliken **övervaka** och se status för pipelinen (förlopp, fel och data flöde).
    * Välj **Redigera pipeline** om du vill redigera länkade tjänster, data uppsättningar och pipeliner.
    * Slutför uppgiften kopiera data genom att klicka på **Slutför**

    ![Distributionssida](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure datautforskaren-anslutningen](/azure/data-factory/connector-azure-data-explorer) i Azure Data Factory.

* Läs mer om hur du redigerar länkade tjänster, data uppsättningar och pipeliner i [Data Factory användar gränssnitt](/azure/data-factory/quickstart-create-data-factory-portal).

* Lär dig mer om [Azure datautforskaren frågor](/azure/data-explorer/web-query-data) för data frågor.
