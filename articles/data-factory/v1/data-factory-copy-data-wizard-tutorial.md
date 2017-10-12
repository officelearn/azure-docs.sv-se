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
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 6b42ad8d52f2c25327508f8fbfa14292169d1c05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

Den här kursen visar hur du använder den **Kopieringsguiden** för att kopiera data från ett Azure blob storage till en Azure SQL database. 

Med **Kopieringsguiden** för Azure Data Factory kan du snabbt skapa en pipeline för data som kopierar data från ett dataarkiv som stöds till ett måldataarkiv som stöds. Därför rekommenderar vi att du använder guiden som ett första steg för att skapa en exempel-pipeline för dataflödescenarier. En lista över datakällor som stöds som källor och mottagare finns i [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

Den här guiden beskriver hur du skapar en Azure-datafabrik. Starta guiden Kopiera, och utför ett antal åtgärder för att ge information om datainmatning/rörelsescenarier. När du slutför stegen i guiden skapar guiden automatiskt en pipeline med en kopieringsaktivitet som kopierar data från en Azure-blob till en Azure SQL-databas. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.

## <a name="prerequisites"></a>Krav
Slutför stegen i artikeln [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) innan du fortsätter med självstudierna.

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure-portalen för att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactory**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **+NY** på den vänstra menyn, klicka på **Data + Analys** och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. På bladet **Ny datafabrik**:
   
   1. Ange **ADFTutorialDataFactory** som **namn**.
       Namnet på Azure Data Factory måste vara globalt unikt. Om följande fel returneras: `Data factory name “ADFTutorialDataFactory” is not available` ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactoryÅÅÅÅMMDD) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.  
      
       ![Datafabriksnamnet är inte tillgängligt](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Välj din Azure-**prenumeration**.
   3. För resursgruppen utför du något av följande steg: 
      
      - Välj **Använd befintlig** och välj en befintlig resursgrupp.
      - Välj **Skapa nytt** och ange ett namn för en resursgrupp.
          
        Vissa av stegen i den här självstudien förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../../azure-resource-manager/resource-group-overview.md).
   4. Välj **plats** för datafabriken.
   5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.  
   6. Klicka på **Skapa**.
      
       ![Bladet Ny datafabrik](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. När datafabriken har skapats visas bladet **Datafabrik** enligt nedanstående bild:
   
   ![Datafabrikens startsida](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Använda guiden Kopiera
1. På bladet Datafabrik klickar du på ikonen **Kopiera data [FÖRHANDSVERSION]** för att starta **guiden Kopiera**. 
   
   > [!NOTE]
   > Om du ser att webbläsaren har fastnat på ”Auktoriserar...”, inaktiverar/avmarkerar du inställningen **Blockera cookies från tredje part och platsdata** (eller) behåller den aktiverad och skapar ett undantag för **login.microsoftonline.com**. Försök sedan starta guiden igen.
2. På sidan **Egenskaper**:
   
   1. Ange **CopyFromBlobToAzureSql** som **aktivitetsnamn**
   2. Ange en **beskrivning** (valfritt).
   3. Ändra **tid, startdatum** och **tid, slutdatum** så att slutdatumet är dagens datum och startdatum är fem dagar tidigare.  
   4. Klicka på **Nästa**.  
      
      ![Verktyget Kopiera – sidan Egenskaper](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. På sidan **Källans datalager** klickar du på ikonen **Azure blobblagring**. Du kan använda den här sidan till att ange källans datalager för kopieringsaktiviteten. 
   
    ![Verktyget Kopiera – sidan Källans datalager](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. På sidan **Ange konto för Azure blobblagring**:
   
   1. Ange **AzureStorageLinkedService** som **Namn på länkad tjänst**.
   2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
   3. Välj din Azure-**prenumeration**.  
   4. Välj ett **Azure-lagringskonto** i listan med Azure-lagringskonton som är tillgängliga för den prenumeration som du har valt. Du kan också välja att ange inställningar för lagringskontot manuellt genom att välja alternativet **Ange manuellt** för **Val av kontometod**. Klicka sedan på **Nästa**. 
      
      ![Verktyget Kopiera – Ange konto för Azure blobblagring](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. På sidan **Välj indatafil eller mapp**:
   
   1. Dubbelklicka på **adftutorial** (mapp).
   2. Välj **emp.txt** och klicka på **Välj**
      
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
   6. Klicka på **Nästa**.  
      
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
2. Övervakmingsappen startas i en separat flik i webbläsaren.   
   
   ![Övervakningsapp](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Klicka på knappen **Uppdatera** längst ner i listan **AKTIVITETSFÖNSTER** för att se senaste statusarna. Du kan se fem aktivitetsfönster i fem dagar mellan start- och sluttider för din pipeline. Listan uppdateras inte automatiskt, så du kan behöva klicka på uppdatera några gånger innan du ser alla aktivitetsfönster i tillståndet Klar. 
4. Välj ett aktivitetsfönster i listan. Se information om detta i fönstret **Aktivitetshanteraren** till höger.

    ![Information om aktivitetsfönstret](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Lägg märke till att datumen 11, 12, 13, 14 och 15 visas i grön färg, vilket innebär att dagliga utdatasegment för dessa datum har redan skapats. Du kan också se den här färgkodningen på pipeline och datamängd för utdata i diagramvyn. I det föregående steget, lägg märke till att två segment redan har skapats, ett segment bearbetas och de andra två väntar på att bearbetas (baserat på färgkodning). 

    Mer information om hur du använder det här programmet finns i artikeln [Övervaka och hantera pipeline med övervakningsappen](data-factory-monitor-manage-app.md).

## <a name="next-steps"></a>Nästa steg
I den här kursen används Azure blob storage som ett datalager för källa och en Azure SQL-databas som ett dataarkiv som mål i en kopieringsåtgärd. Följande tabell innehåller en lista över datalager som stöds som källor och mål av kopieringsaktiviteten: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Klicka på länken för datalagret i tabellen för mer information om fält/egenskaper som visas i kopieringsguiden för datalager. 