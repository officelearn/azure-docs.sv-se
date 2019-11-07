---
title: Kopiera nya och ändrade filer genom LastModifiedDate med Azure Data Factory
description: Lär dig hur du använder en lösnings mall för att kopiera nya och ändrade filer genom att LastModifiedDate med Azure Data Factory.
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
ms.openlocfilehash: aaa7114113d5f0330d2dc7d656b0d91963931512
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684227"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopiera nya och ändrade filer genom LastModifiedDate med Azure Data Factory

Den här artikeln beskriver en lösnings mall som du kan använda för att kopiera nya och ändrade filer endast av LastModifiedDate från en filbaserad lagring till ett mål lager. 

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen väljer först de nya och ändrade filerna enbart efter deras attribut **LastModifiedDate**och kopierar sedan de valda filerna från data käll arkivet till data destinations lagret.

Mallen innehåller en aktivitet:
- **Kopiera** för att kopiera nya och ändrade filer enbart av LastModifiedDate från ett fil lager till ett mål lager.

Mallen definierar fyra parametrar:
-  *FolderPath_Source* är sökvägen till mappen där du kan läsa filerna från käll arkivet. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *FolderPath_Destination* är sökvägen till mappen där du vill kopiera filer till mål lagret. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *LastModified_From* används för att välja de filer vars LastModifiedDate-attribut är efter eller lika med det här datetime-värdet.  För att kunna välja endast nya filer, som inte har kopierats senaste gången, kan det här datetime-värdet vara den tid då pipelinen utlöstes senast. Du kan ersätta standardvärdet "2019-02-01T00:00:00Z" till din förväntade LastModifiedDate i UTC-tidszonen. 
-  *LastModified_To* används för att markera de filer vars LastModifiedDate-attribut är före detta datetime-värde. För att kunna välja endast nya filer, som inte har kopierats senast, kan det här datetime-värdet vara aktuell tid.  Du kan ersätta standardvärdet "2019-02-01T00:00:00Z" till din förväntade LastModifiedDate i UTC-tidszonen. 

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Gå till mall **Kopiera endast nya filer av LastModifiedDate**. Skapa en **ny** anslutning till ditt käll lagrings lager. Käll lagrings lagret är den plats där du vill kopiera filer.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Välj först lagrings **typ**. När du har indata på lagrings **kontots namn** och **konto nyckeln**. Välj slutligen **Slutför**.

    ![Mata in en anslutnings sträng](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Skapa en **ny** anslutning till mål arkivet. Mål lagret är dit du vill kopiera filer till. Du måste också mata in anslutnings informationen för data destinations lagret på samma sätt som du gjorde i steg 2.

    ![Skapa en ny anslutning till målet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Du ser pipelinen tillgänglig i panelen, som du ser i följande exempel:

    ![Visa pipelinen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Välj **Felsök**, Skriv värdet för **parametrarna** och välj **Slutför**.  I bilden nedan anger vi parametrarna som följer.
   - **FolderPath_Source** =  **/Source/**
   - **FolderPath_Destination** =  **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     Exemplet indikerar de filer som senast ändrades inom TimeSpan mellan *2019-02-01T00:00:00Z* och *2019-03-01T00:00:00Z* kopieras från en mapp */Source/* till en mapp */destination/* .  Du kan ersätta dessa med dina egna parametrar.
    
     ![Köra en pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Granska resultatet. Du kommer bara att se de filer som senast ändrades inom det konfigurerade TimeSpan som har kopierats till mål lagret.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Nu kan du lägga till en rullande Windows-utlösare för att automatisera den här pipelinen, så att pipelinen alltid kan kopiera nya och ändrade filer endast av LastModifiedDate med jämna mellanrum.  Välj **Lägg till utlösare**och välj **nytt/redigera**.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. I fönstret **Lägg till utlösare** väljer du **+ ny**.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Välj **rullande-fönstret** för utlösnings typen, ange **var 15: e minut** som upprepning (du kan ändra till valfri intervall tid) och välj sedan **Nästa**.

    ![Skapa utlösare](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Skriv värdet för **utlösarens körnings parametrar** enligt följande och välj **Slutför**.
    - **FolderPath_Source** =  **/Source/** .  Du kan ersätta med din mapp i käll data lagret.
    - **FolderPath_Destination** =  **/destination/** .  Du kan ersätta med din mapp i mål data lagret.
    - **LastModified_From** =   **\@trigger (). outputs. windowStartTime**.  Det är en system variabel från utlösaren som fastställer tidpunkten då pipelinen utlöstes senast.
    - **LastModified_To** =  **\@trigger (). outputs. windowEndTime**.  Det är en system variabel från utlösaren som fastställer tidpunkten då pipelinen utlöses för tillfället.
    
    ![Indataparametrar](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Välj **Publicera alla**.
    
    ![Publicera alla](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Skapa nya filer i din källmapp i data käll arkivet.  Du väntar nu på att pipelinen ska utlösas automatiskt och endast de nya filerna kopieras till mål lagret.

14. Välj fliken **övervakning** i den vänstra navigerings panelen och vänta i cirka 15 minuter om upprepningen av utlösaren har angetts till var 15: e minut. 

    ![Välj övervakning](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Granska resultatet. Du ser att din pipeline aktive ras automatiskt var 15: e minut, och bara nya eller ändrade filer från käll arkivet kopieras till mål lagret i varje pipeline-körning.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
