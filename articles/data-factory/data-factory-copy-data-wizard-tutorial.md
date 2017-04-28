---
title: "Självstudie: Skapa en pipeline med Copy Wizard | Microsoft Docs"
description: "I de här självstudierna skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet, med hjälp av guiden Kopiera som stöds av Data Factory"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/24/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: fbf77e9848ce371fd8d02b83275eb553d950b0ff
ms.openlocfilehash: 5a50f583831b398ae22416e7ade23c33846de55c
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Guiden Data Factory-kopia
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

Med **Guiden Kopiera** i Azure Data Factory kan du snabbt och enkelt skapa en pipeline som implementerar datainmatning/rörelsescenarier. Därför rekommenderar vi att du använder guiden som ett första steg för att skapa en exempel-pipeline för dataflödescenarier. Den här guiden beskriver hur du skapar en Azure-datafabrik. Starta guiden Kopiera, och utför ett antal åtgärder för att ge information om datainmatning/rörelsescenarier. När du slutför stegen i guiden skapar guiden automatiskt en pipeline med en kopieringsaktivitet som kopierar data från en Azure-blob till en Azure SQL-databas. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. 

## <a name="prerequisites"></a>Krav
- Gå igenom [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för att få en översikt av självstudierna och slutför de **nödvändiga** stegen.


## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure-portalen för att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactory**.

1. När du har loggat in på [Azure-portalen](https://portal.azure.com) klickar du på **+ NY** i det övre vänstra hörnet. Klicka sedan på **Information + analys** och **Datafabrik**. 
   
   ![Nytt->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. På bladet **Ny datafabrik**:
   
   1. Ange **ADFTutorialDataFactory** som **namn**.
       Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: **Datafabriksnamnet ”ADFTutorialDataFactory” är inte tillgängligt**, ändrar du namnet på datafabriken (till exempel yournameADFTutorialDataFactory) och försöker skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.  
      
       ![Datafabriksnamnet är inte tillgängligt](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
      
      > [!NOTE]
      > Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
      > 
      > 
   2. Välj din Azure-**prenumeration**.
   3. För resursgruppen utför du något av följande steg: 
      
      - Välj **Använd befintlig** och välj en befintlig resursgrupp.
      - Välj **Skapa nytt** och ange ett namn för en resursgrupp.
         
          Vissa av stegen i den här självstudien förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).
   4. Välj **plats** för datafabriken.
   5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.  
   6. Klicka på **Skapa**.
      
       ![Bladet Ny datafabrik](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. När datafabriken har skapats visas bladet **Datafabrik** enligt nedanstående bild:
   
   ![Datafabrikens startsida](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Använda guiden Kopiera
1. På startsidan Datafabrik klickar du på ikonen **Kopiera data** för att starta **guiden Kopiera**. 
   
   > [!NOTE]
   > Om du ser att webbläsaren har fastnat på ”Auktoriserar...”, inaktiverar/avmarkerar du inställningen **Blockera cookies från tredje part och platsdata** (eller) behåller den aktiverad och skapar ett undantag för **login.microsoftonline.com**. Försök sedan starta guiden igen.
   > 
   > 
2. På sidan **Egenskaper**:
   
   1. Ange **CopyFromBlobToAzureSql** som **aktivitetsnamn**
   2. Ange en **beskrivning** (valfritt).
   3. Ändra **tid, startdatum** och **tid, slutdatum** så att slutdatumet är dagens datum och startdatum är fem dagar före dagens datum.  
   4. Klicka på **Next**.  
      
      ![Verktyget Kopiera – sidan Egenskaper](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. På sidan **Källans datalager** klickar du på ikonen **Azure blobblagring**. Du kan använda den här sidan till att ange källans datalager för kopieringsaktiviteten. Du kan använda en länkad tjänst för ett befintligt datalager (eller) ange ett nytt datalager. Om du vill använda en befintlig länkad tjänst klickar du på **FRÅN BEFINTLIGA LÄNKADE TJÄNSTER** och väljer lämplig länkad tjänst. 
   
    ![Verktyget Kopiera – sidan Källans datalager](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. På sidan **Ange konto för Azure blobblagring**:
   
   1. Ange **AzureStorageLinkedService** som **Namn på länkad tjänst**.
   2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
   3. Välj din Azure-**prenumeration**.  
   4. Välj ett **Azure-lagringskonto** i listan med Azure-lagringskonton som är tillgängliga för den prenumeration som du har valt. Du kan också välja att ange inställningar för lagringskontot manuellt genom att välja alternativet **Ange manuellt** för **Val av kontometod**. Klicka sedan på **Nästa**. 
      
      ![Verktyget Kopiera – Ange konto för Azure blobblagring](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. På sidan **Välj indatafil eller mapp**:
   
   1. Navigera till mappen **adftutorial**.
   2. Välj **emp.txt** och klicka på **Välj**
   3. Klicka på **Nästa**. 
      
      ![Verktyget Kopiera – Välj indatafil eller mapp](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. På sidan **Välj indatafil eller mapp** klickar du på **Nästa**. Välj inte **Binär kopia**. 
   
    ![Verktyget Kopiera – Välj indatafil eller mapp](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. På den **formatinställningar** kan du se avgränsare och det schema som upptäcks automatiskt i guiden genom att parsa filen. Du kan också ange avgränsare manuellt så att guiden Kopiera inte identifierar eller åsidosätter automatiskt. Klicka på **Nästa** när du har granskat avgränsarna och förhandsgranskat data. 
   
    ![Verktyget Kopiera – Filformatinställningar](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. På sidan Måldatalager klickar du på ikonen **Azure SQL-Database** och sedan på **Nästa**.
   
    ![Verktyget kopiera - Välj målarkiv](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. På sidan **Ange Azure SQL-databas**:
   
   1. Ange **AzureSqlLinkedService** i fältet **Anslutningsnamn**.
   2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för server/databasval**.
   3. Välj din Azure-**prenumeration**.  
   4. Välj **Servernamn** och **Databas**.
   5. Ange **Användarnamn** och **Lösenord**.
   6. Klicka på **Next**.  
      
      ![Verktyget Kopiera - Ange Azure SQL-databas](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. På sidan **Tabellmappning** väljer du **emp** i fältet **Mål** i listrutan. Klicka på **nedåtpilen** (valfritt) för att visa schemat och för att förhandsgranska datan.
    
     ![Verktyget Kopiera – Tabellmappning](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. På sidan **Schemamappning** klickar du på **Nästa**.
    
    ![Verktyget Kopiera - schemamappning](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. På sidan **Prestandainställningar** klickar du på **Nästa**. 
    
    ![Verktyget Kopiera - prestandainställningar](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Granska informationen på sidan **Sammanfattning** och klicka på **Slutför**. Guiden skapar två länkade tjänster, två datauppsättningar (indata och utdata) och en pipeline i datafabriken (från den plats där du startade guiden Kopiera). 
    
    ![Verktyget Kopiera - prestandainställningar](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Starta övervakning och hantera program
1. På **Distribution**-sidan, klickar du på länken: `Click here to monitor copy pipeline`.
   
   ![Verktyget Kopiera – Distributionen är klar](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. Använd anvisningarna i [Övervaka och hantera pipeline med övervakningsappen](data-factory-monitor-manage-app.md) för att få mer information om hur du övervakar den pipeline som du skapade. Klicka på ikonen **Uppdatera** i listan **AKTIVITETSFÖNSTER** för att se sektorn. 
   
   ![Övervakningsapp](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png) 
   
   
   Klicka på knappen **Uppdatera** längst ner i listan **AKTIVITETSFÖNSTER ** för att se senaste status. Den uppdateras inte automatiskt. 

> [!NOTE]
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg din första pipeline för att omvandla data med Hadoop-kluster).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory).

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |


