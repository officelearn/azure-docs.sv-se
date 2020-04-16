---
title: Transformering med Azure Databricks
description: Lär dig hur du använder en lösningsmall för att omvandla data med hjälp av en Databricks-anteckningsbok i Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 65b89a13637f5a4e1712995a6ac58d88b4421806
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414853"
---
# <a name="transformation-with-azure-databricks"></a>Transformering med Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien skapar du en end-to-end-pipeline som innehåller **aktiviteterna Verifiering,** **Kopiera data**och **Anteckningsbok** i Azure Data Factory.

- **Valideringen** säkerställer att källdatauppsättningen är klar för nedströmsförbrukning innan du utlöser kopierings- och analysjobbet.

- **Kopiera data** duplicerar källdatauppsättningen till diskhonlagringen, som är monterad som DBFS i azure databricks-anteckningsboken. På så sätt kan datauppsättningen direkt förbrukas av Spark.

- **Anteckningsboken** utlöser den bärbara datorn Databricks som omvandlar datauppsättningen. Den lägger också till datauppsättningen i en bearbetad mapp eller Azure SQL Data Warehouse.

För enkelhetens skull skapar mallen i den här självstudien ingen schemalagd utlösare. Du kan lägga till en om det behövs.

![Diagram över rörledningen](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Krav

- Ett Azure Blob-lagringskonto `sinkdata` med en behållare som kallas för användning som en mottagare.

  Anteckna lagringskontonamnet, behållarnamnet och åtkomstnyckeln. Du behöver dessa värden senare i mallen.

- En Azure Databricks-arbetsyta.

## <a name="import-a-notebook-for-transformation"></a>Importera en anteckningsbok för omvandling

Så här **Transformation** importerar du en transformationsanteckningsbok till databricksarbetsytan:

1. Logga in på din Azure Databricks-arbetsyta och välj sedan **Importera**.
       ![Menykommando för import](media/solution-template-Databricks-notebook/import-notebook.png) av en arbetsyta Arbetsytans sökväg kan skilja sig från den som visas, men kom ihåg den senare.
1. Välj **Importera från: URL**. Skriv i `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`textrutan .

   ![Val för import av en anteckningsbok](media/solution-template-Databricks-notebook/import-from-url.png)

1. Nu ska vi uppdatera anteckningsboken **Transformation** med din lagringsanslutningsinformation.

   Gå till **kommando 5** i den importerade anteckningsboken enligt följande kodavsnitt.

   - Ersätt `<storage name>` `<access key>` och med din egen lagringsanslutningsinformation.
   - Använd lagringskontot `sinkdata` med behållaren.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Generera en **Databricks-åtkomsttoken** för Data Factory för att komma åt Databricks.
   1. I din Databricks-arbetsyta väljer du din användarprofilikon längst upp till höger.
   1. Välj **Användarinställningar**.
    ![Menykommando för användarinställningar](media/solution-template-Databricks-notebook/user-setting.png)
   1. Välj **Generera ny token** under fliken **Åtkomsttoken.**
   1. Välj **Generera**.

    ![Knappen "Generera"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Spara åtkomsttoken* för senare användning för att skapa en Databricks-länkad tjänst. Åtkomsttoken ser `dapi32db32cbb4w6eee18b7d87e45exxxxxx`ut ungefär som .

## <a name="how-to-use-this-template"></a>Så här använder du den här mallen

1. Gå till mallen **Transformation with Azure Databricks** och skapa nya länkade tjänster för följande anslutningar.

   ![Inställning av anslutningar](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Källa Blob-anslutning** - för att komma åt källdata.

       I den här övningen kan du använda den offentliga blob-lagring som innehåller källfilerna. Referera till följande skärmbild för konfigurationen. Använd följande **SAS-URL** för att ansluta till källlagring (skrivskyddad åtkomst):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Val för autentiseringsmetod och SAS-URL](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Målblobanslutning** - om du vill lagra kopierade data.

       I fönstret **Nytt länkat tjänst** väljer du din sink storage-blob.

       ![Sink storage blob som en ny länkad tjänst](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** - för att ansluta till Databricks-klustret.

        Skapa en Databricks-länkad tjänst med hjälp av åtkomstnyckeln som du genererade tidigare. Du kan välja att välja ett *interaktivt kluster* om du har ett. I det här exemplet används alternativet **Nytt jobbkluster.**

        ![Val för anslutning till klustret](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Välj **Använd den här mallen**. Du ser en pipeline skapad.

    ![Skapa en pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introduktion och konfiguration av pipeline

I den nya pipelinen konfigureras de flesta inställningar automatiskt med standardvärden. Granska konfigurationerna för pipelinen och gör nödvändiga ändringar.

1. Kontrollera att **källdatauppsättningsvärdet** är inställt på det som `SourceAvailabilityDataset` du skapade tidigare i flaggan Tillgänglighet för **valideringsaktivitet.** **Availability flag**

   ![Värde för källdatauppsättning](media/solution-template-Databricks-notebook/validation-settings.png)

1. Kontrollera flikarna Källa och **Sink** i **fil-till-blob-loben** **Kopiera** **dataaktivitet.** Ändra inställningar om det behövs.

   - **Source** Fliken ![Källa Källa](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Fliken ![Sink för **handfat** för handfat](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Granska och uppdatera sökvägar och inställningar efter behov i **aktivitetsomvandlingen**för **anteckningsbok.**

   **Databricks länkad tjänst** ska fyllas i i förfyllnad ![med värdet från ett tidigare steg, som visad: Fyll i värde för den länkade databricks-tjänsten](media/solution-template-Databricks-notebook/notebook-activity.png)

   Så här kontrollerar du inställningarna **för anteckningsboken:**
  
    1. Välj fliken **Inställningar.** Kontrollera att standardsökvägen är korrekt för **sökväg för anteckningsbok.** Du kan behöva bläddra och välja rätt sökväg till anteckningsboken.

       ![Sökväg till anteckningsbok](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Expandera **väljaren Basparametrar** och kontrollera att parametrarna matchar det som visas i följande skärmbild. Dessa parametrar skickas till databricks-anteckningsboken från Data Factory.

       ![Basparametrar](media/solution-template-Databricks-notebook/base-parameters.png)

1. Kontrollera att **pipelineparametrarna** matchar vad ![som visas i följande skärmbild: Pipelineparametrar](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Anslut till dina datauppsättningar.

   - **SourceAvailabilityDataset** - för att kontrollera att källdata är tillgängliga.

     ![Val för länkad tjänst och filsökväg för SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - för att komma åt källdata.

       ![Val för länkad tjänst och filsökväg för SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - för att kopiera data till sink-målplatsen. Ange följande värden:

     - **Länkad tjänst** - `sinkBlob_LS`, skapad i ett tidigare steg.

     - **Sökväg till fil** - `sinkdata/staged_sink`.

       ![Val för länkad tjänst och filsökväg för DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Välj **Felsöka** om du vill köra pipelinen. Du hittar länken till Databricks loggar för mer detaljerade Spark loggar.

    ![Länk till Databricks loggar från utdata](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Du kan också verifiera datafilen med hjälp av Azure Storage Explorer.

    > [!NOTE]
    > För korrelerar med Data Factory pipeline körs, lägger det här exemplet pipeline kör ID från datafabriken till utdatamappen. Detta hjälper till att hålla reda på filer som genereras av varje körning.
    > ![Bifogat pipeline-kör-ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
