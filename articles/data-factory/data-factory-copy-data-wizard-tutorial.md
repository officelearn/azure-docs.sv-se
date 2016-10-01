<properties 
    pageTitle="Självstudier: Skapa en pipeline med hjälp av guiden Kopiera" 
    description="I de här självstudierna skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet, med hjälp av guiden Kopiera som stöds av Data Factory" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" **
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/16/2016" 
    ms.author="spelluru"/>


# Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Guiden Data Factory-kopia
> [AZURE.SELECTOR]
- [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)

I de här självstudierna använder du guiden Data Factory-kopia till att skapa en pipeline med en kopieringsaktivitet i en datafabrik. Först skapar du en datafabrik med hjälp av Azure-portalen. Sedan använder du guiden Kopiera för att skapa Data Factory-länkade tjänster, datauppsättningar och en pipeline med en kopieringsaktivitet som kopierar data från en Azure Blob Storage till en Azure SQL-databas. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. 

> [AZURE.IMPORTANT] Läs igenom artikeln [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) och slutför de **nödvändiga** stegen innan du fortsätter med självstudierna.

## Skapa en datafabrik
I det här steget använder du Azure Portal för att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactory**.

1.  När du har loggat in på [Azure Portal](https://portal.azure.com), klickar du på **+ NYTT** i det övre vänstra hörnet, väljer **Dataanalys** på bladet **Skapa** och klickar på **Data Factory** på bladet **Dataanalys**. 

    ![Nytt->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. På bladet **Ny datafabrik**:
    1. Ange **ADFTutorialDataFactory** som **namn**. 
    
        ![Bladet Ny datafabrik](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
    2. Klicka på **RESURSGRUPPENS NAMN** och gör följande:
        1. Klicka på **Skapa en ny resursgrupp**.
        2. På bladet **Skapa resursgrupp** anger du **ADFTutorialResourceGroup** som **namn** på resursgruppen och klickar på **OK**. 

            ![Skapa resursgrupp](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

        Vissa av stegen i den här självstudien förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../resource-group-overview.md).  
7. På bladet **Ny datafabrik** ser du att **Lägg till på startsidan** har valts.
8. Klicka på **Skapa** på bladet **Ny datafabrik**.

    Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: **Datafabriksnamnet ”ADFTutorialDataFactory” är inte tillgängligt**, ändrar du namnet på datafabriken (till exempel yournameADFTutorialDataFactory) och försöker skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.  
     
    ![Datafabriksnamnet är inte tillgängligt](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
    
    > [AZURE.NOTE] Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.  

9. Klicka på hubben **MEDDELANDEN** till vänster och se om det finns några meddelanden från processen. Klicka på **X** för att stänga bladet **MEDDELANDEN** om det är öppet. 
10. När datafabriken har skapats visas bladet **DATAFABRIK** enligt nedanstående bild:

    ![Datafabrikens startsida](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## Skapa pipeline

1. På startsidan Datafabrik klickar du på ikonen **Kopiera data** för att starta **guiden Kopiera**. 

    > [AZURE.NOTE] Om du ser att webbläsaren har fastnat på ”Auktoriserar...”, inaktiverar/avmarkerar du inställningen **Blockera cookies från tredje part och platsdata** (eller) behåller den aktiverad och skapar ett undantag för **login.microsoftonline.com**. Försök sedan starta guiden igen.
2. På sidan **Egenskaper**:
    1. Ange **CopyFromBlobToAzureSql** som **aktivitetsnamn**
    2. Ange en **beskrivning** (valfritt).
    3. Observera **Startdatum/tid** och **Slutdatum/tid**. Ändra **Slutdatum/tid** till dagen efter **Startdatum/tid**. 
    3. Klicka på **Nästa**.  

    ![Verktyget Kopiera – sidan Egenskaper](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. På sidan **Källans datalager** klickar du på ikonen **Azure blobblagring**. Du kan använda den här sidan till att ange källans datalager för kopieringsaktiviteten. Du kan använda en länkad tjänst för ett befintligt datalager (eller) ange ett nytt datalager. Om du vill använda en befintlig länkad tjänst klickar du på **FRÅN BEFINTLIGA LÄNKADE TJÄNSTER** och väljer lämplig länkad tjänst. 

    ![Verktyget Kopiera – sidan Källans datalager](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. På sidan **Ange konto för Azure blobblagring**:
    1. Ange **AzureStorageLinkedService** som **Namn på länkad tjänst**.
    2. Kontrollera **Från Azure-prenumerationer** för **Val av kontometod**. 
    3. Välj ett **Azure-lagringskonto** i listan med Azure-lagringskonton som är tillgängliga för den prenumeration som du har valt. Du kan också välja att ange inställningar för lagringskontot manuellt genom att välja alternativet **Ange manuellt** för **Val av kontometod**. Klicka sedan på **Nästa**. 

    ![Verktyget Kopiera – Ange konto för Azure blobblagring](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. På sidan **Välj indatafil eller mapp**:
    1. Navigera till mappen **adftutorial**.
    2. Välj **emp.txt** och klicka på **Välj**
    3. Klicka på **Nästa**. 

    ![Verktyget Kopiera – Välj indatafil eller mapp](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. På sidan **Välj indatafil eller mapp** klickar du på **Nästa**. Välj inte **Binär kopia**. 

    ![Verktyget Kopiera – Välj indatafil eller mapp](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
8. På sidan **Filformatinställningar** väljer du **standard**värden och klickar på **Nästa**.

    ![Verktyget Kopiera – Filformatinställningar](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. På sidan Måldatalager klickar du på ikonen **Azure SQL Database** och sedan på **Nästa**.
9. På sidan **Ange Azure SQL-databas**:
    1. Ange **AzureSqlLinkedService** i fältet **Namn på länkad tjänst**. 
    2. Kontrollera att **Urvalsmetod för server/databas** är inställd på **Från Azure-prenumerationer**.
    3. Välj **Servernamn** och **Databas**.
    4. Ange **Användarnamn** och **Lösenord**.
    5. Klicka på **Nästa**.  
9. På sidan **Tabellmappning** väljer du **emp** i fältet **Mål** i listrutan. Klicka på **nedåtpilen** (valfritt) för att visa schemat och för att förhandsgranska datan.

    ![Verktyget Kopiera – Tabellmappning](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
10. På sidan **Schemamappning** klickar du på **Nästa**.
11. På sidan **Prestandainställningar** klickar du på **Nästa**. 
11. Granska informationen på sidan **Sammanfattning** och klicka på **Slutför**. Guiden skapar två länkade tjänster, två datauppsättningar (indata och utdata) och en pipeline i datafabriken (från den plats där du startade guiden Kopiera). 
12. På sidan **Distributionen är klar** klickar du på länken: **Klicka här för att övervaka kopiering av pipeline**.

    ![Verktyget Kopiera – Distributionen är klar](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
13. Använd anvisningarna i [Övervaka och hantera pipeline med övervakningsappen](data-factory-monitor-manage-app.md) för att få mer information om hur du övervakar den pipeline som du skapade. Klicka på ikonen **Uppdatera** i listan **AKTIVITETSFÖNSTER** för att se sektorn. 

    ![Övervakningsapp](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png) 
 

## Se även
| Avsnitt | Beskrivning |
| :---- | :---- |
| [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) | Den här artikeln innehåller detaljerad information om kopieringsaktiviteten som du använde i självstudien. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) | I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) | I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) | I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory.
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) | Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. 


<!--HONumber=Sep16_HO3-->


