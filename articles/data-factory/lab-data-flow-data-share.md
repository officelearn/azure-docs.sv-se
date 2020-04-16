---
title: Dataintegrering med Azure Data Factory och Azure Data Share
description: Kopiera, transformera och dela data med Azure Data Factory och Azure Data Share
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 7d453b2724c308e48366d653a51d9e6aa8e82c96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415928"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Dataintegrering med Azure Data Factory och Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När kunderna ger sig ut på sina moderna datalager- och analysprojekt kräver de inte bara mer data utan också mer insyn i sina data över hela dataområdet. Den här workshopen dyker upp i hur förbättringar av Azure Data Factory och Azure Data Share förenklar dataintegration och datahantering i Azure. 

Från att aktivera kodfri ETL/ELT till att skapa en omfattande vy över dina data, kommer förbättringar i Azure Data Factory att ge dina datatekniker möjlighet att tryggt ta in mer data och därmed mer värde till ditt företag. Med Azure Data Share kan du göra affärer med affärsdelning på ett styrt sätt.

I den här workshopen använder du Azure Data Factory (ADF) för att få in data från en Azure SQL-databas (SQL DB) i Azure Data Lake Storage gen2 (ADLS gen2). När du har landat data i sjön omvandlar du dem via mappningsdataflöden, datafabrikens inbyggda omvandlingstjänst och sänker den till Azure Synapse Analytics (tidigare SQL DW). Sedan delar du tabellen med transformerade data tillsammans med ytterligare data med Azure Data Share. 

De data som används i detta labb är New York City taxi data. Om du vill importera den till din Azure SQL-databas hämtar du [filen taxidata bacpac](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

* **Azure SQL Database**: Om du inte har en SQL DB kan du läsa om hur du [skapar ett SQL DB-konto](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Azure Data Lake Storage Gen2-lagringskonto:** Om du inte har ett ADLS Gen2-lagringskonto kan du läsa om hur du [skapar ett ADLS Gen2-lagringskonto](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Azure Synapse Analytics (tidigare SQL DW):** Om du inte har en Azure Synapse Analytics (tidigare SQL DW) lär du dig hur du [skapar en Azure Synapse Analytics-instans](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

* **Azure Data Factory**: Om du inte har skapat en datafabrik läser du hur [du skapar en datafabrik](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).

* **Azure Data Share**: Om du inte har skapat en dataresurs läser du hur du [skapar en dataresurs](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Konfigurera din Azure Data Factory-miljö

I det här avsnittet får du lära dig hur du kommer åt Azure Data Factory-användarupplevelsen (ADF UX) från Azure-portalen. En gång i ADF UX konfigurerar du tre länkade tjänster för vart och ett av de datalager vi använder: Azure SQL DB, ADLS Gen2 och Azure Synapse Analytics.

I Azure Data Factory definierar länkade tjänster anslutningsinformationen till externa resurser. Azure Data Factory stöder för närvarande över 85 kopplingar.

### <a name="open-the-azure-data-factory-ux"></a>Öppna Azure Data Factory UX

1. Öppna [Azure-portalen](https://portal.azure.com) i Microsoft Edge eller Google Chrome.
1. Sök efter "Datafabriker" med hjälp av sökfältet högst upp på sidan

    ![Portalen](media/lab-data-flow-data-share/portal1.png)
1. Klicka på din datafabriksresurs för att öppna upp dess resursblad.

    ![Portalen](media/lab-data-flow-data-share/portal2.png)
1. Klicka på **Författare och Monitor** för att öppna upp ADF UX. ADF UX kan också nås på adf.azure.com.

    ![Portalen](media/lab-data-flow-data-share/portal3.png)
1. Du omdirigeras till ADF UX:s hemsida. Den här sidan innehåller snabbstarter, instruktionsvideor och länkar till självstudier för att lära dig datafabrikskoncept. Om du vill börja skapa klickar du på pennikonen i vänstra sidofältet.

    ![Portalen](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en azure SQL-databaslänkade tjänst

1. Författarsidan skapar du datafabriksresurser som pipelines, datauppsättningar, dataflöden, utlösare och länkade tjänster. Om du vill skapa en länkad tjänst klickar du på knappen **Anslutningar** längst ned till höger.

    ![Portalen](media/lab-data-flow-data-share/configure2.png)
1. Klicka på **Ny** på fliken Anslutningar om du vill lägga till en ny länkad tjänst.

    ![Portalen](media/lab-data-flow-data-share/configure3.png)
1. Den första länkade tjänsten som du ska konfigurera är en Azure SQL DB. Du kan använda sökfältet för att filtrera datalagret. Klicka på panelen **Azure SQL Database** och klicka på Fortsätt.

    ![Portalen](media/lab-data-flow-data-share/configure4.png)
1. I konfigurationsfönstret för SQL DB anger du SQLDB som ditt länkade tjänstnamn. Ange i dina autentiseringsuppgifter så att datafabriken kan ansluta till databasen. Om du använder SQL-autentisering anger du i servernamnet, databasen, användarnamnet och lösenordet. Du kan kontrollera att anslutningsinformationen är korrekt genom att klicka på **Testa anslutning**. Klicka på **Skapa** när du är klar.

    ![Portalen](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Skapa en Azure Synapse Analytics-länkad tjänst

1. Upprepa samma process för att lägga till en Azure Synapse Analytics-länkad tjänst. Klicka på **Ny**på fliken Anslutningar . Välj **panelen Azure Synapse Analytics (tidigare SQL DW)** och klicka på Fortsätt.

    ![Portalen](media/lab-data-flow-data-share/configure6.png)
1. I konfigurationsfönstret för länkade tjänster anger du SQLDW som ditt länkade tjänstnamn. Ange i dina autentiseringsuppgifter så att datafabriken kan ansluta till databasen. Om du använder SQL-autentisering anger du i servernamnet, databasen, användarnamnet och lösenordet. Du kan kontrollera att anslutningsinformationen är korrekt genom att klicka på **Testa anslutning**. Klicka på **Skapa** när du är klar.

    ![Portalen](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Skapa en Azure Data Lake Storage Gen2-länkad tjänst

1. Den senaste länkade tjänsten som behövs för det här labbet är en Azure Data Lake Storage gen2.  Klicka på **Ny**på fliken Anslutningar . Välj panelen **Azure Data Lake Storage Gen2** och klicka på Fortsätt.

    ![Portalen](media/lab-data-flow-data-share/configure8.png)
1. I konfigurationsfönstret för länkade tjänster anger du ADLSGen2 som ditt länkade tjänstnamn. Om du använder autentisering med kontonyckel väljer du ditt adls gen2-lagringskonto i listrutan **Lagringskontonamn.** Du kan kontrollera att anslutningsinformationen är korrekt genom att klicka på **Testa anslutning**. Klicka på **Skapa** när du är klar.

    ![Portalen](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Aktivera felsökningsläge för dataflöde

I avsnittet *Omvandla data med hjälp av mappningsdataflöde*skapar du mappningsdataflöden. En bästa praxis innan du skapar mappningsdataflöden är att aktivera felsökningsläge, vilket gör att du kan testa omvandlingslogiken på några sekunder i ett aktivt spark-kluster.

Om du vill aktivera felsökning klickar du på skjutreglaget **Dataflödesfelsökning** i fabriksfältets övre fält. Klicka på ok när bekräftelsedialogrutan visas. Klustret tar cirka 5-7 minuter att starta. Fortsätt till *Ingest data från Azure SQL DB till ADLS gen2 med hjälp av kopieringsaktiviteten* medan den initierar.

![Portalen](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Övriga data med kopieringsaktiviteten

I det här avsnittet ska du skapa en pipeline med en kopieringsaktivitet som äter en tabell från en Azure SQL DB till ett ADLS gen2-lagringskonto. Du får lära dig hur du lägger till en pipeline, konfigurerar en datauppsättning och felsöker en pipeline via ADF UX. Konfigurationsmönstret som används i det här avsnittet kan tillämpas på kopiering från ett relationsdatalager till ett filbaserat datalager.

I Azure Data Factory är en pipeline en logisk gruppering av aktiviteter som tillsammans utför en uppgift. En aktivitet definierar en åtgärd som ska utföras på dina data. En datauppsättning pekar på de data som du vill använda i en länkad tjänst.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Skapa en pipeline med en kopieringsaktivitet

1. Öppna den nya resursmenyn i fönstret fabriksresurser. Välj **Pipeline**.

    ![Portalen](media/lab-data-flow-data-share/copy1.png)
1. På fliken **Allmänt** på pipeline-arbetsytan namnger du pipelinen något beskrivande, till exempel "IngestAndTransformTaxiData".

    ![Portalen](media/lab-data-flow-data-share/copy2.png)
1. Öppna dragspelet **Flytta och omforma** i aktivitetsfönstret på pipeline-arbetsytan och dra **dataaktiviteten Kopiera** till arbetsytan. Ge kopieringsaktiviteten ett beskrivande namn som "IngestIntoADLS".

    ![Portalen](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurera Azure SQL DB-källdatauppsättning

1. Klicka på fliken **Källa för** kopieringsaktiviteten. Om du vill skapa en ny datauppsättning klickar du på **Ny**. Din källa kommer att vara tabellen 'dbo. TripData' som finns i den länkade tjänsten "SQLDB" konfigurerad tidigare.

    ![Portalen](media/lab-data-flow-data-share/copy4.png)
1. Sök efter **Azure SQL Database** och klicka på Fortsätt.

    ![Portalen](media/lab-data-flow-data-share/copy5.png)
1. Ring din datauppsättning "TripData". Välj "SQLDB" som länkad tjänst. Välj tabellnamn 'dbo. TripData' från listrutan tabellnamn. Importera schemat **Från anslutning/butik**. Klicka på OK när du är klar.

    ![Portalen](media/lab-data-flow-data-share/copy6.png)

Du har skapat källdatauppsättningen. Kontrollera att standardvärdet **Tabellen** är markerad i användningsfrågefältet i källinställningarna.

### <a name="configure-adls-gen-2-sink-dataset"></a>Konfigurera ADLS Gen 2 sink-datauppsättning

1. Klicka på fliken **Handfat** för kopieringsaktiviteten. Om du vill skapa en ny datauppsättning klickar du på **Ny**.

    ![Portalen](media/lab-data-flow-data-share/copy7.png)
1. Sök efter **Azure Data Lake Storage Gen2** och klicka på Fortsätt.

    ![Portalen](media/lab-data-flow-data-share/copy8.png)
1. Välj **AvgränsadText** i fönstret Välj format när du skriver till en csv-fil. Klicka på Fortsätt.

    ![Portalen](media/lab-data-flow-data-share/copy9.png)
1. Namnge din sink-datauppsättning "TripDataCSV". Välj ADLSGen2 som länkad tjänst. Ange var du vill skriva din csv-fil. Du kan till exempel skriva `trip-data.csv` data `staging-container`till filen i behållaren . Ange **första raden som rubrik** till true som du vill att utdata ska ha rubriker. Eftersom det inte finns någon fil i målet ännu anger du **Importschema** till **Ingen**. Klicka på OK när du är klar.

    ![Portalen](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testa kopieringsaktiviteten med en pipeline-felsökningskörning

1. Om du vill kontrollera att kopieringsaktiviteten fungerar korrekt klickar du på **Felsök** högst upp på pipeline-arbetsytan för att köra en felsökningskörning. Med en felsökningskörning kan du testa pipelinen antingen från slutpunkt till slutpunkt eller tills en brytpunkt innan du publicerar den till datafabrikstjänsten.

    ![Portalen](media/lab-data-flow-data-share/copy11.png)
1. Om du vill övervaka felsökningskörningen går du till fliken **Utdata** på pipeline-arbetsytan. Övervakningsskärmen kommer automatiskt att refresh var 20 sekunder eller när du manuellt klickar på uppdatera knappen. Kopieringsaktiviteten har en särskild övervakningsvy som kan nås genom att klicka på ikonen glasögon i kolumnen **Åtgärder.**

    ![Portalen](media/lab-data-flow-data-share/copy12.png)
1. Kopieringsövervakningsvyn ger aktivitetens körningsinformation och prestandaegenskaper. Du kan se information som data läsa / skriftliga, rader läsa / skriftliga, filer läsa / skriftliga och dataflöde. Om du har konfigurerat allt korrekt bör du se 49 999 rader skrivna i en fil i ADLS-diskbänken.

    ![Portalen](media/lab-data-flow-data-share/copy13.png)
1. Innan du går vidare till nästa avsnitt föreslås att du publicerar ändringarna i datafabrikstjänsten genom att klicka på **Publicera alla** i fabriksfältet. Azure Data Factory är inte täckt i det här labbet, men stöder fullständig git-integrering. Git-integrering möjliggör versionskontroll, iterativsparning i en databas och samarbete på en datafabrik. Mer information finns [i källkontroll i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration).

    ![Portalen](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Omvandla data med Mappa dataflöden

Nu när du har kopierat data till Azure Data Lake Storage är det dags att ansluta och aggregera dessa data till ett informationslager. Vi kommer att använda mappningsdataflöde, Azure Data Factorys visuellt utformade omvandlingstjänst. Mappning av dataflöden gör det möjligt för användare att utveckla omvandlingslogikkodfri och köra dem på sparkkluster som hanteras av ADF-tjänsten.

Dataflödet som skapas i det här steget ingår i "TripDataCSV"-datauppsättningen som skapats i föregående avsnitt med en tabell 'dbo. TripFares lagras i "SQLDB" baserat på fyra nyckelkolumner. Sedan sammanställs data baserat `payment_type` på kolumn för att beräkna medelvärdet av vissa fält och skrivs i en Azure Synapse Analytics-tabell.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Lägga till en dataflödesaktivitet i pipelinen

1. Öppna dragspelet **Flytta och omforma** i aktivitetsfönstret på pipeline-arbetsytan och dra **dataflödesaktiviteten** till arbetsytan.

    ![Portalen](media/lab-data-flow-data-share/dataflow1.png)
1. I sidofönstret som öppnas väljer du **Skapa nytt dataflöde** och väljer **Mappa dataflöde**. Klicka på **OK**.

    ![Portalen](media/lab-data-flow-data-share/dataflow2.png)
1. Du dirigeras till dataflödesarbetsytan där du ska bygga din omvandlingslogik. På fliken Allmänt namnger du ditt dataflöde "JoinAndAggregateData".

    ![Portalen](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Konfigurera din resedata csv-källa

1. Det första du vill göra är att konfigurera dina två källomvandlingar. Den första källan pekar på "TripDataCSV" AvimitedText-datauppsättningen. Om du vill lägga till en källomvandling klickar du på rutan **Lägg till källa** på arbetsytan.

    ![Portalen](media/lab-data-flow-data-share/dataflow4.png)
1. Namnge källan "TripDataCSV" och välj "TripDataCSV"-datauppsättningen från källrullgardinsmenyn. Om du kommer ihåg det importerade du inte ett schema från början när du skapade den här datauppsättningen eftersom det inte fanns några data där. Eftersom `trip-data.csv` det finns nu klickar du på **Redigera** för att gå till fliken inställningar för datauppsättning.

    ![Portalen](media/lab-data-flow-data-share/dataflow5.png)
1. Gå till fliken **Schema** och klicka på **Importera schema**. Välj **Från anslutning/butik** om du vill importera direkt från filarkivet. 14 kolumner av typen sträng ska visas.

    ![Portalen](media/lab-data-flow-data-share/dataflow6.png)
1. Gå tillbaka till dataflödet JoinAndAggregateData. Om felsökningsklustret har startat (indikeras av en grön cirkel bredvid felsökningsreglaget) kan du få **Refresh** en ögonblicksbild av data på fliken Förhandsgranskning av **data.**

    ![Portalen](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Förhandsgranskning av data skriver inte data.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurera dina resepriser SQL DB-källa

1. Den andra källan du lägger till pekar på SQL DB-tabellen 'dbo. TripFares. Under din "TripDataCSV"-källa kommer det att finnas en annan **add Source-ruta.** Klicka på den om du vill lägga till en ny källomvandling.

    ![Portalen](media/lab-data-flow-data-share/dataflow8.png)
1. Namnge denna källa "TripFaresSQL". Klicka på **Ny** bredvid fältet källdatauppsättning om du vill skapa en ny SQL DB-datauppsättning.

    ![Portalen](media/lab-data-flow-data-share/dataflow9.png)
1. Välj panelen **Azure SQL Database** och klicka på Fortsätt. *Du kanske märker att många av kopplingarna i datafabriken inte stöds vid mappning av dataflöde. Om du vill omvandla data från någon av dessa källor matar du in dem i en källa som stöds med kopieringsaktiviteten*.

    ![Portalen](media/lab-data-flow-data-share/dataflow10.png)
1. Ring din datauppsättning "TripFares". Välj "SQLDB" som länkad tjänst. Välj tabellnamn 'dbo. TripFares från rullgardinsmenyn för tabellnamn. Importera schemat **Från anslutning/butik**. Klicka på OK när du är klar.

    ![Portalen](media/lab-data-flow-data-share/dataflow11.png)
1. Om du vill verifiera dina data hämtar du en förhandsgranskning av data på fliken **Förhandsgranskning av data.**

    ![Portalen](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Inner join TripDataCSV och TripFaresSQL

1. Om du vill lägga till en ny omvandling klickar du på plusikonen längst ned till höger i "TripDataCSV". Under **Flera indata/utdata**väljer du **Anslut**.

    ![Portalen](media/lab-data-flow-data-share/join1.png)
1. Namnge din join transformation "InnerJoinWithTripFares". Välj "TripFaresSQL" i rullgardinsmenyn för höger ström. Välj **Inre** som kopplingstyp. Mer information om de olika kopplingstyperna i mappningsdataflödet finns i [kopplingstyper](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types).

    Välj vilka kolumner du vill matcha på från varje ström via listrutan **Join villkor.** Om du vill lägga till ytterligare ett kopplingsvillkor klickar du på plusikonen bredvid ett befintligt villkor. Som standard kombineras alla kopplingsvillkor med en OCH-operatör, vilket innebär att alla villkor måste vara uppfyllda för en matchning. I det här labbet vill `medallion` `hack_license`vi `vendor_id`matcha på kolumner , , och`pickup_datetime`

    ![Portalen](media/lab-data-flow-data-share/join2.png)
1. Kontrollera att du har sammanfogat 25 kolumner tillsammans med en förhandsgranskning av data.

    ![Portalen](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Sammanlagt efter payment_type

1. När du har slutfört din join transformation, lägga till en sammanlagd omvandling genom att klicka på plusikonen bredvid InnerJoinWithTripFares. Välj **Aggregat** under **Schemamodifierare**.

    ![Portalen](media/lab-data-flow-data-share/agg1.png)
1. Namnge den sammanlagda omvandlingen "AggregateByPaymentType". Välj `payment_type` som grupp för kolumn.

    ![Portalen](media/lab-data-flow-data-share/agg2.png)
1. Gå till fliken **Aggregat.** Här anger du två aggregeringar:
    * Det genomsnittliga priset grupperat efter betalningstyp
    * Den totala resans avstånd grupperad efter betalningstyp

    Först ska du skapa det genomsnittliga biljettprisuttrycket. Skriv "average_fare" i textrutan **Lägg till eller markera en kolumn.**

    ![Portalen](media/lab-data-flow-data-share/agg3.png)
1. Om du vill ange ett aggregeringsuttryck klickar du på den blå rutan Med namnet **Enter-uttryck**. Detta öppnar dataflödesuttrycksverktyget, ett verktyg som används för att visuellt skapa dataflödesuttryck med hjälp av indataschema, inbyggda funktioner och åtgärder samt användardefinierade parametrar. Mer information om uttrycksverktygets funktioner finns i [dokumentverktygets dokumentation](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    Om du vill hämta `avg()` det genomsnittliga priset använder `total_amount` du aggregeringsfunktionen för att aggregera kolumnen som castas till ett heltal med `toInteger()`. I dataflödesuttrycksspråket `avg(toInteger(total_amount))`definieras detta som . Klicka på **Spara och avsluta** när du är klar.

    ![Portalen](media/lab-data-flow-data-share/agg4.png)
1. Om du vill lägga till ytterligare ett aggregeringsuttryck klickar du på plusikonen bredvid `average_fare`. Välj **Lägg till kolumn**.

    ![Portalen](media/lab-data-flow-data-share/agg5.png)
1. Skriv "total_trip_distance" i textrutan **Lägg till eller markera en kolumn.** Precis som i det sista steget öppnar du uttrycksbyggaren för att ange uttrycket.

    Om du vill hämta det `sum()` totala trippavståndet `trip_distance` använder du aggregeringsfunktionen för att aggregera kolumnen som castas till ett heltal med `toInteger()`. I dataflödesuttrycksspråket `sum(toInteger(trip_distance))`definieras detta som . Klicka på **Spara och avsluta** när du är klar.

    ![Portalen](media/lab-data-flow-data-share/agg6.png)
1. Testa omvandlingslogiken på fliken **Förhandsgranskning av data.** Som du kan se finns det betydligt färre rader och kolumner än tidigare. Endast de tre kolumnerna för grupper och aggregering som definierats i den här omvandlingen fortsätter nedströms. Eftersom det bara finns fem betalningstypsgrupper i exemplet matas bara fem rader ut.

    ![Portalen](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurera azure synapse analytics-diskho

1. Nu när vi har slutfört vår omvandlingslogik är vi redo att sänka våra data i en Azure Synapse Analytics-tabell. Lägg till en sink-omformning under avsnittet **Mål.**

    ![Portalen](media/lab-data-flow-data-share/sink1.png)
1. Namnge din diskbänk 'SQLDWSink'. Klicka på **Ny** bredvid fältet sink-datauppsättning om du vill skapa en ny Azure Synapse Analytics-datauppsättning.

    ![Portalen](media/lab-data-flow-data-share/sink2.png)

1. Välj **panelen Azure Synapse Analytics (tidigare SQL DW)** och klicka på Fortsätt.

    ![Portalen](media/lab-data-flow-data-share/sink3.png)
1. Anropa din datauppsättning "AggregatedTaxiData". Välj SQLDW som länkad tjänst. Välj **Skapa ny tabell** och namnge den nya tabellen dbo. AggregateTaxiData. Klicka på OK när du är klar

    ![Portalen](media/lab-data-flow-data-share/sink4.png)
1. Gå till fliken **Inställningar** i diskhon. Eftersom vi skapar en ny tabell måste vi välja **Återskapa tabell** under tabellåtgärd. Avmarkera **Aktivera mellanlagring**, som växlar om vi infogar rad för rad eller i batch.

    ![Portalen](media/lab-data-flow-data-share/sink5.png)

Du har skapat ditt dataflöde. Nu är det dags att köra den i en pipeline-aktivitet.

### <a name="debug-your-pipeline-end-to-end"></a>Felsök pipelinen från på slutet

1. Gå tillbaka till fliken för **pipelinen IngestAndTransformData.** Lägg märke till den gröna rutan på kopieringsaktiviteten "IngestIntoADLS". Dra över den till dataflödesaktiviteten JoinAndAggregateData. Detta skapar en "på framgång", vilket gör att dataflödesaktiviteten bara körs om kopian lyckas.

    ![Portalen](media/lab-data-flow-data-share/pipeline1.png)
1. Som vi gjorde för kopieringsaktiviteten klickar du på **Felsök** för att köra en felsökningskörning. För felsökningskörningar använder dataflödesaktiviteten det aktiva felsökningsklustret i stället för att snurra upp ett nytt kluster. Den här pipelinen tar en dryg minut att utföra.

    ![Portalen](media/lab-data-flow-data-share/pipeline2.png)
1. Precis som kopieringsaktiviteten har dataflödet en särskild övervakningsvy som används av glasögonikonen när aktiviteten är slutförd.

    ![Portalen](media/lab-data-flow-data-share/pipeline3.png)
1. I övervakningsvyn kan du se ett förenklat dataflödesdiagram tillsammans med körningstider och rader i varje körningsfas. Om det görs på rätt sätt bör du ha aggregerat 49 999 rader i fem rader i den här aktiviteten.

    ![Portalen](media/lab-data-flow-data-share/pipeline4.png)
1. Du kan klicka på en omvandling för att få ytterligare information om körningen, till exempel partitioneringsinformation och nya/uppdaterade/tappade kolumner.

    ![Portalen](media/lab-data-flow-data-share/pipeline5.png)

Du har nu slutfört datafabrikens del av det här labbet. Publicera dina resurser om du vill operationalisera dem med utlösare. Du har kört en pipeline som inmatade data från Azure SQL Database till Azure Data Lake Storage med hjälp av kopieringsaktiviteten och sammanställde sedan dessa data till en Azure Synapse Analytics. Du kan kontrollera att data har skrivits genom att titta på själva SQL Server.

## <a name="share-data-using-azure-data-share"></a>Dela data med Azure Data Share

I det här avsnittet får du lära dig hur du konfigurerar en ny dataresurs med Azure-portalen. Detta innebär att skapa en ny dataresurs som innehåller datauppsättningar från Azure Data Lake Store Gen2 och Azure SQL Data Warehouse. Du konfigurerar sedan ett schema för ögonblicksbilder, vilket ger datakonsumenterna ett alternativ för att automatiskt uppdatera de data som delas med dem. Sedan bjuder du in mottagare till din dataresurs. 

När du har skapat en dataresurs byter du hattar och blir *datakonsument.* Som datakonsument går du igenom flödet för att acceptera en inbjudan datadelning, konfigurera var du vill att data ska tas emot och mappa datauppsättningar till olika lagringsplatser. Sedan ska du utlösa en ögonblicksbild som kopierar data som delas med dig till det angivna målet. 

### <a name="sharing-data-data-provider-flow"></a>Dela data (dataproviderflöde)

1. Öppna Azure-portalen i Microsoft Edge eller Google Chrome.

1. Sök efter **dataresurser** med hjälp av sökfältet högst upp på sidan

    ![Portalen](media/lab-data-flow-data-share/portal-ads.png)

1. Välj datadelningskontot med "Provider" i namnet. **DataProvider0102**. 

1. Välj **Börja dela dina data**

    ![Börja dela](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Välj **+Skapa** om du vill börja konfigurera den nya dataresursen. 

1. Under **Dela namn**anger du ett namn som du väljer. Detta är det resursnamn som kommer att ses av din datakonsument, så se till att ge det ett beskrivande namn som TaxiData.

1. Under **Beskrivning**placerar du i en mening som beskriver innehållet i dataresursen. Dataresursen innehåller världsomspännande taxiresadata som lagras i ett antal butiker, inklusive Azure SQL Data Warehouse och Azure Data Lake Store. 

1. Under **Användarvillkor**anger du en uppsättning villkor som du vill att din datakonsument ska följa. Några exempel är "Distribuera inte dessa data utanför organisationen" eller "Se juridiskt avtal". 

    ![Dela information](media/lab-data-flow-data-share/ads-details.png)

1. Välj **Fortsätt**. 

1. Välj **Lägg till datauppsättningar** 

    ![Lägg till datauppsättning](media/lab-data-flow-data-share/add-dataset.png)

1. Välj **Azure SQL Data Warehouse** för att välja en tabell från Azure SQL Data Warehouse som dina ADF-omvandlingar landade i.

    ![Lägg till datauppsättning](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Azure SQL Data Warehouse kallas nu Azure Synapse Analytics

1. Du får ett skript som ska köras innan du kan fortsätta. Skriptet som tillhandahålls skapar en användare i SQL-databasen så att Azure Data Share MSI kan autentisera för dess räkning. 

> [!IMPORTANT]
> Innan du kör skriptet måste du ange dig själv som Active Directory-administratör för SQL Server. 

1. Öppna en ny flik och navigera till Azure-portalen. Kopiera skriptet som du vill skapa en användare i databasen som du vill dela data från. Gör detta genom att logga in i EDW-databasen med Query Explorer (förhandsversion) med AAD-autentisering. 

    Du måste ändra skriptet så att användaren som skapas finns inom parentes. T.ex:
    
    skapa användare [dataprovider-xxxx] från extern inloggning;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Växla tillbaka till Azure Data Share där du lade till datauppsättningar i din dataresurs. 

1. Välj **EDW** för SQL Data Warehouse och välj **AggregatedTaxiData** för tabellen. 

1. Välj **Lägg till datauppsättning**

    Vi har nu en SQL-tabell som är en del av vår datauppsättning. Därefter lägger vi till ytterligare datauppsättningar från Azure Data Lake Store. 

1. Välj **Lägg till datauppsättning** och välj **Azure Data Lake Store Gen2**

    ![Lägg till datauppsättning](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Välj **nästa**

1. Expandera *wwtaxidata*. Expandera *Boston Taxi Data*. Observera att du kan dela ned till filnivån. 

1. Välj mappen *Boston Taxi Data* om du vill lägga till hela mappen i dataresursen. 

1. Välj **Lägg till datauppsättningar**

1. Granska de datauppsättningar som har lagts till. Du bör ha en SQL-tabell och en ADLSGen2-mapp tillagd i dataresursen. 

1. Välj **Fortsätt**

1. På den här skärmen kan du lägga till mottagare i din dataresurs. Mottagarna du lägger till får inbjudningar till din dataresurs. För detta labb måste du lägga till i 2 e-postadresser:

    1. E-postadressen till den Azure-prenumeration du är i. 

        ![Lägga till mottagare](media/lab-data-flow-data-share/add-recipients.png)

    1. Lägg till den fiktiva datakonsumenten *janedoe@fabrikam.com*.

1. På den här skärmen kan du konfigurera en ögonblicksbildinställning för din datakonsument. Detta gör det möjligt för dem att få regelbundna uppdateringar av dina data med ett intervall som definieras av dig. 

1. Kontrollera **Ögonblicksbild schema** och konfigurera en timmes uppdatering av dina data med hjälp av kryssrutan *Återkommande.*  

1. Välj **Skapa**.

    Du har nu en aktiv dataresurs. Gör det du kan granska som en dataleverantör när du skapar en dataresurs. 

1. Välj den dataresurs som du skapade med titeln **DataProvider**. Du kan navigera till den genom att välja **Skickade resurser** i **Datadelning**. 

1. Klicka på Schemat för ögonblicksbild. Du kan inaktivera schemat för ögonblicksbilder om du vill. 

1. Välj sedan fliken **Datauppsättningar.** Du kan lägga till ytterligare datauppsättningar i den här dataresursen när den har skapats. 

1. Välj fliken **Dela prenumerationer.** Det finns ännu inga delningsprenumerationer eftersom din datakonsument ännu inte har accepterat din inbjudan.

1. Navigera till fliken **Inbjudningar.** Här visas en lista över väntande inbjudningar. 

    ![Väntande inbjudningar](media/lab-data-flow-data-share/pending-invites.png)

1. Välj inbjudan *janedoe@fabrikam.com*till . Välj Ta bort. Om mottagaren ännu inte har accepterat inbjudan kan han eller hon inte längre göra det. 

1. Välj fliken **Historik.** Ingenting visas ännu eftersom din datakonsument ännu inte har accepterat din inbjudan och utlöst en ögonblicksbild. 

### <a name="receiving-data-data-consumer-flow"></a>Ta emot data (datakonsumentflöde)

Nu när vi har granskat vår dataandel är vi redo att byta kontext och bära vår datakonsumenthatt. 

Du bör nu ha en Azure Data Share-inbjudan i inkorgen från Microsoft Azure. Starta Outlook Web Access (outlook.com) och logga in med de autentiseringsuppgifter som angetts för din Azure-prenumeration.

I e-postmeddelandet som du borde ha fått klickar du på "Visa inbjudan >". Nu kommer du att simulera datakonsumentupplevelsen när du accepterar en inbjudning till dataleverantörer till deras dataresurs. 

![Inbjudan till e-post](media/lab-data-flow-data-share/email-invite.png)

Du kan bli ombedd att välja en prenumeration. Se till att du väljer den prenumeration du har arbetat i för det här labbet. 

1. Klicka på inbjudan med titeln *DataProvider*. 

1. På den här inbjudningsskärmen får du se olika information om den dataresurs som du konfigurerade tidigare som dataleverantör. Granska detaljerna och acceptera användarvillkoren om de tillhandahålls.

1. Välj den prenumerations- och resursgrupp som redan finns för ditt labb. 

1. För **datadelningskonto**väljer du **DataConsumer**. Du kan också skapa ett nytt datadelningskonto. 

1. Bredvid **Mottaget resursnamn**kommer du att märka att standardresursnamnet är det namn som angavs av dataleverantören. Ge resursen ett eget namn som beskriver de data du **TaxiDataShare**ska ta emot, t.ex.

    ![Inbjudan accepterar](media/lab-data-flow-data-share/consumer-accept.png)

1. Du kan välja att **acceptera och konfigurera nu** eller Acceptera och konfigurera **senare**. Om du väljer att acceptera och konfigurera nu anger du ett lagringskonto där alla data ska kopieras. Om du väljer att acceptera och konfigurera senare kommer datauppsättningarna i resursen att tas bort och du måste mappa dem manuellt. Vi kommer att välja det senare. 

1. Välj **Acceptera och konfigurera senare**. 

    När du konfigurerar det här alternativet skapas en aktieprenumeration, men det finns ingenstans för data att landa eftersom ingen destination har mappats. 

    Därefter konfigurerar vi datauppsättningsmappningar för dataresursen. 

1. Välj den mottagna resursen (namnet du angav i steg 5).

    **Utlösarögonblicksbilden** är nedtonad men resursen är aktiv. 

1. Välj fliken **Datauppsättningar.** Observera att varje datauppsättning är Avmappad, vilket innebär att den inte har något mål att kopiera data till. 

    ![ommappade datauppsättningar](media/lab-data-flow-data-share/unmapped.png)

1. Markera SQL Data Warehouse Table och välj sedan **+ Mappa till mål**.

1. Välj listrutan **Måldatatyp** till höger på skärmen. 

    Du kan mappa SQL-data till ett brett spektrum av datalager. I det här fallet mappar vi till en Azure SQL-databas.

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    (Valfritt) Välj **Azure Data Lake Store Gen2** som måldatatyp. 
    
    (Valfritt) Välj det konto för prenumeration, resursgrupp och lagring som du har arbetat i. 
    
    (Valfritt) Du kan välja att ta emot data i din datasjö i antingen csv- eller parkettformat. 

1. Välj Azure SQL Database bredvid Datatyp för **Mål.** 

1. Välj det konto för prenumeration, resursgrupp och lagring som du har arbetat i. 

    ![karta till sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Innan du kan fortsätta måste du skapa en ny användare i SQL Server genom att köra skriptet. Kopiera först skriptet till Urklipp. 

1. Öppna en ny Azure-portalflik. Stäng inte din befintliga flik eftersom du måste komma tillbaka till den om ett ögonblick. 

1. Navigera till **SQL-databaser**på den nya fliken som du öppnade .

1. Välj SQL-databasen (det bör bara finnas en i din prenumeration). Var noga med att inte välja SQL Data Warehouse. 

1. Välj **Frågeredigerare (förhandsgranskning)**

1. Använd AAD-autentisering för att logga in på Frågeredigeraren. 

1. Kör frågan som anges i dataresursen (kopieras till Urklipp i steg 14). 

    Med det här kommandot kan Azure Data Share-tjänsten använda hanterade identiteter för Azure Services för att autentisera till SQL Server för att kunna kopiera data till den. 

1. Gå tillbaka till den ursprungliga fliken och välj **Karta för att rikta in dig på**.

1. Välj sedan mappen Azure Data Lake Gen2 som ingår i datauppsättningen och mappa den till ett Azure Blob Storage-konto. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Med alla datauppsättningar mappade är du nu redo att börja ta emot data från dataprovidern. 

    ![Mappade](media/lab-data-flow-data-share/all-mapped.png)
    
1. Välj **information**. 

    Observera att **ögonblicksbilden av utlösaren** inte längre är nedtonad, eftersom dataresursen nu har mål att kopiera till.

1. Välj Utlösarbild -> fullständig kopia. 

    ![Utlösa](media/lab-data-flow-data-share/trigger-full.png)

    Då börjar du kopiera data till ditt nya datadelningskonto. I ett verkligt scenario skulle dessa data komma från en tredje part. 

    Det tar cirka 3-5 minuter innan data kommer över. Du kan övervaka förloppet genom att klicka på fliken **Historik.** 

    Medan du väntar navigerar du till den ursprungliga dataresursen (DataProvider) och visar statusen för fliken **Aktieprenumerationer** och **historik.** 

1. Navigera tillbaka till datakonsumentens dataandel. När statusen för utlösaren har lyckades navigerar du till mål-SQL-databasen och datasjön för att se att data har landat i respektive butiker. 

Grattis, du har slutfört labbet!


