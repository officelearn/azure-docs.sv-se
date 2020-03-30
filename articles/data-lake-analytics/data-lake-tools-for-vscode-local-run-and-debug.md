---
title: Felsöka U-SQL-jobb – Azure Data Lake Tools för Visual Studio-kod
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio-kod för att köra och felsöka U-SQL-jobb lokalt.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72030030"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Kör U-SQL och felsöka lokalt i Visual Studio-kod
I den här artikeln beskrivs hur du kör U-SQL-jobb på en lokal utvecklingsdator för att snabba upp tidiga kodningsfaser eller felsöka kod lokalt i Visual Studio Code. Instruktioner om Azure Data Lake Tool för Visual Studio-kod finns i [Använda Azure Data Lake Tools för Visual Studio-kod](data-lake-analytics-data-lake-tools-for-vscode.md).

Endast Windows-installationer av Azure Data Lake Tools för Visual Studio stöder åtgärden för att köra U-SQL lokalt och felsöka U-SQL lokalt. Installationer på macOS- och Linux-baserade operativsystem stöder inte den här funktionen.

## <a name="set-up-the-u-sql-local-run-environment"></a>Konfigurera den lokala körningsmiljön U-SQL

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten och ange sedan **ADL: Ladda ned lokalt körpaket** för att hämta paketen.  

   ![Ladda ned ADL LocalRun Dependency-paketen](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Leta reda på beroendepaketen från sökvägen som visas i **fönstret Utdata** och installera sedan BuildTools och Win10SDK 10240. Här är en exempelsökväg:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Leta reda på beroendepaketen](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Om du vill installera **BuildTools**klickar du på visualcppbuildtools_full.exe i mappen LocalRunDependency och följer sedan guideinstruktionerna.   

    ![Installera BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Om du vill installera **Win10SDK 10240**klickar du på sdksetup.exe i mappen LocalRunDependency/Win10SDK_10.0.10240_2 och följer sedan guidens instruktioner.  

    ![Installera Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Ställ in miljövariabeln. Ställ in **variabeln SCOPE_CPP_SDK** miljö till:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Starta den lokala körningstjänsten och skicka U-SQL-jobbet till ett lokalt konto 
För förstagångsanvändaren använder du **ADL: Ladda ned lokalt körpaket** för att hämta lokala körningspaket, om du inte har [konfigurerat lokalkörningsmiljö för U-SQL](#set-up-the-u-sql-local-run-environment).

1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten och ange sedan **ADL: Starta lokal körningstjänst**.   
2. Välj **Acceptera** om du vill acceptera licensvillkoren för programvara från Microsoft för första gången. 

   ![Acceptera licensvillkoren för programvara från Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Cmd-konsolen öppnas. För förstagångsanvändare måste du ange **3**och sedan hitta sökvägen till den lokala mappen för datainmatning och datautdata. Om du inte lyckas definiera banan med omvänt snedstreck kan du prova att snedstrecka framåt. För andra alternativ kan du använda standardvärdena.

   ![DataSjöverktyg för lokalkörningskod för Visual Studio-kod](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Välj Ctrl+Skift+P om du vill öppna kommandopaletten, ange **ADL: Skicka jobb**och välj sedan **Lokalt** för att skicka jobbet till ditt lokala konto.

   ![Datasjöverktyg för Visual Studio-kod väljer lokal](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. När du har skickat in jobbet kan du visa inlämningsinformationen. Om du vill visa inlämningsinformationen väljer du **jobUrl** i **utdatafönstret.** Du kan också visa status för jobböverföring från cmd-konsolen. Ange **7** i cmd-konsolen om du vill veta mer jobbinformation.

   ![DataSjöverktyg för lokal körning](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![av DataSjöverktyg för lokal körning av DataSjö för lokal körning av DataSjöverktyg för lokal körning](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Starta en lokal felsökning för U-SQL-jobbet  
För förstagångsanvändaren:

1. Använd **ADL: Ladda ned lokalt körpaket** för att hämta lokala körpaket, om du inte har [konfigurerat lokal körningsmiljö för U-SQL](#set-up-the-u-sql-local-run-environment).
2. Installera .NET Core SDK 2.0 enligt vad som föreslås i meddelanderutan, om det inte är installerat.
 
  ![påminnelse installerar Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installera C# för Visual Studio-kod enligt förslaget i meddelanderutan om den inte är installerad.Klicka på **Installera** för att fortsätta och starta sedan om VSCode.

    ![Påminnelse om att installera C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Följ stegen nedan för att utföra lokal felsökning:
  
1. Välj Ctrl+Skift+P om du vill öppna kommandopaletten och ange sedan **ADL: Starta lokal körningstjänst**. Cmd-konsolen öppnas. Kontrollera att **DataRoot** är inställt.
2. Ange en brytpunkt i c#-kod bakom.
3. Tillbaka till skriptredigeraren, högerklicka och välj **ADL: Lokal felsökning**.
    
   ![Lokala felsökningsresultat för DataSjöverktyg för lokal felsökning av Visual Studio-kod](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Utveckla U-SQL med Python, R och CSharp för Azure Data Lake Analytics i VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Komma igång med DataSjöanalys med PowerShell](data-lake-analytics-get-started-powershell.md)
* [Komma igång med Data Lake Analytics med Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Använda Data Lake Tools för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda katalogen DataSjöanalys(U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
