---
title: "Gränssnitt för Microsoft Azure StorSimple Data Manager | Microsoft Docs"
description: "Beskriver hur du använder Användargränssnittet för StorSimple Data Manager-tjänsten"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d704cf8e6840c6a7b0a637c404d421f9f1497c46
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Hantera StorSimple Data Manager-tjänsten i Azure-portalen

Den här artikeln förklarar hur du kan använda StorSimple Data Manager-UI för att omvandla data på enheter för StorSimple 8000-serien. Omvandlade data kan sedan användas av andra Azure-tjänster, till exempel Azure Media Services, Azure HDInsight, Azure Machine Learning och Azure Search.


## <a name="use-storsimple-data-transformation"></a>Använda StorSimple Data Transformation

Datahanteraren StorSimple är resursen i vilken data transformation instansieras. DTS-tjänsten kan du omvandla data från StorSimple-format till native-format i blobbar eller Azure-filer. Du måste ange information om enheten StorSimple 8000-serien och data av intresse som du vill omvandla för att omvandla StorSimple inbyggda Formatdata.

### <a name="create-a-storsimple-data-manager-service"></a>Skapa en StorSimple Data Manager-tjänst

Utför följande steg för att skapa en StorSimple Data Manager-tjänsten.

1. Använd dina Microsoft-kontouppgifter för att logga in på [Azure Portal](https://portal.azure.com/).

2. Klicka på **+ skapa en resurs** och söka för StorSimple Data Manager.

    ![Skapa en StorSimple Data Manager-tjänst 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. StorSimple Data Manager och klicka sedan på **skapa**.
    
    ![Skapa StorSimple Data Manager-tjänsten 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Ange följande för den nya tjänsten:

    1. Ange ett unikt **tjänstnamnet** för din StorSimple Data Manager. Det är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

    2. Välj en **prenumeration** från den nedrullningsbara listan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet är automatiskt ifyllda (och inte valbar) om du bara har en prenumeration.

    3. Välj en befintlig resursgrupp eller skapa en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4. Ange den **plats** för tjänsten som innehåller dina lagringskonton och StorSimple Data Manager-tjänsten. StorSimple Device Manager-tjänsten, Data Manager och det associerade lagringskontot ska i regionerna som stöds.
    
    5. Om du vill hämta en länk till den här tjänsten på instrumentpanelen, Välj **fäst på instrumentpanelen**.
    
    6. Klicka på **Skapa**.

    ![Skapa en StorSimple Data Manager-tjänst 3](./media/storsimple-data-manager-ui/create-service-4.png)

Det tar några minuter att skapa tjänsten. Du ser ett meddelande när tjänsten har skapats och den nya tjänsten visas.

### <a name="create-a-data-transformation-job-definition"></a>Skapa en data transformation jobbdefinition

Inom en StorSimple Data Manager-tjänsten måste du skapa en data transformation jobbdefinitionen. En jobbdefinition anger information om StorSimple-data som du vill flytta till ett lagringskonto i native-format. När du skapar en jobbdefinition, kan du köra jobbet igen med olika körningsinställningar.

Utför följande steg för att skapa en jobbdefinition av.

1. Navigera till den tjänst som du skapade. Gå till **Management > jobbdefinition**.

2. Klicka på **+ jobbet definition**.

    ![Klicka på + jobbdefinitionen](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Ange ett namn för din jobbdefinitionen. Namnet kan vara mellan 3 och 63 tecken. Namnet får innehålla versaler och gemener, siffror och bindestreck.

4. Ange en plats där ditt jobb körs. Den här platsen kan skilja sig från den plats där tjänsten distribueras.

5. Klicka på **källa** att ange data källdatabasen.

    ![Konfigurera källdatorn data lagringsplatsen](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Eftersom det är en ny Data Manager-tjänst har inga databaser konfigurerats. I **konfigurera datakällan**, ange information om enheten StorSimple 8000-serien och data av intresse.

   Lägg till din StorSimple-Enhetshanteraren som en data-databas, klicka på **Lägg till ny** i data databasen listrutan och klicka sedan på **Lägg till Data databas**.

    ![Lägga till nya data lagringsplatsen](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
    1. Välj **StorSimple 8000-serien Manager** som lagringsplats för datatyp.
    
    2. Ange ett eget namn för din data källdatabasen.
    
    3. I listrutan väljer du en prenumeration som är associerad med din StorSimple Device Manager-tjänst.
    
    4. Ange namnet på StorSimple Enhetshanteraren för den **resurs**.

    5. Ange den **Service datakryptering** nyckel för StorSimple Device Manager-tjänsten. 

    ![Konfigurera källdatorn data lagringsplatsen 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

    Klicka på **OK** när du är klar. Detta sparar data-databasen. Återanvända den här StorSimple Enhetshanteraren i andra jobbdefinitioner utan att ange parametrarna igen. Det tar några sekunder efter att du klickar på **OK** för nyskapade källdatabasen data visas i listrutan.

7. I listrutan för **Data databasen**, Välj en lagringsplats för data som du skapade. 

    1. Ange namnet på StorSimple 8000-serien enheten som innehåller data av intresse.

    2. Ange namnet på den volym som finns på den virtuella StorSimple-enheten som innehåller dina data av intresse.

    3. I den **Filter** underavsnittets, ange rotkatalogen som innehåller dina data av intresse för _\MyRootDirectory\Data_ format. Enhetsbeteckningar sådana _\C:\Data_ stöds inte. Du kan också lägga till alla filfilter.

    4. Tjänsten data transformation fungerar på de data som skickas till Azure via ögonblicksbilder. När du kör det här jobbet kan du ta en säkerhetskopia varje gång det här jobbet körs (för att arbeta med senaste data) eller använda den senaste säkerhetskopian i molnet (om du arbetar med vissa arkiverade data).

    5. Klicka på **OK**.

    ![Konfigurera källdatorn data lagringsplatsen 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Därefter måste data måldatabasen konfigureras. Välj storage-konton för att placera filer i BLOB på det kontot. Välj i listrutan, **Lägg till ny** och sedan **konfigurera inställningar för**.

9. Välj typ av måldatabasen som du vill lägga till och andra parametrar som är kopplade till databasen.

    Om du väljer ett mål för Storage-konto typ måste du ange ett eget namn, prenumeration (Välj samma som för tjänsten eller andra), och ett lagringskonto.
        ![Konfigurera mål data lagringsplatsen 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    Storage-kö skapas när jobbet körs. Den här kön fylls med meddelanden om transformerade blobbar i takt med att de är klara. Namnet på den här kön är detsamma som namnet på jobbdefinitionen.
    
10. När du lägger till en lagringsplats för data, vänta ett par minuter.
    
    1. Välj den databas du skapade som mål i listrutan i den **mål kontonamn**.

    2. Välj lagringstypen som blobbar eller filer. Ange namnet på lagringsbehållaren där omvandlade data finns. Klicka på **OK**.

        ![Konfigurera mål data lagringsplatsen storage-konto](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Du kan också kontrollera alternativet för att presentera en uppskattning av jobbet varaktighet innan du kör jobbet. Klicka på **OK** att skapa jobbdefinitionen. Din jobbdefinitionen är slutförd. Du kan använda den här jobbdefinitionen flera gånger via Användargränssnittet med olika körningsinställningar.

    ![Fullständig jobbdefinitionen](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Nyligen skapade jobbdefinitionen läggs till i listan över definitioner av jobb för den här tjänsten.

### <a name="run-the-job-definition"></a>Kör jobbdefinitionen

Du måste köra den när du behöver flytta data från StorSimple till lagringskontot som du har angett i jobbdefinitionen. Vid körning, kan vissa parametrar anges på olika sätt. Stegen är följande:

1. Välj din StorSimple Data Manager-tjänsten och gå till **Management > jobbdefinition**. Välj och klicka på jobbdefinitionen som du vill köra.
     
     ![Starta jobbet kör 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klicka på **kör nu**.
     
     ![Starta jobbet kör 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klicka på **körningsinställningar** att ändra alla inställningar som du kanske vill ändra för det här jobbet körs. Klicka på **OK** och klicka sedan på **kör** att starta utskriften.

    ![Starta jobbet kör 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. För att övervaka jobbet, gå till **jobb** i din StorSimple Data Manager. Förutom övervakning i den **jobb** bladet du kan också lyssna på lagring kön där ett meddelande läggs varje gång en fil har flyttats från StorSimple till lagringskontot.

    ![Starta jobbet köras 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Nästa steg

[Använd .NET SDK för att starta jobb StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).