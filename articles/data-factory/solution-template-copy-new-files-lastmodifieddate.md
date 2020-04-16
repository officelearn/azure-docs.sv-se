---
title: Kopiera nya och ändrade filer efter LastModifiedDate
description: Lär dig hur du använder en lösningsmall för att kopiera nya och ändrade filer efter LastModifiedDate med Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 979977b2dd2eb5742d4e488623c79cb91427f055
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414804"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopiera nya och ändrade filer efter LastModifiedDate med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs en lösningsmall som du kan använda för att kopiera nya och ändrade filer endast av LastModifiedDate från ett filbaserat arkiv till ett målarkiv. 

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen väljer först de nya och ändrade filerna endast av deras attribut **LastModifiedDate**och kopierar sedan de markerade filerna från datakällarkivet till datamålarkivet.

Mallen innehåller en aktivitet:
- **Kopiera** om du bara vill kopiera nya och ändrade filer efter LastModifiedDate från ett filarkiv till ett målarkiv.

Mallen definierar sex parametrar:
-  *FolderPath_Source* är mappsökvägen där du kan läsa filerna från källarkivet. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *Directory_Source* är undermappssökvägen där du kan läsa filerna från källarkivet. Du måste ersätta standardvärdet med din egen undermappssökväg.
-  *FolderPath_Destination* är mappsökvägen där du vill kopiera filer till målarkivet. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *Directory_Destination* är undermappssökvägen där du vill kopiera filer till målarkivet. Du måste ersätta standardvärdet med din egen undermappssökväg.
-  *LastModified_From* används för att välja de filer vars LastModifiedDate-attribut är efter eller lika med det här datetime-värdet.  För att välja de nya filerna bara, som inte har kopierats förra gången, kan det här datetime-värdet vara den tidpunkt då pipelinen utlöstes förra gången. Du kan ersätta standardvärdet '2019-02-01T00:00:00Z' till ditt förväntade LastModifiedDate i UTC-tidszon. 
-  *LastModified_To* används för att välja de filer vars LastModifiedDate-attribut är före detta datetime-värde. För att välja de nya filerna bara, som inte har kopierats förra gången, kan detta datetime-värde vara nutid.  Du kan ersätta standardvärdet '2019-02-01T00:00:00Z' till ditt förväntade LastModifiedDate i UTC-tidszon. 

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösningsmallen

1. Gå till **mallEn Kopiera nya filer endast efter LastModifiedDate**. Skapa en **ny** anslutning till källlagringsarkivet. Källlagringsarkivet är där du vill kopiera filer från.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Skapa en **ny** anslutning till målarkivet. Målarkivet är där du vill kopiera filer till. 

    ![Skapa en ny anslutning till målet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Du kommer att se den pipeline som är tillgänglig på panelen, som visas i följande exempel:

    ![Visa pipelinen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Välj **Felsökning**, skriv värdet för **parametrarna** och välj **Slutför**.  På bilden nedan ställer vi in parametrarna som följande.
   - **FolderPath_Source** = källar
   - **Directory_Source** = undermapp
   - **FolderPath_Destination** = destinationsmapp
   - **Directory_Destination** = undermapp
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Exemplet är att filerna, som senast har ändrats inom tidsintervallet (**2019-02-01T00:00:00Z** till **2019-03-01T00:00:00Z**) kopieras från källsökvägskällan **källmapp/undermapp** till **målsökvägens destinationmapp/undermapp**.  Du kan ersätta dessa med dina egna parametrar.

    ![Köra en pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Granska resultatet. Du ser bara de filer som senast ändrades inom det konfigurerade tidsintervallet har kopierats till målarkivet.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Nu kan du lägga till en utlösare för tumlande fönster för att automatisera den här pipelinen, så att pipelinen alltid kan kopiera nya och ändrade filer endast av LastModifiedDate med jämna mellanrum.  Välj **Lägg till utlösare**och välj **Ny/Redigera**.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Välj **+ Nytt**i fönstret **Lägg utlösare** .

9. Välj **Tumlande fönster** för utlösartypen, ange **Var 15:e minut** som upprepning (du kan ändra till valfri intervalltid). Välj **Ja** för aktiverad ruta och välj sedan **OK**.

    ![Skapa utlösaren](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Ange värdet för parametrarna för **utlösarkörning** enligt följande och välj **Slutför**.
    - **FolderPath_Source****källamapp .** =   Du kan ersätta med mappen i källdatalagret.
    - **Directory_Source****undermapp .** =   Du kan ersätta med undermappen i källdatalagret.
    - **FolderPath_Destination****destinationfolder .** =   Du kan ersätta med mappen i måldatalagret.
    - **Directory_Destination****undermapp .** =   Du kan ersätta med undermappen i måldatalagret.
    - **LastModified_From** =  **\@utlösare().outputs.windowStartTime**.  Det är en systemvariabel från utlösaren som bestämmer när pipelinen utlöstes förra gången.
    - **LastModified_To** = **\@utlösare().outputs.windowEndTime**.  Det är en systemvariabel från utlösaren som bestämmer när pipelinen utlöses den här gången.
    
    ![Indataparametrar](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Välj **Publicera alla**.
    
    ![Publicera alla](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Skapa nya filer i källmappen i datakällarkivet.  Du väntar nu på att pipelinen ska utlösas automatiskt och endast de nya filerna kopieras till målarkivet.

13. Välj fliken **Bildskärm** i den vänstra navigeringspanelen och vänta i ungefär 15 minuter om upprepningen av utlösaren har ställts in på var 15:e minut. 

14. Granska resultatet. Du kommer att se din pipeline kommer att utlösas automatiskt var 15 minuter, och endast de nya eller ändrade filer från källarkivet kommer att kopieras till målarkivet i varje pipeline-körning.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
