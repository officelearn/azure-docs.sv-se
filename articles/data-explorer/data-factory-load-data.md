---
title: Kopiera data från Azure Data Factory till Azure Data Explorer
description: I det här avsnittet får du lära dig att mata in (load) data i Datautforskaren i Azure med hjälp av verktyg för Azure Data Factory-kopia
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c3c5484d865c73a6f478ffc9ad4c3fc86c2c8170
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59619209"
---
# <a name="copy-data-from-azure-data-factory-to-azure-data-explorer"></a>Kopiera data från Azure Data Factory till Azure Data Explorer

Azure Data Explorer är en analystjänst för snabba, fullständigt hanterade data för realtidsanalys på stora mängder data som strömmas från många källor, exempelvis program, webbplatser och IoT-enheter. Upprepade gånger utforska data och identifiera mönster och avvikelser för att förbättra produkter, förbättra kundupplevelser, övervakning av enheter och öka åtgärder. Utforska nya frågor och få svar på några minuter. Azure Data Factory är en fullständigt hanterad molnbaserad dataintegreringstjänst. Du kan använda tjänsten för att fylla i Azure Data Explorer databasen med data från ditt befintliga system och spara tid när du skapar Analyslösningar.

Azure Data Factory erbjuder följande fördelar vid inläsning av data i Datautforskaren i Azure:

* **Enkelt att konfigurera**: En intuitiv guiden med inga skript som krävs.
* **Omfattande stöd för datalager**: Inbyggt stöd för ett stort utbud av lokala och molnbaserade datalager. En detaljerad lista finns i tabellen med [datalager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Säker och kompatibel**: Data överförs via HTTPS- eller ExpressRoute. Tjänsten för global närvaro säkerställer att dina data aldrig lämnar geografisk gräns.
* **Högpresterande**: Upp till 1 GB/s data läses in hastighet i Datautforskaren i Azure. Mer information finns i [kopiera aktivitet prestanda](/azure/data-factory/copy-activity-performance).

Den här artikeln visar hur du använder verktyget kopieringsdata i Data Factory för att läsa in data från Amazon S3 i Datautforskaren i Azure. Du kan följa liknande steg för att kopiera data från andra datalager som [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), och [filsystem](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och databas](create-cluster-database-portal.md)
* Amazon S3.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Välj den **skapa en resurs** knappen (+) i det övre vänstra hörnet i portalen > **Analytics** > **Data Factory**.

   ![Skapa en ny datafabrik](media/data-factory-load-data/create-adf.png)

1. I den **ny datafabrik** anger värden för följande fält och välj sedan **skapa**.

    ![Sida för ny datafabrik](media/data-factory-load-data/my-new-data-factory.png)

    **Inställning**  | **Fältbeskrivning**
    |---|---|
    | **Namn** | Ange ett globalt unikt namn för din datafabrik. Om du får felet *”datafabriksnamnet \"LoadADXDemo\" är inte tillgänglig”*, ange ett annat namn för data factory. Regler för namngivning av Data Factory-artefakter, se [Data Factory – namnregler](/azure/data-factory/naming-rules).|
    | **Prenumeration** | Välj din Azure-prenumeration där du vill skapa data factory. |
    | **Resursgrupp** | Välj **Skapa nytt** och ange namnet på en ny resursgrupp. Välj **Använd befintlig**, om du har en befintlig resursgrupp. |
    | **Version** | Välj **V2** |
    | **Plats** | Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av data factory kan finnas i andra platser eller regioner. |
    | | |

1. Välj meddelanden i verktygsfältet för att övervaka skapandeprocessen. När datafabriken har skapats går du till datafabriken som du skapade. Den **Data Factory** startsidan öppnas.

   ![Datafabrikens startsida](media/data-factory-load-data/data-factory-home-page.png)

1. Välj den **författare och Övervakare** att starta programmet i en separat flik.

## <a name="load-data-into-azure-data-explorer"></a>Läs in data till Azure Data Explorer

Data kan läsas in från många typer av [datalager](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) i Datautforskaren i Azure. Det här avsnittet beskriver att läsa in data från Amazon S3.

Det finns två sätt att läsa in data i Datautforskaren i Azure med Azure Data Factory:

* Azure Data Factory-användargränssnitt - [ **författare** fliken](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **kopieringsdata** verktyget](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) används i den här artikeln.

### <a name="copy-data-from-amazon-s3-source"></a>Kopiera data från Amazon S3 (källa)

1. I den **nu sätter vi igång** väljer den **kopieringsdata** att starta verktyget kopieringsdata.

   ![Panel för verktyget kopiera data](media/data-factory-load-data/copy-data-tool-tile.png)

1. I den **egenskaper** anger **aktivitetsnamn** och välj **nästa**.

    ![Kopiera från egenskaper för datakälla](media/data-factory-load-data/copy-from-source.png)

1. I den **källdatalagret** klickar du på **+ Skapa ny anslutning**.

    ![Källdata Skapa anslutning](media/data-factory-load-data/source-create-connection.png)

1. Välj **Amazon S3**, och välj sedan **Fortsätt**

    ![Ny länkad tjänst](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. I den **ny länkad tjänst (Amazon S3)** gör du följande steg:

    ![Ange Amazon S3 länkad tjänst](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Ange **namn** ny länkad tjänst.
    * Välj **Anslut via integration runtime** värdet i listrutan.
    * Ange den **Åtkomstnyckelns ID** värde.
    * Ange den **hemliga åtkomstnyckel** värde.
    * Välj **Testanslutning** att testa anslutningen för länkad tjänst som du skapade.
    * Välj **Slutför**.

1. I den **källdatalagret** sidan ser du din nya AmazonS31-anslutning. Välj **Nästa**.

   ![Källans datalager skapade anslutningen](media/data-factory-load-data/source-data-store-created-connection.png)

1. I den **Välj indatafil eller mapp** sidan:

    1. Bläddra till mappen/filen som du vill kopiera. Välj mappen/filen.
    1. Välj kopieringsbeteendet efter behov. Behåll **binär kopia** avmarkerat.
    1. Välj **Nästa**.

    ![Välj indatafil eller mapp](media/data-factory-load-data/source-choose-input-file.png)

1. I den **filformat inställningar** väljer du de relevanta inställningarna för fil- och klicka på **nästa**.

   ![Välj indatafil eller mapp](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopiera data till Azure Data Explorer (målet)

Azure Data Explorer ny länkad tjänst har skapats för att kopiera data i måltabellen för Azure Data Explorer (mottagare) som anges nedan.

1. I den **måldatalager** kan du använda en befintlig måldatalagret anslutning eller ange ett nytt datalager genom att klicka på **+ Skapa ny anslutning**.

    ![Sidan Måldatalager](media/data-factory-load-data/destination-create-connection.png)

1. I den **ny länkad tjänst** väljer **Azure Data Explorer**, och välj sedan **Fortsätt**

    ![Välj Azure Data Explorer – ny länkad tjänst](media/data-factory-load-data/adx-select-new-linked-service.png)

1. I den **ny länkad tjänst (Azure Data Explorer)** gör du följande steg:

    ![ADX ny länkad tjänst](media/data-factory-load-data/adx-new-linked-service.png)

   * Välj **namn** länkad tjänst för Azure Data Explorer.
   * I **Kontometod**: 
        * Välj den **från Azure-prenumeration** knappen och välj din **Azure-prenumeration** konto. Välj din **kluster**. Observera listrutan kommer bara en lista över kluster som tillhör användaren.
        * Du kan också välja **ange manuellt** alternativknappen och ange din **Endpoint**.
    * Ange den **klient**.
    * Ange **huvudkonto-ID för tjänstens**.
    * Välj **nyckel för tjänstens huvudnamn** och ange **nyckel för tjänstens huvudnamn**.
    * Välj din **databasen** från den nedrullningsbara menyn. Du kan också välja **redigera** kryssrutan och ange databasnamnet.
    * Välj **Testanslutning** att testa anslutningen för länkad tjänst som du skapade. Om du kan ansluta till din konfiguration, en grön bockmarkering **anslutningen lyckades** visas.
    * Välj **Slutför** för att skapa den länkade tjänsten.

    > [!NOTE]
    > Tjänstens huvudnamn används av Azure Data Factory för att få åtkomst till Datautforskaren i Azure-tjänsten. För tjänstens huvudnamn [och skapa en Azure Active Directory (Azure AD) tjänstens huvudnamn](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad). Använd inte den **Azure Key Vault** metod.

1. Den **måldatalager** öppnas. Datautforskaren i Azure-dataanslutning som du skapade är tillgängliga för användning. Välj **nästa** att konfigurera anslutningen.

    ![ADX måldatalager](media/data-factory-load-data/destination-data-store.png)

1. I **Välj utdatafil eller mapp**, ange mappnamn utdata, inställningar och välj **nästa**.

    ![Ange Utdatamappen](media/data-factory-load-data/specify-path.png)

1. I **tabellmappning**, ange tabellnamnet mål och välj **nästa**.

    ![Tabellmappning för mål-datauppsättning](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. I den **kolumnmappning** sidan:
    * Första mappningen utförs av ADF enligt [ADF schemamappning](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Ange den **kolumnmappningarna** för måltabellen för Azure Data Factory. Standardmappningen visas från källa till ADF måltabellen.
        * Avmarkera de kolumner som du inte behöver definiera din kolumnmappning.
    * Andra mappningen inträffar när den här tabelldata matas in i Datautforskaren i Azure. Mappningen utförs enligt [regler för mappning av CSV](/azure/kusto/management/mappings#csv-mapping). Observera att även om datakällan inte CSV-format, ADF har konverterat datan till tabellformat, därför CSV mappningen är bara relevant mappningen i det här skedet.
        * Under **Azure Data Explorer (Kusto) mottagare egenskaper** lägga till den relevanta **inmatning mappningsnamn** (valfritt) så att kolumnmappning kan användas.
        * Om **inmatning mappningsnamn** har inte angetts ”by-name” mappning ordning som definierats i **kolumnmappningarna** avsnittet sker. Om ”by-name” mappningen misslyckas, försöker Azure Data Explorer att mata in data i en ”efter kolumn position” ordning (maps genom-placering som standard).
    * Välj **nästa**

    ![Kolumnmappningen för mål-datauppsättning](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. På sidan **Inställningar**:
    * Ange den relevanta **fault toleransinställningar**.
    * **Prestandainställningar**: Aktivera mellanlagring är inte tillämplig. **Avancerade inställningar** inkluderar kostnaden överväganden. Lämna som är om inga särskilda behov.
    * Välj **Nästa**.

    ![Kopiera inställningar](media/data-factory-load-data/copy-data-settings.png)

1. I **sammanfattning**, granskar du inställningarna och välj **nästa**.

    ![Kopiera data sammanfattning](media/data-factory-load-data/copy-data-summary.png)

1. I den **Distributionssidan**:
    * Välj **övervakaren** att växla till den **övervakaren** fliken och se status för pipelinen (flow förloppet, fel och data).
    * Välj **redigera Pipeline** Redigera länkade tjänster, datauppsättningar och pipeliner.
    * Välj **Slutför** till fullständig kopia data aktivitet

    ![Distributionssida](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du redigerar länkade tjänster, datauppsättningar och pipeliner i den [Data Factory-användargränssnitt](/azure/data-factory/quickstart-create-data-factory-portal)

* Lär dig mer om [Datautforskaren i Azure-frågor](/azure/data-explorer/web-query-data) för att fråga data.
