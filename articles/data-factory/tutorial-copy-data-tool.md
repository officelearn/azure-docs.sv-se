---
title: Kopiera data från Azure Blob-lagring till SQL med verktyget Kopiera data
description: Skapa en Azure-datafabrik och använd sedan verktyget Kopiera data för att kopiera data från Azure Blob-lagring till en SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: be9395b908461b54b607fff32747ca0d9f20f45c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418668"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopiera data från Azure Blob-lagring till en SQL-databas med hjälp av verktyget Kopiera data

> [!div class="op_single_selector" title1="Välj den version av datafabrikstjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från Azure Blob-lagring till en SQL-databas.

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:
> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**: Använd Blob-lagring som _källdatalager._ Om du inte har ett Azure Storage-konto läser du instruktionerna i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).
* **Azure SQL Database**: Använd en SQL-databas som sink-datalager. _sink_ Om du inte har en SQL-databas läser du instruktionerna i [Skapa en SQL-databas](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered blob-lagringen och SQL-databasen för självstudien genom att utföra dessa steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Starta **Anteckningar**. Kopiera följande text och spara den i en fil med namnet **inputEmp.txt** på din disk:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Skapa en container med namnet **adfv2tutorial** och ladda upp filen inputEmp.txt till containern. Du kan använda Azure-portalen eller olika verktyg som [Azure Storage Explorer](https://storageexplorer.com/) för att utföra dessa uppgifter.

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

1. Använd följande SQL-skript för att skapa en tabell med namnet **dbo.emp** i SQL-databasen:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Ge Azure-tjänster åtkomst till SQL Server. Kontrollera att inställningen **Tillåt Azure-tjänster och resurser för åtkomst till den här servern** är aktiverad för servern som kör SQL Database. Med den här inställningen kan Data Factory skriva data till din databasinstans. Om du vill verifiera och aktivera den här inställningen går du till Azure SQL-server > Security > Firewalls och virtuella nätverk > anger **alternativet Tillåt Azure-tjänster och resurser för att komma åt det här serveralternativet** till **ON**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resurs** > **Analytics** > **Data Factory:**

    ![Skapa ny datafabrik](./media/doc-common-process/new-azure-data-factory-menu.png)
1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

    Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:

    ![Felmeddelande för ny datafabrik](./media/doc-common-process/name-not-available-error.png)

    Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig**och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny**och ange namnet på en resursgrupp.
    
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

1. För **version** väljer du **V2**.
1. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL Database) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas på andra platser och i andra regioner.
1. Välj **Skapa**.

1. När den har skapats visas startsidan för **Data Factory**.

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)
1. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Sidan **Nu sätter vi igång** visas. Välj panelen **Kopiera data** för att starta verktyget Kopiera data.

    ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)
1. På sidan **Egenskaper** under **Aktivitetsnamn**, anger du **CopyFromBlobToSqlPipeline**. Välj sedan **Nästa**. Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn.
    ![Skapa en pipeline](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning

    b. Välj **Azure Blob Storage** i galleriet och välj sedan **Fortsätt**.

    c. På sidan **Ny länkad tjänst** väljer du din Azure-prenumeration och väljer ditt lagringskonto i listan **För lagringskontonamn.** Testa anslutningen och välj sedan **Skapa**.

    d. Välj den nyligen skapade länkade tjänsten som källa och klicka sedan på **Nästa**.

    ![Välj källa för den länkade tjänsten](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Gör följande på sidan för att **välja indatafil eller -mapp**:

    a. Klicka på **Bläddra** för att gå till mappen **adfv2tutorial/input**, välj filen **inputEmp.txt** och klicka på **Välj**.

    b. Klicka på **Nästa** för att gå vidare till nästa steg.

1. På sidan **Inställningar för filformat** aktiverar du kryssrutan för *Första raden som rubrik*. Observera att verktyget automatiskt identifierar kolumn- och radavgränsare. Välj **Nästa**. Du kan också förhandsgranska data och visa schemat för indata på den här sidan.

    ![Filformatinställningar](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Gör följande på sidan **Måldatalager**:

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning

    b. Välj **Azure SQL Database** i galleriet och välj sedan **Fortsätt**.

    c. På sidan **Ny länkad tjänst** väljer du servernamn och DB-namn i listrutan och anger användarnamn och lösenord och väljer sedan **Skapa**.

    ![Konfigurera Azure SQL DB](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Välj den nyligen skapade länkade tjänsten som mottagare och klicka sedan på **Nästa**.

1. På sidan **Tabellmappning** väljer du tabellen **[dbo].[emp]** och sedan **Nästa**.

1. På sidan **Kolumnmappning** märker du att den andra och den tredje kolumnen i indatafilen mappas till kolumnerna **Förnamn** och **Efternamn** i **emp-tabellen.** Justera mappningen för att kontrollera att det inte finns något fel och välj sedan **Nästa**.

    ![Sida för kolumnmappning](./media/tutorial-copy-data-tool/column-mapping.png)

1. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.
1. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.
1. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).
 
    ![Övervaka pipeline](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. På sidan Pipeline-körningar väljer du **Uppdatera** för att uppdatera listan. Klicka på länken under **PIPELINE NAMN** om du vill visa information om aktivitetskörning eller köra pipelinen igen. 
    ![Pipeline-körning](./media/tutorial-copy-data-tool/pipeline-run.png)

1. På sidan Aktivitetskörning väljer du länken **Information** (ikonen Glasögon) under kolumnen **AKTIVITETSNAMN** för mer information om kopiering. Om du vill gå tillbaka till vyn Pipeline runs väljer du länken **ALLA pipelinekörningar** på sökvägen för sökvägen. Välj **Uppdatera** för att uppdatera vyn.

    ![Övervaka aktivitetskörningar](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Kontrollera att data infogas i **dbo.emp-tabellen** i SQL-databasen.


1. Klicka på fliken **Författare** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).

    ![Fliken Välj författare](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från Blob-lagring till en SQL-databas. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till nästa självstudie om du vill lära dig att kopiera data från en lokal plats till molnet:

>[!div class="nextstepaction"]
>[Kopiera data från en lokal plats till molnet](tutorial-hybrid-copy-data-tool.md)
