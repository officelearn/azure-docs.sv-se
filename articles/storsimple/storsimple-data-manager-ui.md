---
title: Användargränssnittet för Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: Beskriver hur du använder StorSimple Data Manager-tjänstens användargränssnitt
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: fa897b4b77f7f5869eab2ba2e7db9afbd84febfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631504"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Hantera StorSimple Data Manager-tjänsten i Azure-portalen

Den här artikeln förklarar hur du kan använda StorSimple Data Manager-UI för att omvandla lagrade data på enheter för StorSimple 8000-serien. Transformerade data kan sedan användas av andra Azure-tjänster som Azure Media Services, Azure HDInsight, Azure Machine Learning och Azure Search.


## <a name="use-storsimple-data-transformation"></a>Använda StorSimple Dataomvandling

StorSimple Data Manager är resursen i vilken data transformation instantieras. Tjänsten transformering av Data kan du omvandla data från StorSimple-format till ursprungligt format i blobbar eller Azure Files. För att omvandla data för StorSimple-ursprungligt format, måste du ange information om din enhet i StorSimple 8000-serien och data av intresse som du vill omvandla.

### <a name="create-a-storsimple-data-manager-service"></a>Skapa en StorSimple Data Manager-tjänst

Utför följande steg för att skapa en StorSimple Data Manager-tjänsten.

1. Använd dina Microsoft-kontouppgifter för att logga in på [Azure Portal](https://portal.azure.com/).

2. Klicka på **+ skapa en resurs** och sökning för StorSimple Data Manager.

    ![Skapa en StorSimple Data Manager-tjänst 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klicka på StorSimple Data Manager och klicka sedan på **skapa**.
    
    ![Skapa en StorSimple Data Manager-tjänsten 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. För den nya tjänsten anger du följande:

   1. Ange ett unikt **tjänstnamn** för din StorSimple Data Manager. Det är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

   2. Välj en **prenumeration** från den nedrullningsbara listan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet är automatiskt ifylld (och inte valbar) om du bara har en prenumeration.

   3. Välj en befintlig resursgrupp eller skapa en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Ange den **plats** för din tjänst som lagrar dina lagringskonton och StorSimple Data Manager-tjänsten. StorSimple Device Manager-tjänsten, Data Manager-tjänsten och det associerade lagringskontot ska i regionerna som stöds.
    
   5. Välj för att få en länk till den här tjänsten på instrumentpanelen, **fäst på instrumentpanelen**.
    
   6. Klicka på **Skapa**.

      ![Skapa en StorSimple Data Manager-tjänst 3](./media/storsimple-data-manager-ui/create-service-4.png)

Det tar några minuter att skapa tjänsten. Du ser ett meddelande när tjänsten har skapats och den nya tjänsten visas.

### <a name="create-a-data-transformation-job-definition"></a>Skapa en jobbdefinition för omvandling av data

I en StorSimple Data Manager-tjänsten måste du skapa en jobbdefinition för omvandling av data. En jobbdefinition anger information om StorSimple-data som du är intresserad av att flytta till ett lagringskonto i ursprungligt format. När du skapar en jobbdefinition, kan du köra det här jobbet igen med olika körningsinställningar.

Utför följande steg för att skapa en jobbdefinition.

1. Navigera till den tjänst som du skapade. Gå till **Management > jobbdefinition**.

2. Klicka på **+ jobb definition**.

    ![Klicka på + jobbdefinition](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Ange ett namn på din jobbdefinition. Namnet kan vara mellan 3 och 63 tecken. Namnet får innehålla versaler och gemener, siffror och bindestreck.

4. Ange en plats där jobbet körs. Den här platsen kan skilja sig från den plats där tjänsten distribueras.

5. Klicka på **källa** att ange datalager för källa.

    ![Konfigurera källa data lagringsplats](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Eftersom detta är en ny Data Manager-tjänst konfigureras inga datalager. I **konfigurera datakällan**, ange information om din enhet i StorSimple 8000-serien och data av intresse.

   Lägg till din StorSimple Device Manager som ett datalager, klicka på **Lägg till ny** i listrutan för data-databasen och klicka sedan på **Lägg till databas**.

    ![Lägg till ny data-lagringsplats](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Välj **StorSimple 8000-serien Manager** som lagringsplats för datatyp.
    
   2. Ange ett eget namn för centrallagret data.
    
   3. I listrutan väljer du en prenumeration som är associerade med din StorSimple Device Manager-tjänsten.
    
   4. Ange namnet på StorSimple Device Manager för den **Resource**.

   5. Ange den **Service datakryptering** nyckel för StorSimple Device Manager-tjänsten. 

      ![Konfigurera källa data lagringsplatsen 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Klicka på **OK** när du är klar. Detta sparar ditt datalager. Återanvända StorSimple Device Manager i andra jobbdefinitioner utan att ange dessa parametrar igen. Det tar några sekunder när du klickar på **OK** för nyligen skapade källdatabasen data visas i listrutan.

7. I listrutan för **datalager**, Välj den databas du skapade. 

   1. Ange namnet på den enhet i StorSimple 8000-serien som innehåller data av intresse.

   2. Ange namnet på den volym som finns på StorSimple-enheten där dina data av intresse.

   3. I den **Filter** underavsnitt, ange rotkatalogen som innehåller dina data av intresse i _\MyRootDirectory\Data_ format. Enhetsbeteckningar, till exempel _\C:\Data_ stöds inte. Du kan också lägga till alla filfilter.

   4. Omvandling datatjänst fungerar på de data som skickas till Azure via ögonblicksbilder. När du kör det här jobbet kan du göra en säkerhetskopia varje gång det här jobbet körs (för att fungera på senaste data) eller Använd den senaste säkerhetskopian i molnet (om du arbetar med vissa arkiverade data).

   5. Klicka på **OK**.

      ![Konfigurera källa data lager 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Därefter måste data måldatabasen konfigureras. Välj lagringskonton för att placera filer i BLOB-objekt i det kontot. Välj i listrutan **Lägg till ny** och sedan **konfigurera inställningar för**.

9. Välj typ av mållagringsplatsen som du vill lägga till och andra parametrar som är associerade med databasen.

    Om du väljer ett mål för Storage-konto typ måste du ange ett eget namn, prenumeration (Välj samma som för tjänsten eller andra), och ett lagringskonto.
        ![Konfigurera mållagringsplatsen data 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    En storage-kö skapas när jobbet körs. Den här kön fylls med meddelanden om transformerade blobbar i takt med att de är klara. Namnet på den här kön är detsamma som namnet på jobbdefinitionen.
    
10. När du lägger till databasen du vänta ett par minuter.
    
    1. Välj den databas som du skapade som mål i listrutan i det **målkontonamnet**.

    2. Välj typ som blobar eller filer. Ange namnet på behållaren där omvandlade data finns. Klicka på **OK**.

        ![Konfigurera mållagringskontot data-lagringsplats](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Du kan också kontrollera att alternativet för att presentera en uppskattning av jobbet varaktighet innan du kör jobbet. Klicka på **OK** skapa jobbdefinitionen. Din jobbdefinition är slutförd. Du kan använda den här jobbdefinitionen flera gånger via Gränssnittet med olika körningsinställningar.

    ![Fullständig jobbdefinition](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Nyligen skapade jobbdefinitionen läggs till i listan över definitioner av jobb för den här tjänsten.

### <a name="run-the-job-definition"></a>Kör jobbdefinitionen

När du behöver flytta data från StorSimple till det lagringskonto som du har angett i jobbdefinitionen kan behöva du köra den. Vid körning, kan vissa parametrar anges på olika sätt. Stegen är följande:

1. Välj StorSimple Data Manager-tjänsten och gå till **Management > jobbdefinition**. Välj och klicka på jobbdefinitionen som du vill köra.
     
     ![Starta ett jobb har körts 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klicka på **kör nu**.
     
     ![Starta ett jobb har körts 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klicka på **körningsinställningar** att ändra alla inställningar som du kanske vill ändra för det här jobbet körs. Klicka på **OK** och klicka sedan på **kör** att starta jobbet.

    ![Starta ett jobb har körts 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Om du vill övervaka det här jobbet går du till **jobb** i StorSimple Data Manager. Förutom övervakning i den **jobb** bladet, du kan även lyssna på lagringskön där ett meddelande läggs varje gång en fil har flyttats från StorSimple till lagringskontot.

    ![Starta ett jobb har körts 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>Nästa steg

[Använda .NET SDK för att starta StorSimple Data Manager-jobb](storsimple-data-manager-dotnet-jobs.md).