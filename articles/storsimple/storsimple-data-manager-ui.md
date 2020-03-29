---
title: Microsoft Azure StorSimple Data Manager-gränssnitt
description: Beskriver hur du använder StorSimple Data Manager-tjänstgränssnittet
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 85be49ad88ac62d90235c3da6b89b0da6a11487c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933760"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Hantera Tjänsten StorSimple Data Manager i Azure-portalen

I den här artikeln beskrivs hur du kan använda StorSimple Data Manager-användargränssnittet för att omvandla data som finns på StorSimple 8000-seriens enheter. Transformerade data kan sedan förbrukas av andra Azure-tjänster som Azure Media Services, Azure HDInsight, Azure Machine Learning och Azure Cognitive Search.


## <a name="use-storsimple-data-transformation"></a>Använda StorSimple-dataomvandling

StorSimple Data Manager är den resurs inom vilken dataomvandling instansieras. Med tjänsten Data transformation kan du omvandla data från StorSimple-formatet till ursprungsformat i blobbar eller Azure-filer. Om du vill omvandla data i StorSimples ursprungliga format måste du ange information om storsimple 8000-seriens enhet och de data av intresse som du vill omvandla.

### <a name="create-a-storsimple-data-manager-service"></a>Skapa en StorSimple Data Manager-tjänst

Utför följande steg för att skapa en StorSimple Data Manager-tjänst.

1. Använd dina Microsoft-kontouppgifter för att logga in på [Azure Portal](https://portal.azure.com/).

2. Klicka på **+ Skapa en resurs** och sök efter StorSimple Data Manager.

    ![Skapa en StorSimple Data Manager-tjänst 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klicka på StorSimple Data Manager och sedan på **Skapa**.
    
    ![Skapa en StorSimple Data Manager-tjänst 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. För den nya tjänsten anger du följande:

   1. Ange ett unikt **tjänstnamn** för din StorSimple Data Manager. Det är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

   2. Välj en **prenumeration** i listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet fylls i automatiskt (och kan inte väljas) om du bara har en prenumeration.

   3. Välj en befintlig resursgrupp eller skapa en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Ange **platsen** för din tjänst som rymmer dina lagringskonton och tjänsten StorSimple Data Manager. Tjänsten StorSimple Enhetshanteraren, DataHanteraren och det associerade lagringskontot ska alla finnas i de regioner som stöds.
    
   5. Om du vill få en länk till den här tjänsten på instrumentpanelen väljer du **Fäst på instrumentpanelen**.
    
   6. Klicka på **Skapa**.

      ![Skapa en StorSimple Data Manager-tjänst 3](./media/storsimple-data-manager-ui/create-service-4.png)

Det tar några minuter att skapa tjänsten. Du ser ett meddelande när tjänsten har skapats och den nya tjänsten visas.

### <a name="create-a-data-transformation-job-definition"></a>Skapa en jobbdefinition för dataomvandling

I en StorSimple Data Manager-tjänst måste du skapa en jobbdefinition för dataomvandling. En jobbdefinition anger information om De StorSimple-data som du är intresserad av att flytta till ett lagringskonto i det ursprungliga formatet. När du har skapat en jobbdefinition kan du köra det här jobbet igen med olika körningsinställningar.

Gör följande för att skapa en jobbdefinition.

1. Navigera till den tjänst som du skapade. Gå till **Hanterings- > jobbdefinitioner**.

2. Klicka på **+ Jobbdefinition**.

    ![Klicka på +Jobbdefinition](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Ange ett namn för jobbdefinitionen. Namnet kan vara mellan 3 och 63 tecken. Namnet kan innehålla versaler och gemener, siffror och bindestreck.

4. Ange en plats där jobbet körs. Den här platsen kan skilja sig från den plats där tjänsten distribueras.

5. Klicka på **Källa** om du vill ange källdatadatabasen.

    ![Konfigurera repo för källdata](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Eftersom detta är en ny Data Manager-tjänst har inga datadatabaser konfigurerats. I **Konfigurera datakälla**anger du information om storsimple 8000-seriens enhet och data av intresse.

   Om du vill lägga till StorSimple Device Manager som datadatabas klickar du på **Lägg till ny** i listrutan för datadatabasen och sedan på Lägg till **datadatabas**.

    ![Lägg till ny datarepo](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Välj **StorSimple 8000-seriehanteraren** som datadatabastyp.
    
   2. Ange ett eget namn för källdatadatabasen.
    
   3. Välj en prenumeration som är kopplad till Tjänsten StorSimple Device Manager i listrutan.
    
   4. Ange namnet på StorSimple-enhetshanteraren för **resursen**.

   5. Ange krypteringsnyckeln för **tjänstdata** för Tjänsten StorSimple Device Manager. 

      ![Konfigurera källdataåterupp 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Klicka på **OK** när du är klar. Detta sparar din datadatabas. Återanvända den här StorSimple-enhetshanteraren i andra jobbdefinitioner utan att ange dessa parametrar igen. Det tar några sekunder efter att du klickar på **OK** för den nyskapade källdatadatabasen att visas i listrutan.

7. Välj den datadatabas som du skapade i listrutan för **datadatabasen.** 

   1. Ange namnet på storsimilera 8000-serien enhet som innehåller data av intresse.

   2. Ange namnet på volymen som finns på StorSimple-enheten som har dina data av intresse.

   3. I **underavsnittet Filter** anger du rotkatalogen som innehåller dina data av intresse i _\MyRootDirectory\Data-format._ Enhetsbeteckningar som _\C:\Data_ stöds inte. Du kan också lägga till filfilter här.

   4. Dataomvandlingstjänsten fungerar bara på den senaste ögonblicksbilden av data som skjuts upp till Azure.

   5. Klicka på **OK**.

      ![Konfigurera källdataåterpo 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Därefter måste måldatadatabasen konfigureras. Välj lagringskonton för att placera filer i blobbar i det kontot. I listrutan väljer du **Lägg till ny** och **konfigurerar**sedan inställningar .

9. Välj den typ av måldatabas som du vill lägga till och de andra parametrar som är associerade med databasen.

    Om du väljer ett mål för lagringskontotyp kan du ange ett eget namn, en prenumeration (välj samma som tjänstens eller det andra) och ett lagringskonto.
        ![Konfigurera måldata repo 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    En lagringskö skapas när jobbet körs. Den här kön fylls med meddelanden om transformerade blobbar i takt med att de är klara. Namnet på den här kön är detsamma som namnet på jobbdefinitionen.
    
10. När du har lagt till datadatabasen väntar du ett par minuter.
    
    1. Välj den databas som du skapade som mål i listrutan i **målkontonamnet**.

    2. Välj lagringstyp som blobbar eller filer. Ange namnet på lagringsbehållaren där transformerade data finns. Klicka på **OK**.

        ![Konfigurera måldatakonto för lagring av lagring av måldata](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Du kan också markera alternativet för att presentera en uppskattning av jobbets varaktighet innan du kör jobbet. Klicka på **OK** om du vill skapa jobbdefinitionen. Din jobbdefinition är nu klar. Du kan använda den här jobbdefinitionen flera gånger via användargränssnittet med olika körningsinställningar.

    ![Fullständig jobbdefinition](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Den nyskapade jobbdefinitionen läggs till i listan över jobbdefinitioner för den här tjänsten.

### <a name="run-the-job-definition"></a>Kör jobbdefinitionen

När du behöver flytta data från StorSimple till det lagringskonto som du har angett i jobbdefinitionen måste du köra den. Vid körning kan vissa parametrar anges på olika sätt. Stegen är följande:

1. Välj tjänsten StorSimple Data Manager och gå till **Hantering > Jobbdefinitioner**. Markera och klicka på den jobbdefinition som du vill köra.
     
     ![Starta jobbkörning 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klicka på **Kör nu**.
     
     ![Starta jobbkörning 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klicka på **Kör inställningar** om du vill ändra de inställningar som du kanske vill ändra för den här jobbkörningen. Klicka på **OK** och sedan på **Kör** för att starta jobbet.

    ![Starta jobbkörning 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Om du vill övervaka det här jobbet går du till **Jobb** i StorSimple Data Manager. Förutom övervakning i **jobbbladet** kan du också lyssna i lagringskön där ett meddelande läggs till varje gång en fil flyttas från StorSimple till lagringskontot.

    ![Starta jobbkörning 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Visa loggar efter slutförande av jobbet

När ett jobb har slutförts kan du visa jobbets status. Jobbstatus kan **lyckades**, **delvis lyckades** och **misslyckades**. Du kan visa listan över filer som har kopierats och filer som inte kunde kopieras. Dessa listor är tillgängliga i en behållare som kallas **"storsimple-data-manager-joblogs"** i ditt mållagringskonto. I den här behållaren kan du söka efter en mapp med samma namn som jobbdefinitionen. I detta skapas en mapp för varje jobbkörning som ska innehålla dina listor. Namnet på den här mappen kommer att vara GUID för jobbet, som du kan få från sidan jobbinformation. Alternativt ser du i de flesta fall en länk för kopieringsloggarna på själva jobbsidan.
Det finns 2 uppsättning CSV-filer som du kommer att se i den här mappen. Alla filer som börjar med **kopieradfillista...** kommer att innehålla en lista över kopierade filer. Alla filer som börjar med **failedfilelist...** innehåller filer som inte kunde kopieras, tillsammans med ett felmeddelande.


## <a name="next-steps"></a>Nästa steg

[Använd .NET SDK för att starta StorSimple Data Manager-jobb](storsimple-data-manager-dotnet-jobs.md).
