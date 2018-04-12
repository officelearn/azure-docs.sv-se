---
title: "Läs in data till Azure SQL Data Warehouse – Data Factory | Microsoft Docs"
description: "Den här kursen läser in data i Azure SQL Data Warehouse med hjälp av Azure Data Factory och använder en SQL Server-databas som datakälla."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Läs in data till SQL Data Warehouse med Data Factory

Du kan använda Azure Data Factory för att läsa in data till Azure SQL Data Warehouse från någon av de [stöds källa datalager](../data-factory/copy-activity-overview.md). T.ex, du kan läsa in data från en Azure SQL-databas eller en Oracle-databas till en SQL data warehouse med hjälp av Data Factory. Kursen i den här artikeln visar hur du läser in data från en lokal SQL Server-databas till en SQL data warehouse.

**Tid uppskattning**: den här självstudiekursen tar cirka 10 – 15 minuter för att slutföra när alla krav är uppfyllda.

## <a name="prerequisites"></a>Krav

- Du behöver en **SQL Server-databas** med tabeller som innehåller data som ska kopieras över till SQL data warehouse.  

- Du behöver ett online **SQL Data Warehouse**. Om du inte redan har ett data warehouse, lär du dig hur du [skapar ett Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md).

- Du behöver ett **Azure Storage-konto**. Om du inte redan har ett lagringskonto, lär du dig hur du [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md). Leta upp storage-konto och datalagret i samma Azure-region för bästa prestanda.

## <a name="configure-a-data-factory"></a>Konfigurera en datafabrik
1. Logga in på [Azure-portalen][].
2. Leta upp ditt data warehouse och klicka på för att öppna den.
3. I bladet huvudsakliga klickar du på **Läs in Data** > **Azure Data Factory**.

    ![Starta guiden Läs in Data](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Om du inte har en datafabrik i din Azure-prenumeration visas en **nya Data Factory** dialogrutan i en separat flik i webbläsaren. Fyll i den begärda informationen och klicka på **skapa**. När datafabriken har skapats kan den **nya Data Factory** dialogrutan stängs och du ser den **Välj Data Factory** dialogrutan.

    Om du har en eller flera datafabriker redan i Azure-prenumeration kan du se den **Välj Data Factory** dialogrutan. I den här dialogrutan kan du antingen välja en befintlig datafabrik eller klicka på **skapa nya data factory** att skapa en ny.

    ![Konfigurera Data Factory](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. I den **Välj Data Factory** dialogrutan den **läsa in data** alternativet är markerat som standard. Klicka på **nästa** börja skapa en aktivitet för datainläsning.

## <a name="configure-the-data-factory-properties"></a>Konfigurera egenskaper för data factory
Nu när du har skapat en datafabrik, är nästa steg att konfigurera schemat för datainläsning.

1. För **aktivitet**, ange **DWLoadData fromSQLServer**.
2. Använd standard **kör nu en gång** klickar du på **nästa**.

    ![Konfigurera belastningen schema](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>Konfigurera datalager för källa och gateway
Nu kan du se Data Factory om lokala SQL Server-databasen som du vill läsa in data.

1. Välj **SQL Server** stöds källdata lagra katalog och på **nästa**.

    ![Välj källa för SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. En **ange SQL Server-databas lokalt** visas. Först **anslutningsnamn** är fältet fylls i automatiskt. Det andra fältet uppmanas du att ange namnet på den **Gateway**. Om du använder en befintlig datafabrik som redan har en gateway kan du återanvända gatewayen genom att välja den nedrullningsbara listan. Klicka på den **skapa Gateway** länken för att skapa en Data Management Gateway.  

    > [!NOTE]
    > Om käll-datalagret är lokalt eller i en virtuell Azure IaaS-dator, en Data Management Gateway måste anges. En gateway har en 1-1-relation med en datafabrik. Det går inte att använda från en annan data factory, men den kan användas av flera uppgifter med i samma data factory för datainläsning. En gateway kan användas för att ansluta till flera datalager om uppgifter för datainläsning.
    >
    > Detaljerad information om gateway finns [Data Management Gateway](../data-factory/v1/data-factory-data-management-gateway.md) artikel.

3. En **skapa Gateway** dialogrutan visas. Namn, ange **GatewayForDWLoading**, och klicka på **skapa**.

4. En **konfigurera Gateway** dialogrutan visas. Klicka på **starta Expressinstallationen på den här datorn** för att automatiskt hämta, installera och registrera Data Management Gateway på den aktuella datorn. Förloppet visas i ett popup-fönster. Om datorn inte kan ansluta till datalagret kan du manuellt [ladda ned och installera gatewayen](https://www.microsoft.com/download/details.aspx?id=39717) lagra på en dator som kan ansluta till data och använder sedan för att registrera.
    > [!NOTE]
    > Expressinstallation fungerar internt med Microsoft Edge och Internet Explorer. Om du använder Google Chrome, måste du först installera tillägget ClickOnce från Chrome web store.

    ![Starta installationen av Express](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Vänta tills gateway-installationen ska kunna slutföras. När gatewayen har registrerats och är online, popup-fönstret stängs och den nya gatewayen visas i fältet för gateway. Sedan fylla i resten obligatoriska fält enligt följande och klicka sedan på **nästa**.
    - **Servernamnet**: namnet på den lokala SQL Server.
    - **Databasnamnet**: SQL Server-databas.
    - **Autentiseringsuppgifter kryptering**: använder standard ”av webbläsaren”.
    - **Autentiseringstypen**: Välj typ av autentisering som du använder.
    - **Användarnamnet** och **lösenord**: Ange användarnamn och lösenord för en användare som har behörighet att kopiera data.

    ![Starta installationen av Express](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. Nästa steg är att välja tabeller som du vill kopiera data från. Du kan filtrera tabeller med hjälp av nyckelord. Och du kan förhandsgranska data och tabellen schema i den nedre panelen. När du slutför ditt val klickar du på **nästa**.

    ![Välj tabeller](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Konfigurera mål, ditt SQL Data Warehouse

Nu kan du se Data Factory om informationen som mål.

1. SQL Data Warehouse anslutningsinformationen fylls i automatiskt. Ange lösenordet för användarnamnet. och klicka på **nästa**.

    ![Konfigurera mål](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. Mappningen för en intelligent tabellen visas maps källa måltabell baserat på namn. Om tabellen inte finns i målet, som standard skapar ADF en med samma namn (Detta gäller för SQL Server eller Azure SQL Database som källa). Du kan också välja att mappa till en befintlig tabell. Granska och klicka på **nästa**.

    ![Mappa tabeller](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Granska mappningen schemat och leta efter fel eller varningsmeddelanden. Intelligent mappningen baserat på kolumnnamn. Om det finns en typkonvertering för data som inte stöds mellan käll- och kolumnen, visas ett felmeddelande om du tillsammans med motsvarande register. Om du väljer att låta Data Factory automatiskt skapa tabellerna kan rätt datatypkonvertering inträffa om du behöver åtgärda inkompatibilitet mellan käll- och lagrar.

    ![Mappa schema](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Klicka på **Nästa**.

## <a name="configure-the-performance-settings"></a>Konfigurera prestandainställningar för
Prestanda-konfigurationer som du konfigurerar ett Azure storage-konto som används för mellanlagring av data innan den läses in i SQL Data Warehouse performantly med [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). När kopieringen är klar rensas mellanliggande data i lagring automatiskt.

Välj ett befintligt Azure Storage-konto och klicka på **nästa**.

![Konfigurera fristående blob](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Granska sammanfattningsinformation och distribuera pipelinen

Granska konfigurationen och klickar på **Slutför** för att distribuera pipelinen.

![Distribuera data factory](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Övervaka förloppet för datainläsning

Du kan se förlopp och resultat i den **distribution** sidan.

1. När distributionen är klar klickar du på länken **Klicka här för att övervaka kopiera pipeline** att övervaka förloppet för datainläsning.

    ![Övervaka pipeline](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. Den nyligen skapade **DWLoadData fromSQLServer** pipeline för inläsning av data är automatisk för från den vänstra **Resursläsaren**.

    ![Visa pipeline](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Klicka i pipeline i den mittersta panelen om du vill se detaljerad status för varje tabell som mappar till en aktivitet.

    ![Visa tabellen aktivitet](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. Ytterligare Klicka i en aktivitet och du ser de data som lästes in informationen i den högra panelen inklusive datastorleken, rader, genomflöde och så vidare.

    ![Visa tabellen Aktivitetsinformation](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. Om du vill starta den här övervakningsvyn senare, gå till ditt SQL Data Warehouse, klickar du på **Läs in Data > Azure Data Factory**, Välj din factory och välj **övervaka befintliga läser in aktiviteter**.

## <a name="next-steps"></a>Nästa steg

Om du vill migrera din databas till SQL Data Warehouse, se [migreringsöversikt](sql-data-warehouse-overview-migrate.md).

Om du vill veta mer om Azure Data Factory och dess funktioner för flytt av data, finns i följande artiklar:

- [Introduktion till Azure Data Factory](../data-factory/introduction.md)
- [Flytta data med hjälp av Kopieringsaktiviteten](../data-factory/copy-activity-overview.md)
- [Flytta data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory](../data-factory/connector-azure-sql-data-warehouse.md)

Om du vill utforska dina data i SQL Data Warehouse, finns i följande artiklar:

- [Ansluta till SQL Data Warehouse med Visual Studio och SSDT](sql-data-warehouse-query-visual-studio.md)
- [Visuella data med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Azure-portalen]: https://portal.azure.com
