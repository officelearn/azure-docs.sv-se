---
title: Kör en Databricks Notebook med Databricks Notebook-aktiviteten i Azure Data Factory
description: Lär dig hur du kan använda Databricks Notebook-aktiviteten i Azure Data Factory för att köra en Databricks Notebook mot Databricks-jobbklustret.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 78954e2dd00e425d2dfdd81d2c3e386f199f4f8f
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311054"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Kör en Databricks Notebook med Databricks Notebook-aktiviteten i Azure Data Factory

I den här självstudien använder du Azure Portal för att skapa en Azure Data Factory-pipeline som kör en Databricks Notebook mot Databricks-jobbklustret. Den skickar även Azure Data Factory-parametrar till Databricks Notebook under körning.

I den här självstudiekursen får du göra följande:

  - Skapa en datafabrik.

  - Skapa en pipeline som använder en Databricks Notebook-aktivitet.

  - Utlös en pipelinekörning.

  - Övervaka pipelinekörningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Nödvändiga komponenter

  - **Azure Databricks-arbetsyta**. [Skapa en ny Azure Databricks-arbetsyta](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) eller använd en befintlig. Du skapar en Python Notebook i Azure Databricks-arbetsytan. Sedan kör du denna notebook och skickar parametrar till den med hjälp av Azure Data Factory.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1.  Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.

2.  Välj **Nytt** på den vänstra menyn, välj **Data och analys** och välj sedan **Data Factory**.

    ![Skapa en ny datafabrik](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  I fönstret **Ny datafabrik** anger du **ADFTutorialDataFactory** under **Namn**.

    Namnet på Azure Data Factory måste vara *globalt unikt*. Om du ser följande fel ska du ändra namnet på datafabriken. (Använd till exempel **\<dittnamn\>ADFTutorialDataFactory**). Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](https://docs.microsoft.com/azure/data-factory/naming-rules).

    ![Ange ett namn för den nya datafabriken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**.

5.  Gör något av följande för **Resursgrupp**:
    
    - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.
    
    - Välj **Skapa ny** och ange namnet på en resursgrupp.

    Vissa av stegen i den här snabbstarten förutsätter att du använder namnet **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  För **Version** väljer du **V2 (förhandsversion)**.

2.  För **Plats** väljer du en plats för datafabriken.

    För närvarande kan du endast skapa datafabriker i Data Factory V2 i regionerna USA, östra; USA; östra 2 och Europa, västra. De datalager (som Azure Storage och Azure SQL Database) och beräkningar (som HDInsight) som används i Data Factory kan finnas i andra regioner.

3.  Välj **fäst till instrumentpanelen**.

4.  Välj **Skapa**.

5.  Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**:

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  När datafabriken har skapats visas sidan **Datafabrik**. Välj ikonen **Författare och övervakare** för att starta programmet Data Factory UI på en separat flik.

    ![Starta användargränssnittsprogrammet för datafabriken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster

I det här avsnittet skapar du en Databricks-länkad tjänst. Den här länkade tjänsten innehåller anslutningsinformation till Databricks-klustret:

### <a name="create-an-azure-databricks-linked-service"></a>Skapa en länkad Azure Databricks-tjänst

1.  På sidan **Nu sätter vi igång** växlar du till fliken **Redigera** i den vänstra panelen.

    ![Redigera den nya länkade tjänsten](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  Välj **Anslutningar** längst ned i fönstret och sedan **+Ny**.
    
    ![Skapa en ny anslutning](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  I fönstret **Ny länkad tjänst** väljer du **Data Store** \> **Azure Databricks** och klickar på **Fortsätt**.
    
    ![Ange en länkad Databricks-tjänst](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):
    
    1.  Som **Namn** anger du ***AzureDatabricks\_LinkedService***
    
    2.  Som **Kluster** väljer du ett **Nytt kluster**
    
    3.  Som **Domän/Region** väljer du den region där Azure Databricks-arbetsytan finns.
    
    4.  Som **Klusternodtyp** väljer du **Standard\_D3\_v2** för den här självstudiekursen.
    
    5.  Generera en **Åtkomsttoken** från Azure Databricks-arbetsplatsen. Du hittar anvisningar [här](https://docs.databricks.com/api/latest/authentication.html#generate-token).
    
    6.  Som **Klusterversion** väljer du **4.0 Beta** (senaste versionen)
    
    7.  Som **Antal arbetsnoder** anger du **2**.
    
    8.  Välj **Slutför**

        ![Slutför skapandet av den länkade tjänsten](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>Skapa en pipeline

1.  Välj knappen **+** (plus) och sedan **Pipeline** från menyn.

    ![Knappar för att skapa en ny pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  Skapa en **parameter** som ska användas i **pipelinen**. Senare kan du skicka den här parametern till Databricks Notebook-aktiviteten. I den tomma pipelinen klickar du på fliken **Parametrar**, sedan på **Ny** och ger den namnet ”**namn**”.

    ![Skapa en ny parameter](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Skapa namnparametern](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  Gå till verktygsfältet **Aktiviteter** och expandera **Databricks**. Dra aktiviteten **Notebook** från verktygsfältet **Aktiviteter** till pipelinedesignytan.

    ![Dra denna notebook till designytan](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  Utför följande steg i egenskaperna för **Databricks** **Notebook**-aktivitetsfönstret längst ned:

    a. Växla till fliken **Settings** (Inställningar).

    b. Välj **myAzureDatabricks\_LinkedService** (som du skapade i föregående procedur).

    c. Välj en Databricks **Notebook-sökväg**. Nu ska vi skapa en notebook och ange sökvägen här. Du kan hämta Notebook-sökvägen genom att följa de kommande stegen.

       1. Starta din Azure Databricks-arbetsyta

       2. Skapa en **Ny mapp** i arbetsplatsen och ge den namnet **adftutorial**.

          ![Skapa en ny mapp](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [Skapa en ny Notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) och namnge den **mynotebook** under **adftutorial**-mappen **,** klicka sedan på **skapa.**

          ![Skapa en ny notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Ange egenskaperna för denna nya notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. I din nyligen skapade notebook, ”mynotebook”, lägger du till följande kod:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Skapa widgetar för parametrar](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. **Notebook-sökvägen** är i det här fallet **/adftutorial/mynotebook**

5.  Växla tillbaka till **redigeringsverktyget för Data Factory-användargränssnittet**. Gå till fliken **inställningar** under **Aktiviteten Notebook1**. 
    
    a.  **Lägg till parameter** i Notebook-aktiviteten. Du använder samma parameter som du lade till tidigare i **Pipeline**.

       ![Lägg till parameter](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    b.  Namnge parametern **indata** och ange värdet som uttrycket **@pipeline().parameters.name**.

6.  Verifiera pipelinen genom att välja knappen **Verifiera** i verktygsfältet. Klicka på knappen **\>\>** (högerpil) för att stänga verifieringsfönstret.

    ![Verifiera pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  Välj **Publicera alla**. Data Factory-gränssnittet publicerar entiteter (länkade tjänster och pipelines) till Azure Data Factory-tjänsten.

    ![Publicera de nya datafabriksentiteterna](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Utlös en pipelinekörning

Välj **Utlös** i verktygsfältet och sedan **Trigger Now** (Utlös nu).

![Välj kommandot Utlös nu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

Dialogrutan **Kör pipeline** efterfrågar parametern **namn**. Använd **/path/filename** som den här parametern. Klicka på **Slutför.**

![Ange ett värde för namnparametrarna](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1.  Välj fliken **Övervaka**. Bekräfta att du ser en pipelinekörning. Det tar cirka 5–8 minuter att skapa ett Databricks-jobbkluster där en notebook körs.

    ![Övervaka pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  Klicka på **Uppdatera** då och då så att du ser pipelinekörningens status.

3.  Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna väljer på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Åtgärd**.

    ![Visa aktivitetskörningar](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Du kan gå tillbaka till vyn över pipelinekörningar genom att välja länken **Pipelines**.

## <a name="verify-the-output"></a>Verifiera utdata

Du kan logga in på **Azure Databricks-arbetsytan**, gå till **Kluster** och sedan se status för **jobbet**: *väntar på att köras, körs eller har avslutats*.

![Visa jobbklustret och jobbet](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Du kan klicka på **jobbnamnet** och navigera för att se ytterligare information. Om körningen lyckas kan du validera parametrarna som skickats samt utdata för en Python Notebook.

![Visa körningsinformation och utdata](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>Nästa steg

Pipelinen i det här exemplet utlöser en Databricks Notebook-aktivitet och skickar en parameter till den. Du har lärt dig att:

  - Skapa en datafabrik.

  - Skapa en pipeline som använder en Databricks Notebook-aktivitet.

  - Utlös en pipelinekörning.

  - Övervaka pipelinekörningen.
