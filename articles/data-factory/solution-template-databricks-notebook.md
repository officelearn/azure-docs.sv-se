---
title: Omvandla data med hjälp av Databricks i Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en lösningsmall för att omvandla data med hjälp av en Databricks notebook i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: bb9277a9e8f5e8bacb6b7aab7b9a5b411c86edbd
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967725"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Omvandla data med hjälp av Databricks i Azure Data Factory

I den här självstudien skapar du en pipeline för slutpunkt till slutpunkt som innehåller **Lookup**, **kopia**, och **Databricks notebook** aktiviteter i Data Factory.

-   **Lookup** eller GetMetadata-aktiviteten används för att se till att datauppsättningen för källan är redo för underordnade förbrukning, innan du aktiverar kopiera och analytics-jobbet.

-   **Kopiera aktivitet** kopierar källfilen / datauppsättning för mottagare-lagring. Mottagare lagringen är monterad som antingen i Databricks notebook så att datauppsättningen kan användas direkt av Spark.

-   **Databricks notebook-aktiviteten** utlöser Databricks notebook som omvandlar datauppsättningen och lägger till en bearbetade mapp / SQL DW.

För att den här mallen enkelt skapar inte mallen en schemalagd utlösare. Du kan lägga till som om det behövs.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Förutsättningar

1.  Skapa en **blob storage-konto** och en behållare kallas `sinkdata` som ska användas som **mottagare**. Anteckna den **lagringskontonamn**, **behållarnamn**, och **åtkomstnyckel**, eftersom de refereras till längre fram i mallen.

2.  Kontrollera att du har en **Azure Databricks-arbetsytan** eller skapa en ny.

1.  **Importera anteckningsboken för ETL**. Importera den nedan transformeringen notebook till Databricks-arbetsytan. (Det behöver inte finnas på samma plats som nedan, men kom ihåg den sökväg som du väljer för senare.) Importera anteckningsboken från följande URL genom att ange URL: en i URL-fält: `https://Data Factorylabstaging1.blob.core.windows.net/share/Transformations.html`. Välj **Import**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Nu ska vi uppdatera den **omvandling** bärbar dator med din **storage anslutningsinformation** (namn och nyckel). Gå till **kommandot 5** i importerade anteckningsboken ovan, Ersätt den med den nedan kodfragmentet när du har ersatt de markerade värdena. Se till att det här kontot är samma lagringskonto som skapades tidigare och innehåller de `sinkdata` behållare.

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

1.  Generera en **Databricks åtkomsttoken** för Data Factory för att få åtkomst till Databricks. **Spara åtkomsttoken** för senare användning i att skapa en Databricks-länkad tjänst, som ser ut ungefär så 'dapi32db32cbb4w6eee18b7d87e45exxxxxx'

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Skapa länkade tjänster och datauppsättningar

1.  Skapa en ny **länkade tjänster** i Användargränssnittet för Data Factory genom att gå till *anslutningar länkade tjänster + ny*

    1.  **Källan** – för att komma åt datakällan. Du kan använda den offentliga blob-lagring som innehåller källfiler för det här exemplet.

        Välj **Blobblagring**, använda den nedan **SAS-URI** att ansluta till källagringen (skrivskyddad åtkomst).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Mottagare** – för att kopiera data till.

        Välj ett lagringskonto som skapats i den [förutsättning 1](#prerequisite), i sink länkad tjänst.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – för att ansluta till Databricks-kluster

        Skapa en länkad tjänst med åtkomstnyckel som genererats i Databricks [nödvändiga 2.c](#prerequisite). Om du har en *interaktiva kluster*, du kan välja som. (Det här exemplet används den *nya jobbklustret* alternativet.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Skapa **datauppsättningar**

    1.  Skapa **'sourceAvailability_Dataset'** att kontrollera om datakällan är tillgänglig

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Källdatauppsättning –** för att kopiera källdata (med binär kopia)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Datauppsättning för mottagare** – för att kopiera till mottagaren / målplats

        1.  Länkad tjänst - Välj 'sinkBlob_LS' skapades i 1.b

        2.  Filsökväg - ' sinkdata/staged_sink'

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Skapa aktiviteter

1.  Skapa en Lookup-aktivitet '**tillgänglighet flaggan**”för att göra en källa tillgänglighetskontroll (Lookup- eller GetMetadata kan användas). Välj ”sourceAvailability_Dataset' skapades i 2.a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Skapa en Kopieringsaktivitet '**filen till blob**”för att kopiera datauppsättningen från källa till mottagare. I det här fallet är data binär fil. Referens för den nedan skärmbilder för käll- och mottagar-konfigurationer i kopieringsaktiviteten.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definiera **pipeline-parametrar**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Skapa en **Databricks-aktivitet**

    Välj den länkade tjänsten som skapats i föregående steg.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Konfigurera den **inställningar**. Skapa **Base parametrar** som visas i skärmbilden och skapa parametrar som ska skickas till Databricks notebook från Data Factory. Bläddra och **Välj** den **rätt notebook-sökvägen** laddades upp i **förutsättning 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Kör pipelinen**. Du hittar länk till Databricks loggar för mer detaljerad Spark-loggar.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Du kan också kontrollera datafilen med storage explorer. (För korrelering med Data Factory pipeline-körningar, det här exemplet lägger till pipelinekörningens ID från data factory till utdatamappen. På så sätt kan du spåra tillbaka de filer som skapas via varje körning.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)