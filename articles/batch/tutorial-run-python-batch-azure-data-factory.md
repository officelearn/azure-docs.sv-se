---
title: Kör Python-skript via Data Factory - Azure Batch Python
description: Självstudiekurs - Lär dig hur du kör Python-skript som en del av en pipeline via Azure Data Factory med Azure Batch.
services: batch
author: mammask
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 2995c5da4491f14471d9ed03022a144a02beab5a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201838"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Självstudiekurs: Kör Python-skript via Azure Data Factory med Azure Batch

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * autentisera med Batch- och Storage-konton
> * Utveckla och köra ett skript i Python
> * skapa en pool med beräkningsnoder för att köra ett program
> * Schemalägg dina Python-arbetsbelastningar
> * Övervaka din analyspipeline
> * Komma åt dina loggfiler

Exemplet nedan kör ett Python-skript som tar emot CSV-indata från en blob-lagringsbehållare, utför en datamanipuleringsprocess och skriver utdata till en separat blob-lagringsbehållare.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

* En installerad [Python-distribution](https://www.python.org/downloads/) för lokal testning.
* [Azure-paketet.](https://pypi.org/project/azure/) `pip`
* Ett Azure Batch-konto och ett länkat Azure Storage-konto. Se [Skapa ett batchkonto](quick-create-portal.md#create-a-batch-account) för mer information om hur du skapar och länkar batchkonton till lagringskonton.
* Ett Azure Data Factory-konto. Se [Skapa en datafabrik](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) för mer information om hur du skapar en datafabrik via Azure-portalen.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Utforskaren för Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Skapa en batchpool med Batch Explorer

I det här avsnittet ska du använda Batch Explorer för att skapa batchpoolen som din Azure Data-fabrikspipeline ska använda. 

1. Logga in på Batch Explorer med dina Azure-autentiseringsuppgifter.
1. Välj ditt batchkonto
1. Skapa en pool genom att välja **Pooler** i det vänstra sidofältet och sedan knappen **Lägg till** ovanför sökformuläret. 
    1. Välj ett ID och visningsnamn. Vi använder `custom-activity-pool` för det här exemplet.
    1. Ange skalningstypen till **Fast storlek**och ange antalet dedikerade noder till 2.
    1. Under **Data science**väljer du **Dsvm Windows** som operativsystem.
    1. Välj `Standard_f2s_v2` som storlek på den virtuella datorn.
    1. Aktivera startuppgiften och `cmd /c "pip install pandas"`lägg till kommandot . Användaridentiteten kan förbli **standardanvändaren**för pool.
    1. Välj **OK**.

## <a name="create-blob-containers"></a>Skapa blob-behållare

Här ska du skapa blob-behållare som lagrar dina indata- och utdatafiler för OCR Batch-jobbet.

1. Logga in i Storage Explorer med dina Azure-autentiseringsuppgifter.
1. Med hjälp av lagringskontot som är länkat till ditt Batch-konto skapar du två blob-behållare (en för indatafiler, en för utdatafiler) genom att följa stegen i [Skapa en blob-behållare](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * I det här exemplet anropar `input`vi vår indatabehållare och vår utdatabehållare `output`.
1. Ladda `main.py` `iris.csv` upp och `input` till indatabehållaren med Storage Explorer genom att följa stegen vid [Hantera blobbar i en blob-behållare](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)


## <a name="develop-a-script-in-python"></a>Utveckla ett skript i Python

Följande Python-skript `iris.csv` läser in datauppsättningen från `input` behållaren, utför en datamanipuleringsprocess och sparar resultaten tillbaka till behållaren. `output`

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Spara skriptet `main.py` som och ladda upp det till Azure Storage-behållaren. **Azure Storage** Var noga med att testa och validera dess funktioner lokalt innan du laddar upp den till din blob-behållare:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Konfigurera en Azure Data Factory-pipeline

I det här avsnittet ska du skapa och validera en pipeline med ditt Python-skript.

1. Följ stegen för att skapa en datafabrik under avsnittet "Skapa en datafabrik" [i den här artikeln](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. I rutan **Fabriksresurser** väljer du knappen + (plus) och väljer sedan **Pipeline**
1. På fliken **Allmänt** anger du namnet på pipelinen som "Kör Python"

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Expandera **Batchservice**i rutan **Aktiviteter** . Dra den anpassade aktiviteten från **verktygslådan Aktiviteter** till pipelinedesignerytan.
1. På fliken **Allmänt** anger du **testPipeline** för Namn

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. På fliken **Azure Batch** lägger du till **batchkontot** som skapades i föregående steg och **Test-anslutning** för att säkerställa att det lyckas

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Ange **Settings** kommandot `python main.py`.
1. Lägg till lagringskontot som skapades i föregående steg för den **resurslänkade tjänsten.** Testa anslutningen för att säkerställa att den lyckas.
1. I **mappsökvägen**väljer du namnet på **Azure Blob** Storage-behållaren som innehåller Python-skriptet och tillhörande indata. Detta hämtar de valda filerna från behållaren till poolnodinstanserna före körningen av Python-skriptet.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet för pipelinen. Bekräfta att pipelinen har verifierats. Du stänger utdata från verifieringen genom att välja &gt;&gt; (högerpil).
1. Klicka på **Felsökning** för att testa pipelinen och se till att den fungerar korrekt.
1. Klicka på **Publicera** om du vill publicera pipelinen.
1. Klicka på **Utlösare** om du vill köra Python-skriptet som en del av en batchprocess.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Övervaka loggfilerna

Om varningar eller fel uppstår genom att skriptet körs kan `stdout.txt` `stderr.txt` du checka ut eller för mer information om utdata som loggats.

1. Välj **Jobb** till vänster i Batch Explorer.
1. Välj det jobb som skapats av datafabriken. Om du har `custom-activity-pool`namngett `adfv2-custom-activity-pool`poolen väljer du .
1. Klicka på aktiviteten som hade en felutträdeskod.
1. Visa `stdout.txt` `stderr.txt` och undersöka och diagnostisera ditt problem.

## <a name="next-steps"></a>Nästa steg

I den här självstudien utforskade du ett exempel som lärde dig hur du kör Python-skript som en del av en pipeline via Azure Data Factory med Azure Batch.

Mer information om Azure Data Factory finns i:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [Pipelines och aktiviteter](../data-factory/concepts-pipelines-activities.md)
> [Anpassade aktiviteter](../data-factory/transform-data-using-dotnet-custom-activity.md)
