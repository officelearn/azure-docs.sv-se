---
title: "Testa och felsöka U-SQL-jobb med hjälp av lokal körning och Azure Data Lake U-SQL-SDK | Microsoft Docs"
description: "Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio och Azure Data Lake U-SQL-SDK för att testa och felsöka U-SQL-jobb på din lokala arbetsstationen."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Testa och felsöka U-SQL-jobb med hjälp av lokal kör och Azure Data Lake U-SQL-SDK

Du kan använda Azure Data Lake Tools för Visual Studio och Azure Data Lake U-SQL SDK för att köra U-SQL-jobb på din arbetsstation, som i Azure Data Lake-tjänsten. Med de här två funktionerna som körs lokalt kan du spara tid när du testar och felsöker dina U-SQL-jobb.

## <a name="understand-the-data-root-folder-and-the-file-path"></a>Förstå data till rotmappen och sökvägen till filen

Både lokal körning och U-SQL-SDK kräver en data-rotmappen. Data-rotmappen är ”lokalt Arkiv” för den lokala beräkningskonto. Det motsvarar att Azure Data Lake Store-konto för Data Lake Analytics-konto. Växla till en annan data-rotmapp är precis som om du växlar till en annan store-konto. Om du vill komma åt ofta delade data med olika dataroten mappar måste du använda absoluta sökvägar i skript. Skapa filen system symboliska länkar (till exempel **mklink** på NTFS) under data till rotmappen att peka till delade data.

Data-rotmappen används för att:

- Lagra metadata, inklusive databaser, tabeller, tabellvärdesfunktioner (Tabellvärdesfunktioner) och sammansättningar.
- Leta upp de inkommande och utgående sökvägar som har definierats som relativa sökvägar i U-SQL. Använda relativa sökvägar gör det enklare att distribuera dina U-SQL-projekt till Azure.

Du kan använda både en relativ sökväg och en lokal absolut sökväg i U-SQL-skript. Den relativa sökvägen är i förhållande till angivna data-sökvägen till rotmappen. Vi rekommenderar att du använder ”/” som sökväg avgränsare att göra skript som är kompatibel med servern. Här följer några exempel på relativa sökvägar och deras motsvarande absoluta sökvägar. I det här exemplet är C:\LocalRunDataRoot data till rotmappen.

|Relativ sökväg|Absolut sökväg|
|-------------|-------------|
|/ABC/def/Input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|ABC/def/Input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/ABC/def/Input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Använd lokal kör från Visual Studio

Data Lake-verktyg för Visual Studio innehåller ett U-SQL lokalt körningen i Visual Studio. Med hjälp av den här funktionen kan du:

- Kör en U-SQL-skript lokalt, tillsammans med C#-sammansättningar.
- Felsöka en C#-sammansättning lokalt.
- Skapa, visa och ta bort kataloger för U-SQL (lokala databaser, sammansättningar, scheman och tabeller) från Server Explorer. Du kan också hitta den lokala katalogen också från Server Explorer.

    ![Data Lake-verktyg för Visual Studio lokala kör lokal katalog](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake-verktyg installationsprogrammet skapar en C:\LocalRunRoot mapp som ska användas som standard data-rotmappen. Standard lokala kör parallellitet är 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Konfigurera lokal körning i Visual Studio

1. Öppna Visual Studio.
2. Öppna **Server Explorer**.
3. Expandera **Azure** > **Datasjöanalys**.
4. Klicka på den **Data Lake** -menyn och klicka sedan på **alternativ och inställningar för**.
5. Expandera i vänster träd **Azure Data Lake**, och expandera sedan **allmänna**.

    ![Konfigurera inställningar för data Lake-verktyg för Visual Studio kör lokalt](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Ett Visual Studio U-SQL-projekt krävs för att utföra lokala kör. Den här delen skiljer sig från att köra U-SQL-skript från Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Att köra ett U-SQL-skript lokalt
1. Öppna projektet U-SQL Visual Studio.   
2. Högerklicka på ett U-SQL-skript i Solution Explorer och klicka sedan på **skicka skript**.
3. Välj **(lokal)** som Analytics-konto för att köra skriptet lokalt.
Du kan också klicka på **(lokal)** överst skriptfönstret-kontot och klicka sedan på **skicka** (eller Använd Ctrl + F5 kortkommandot).

    ![Data Lake-verktyg för Visual Studio lokala kör skicka jobb](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Felsök skript och C#-sammansättningar lokalt

Du kan felsöka C#-sammansättningar utan att skicka och registrera dem till Azure Data Lake Analytics-tjänsten. Du kan ange brytpunkter i både koden bakom filen och ett refererat C#-projekt.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Felsöka lokal kod i koden bakom filen

1. Ange brytpunkter i koden bakom filen.
2. Tryck på F5 för att felsöka skript lokalt.

> [!NOTE]
   > Följande procedur fungerar bara i Visual Studio 2015. Du kan behöva lägga till PDB-filer manuellt i äldre Visual Studio.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Om du vill felsöka lokal kod i ett refererat C#-projekt

1. Skapa ett C#-sammansättningsprojekt och skapa det för att generera DLL-filen för utdata.
2. Registrera DLL-filen med hjälp av ett U-SQL-uttryck:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ange brytpunkter i C#-koden.
4. Tryck på F5 för att felsöka skriptet med referens C#-DLL: en lokalt.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Använd lokal körning från Data Lake U-SQL-SDK

Du kan använda Azure Data Lake U-SQL-SDK för att köra U-SQL-skript lokalt med kommandoraden och programming interfaces förutom att köra U-SQL-skript lokalt med hjälp av Visual Studio. Du kan skala din lokal U-SQL-test via dessa.

Lär dig mer om [Azure Data Lake U-SQL-SDK](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Nästa steg

* Om du vill se en mer komplex fråga, se [analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill visa jobbinformation [Använd jobbet webbläsare och visa jobb för Azure Data Lake Analytics-jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
* Om du vill använda vyn vertex körning finns [använda vyn Vertex körning i Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
