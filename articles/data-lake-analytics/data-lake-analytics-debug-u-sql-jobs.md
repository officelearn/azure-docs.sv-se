---
title: "Felsöka U-SQL-jobb | Microsoft Docs"
description: "Lär dig att felsöka en misslyckad U-SQL-nod med hjälp av Visual Studio."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felsöka användardefinierade C#-kod för misslyckade U-SQL-jobb

U-SQL ger en modellen för utökning med C#, så att du kan skriva koden för att lägga till funktioner, till exempel en anpassad extraktor eller reducer. Läs mer i [U-SQL-programmering guiden](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). I praktiken någon kod måste felsökning och ge endast system för stordata begränsad runtime felsökningsinformation som loggfiler.

Azure Data Lake-verktyg för Visual Studio innehåller en funktion som kallas **misslyckades Vertex felsöka**, vilket gör att du klona en misslyckade jobbet från molnet till den lokala datorn för felsökning. Lokala klonen fångar hela molnmiljön, inklusive alla indata och användarkod.

Följande videoklipp visar misslyckades Vertex Debug i Azure Data Lake-verktyg för Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio kräver följande två uppdateringar, om de inte redan är installerade: [Microsoft Visual C++ 2015 Redistributable uppdatering 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) och [Universal C Runtime för Windows](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Gick inte att hämta hörn till den lokala datorn

När du öppnar ett jobb som misslyckades i Azure Data Lake-verktyg för Visual Studio kan se du ett gult avisering fält med detaljerade felmeddelanden på fliken fel.

1. Klicka på **hämta** att hämta alla nödvändiga resurser och inkommande dataströmmar. Om hämtningen inte slutförs, klicka på **försök**.

2. Klicka på **öppna** När nedladdningen är klar för att generera en lokal felsökningsmiljö. En ny Visual Studio-instans med en lösning för felsökning skapas och öppnas automatiskt.

![Azure Data Lake Analytics U-SQL felsökning i visual studio download vertex](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

Jobb kan inkludera bakomliggande kod källfiler eller registrerade sammansättningar, och dessa två typer har olika scenarier för felsökning.

- [Felsöka ett jobb som misslyckades med bakomliggande kod](#debug-job-failed-with-code-behind)
- [Felsöka ett jobb som misslyckades med sammansättningar](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Felsöka jobb som misslyckades med bakomliggande kod

Om ett U-SQL-jobb misslyckas och jobbet innehåller användarkod (vanligtvis namnet `Script.usql.cs` i ett U-SQL-projekt), att källkoden har importerats till felsökning lösningen.  Därifrån kan du använda Visual Studio felsökningsverktyg (titta på, variabler, etc.) för att felsöka problemet.

> [!NOTE]
> Innan du felsökning, måste du kontrollera att **Common Language Runtime undantag** i fönstret undantag inställningar (**Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL felsökning i visual studio-inställning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Tryck på **F5** att köra kod bakomliggande kod. Det kan köras förrän den har stoppats av ett undantagsfel.

2. Öppna den `ADLTool_Codebehind.usql.cs` filen och ange brytpunkter, tryck på **F5** för felsökning av kod steg för steg.

    ![Azure Data Lake Analytics U-SQL debug-undantag](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Felsöka jobb som misslyckades med sammansättningar

Om du använder registrerade sammansättningar i U-SQL-skript kan systemet kan inte hämta källkoden automatiskt. I det här fallet manuellt lägger till de sammansättningarna källfilerna i lösningen.

### <a name="configure-the-solution"></a>Konfigurera lösningen

1. Högerklicka på **lösning 'VertexDebug' > Lägg till > befintligt projekt...**  att hitta de sammansättningarna källkoden och lägger till projektet i Felsökning lösningen.

    ![Azure Data Lake Analytics U-SQL-debug lägga till projekt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Högerklicka på **LocalVertexHost > Egenskaper** i lösningen och kopiera den **Working Directory** sökväg.

3. Högerklicka på **sammansättningsprojekt källa kod > Egenskaper**väljer den **skapa** fliken längst till vänster och klistra in den kopierade sökvägen som **utdata > Utdatasökvägen**.

    ![Ange sökväg till pdb för Azure Data Lake Analytics U-SQL-felsökning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Tryck på **Ctrl + Alt + E**, kontrollera **Common Language Runtime undantag** i inställningar för undantag.

### <a name="start-debug"></a>Starta felsökning

1. Högerklicka på **Kodprojekt för sammansättningen källa > återskapa** till .pdb utdatafilerna till den `LocalVertexHost` arbetskatalogen.

2. Tryck på **F5** och projektet kommer att köras tills den har stoppats av ett undantagsfel. Du kan se följande varningsmeddelande som du kan ignorera. Det kan ta upp till en minut att komma till skärmen för felsökning.

    ![Azure Data Lake Analytics U-SQL felsökning i visual studio-varning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Öppna källkoden och ange brytpunkter, tryck på **F5** för felsökning av kod steg för steg.

Du kan också använda Visual Studio felsökningsverktyg (titta på, variabler, etc.) för att felsöka problemet.

> [!NOTE]
> Återskapa sammansättningsprojekt källa kod varje gång när du ändrar koden för att generera uppdaterade .pdb filer.

Efter felsökning visar utdatafönstret följande meddelande om projektet har slutförts:

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Azure Data Lake Analytics U-SQL-debug lyckas](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Skicka jobbet

När du har slutfört felsökning, skicka om det misslyckade jobbet.

1. Kopiera C#-koden till bakomliggande kod källfilen för jobb med bakomliggande kod lösningar (vanligtvis `Script.usql.cs`).
2. Registrera uppdaterade .dll sammansättningarna i databasen ADLA för jobb med sammansättningar:
    1. Från Server Explorer eller Cloud Explorer, expandera den **ADLA konto > databaser** nod.
    2. Högerklicka på **sammansättningar** och registrera ditt nya .dll-sammansättningar med databasen ADLA: ![Azure Data Lake Analytics U-SQL-debug registrera sammansättningen](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Skicka jobbet.

## <a name="next-steps"></a>Nästa steg

- [U-SQL-programmering guide](data-lake-analytics-u-sql-programmability-guide.md)
- [Utveckla U-SQL-användardefinierade operatorer för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Självstudier: utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
