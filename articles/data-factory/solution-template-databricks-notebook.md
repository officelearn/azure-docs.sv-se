---
title: Transformera data med hjälp av Databricks
description: Lär dig hur du använder en lösnings mall för att transformera data med hjälp av en Databricks Notebook i Azure Data Factory.
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
ms.openlocfilehash: 5b39e354d503910d20141ce19c625eb79b4a7353
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891005"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformera data med hjälp av Databricks i Azure Data Factory

I den här självstudien skapar du en pipeline från slut punkt till slut punkt som innehåller **Lookup**-, **copy**-och **Databricks Notebook** -aktiviteter i Data Factory.

-   **Lookup** -eller getMetaData-aktiviteten används för att säkerställa att käll data uppsättningen är klar för efterföljande konsumtion innan du utlöser kopierings-och analys jobbet.

-   **Kopierings aktiviteten** kopierar käll filen/data uppsättningen till mottagar lagringen. Mottagar lagringen monteras som DBFS i antecknings boken för Databricks så att data uppsättningen kan konsumeras direkt av Spark.

-   **Databricks Notebook-aktivitet** utlöser Databricks Notebook som transformerar data uppsättningen och lägger till den i en bearbetad mapp/SQL DW.

För att den här mallen ska vara enkel, skapar mallen ingen schemalagd utlösare. Du kan lägga till det om det behövs.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Krav

1.  Skapa ett **Blob Storage-konto** och en behållare med namnet `sinkdata` som ska användas som **mottagare**. Anteckna **lagrings kontots namn**, **behållar namn**och **åtkomst nyckel**, eftersom de refereras senare i mallen.

2.  Se till att du har en **Azure Databricks arbets yta** eller skapa en ny.

1.  **Importera antecknings boken för ETL**. Importera den nedan transformerande antecknings boken till arbets ytan Databricks. (Den behöver inte finnas på samma plats som nedan, men kom ihåg den sökväg du väljer för senare.) Importera antecknings boken från följande URL genom att ange den här URL: en i URL-fältet: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Välj **Importera**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Nu ska vi uppdatera **omvandlings** antecknings boken med din **lagrings anslutnings information** (namn och åtkomst nyckel). Gå till **kommando 5** i den importerade antecknings boken ovan och ersätt den med kodfragmentet nedan när du har ersatt de markerade värdena. Se till att det här kontot är samma lagrings konto som skapades tidigare och att det innehåller `sinkdata` containern.

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

1.  Generera en **Databricks** -åtkomsttoken för Data Factory att få åtkomst till Databricks. **Spara åtkomsttoken** för senare användning i skapa en länkad Databricks-tjänst, som ser ut ungefär som "dapi32db32cbb4w6eee18b7d87e45exxxxxx"

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Skapa länkade tjänster och data uppsättningar

1.  Skapa nya **länkade tjänster** i Data Factory UI genom att gå till *anslutningar länkade tjänster + ny*

    1.  **Källa** – för att få åtkomst till käll data. Du kan använda det offentliga blob-lagring som innehåller källfilerna för det här exemplet.

        Välj **Blob Storage**, Använd SAS- **URI: n** nedan för att ansluta till käll lagring (skrivskyddad åtkomst).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Handfat** – för att kopiera data till.

        Välj en lagrings enhet som skapats i förutsättning 1 i den länkade mottagar tjänsten.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – för att ansluta till Databricks-kluster

        Skapa en länkad Databricks-tjänst med hjälp av åtkomst nyckeln som genererades i förutsättningen 2. c. Om du har ett *interaktivt kluster*kan du välja det. (I det här exemplet används det *nya jobb kluster* alternativet.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Skapa **data uppsättningar**

    1.  Skapa **sourceAvailability_Dataset** för att kontrol lera om käll data är tillgängliga

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Käll data uppsättning –** för att kopiera källdata (med binär kopia)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Data uppsättning för mottagare** – för kopiering till Sink/destinations plats

        1.  Länkad tjänst – Välj sinkBlob_LS som skapats i 1. b

        2.  Fil Sök väg: sinkdata/staged_sink

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Skapa aktiviteter

1.  Skapa en lookup-aktivitets**tillgänglighets flagga**för att göra en käll tillgänglighets kontroll (lookup eller getMetaData kan användas). Välj sourceAvailability_Dataset som skapats i 2. a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Skapa en kopierings aktivitet till**fil-till-BLOB**för att kopiera data uppsättningen från källan till Sink. I det här fallet är datan en binär fil. Referera till skärm dum par för konfigurationer av källa och mottagare i kopierings aktiviteten.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definiera **pipeline-parametrar**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Skapa en **Databricks-aktivitet**

    Välj den länkade tjänst som skapades i ett föregående steg.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Konfigurera **inställningarna**. Skapa **bas parametrar** som visas i skärm bilden och skapa parametrar som ska skickas till den Databricks notebook från Data Factory. Bläddra och **Välj** **rätt Notebook-sökväg** som laddats upp i **krav 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Kör pipelinen**. Du kan hitta en länk till Databricks-loggar för mer detaljerade Spark-loggar.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Du kan också kontrol lera data filen med Storage Explorer. (För att korrelera med Data Factory pipelines körs lägger det här exemplet till pipeline-körningen från Data Factory till mappen utdata. På så sätt kan du spåra filerna som genereras via varje körning.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
