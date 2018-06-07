---
title: Kör U-SQL och felsöka lokalt i Azure Data Lake-verktyg för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio-koden för att köra och felsöka lokalt U-SQL-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: de41120a3a9d399dafecde4225d56767efcd9f38
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624837"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Kör U-SQL och felsöka lokalt i Visual Studio Code
Den här artikeln beskriver hur du kör U-SQL-jobb på en lokal utvecklingsdator att påskynda tidig kodning faser eller för att felsöka kod lokalt i Visual Studio-koden. Anvisningar för Azure Data Lake-verktyg för Visual Studio Code finns [Använd Azure Data Lake-verktyg för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md). 

## <a name="set-up-the-u-sql-local-run-environment"></a>Konfigurera den lokala kör miljön för U-SQL

1. Välj Ctrl + Skift + P för att öppna paletten kommando och ange sedan **ADL: hämta lokala kör beroende** att hämta paketen.  

   ![Hämta ADL LocalRun beroende-paket](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Leta upp beroende paket från den sökväg som visas i den **utdata** rutan, och sedan installera BuildTools och Win10SDK 10240. Här är en exempelsökväg:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Leta upp beroende-paket](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 för att installera **BuildTools**, visualcppbuildtools_full.exe i mappen LocalRunDependency och sedan följa instruktionerna i guiden.   

    ![Installera BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 används för att installera **Win10SDK 10240**, sdksetup.exe i mappen LocalRunDependency/Win10SDK_10.0.10240_2 och sedan följa instruktionerna i guiden.  

    ![Installera Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Ställ in miljövariabeln. Ange den **SCOPE_CPP_SDK** miljövariabeln:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Starta tjänsten lokalt kör och skicka U-SQL-jobbet till ett lokalt konto 
Första gången användaren, Använd **ADL: hämta lokala kör beroende** att hämta lokal kör paket, om du inte har [konfigurera lokal kör U-SQL-miljö](#set-up-the-u-sql-local-run-environment).

1. Välj Ctrl + Skift + P för att öppna paletten kommando och ange sedan **ADL: starta tjänsten för lokala kör**.   
2. Välj **acceptera** att godkänna Licensvillkor för programvara från Microsoft för första gången. 

   ![Acceptera licensvillkoren för programvara från Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Cmd-konsolen öppnas. För första gången en användare, måste du ange **3**, och leta sedan upp lokala sökvägen till dina indata och utdata. Du kan använda standardvärden för andra alternativ. 

   ![Data Lake-verktyg för Visual Studio Code lokala kör cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Välj Ctrl + Skift + P Öppna kommandot paletten genom att ange **ADL: skicka jobbet**, och välj sedan **lokala** att skicka jobb till det lokala kontot.

   ![Data Lake-verktyg för Visual Studio Code Välj lokala](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Du kan visa informationen som skickas automatiskt när du har skickat jobbet. Om du vill visa information skickas, Välj **jobUrl** i den **utdata** fönster. Du kan också visa jobbstatus för överföring från cmd-konsolen. Ange **7** i cmd-konsolen om du vill veta mer jobbinformation.

   ![Data Lake-verktyg för Visual Studio Code lokal körning utdata](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake-verktyg för Visual Studio Code lokal körning cmd-status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Starta en lokal debug för U-SQL-jobb  
För första gången användaren:

1. Använd **ADL: hämta lokala kör beroende** att hämta lokal kör paket, om du inte har [konfigurera lokal kör U-SQL-miljö](#set-up-the-u-sql-local-run-environment).
2. Installera .NET Core SDK 2.0 enligt förslaget i meddelanderutan, om inte installerad.
 
  ![påminnelse installerar Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installera C# för Visual Studio-koden som föreslås i meddelanderutan om inte installerad. Klicka på **installera** och starta sedan om VSCode.

    ![Påminnelse om att installera C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Följ stegen nedan för att göra lokala debug:
  
1. Välj Ctrl + Skift + P för att öppna paletten kommando och ange sedan **ADL: starta tjänsten för lokala kör**. Cmd-konsolen öppnas. Se till att den **DataRoot** har angetts.
2. Ange en brytpunkt i ditt C# bakomliggande kod.
3. Tillbaka till Skriptredigeraren, högerklicka och välj **ADL: lokala felsöka**.
    
   ![Data Lake-verktyg för Visual Studio Code lokala debug-resultat](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Utveckla U-SQL med Python, R och CSharp för Azure Data Lake Analytics i VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Kom igång med Data Lake Analytics med hjälp av PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Använd Data Lake-verktyg för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använd Data Lake Analytics(U-SQL) katalog](data-lake-analytics-use-u-sql-catalog.md)
