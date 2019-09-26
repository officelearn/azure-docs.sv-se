---
title: Felsöka U-SQL-jobb – Azure Data Lake verktyg för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake verktyg för Visual Studio Code för att köra och felsöka U-SQL-jobb lokalt.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 2d78523f776c909845057aaf8660a7c6fba3ef6a
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309890"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Kör U-SQL och Felsök lokalt i Visual Studio Code
I den här artikeln beskrivs hur du kör U-SQL-jobb på en lokal utvecklings dator för att påskynda kod fasen eller för att felsöka kod lokalt i Visual Studio Code. Instruktioner för Azure Data Lake verktyg för Visual Studio Code finns i [använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Endast Windows-installationer av Azure Data Lake verktyg för Visual Studio stöder åtgärden att köra U-SQL lokalt och felsöka U-SQL lokalt. Installationer på macOS-och Linux-baserade operativ system har inte stöd för den här funktionen.

## <a name="set-up-the-u-sql-local-run-environment"></a>Konfigurera den lokala U-SQL-körnings miljön

1. Välj Ctrl + Shift + P för att öppna kommando-paletten och ange **sedan ADL: Ladda ned det lokala** kör paketet för att ladda ned paketen.  

   ![Ladda ned ADL LocalRun-beroende paket](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Leta upp beroende paketen från sökvägen som visas i fönstret **utdata** och installera sedan BuildTools och Win10SDK 10240. Här är en exempel Sök väg:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Leta upp beroende paketen](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2,1 om du vill installera **BuildTools**klickar du på visualcppbuildtools_full. exe i mappen LocalRunDependency och följer sedan anvisningarna i guiden.   

    ![Installera BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2,2 om du vill installera **Win10SDK 10240**klickar du på sdksetup. exe i mappen LocalRunDependency/win10sdk_ 10.0.10240 _2 och följer sedan anvisningarna i guiden.  

    ![Installera Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Konfigurera miljövariabeln. Ställ in miljövariabeln **SCOPE_CPP_SDK** på:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Starta den lokala körnings tjänsten och skicka U-SQL-jobbet till ett lokalt konto 
Använd **ADL för första gången användaren: Hämta lokala kör paket** för att hämta lokala kör paket, om du inte har [konfigurerat en lokal körnings miljö för U-SQL](#set-up-the-u-sql-local-run-environment).

1. Välj Ctrl + Shift + P för att öppna kommando-paletten och ange **sedan ADL: Starta lokal körnings**tjänst.   
2. Välj **acceptera** för att godkänna licens villkoren för program vara från Microsoft för första gången. 

   ![Godkänn licens villkoren för program vara från Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. CMD-konsolen öppnas. För första användare måste du ange **3**och sedan hitta sökvägen till den lokala mappen för dina data indata och utdata. Du kan använda standardvärdena för andra alternativ. 

   ![Data Lake verktyg för Visual Studio Code Local Run cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Välj Ctrl + Shift + P för att öppna kommando-paletten **, ange ADL: Skicka jobbet**och välj sedan **lokal** för att skicka jobbet till ditt lokala konto.

   ![Data Lake verktyg för Visual Studio Code Välj lokal](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. När du har skickat jobbet kan du Visa information om överföringen. Om du vill visa information om sändningen väljer du **jobUrl** i fönstret **utdata** . Du kan också Visa jobbets sändnings status från cmd-konsolen. Ange **7** i cmd-konsolen om du vill veta mer om jobb information.

   ![Data Lake verktyg för Visual Studio Code lokal körning](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![data Lake verktyg för Visual Studio Code lokal kör cmd-status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Starta en lokal fel sökning för U-SQL-jobbet  
För användare i första gången:

1. Använd **ADL: Hämta lokala kör paket** för att hämta lokala kör paket, om du inte har [konfigurerat en lokal körnings miljö för U-SQL](#set-up-the-u-sql-local-run-environment).
2. Installera .NET Core SDK 2,0 som det föreslås i rutan meddelande om det inte är installerat.
 
  ![påminnelse installerar dotNet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installera C# Visual Studio Code enligt rekommendationer i rutan meddelande om det inte är installerat. Klicka på **Installera** för att fortsätta och starta sedan om VSCode.

    ![Påminnelse att installeraC#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Utför lokal fel sökning genom att följa stegen nedan:
  
1. Välj Ctrl + Shift + P för att öppna kommando-paletten och ange **sedan ADL: Starta lokal körnings**tjänst. CMD-konsolen öppnas. Kontrol lera att **DataRoot** har angetts.
2. Ange en Bryt punkt i C# din bakomliggande kod.
3. Tillbaka till skript redigeraren, högerklicka och välj **ADL: Lokal fel**sökning.
    
   ![Data Lake verktyg för det lokala fel söknings resultatet i Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Utveckla U-SQL med python, R och CSharp för Azure Data Lake Analytics i VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Kom igång med Data Lake Analytics med PowerShell](data-lake-analytics-get-started-powershell.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
* [Använd Data Lake verktyg för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
* [Använda Data Lake Analytics-katalogen (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
