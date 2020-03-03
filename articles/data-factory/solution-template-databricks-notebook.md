---
title: Omvandling med Azure Databricks
description: Lär dig hur du använder en lösnings mall för att transformera data med hjälp av en Databricks Notebook i Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227574"
---
# <a name="transformation-with-azure-databricks"></a>Omvandling med Azure Databricks

I den här självstudien skapar du en pipeline från slut punkt till slut punkt som innehåller **verifierings**-, **kopierings**-och **Notebook** -aktiviteter i Data Factory.

-   **Validerings** aktivitet används för att se till att käll data uppsättningen är klar för efterföljande konsumtion innan du utlöser kopierings-och analys jobbet.

-   **Kopierings** aktiviteten kopierar käll filen/data uppsättningen till mottagar lagringen. Mottagar lagringen monteras som DBFS i antecknings boken för Databricks så att data uppsättningen kan konsumeras direkt av Spark.

-   **Databricks Notebook** -aktivitet utlöser Databricks Notebook som transformerar data uppsättningen och lägger till den i en bearbetad mapp/SQL DW.

För att den här mallen ska vara enkel, skapar mallen ingen schemalagd utlösare. Du kan lägga till det om det behövs.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Förutsättningar

1. Skapa ett **Blob Storage-konto** och en behållare med namnet `sinkdata` som ska användas som **mottagare**. Anteckna **lagrings kontots namn**, **behållar namn**och **åtkomst nyckel**, eftersom de refereras senare i mallen.

2. Se till att du har en **Azure Databricks arbets yta** eller skapa en ny.

3. **Importera den bärbara datorn för omvandling**. 
    1. I Azure Databricks kan du referera till följande skärm dum par för att importera en **Transformation** -anteckningsbok till arbets ytan Databricks. Den behöver inte finnas på samma plats som nedan, men kom ihåg sökvägen du väljer för senare.
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. Välj "Importera från: **URL**" och ange följande URL i text rutan:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. Nu ska vi uppdatera **omvandlings** antecknings boken med information om lagrings anslutningen. Gå till **kommando 5** (som visas i nedanstående kodfragment) i den importerade antecknings boken ovan och ersätt `<storage name>`och `<access key>` med din egen lagrings anslutnings information. Se till att det här kontot är samma lagrings konto som skapades tidigare och att det innehåller `sinkdata` containern.

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

5.  Generera en **Databricks** -åtkomsttoken för Data Factory att få åtkomst till Databricks. **Spara åtkomsttoken** för senare användning i skapa en länkad Databricks-tjänst, som ser ut ungefär som "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>Använda den här mallen

1.  Gå till **transformering med Azure Databricks** mall. Skapa nya länkade tjänster för följande anslutningar. 
    
    ![Inställningar för anslutningar](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Källans BLOB-anslutning** – för åtkomst till käll data. 
        
        Du kan använda det offentliga blob-lagring som innehåller källfilerna för det här exemplet. Referens följande skärm bild för konfiguration. Använd **SAS-URL** nedan för att ansluta till käll lagring (skrivskyddad åtkomst): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Mål-BLOB-anslutning** – för att kopiera data till. 
        
        I den länkade mottagar tjänsten väljer du en lagrings enhet som skapats i **förutsättning** 1.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** – för att ansluta till Databricks-kluster.

        Skapa en länkad Databricks-tjänst med hjälp av åtkomst nyckeln som genererades i **förutsättningen** 2. c. Om du har ett *interaktivt kluster*kan du välja det. (I det här exemplet används det *nya jobb kluster* alternativet.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. Välj **Använd den här mallen**så ser du en pipeline som skapats på det sätt som visas nedan:
    
    ![Skapa en pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Introduktion och konfiguration av pipeline

I den nya pipelinen som skapas har de flesta inställningar kon figurer ATS automatiskt med standardvärdena. Kolla in konfigurationerna och uppdateringen där det behövs för att passa dina egna inställningar. Mer information finns under instruktioner och skärm dum par för referens.

1.  En flagga för validering av aktivitets **tillgänglighet** skapas för att göra en käll tillgänglighets kontroll. *SourceAvailabilityDataset* som skapades i föregående steg har valts som data uppsättning.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  En kopierings aktivitet **fil till BLOB** skapas för kopiering av data uppsättningar från källa till mottagare. Referera till skärm dum par för konfigurationer av källa och mottagare i kopierings aktiviteten.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  En **transformering** av en Notebook-aktivitet skapas och den länkade tjänsten som skapades i föregående steg väljs.
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. Välj fliken **Inställningar** . För *Notebook-sökväg*definierar mallen en sökväg som standard. Du kan behöva bläddra och välja rätt Notebook-sökväg som laddats upp i **krav** 2. 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. Kolla in de *grundläggande parametrarna* som skapats som det visas i skärm bilden. De skickas till den Databricks Notebook från Data Factory. 

         ![Bas parametrar](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **Pipeline-parametrar** definieras enligt nedan.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. Konfigurerar data uppsättningar.
    1.  **SourceAvailabilityDataset** skapas för att kontrol lera om käll data är tillgängliga.

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** – för att kopiera källdata.

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** – för kopiering till Sink-/destinations platsen.

        1.  Länkad tjänst – *sinkBlob_LS* skapades i föregående steg.

        2.  Fil Sök väg – *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  Välj **Felsök** för att köra pipelinen. Du kan hitta en länk till Databricks-loggar för mer detaljerade Spark-loggar.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Du kan också kontrol lera data filen med Storage Explorer. (För att korrelera med Data Factory pipelines körs lägger det här exemplet till pipeline-körningen från Data Factory till mappen utdata. På så sätt kan du spåra filerna som genereras via varje körning.)

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
