---
title: Kör U-SQL och Felsök lokalt i Azure Data Lake Tools för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio Code för att köra och felsöka lokalt U-SQL-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 42982e3fa0a854109a6b887640604498ac68847c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632267"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Kör U-SQL och Felsök lokalt i Visual Studio Code
Den här artikeln beskriver hur du kör U-SQL-jobb på en lokal utvecklingsdator påskynda tidiga faser av kodning eller felsöka kod lokalt i Visual Studio Code. Information om Azure Data Lake-verktyg för Visual Studio Code finns [Använd Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Endast Windows-installationer av Azure Data Lake Tools för Visual Studio stöder åtgärden för att köra U-SQL lokalt och felsöka U-SQL lokalt. Installationer på macOS och Linux-baserade operativsystem stöder inte den här funktionen.

## <a name="set-up-the-u-sql-local-run-environment"></a>Konfigurera den lokala körningsmiljön U-SQL

1. Välj Ctrl + Skift + P för att öppna kommandopaletten och ange sedan **ADL: hämta lokala kör paketet** att hämta paketen.  

   ![Ladda ned ADL LocalRun beroendepaket](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Leta upp de beroendepaket från den sökväg som visas i den **utdata** fönstret och sedan installera BuildTools och Win10SDK 10240. Här är ett exempel på sökväg:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Leta upp beroendepaketen](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 för att installera **BuildTools**, klickar du på visualcppbuildtools_full.exe i mappen LocalRunDependency och följ sedan anvisningarna i guiden.   

    ![Installera BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 används för att installera **Win10SDK 10240**, klickar du på sdksetup.exe i mappen LocalRunDependency/Win10SDK_10.0.10240_2 och följ sedan anvisningarna i guiden.  

    ![Installera Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Ställa in miljövariabeln. Ange den **SCOPE_CPP_SDK** miljövariabeln till:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Starta tjänsten för lokal körning och skickar U-SQL-jobb till ett lokalt konto 
För första gången användaren, använda **ADL: hämta lokala kör paketet** att hämta lokala kör paket, om du inte har [konfigurera U-SQL lokalt körningsmiljön](#set-up-the-u-sql-local-run-environment).

1. Välj Ctrl + Skift + P för att öppna kommandopaletten och ange sedan **ADL: starta lokala kör tjänsten**.   
2. Välj **acceptera** att acceptera licensvillkoren för programvara från Microsoft för första gången. 

   ![Acceptera licensvillkoren för programvara från Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Cmd-konsolen öppnas. För första gången en användare, måste du ange **3**, och leta sedan upp den lokal mappsökvägen för dina data indata och utdata. Du kan använda standardvärdena för andra alternativ. 

   ![Data Lake Tools för Visual Studio Code lokala kör cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Välj Ctrl + Skift + P öppna kommandopaletten genom att ange **ADL: skicka jobb**, och välj sedan **lokala** att skicka jobbet till det lokala kontot.

   ![Data Lake Tools för Visual Studio Code Välj lokala](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. När du har skickat jobbet kan du visa information skickas. Om du vill visa information om överföringen, Välj **jobUrl** i den **utdata** fönster. Du kan också visa status för jobbet ansökan från cmd-konsolen. Ange **7** i cmd-konsolen om du vill veta mer jobbinformation.

   ![Data Lake Tools för Visual Studio Code lokal körning utdata](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools för Visual Studio Code lokal körning cmd-status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Starta en lokal felsökning för U-SQL-jobb  
För första gången användaren:

1. Använd **ADL: hämta lokala kör paketet** att hämta lokala kör paket, om du inte har [konfigurera U-SQL lokalt körningsmiljön](#set-up-the-u-sql-local-run-environment).
2. Installera .NET Core SDK 2.0 enligt förslaget i meddelanderutan om inte är installerad.
 
  ![påminnelse installerar Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installera C# för Visual Studio Code som föreslås i meddelanderutan om det inte installerats. Klicka på **installera** att fortsätta och starta sedan om VSCode.

    ![Påminnelse om att installera C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Följ stegen nedan för att utföra lokal Felsökning:
  
1. Välj Ctrl + Skift + P för att öppna kommandopaletten och ange sedan **ADL: starta lokala kör tjänsten**. Cmd-konsolen öppnas. Se till att den **DataRoot** har angetts.
2. Konfigurera en brytpunkt i dina C# bakomliggande kod.
3. Tillbaka till Skriptredigeraren, högerklicka och välj **ADL: lokala felsöka**.
    
   ![Data Lake Tools för Visual Studio Code lokal felsökning resultat](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Utveckla U-SQL med Python, R och c# för Azure Data Lake Analytics i VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Kom igång med Data Lake Analytics med hjälp av PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Använda Data Lake Tools för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda Data Lake Analytics(U-SQL) catalog](data-lake-analytics-use-u-sql-catalog.md)
