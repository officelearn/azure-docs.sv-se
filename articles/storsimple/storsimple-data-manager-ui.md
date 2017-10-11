---
title: "Gränssnitt för Microsoft Azure StorSimple Data Manager | Microsoft Docs"
description: "Beskriver hur du använder StorSimple Data Manager-tjänsten Användargränssnittet (privat förhandsvisning)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>Hantera med hjälp av StorSimple Data Manager-tjänsten Användargränssnittet (privat förhandsvisning)

Den här artikeln förklarar hur du kan använda StorSimple Data Manager-UI för att utföra Dataomvandling på data som finns på StorSimple 8000-serien enheter. Omvandlade data kan sedan användas av andra Azure-tjänster, till exempel Azure Media Services, Azure HDInsight, Azure Machine Learning och Azure Search. 


## <a name="use-storsimple-data-transformation"></a>Använda StorSimple Data Transformation

Datahanteraren StorSimple är resursen inom vilken Data Transformation kan initieras. DTS-tjänsten kan du flytta data från din lokala StorSimple-enhet till blobbar i Azure storage. Därför i arbetsflödet måste du ange information om din StorSimple-enhet och data av intresse som du vill flytta till lagringskontot.

### <a name="create-a-storsimple-data-manager-service"></a>Skapa en StorSimple Data Manager-tjänst

Utför följande steg för att skapa en StorSimple Data Manager-tjänsten.

1. Om du vill skapa en StorSimple Data Manager-tjänst, gå till [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. Klicka på den  **+**  ikon och sökning för StorSimple Data Manager. Klicka på din StorSimple Data Manager-tjänst och klicka sedan på **skapa**.

3. Om din prenumeration aktiveras för att skapa den här tjänsten kan se du följande bladet.

    ![Skapa en resurs för StorSimple Data chefer](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. Ange indata och klicka på **skapa**. Den angivna platsen bör vara det som innehåller dina lagringskonton och StorSimple Manager-tjänsten. För närvarande stöds endast västra USA och Västeuropa regioner. Därför ska StorSimple Manager-tjänsten, Data Manager-tjänsten och det associerade lagringskontot vara i de föregående regionerna som stöds. Det tar ungefär en minut att skapa tjänsten.

### <a name="create-a-data-transformation-job-definition"></a>Skapa en data transformation jobbdefinition

Inom en StorSimple Data Manager-tjänsten måste du skapa en data transformation jobbdefinitionen. En jobbdefinition anger information om de data som du vill flytta till ett lagringskonto i native-format. 

Utför följande steg för att skapa en ny data transformation jobbdefinition.

1.  Navigera till den tjänst som du skapade. Klicka på **+ jobbet Definition**.

    ![Klicka på + jobbdefinitionen](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. Nytt jobb definition blad öppnas. Namnge din jobbdefinitionen och klicka på **källa**. I den **konfigurera datakällan** bladet, ange information om din StorSimple-enhet och data av intresse.

    ![Skapa jobbdefinitionen](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. Eftersom det är en ny Data Manager-tjänst har inga databaser konfigurerats. Om du vill lägga till din StorSimple Manager som en data-databas, klickar du på **Lägg till ny** i data databasen listrutan och klicka sedan på **Lägg till Data databas**.

4. Välj **StorSimple 8000-serien Manager** som databasen skriver och anger egenskaperna för din **StorSimple Manager**. För den **resurs-Id** fältet, måste du ange hur många innan den **:** i Registreringsnyckeln för din StorSimple manager.

    ![Skapa datakälla](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  Klicka på **OK** när du är klar. Detta sparar data databasen och den här StorSimple Manager kan återanvändas i andra jobbdefinitioner utan att ange parametrarna igen. Det tar några sekunder efter att du klickar på **OK** för StorSimple Manager visas i listrutan.

6.  I den **konfigurera datakällan** bladet anger du namnet på en enhet och namn på volymer som innehåller dina data av intresse.

7.  I den **Filter** underavsnittets, ange rotkatalogen som innehåller dina data av intresse (det här fältet måste börja med en `\`). Du kan också lägga till alla filfilter.

8.  Tjänsten data transformation fungerar på de data som skickas till Azure via ögonblicksbilder. När du kör det här jobbet kan du ta en säkerhetskopia varje gång det här jobbet körs (för att arbeta med senaste data) eller använda den senaste säkerhetskopian i molnet (om du arbetar med vissa arkiverade data).

    ![Information om ny datakälla](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. Därefter måste mål-inställningar konfigureras. Det finns 2 typer stöds mål – Azure Media Services och Azure Storage-konton. Välj storage-konton för att placera filer i BLOB på det kontot. Välj media services-konto för att placera filer i tillgångar i kontot. Vi behöver igen och Lägg till en databas. Välj i listrutan, **Lägg till ny** och sedan **konfigurera inställningar för**.

    ![Skapa data sink](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. Här, kan du välja vilken typ av databasen som du vill lägga till och de andra parametrarna som är kopplade till databasen. I båda fallen skapas en kö för lagring när jobbet körs. Den här kön fylls med meddelanden om transformerade blobbar i takt med att de är klara. Namnet på den här kön är detsamma som namnet på jobbdefinitionen. Om du väljer **Media Services** som lagringsplatsen typ, sedan kan du också ange lagringskontouppgifter där kön skapas.

    ![Nya data sink information](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. När du lägger till data-databasen (som tar några sekunder) hittar du lagringsplatsen i listrutan i den **mål kontonamn**.  Välj det mål som du behöver.

12. Klicka på **OK** att skapa jobbdefinitionen. Din jobbdefinition har nu konfigurerats. Du kan använda den här jobbdefinitionen flera gånger via Användargränssnittet.

    ![Lägg till ny jobbdefinition](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>Kör jobbdefinitionen

När du behöver flytta data från StorSimple till lagringskontot som du har angett i jobbdefinitionen behöver du anropar den. Det finns vissa flexibilitet i varje gång du anropar jobbet att ändra parametrarna. Stegen är följande:

1. Välj din StorSimple Data Manager-tjänsten och gå till **övervakning**. Klicka på **kör nu**.

    ![Utlösaren jobbdefinitionen](./media/storsimple-data-manager-ui/run-now.png)

2. Välj jobbdefinitionen som du vill köra. Klicka på **körningsinställningar** att ändra alla inställningar som du kanske vill ändra för det här jobbet körs.

    ![Kör jobbinställningar](./media/storsimple-data-manager-ui/run-settings.png)

3. Klicka på **OK** och klicka sedan på **kör** att starta utskriften. För att övervaka jobbet, gå till den **jobb** sidan i din StorSimple Data Manager.

    ![Lista över jobb och status](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. Förutom övervakning i den **jobb** bladet du kan också lyssna på lagring kön där ett meddelande läggs varje gång en fil har flyttats från StorSimple till lagringskontot.


## <a name="next-steps"></a>Nästa steg

[Använd .NET SDK för att starta jobb StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).