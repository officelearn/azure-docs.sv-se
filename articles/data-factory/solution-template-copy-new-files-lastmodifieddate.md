---
title: Kopiera nya och ändrade filer av LastModifiedDate med Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en lösningsmall för att kopiera nya och ändrade filer av LastModifiedDate med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111947"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopiera nya och ändrade filer av LastModifiedDate med Azure Data Factory

Den här artikeln beskrivs en mall som du kan använda för att kopiera nya och ändrade filer endast av LastModifiedDate från en butik med filbaserad till ett mål. 

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen väljer först nya och ändrade filer endast av deras attribut **LastModifiedDate**, och kopierar sedan dessa valda filer från datalager för källa till datalager för destination.

Mallen innehåller en aktivitet:
- **Kopiera** att kopiera nya och ändrade filer endast av LastModifiedDate från ett filarkiv till ett mål.

Mallen definierar fyra parametrar:
-  *FolderPath_Source* är sökvägen till mappen där du kan läsa filer från arkivet källa. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *FolderPath_Destination* är sökvägen till mappen där du vill kopiera filer till målarkiv. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *LastModified_From* används för att välja filer vars LastModifiedDate-attributet är efter eller lika med det här datum/tid-värdet.  För att markera de nya filerna, som inte har kopierats senast, kan det här datum/tid-värdet vara tiden när pipelinen utlöstes senast. Du kan ersätta standardvärdet ' 2019-02-01T00:00:00Z' till din förväntade LastModifiedDate i UTC-tidszonen. 
-  *LastModified_To* används för att markera filer vars LastModifiedDate-attributet är innan det här datum/tid-värdet. För att markera de nya filerna, som inte har kopierats senast, kan det här datum/tid-värdet vara den aktuella tiden.  Du kan ersätta standardvärdet ' 2019-02-01T00:00:00Z' till din förväntade LastModifiedDate i UTC-tidszonen. 

## <a name="how-to-use-this-solution-template"></a>Hur du använder den här lösningsmallen

1. Gå till mallen **kopiera nya filer endast av LastModifiedDate**. Skapa en **New** anslutning till din butik för lagring av källa. Storage källagringen är där du vill kopiera filer från.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Välj först lagringen **typ**. Efter som indata lagringen **kontonamn** och **kontonyckel**. Välj slutligen **Slutför**.

    ![Ange en anslutningssträng](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Skapa en **New** anslutning till din målarkiv. Målarkiv är där du vill kopiera filer till. Du måste också ange anslutningsinformationen för data målarkiv liknande som du i steg 2.

    ![Skapa en ny anslutning till målet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Pipelinen i panelen visas enligt följande exempel:

    ![Visa pipelinen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Välj **felsöka**, skriva-värdet för den **parametrar** och välj **Slutför**.  I bilden nedan är vi ange parametrarna som följande.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     I exemplet är som anger de filer som senast ändrades i tiden från att *2019-02-01T00:00:00Z* och *2019-03-01T00:00:00Z* ska kopieras från en mapp */source/*  till en mapp */destination/*.  Du kan ersätta dem med dina egna parametrar.
    
     ![Köra en pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Granska resultatet. Visas endast de filer som senast ändrades inom den konfigurerade timespan har kopierats till målarkiv.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Nu kan du lägga till en utlösare för rullande windows för att automatisera denna pipeline, så att pipelinen kan alltid kopiera nya och ändrade filer endast av LastModifiedDate med jämna mellanrum.  Välj **Lägg till utlösare**, och välj **ny/redigera**.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. I den **Lägg till utlösare** väljer **+ ny**.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Välj **rullande fönster** för Utlösartyp, ställer du in **var 15: e minut** som upprepningen (du kan ändra till helst intervall), och välj sedan **nästa**.

    ![Skapa utlösare](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Skriva-värdet för den **parametrar för Objektutlösare kör** som följande och välj **Slutför**.
    - **FolderPath_Source** = **/source/**.  Du kan ersätta med din mapp i källdatalagret.
    - **FolderPath_Destination** = **/destination/**.  Du kan ersätta med din mapp i måldatalager.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  Det är en systemvariabel från utlösaren avgöra hur lång tid när pipelinen utlöstes senast.
    - **LastModified_To** = **@trigger().outputs.windowEndTime**.  Det är en systemvariabel från utlösaren avgöra hur lång tid när pipeline utlöses den här gången.
    
    ![Indataparametrar](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Välj **Publicera alla**.
    
    ![Publicera alla](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Skapa nya filer i mappen källa för datalager för källa.  Du nu väntar pipelinen ska utlösas automatiskt och endast de nya filerna kopieras till målarkiv.

14. Välj **övervakning** i den vänstra navigeringspanelen och vänta tills cirka 15 minuter om upprepningen av utlösaren har ställts in på var 15: e minut. 

    ![Välj övervakning](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Granska resultatet. Du ser din pipeline utlöses automatiskt var 15: e minut och endast de nya eller ändrade filerna från källan store kopieras till målarkiv i varje pipelinekörning.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)