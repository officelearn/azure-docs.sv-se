---
title: Felsöka en användardefinierad C#-kod för misslyckade Azure Data Lake U-SQL-jobb
description: Den här artikeln beskriver hur du felsöker ett misslyckade U-SQL-hörn med Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 5417f66696191cebadc2af9c6d634419a0eb8e5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615278"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felsöka en användardefinierad C#-kod för misslyckade U-SQL-jobb

U-SQL ger en modell för utökningsbarhet med C#. I U-SQL-skript är det enkelt att anropa C#-funktioner och utför analysfunktioner som inte stöder SQL-liknande deklarativa språk. Mer information för utökningsbarhet i U-SQL finns [U-SQL-Programmeringsguide](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

I praktiken kod kan behöva felsökning, men det är svårt att felsöka ett distribuerat jobb med anpassad kod i molnet med begränsad loggfiler. [Azure Data Lake Tools för Visual Studio](https://aka.ms/adltoolsvs) innehåller en funktion som kallas **misslyckades hörn felsöka**, som hjälper dig att du enkelt felsöka fel som inträffar i din anpassade kod. Om U-SQL-jobb misslyckas håller feltillstånd och verktyget hjälper dig att hämta fel molnmiljön till den lokala datorn för felsökning. Lokala nedladdningen fångar hela cloud-miljön, inklusive alla indata och användarkod.

Följande videoklipp visar misslyckades hörn felsökning i Azure Data Lake Tools för Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio krävs följande två uppdateringar för att använda den här funktionen: [Microsoft Visual C++ 2015 Redistributable uppdatering 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) och [Universal C Runtime för Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Hämtning misslyckades hörn till lokal dator

När du öppnar ett misslyckat jobb i Azure Data Lake Tools för Visual Studio kan se du ett gult fält med detaljerade felmeddelanden på fliken fel.

1. Klicka på **hämta** att hämta alla nödvändiga resurser och indataströmmar. Om nedladdningen inte Slutför, klickar du på **försök**.

2. Klicka på **öppna** När nedladdningen är klar för att generera en lokal felsökningsmiljö. Öppnas en ny lösning för felsökning och om du har befintlig lösning öppnas i Visual Studio se till att spara och Stäng den innan du felsökning.

![Hörn download visual studio för felsökning för Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurera miljön för felsökning

> [!NOTE]
> Innan felsökning, bör du kontrollera **Common Language Runtime undantag** i fönstret Undantagsinställningar (**Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL debug visual studio-inställning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

I den nya startade Visual Studio-instansen du kanske eller kanske inte att hitta den användardefinierade C#-källkoden:

1. [Jag kan hitta mitt källkoden i lösningen](#source-code-is-included-in-debugging-solution)

2. [Jag kan inte hitta mitt källkoden i lösningen](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Källkoden ingår i lösningen-felsökning

Det finns två fall att C#-källkod hämtas:

1. Användarkod anges i koden bakom filen (vanligtvis namnet `Script.usql.cs` i ett U-SQL-projekt).

2. Användarkoden definieras i C# klassbiblioteksprojektet för U-SQL-program och registreras som sammansättning med **felsöka info**.

Om källkoden importeras till lösningen kan du använda felsökningsverktyg för Visual Studio (titta på, variabler, etc.) för att felsöka problemet:

1. Starta felsökningen genom att trycka på **F5**. Koden körs tills den har stoppats av ett undantag.

2. Öppna källfilen koden och ange brytpunkter, tryck på **F5** felsöka kod steg för steg.

    ![Azure Data Lake Analytics U-SQL-debug-undantag](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Källkoden ingår inte i lösningen-felsökning

Om användarkoden ingår inte i koden bakom filen eller registrerades inte sammansättningen med **felsöka info**, och sedan källkoden inte ingår automatiskt i Felsökning lösningen. I det här fallet behöver du ytterligare åtgärder för att lägga till källkoden:

1. Högerklicka på **lösning ”VertexDebug” > Lägg till > befintliga projekt...**  att hitta sammansättningen källkoden och lägga till projektet i Felsökning lösningen.

    ![Azure Data Lake Analytics U-SQL-debug lägger du till projekt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Hämta projektet mappsökvägen för **FailedVertexDebugHost** projekt. 

3. Högerklicka på **har lagts till sammansättningen kod projekt > Egenskaper**väljer den **skapa** fliken till vänster och klistra in den kopierade sökvägen som slutar med \bin\debug som **utdata > Utdatasökvägen**. Sökvägen för slutgiltiga utdata som liknar `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Azure Data Lake Analytics U-SQL-debug ange pdb-sökväg](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Efter de här inställningarna, starta felsökning med **F5** och brytpunkter. Du kan också använda Visual Studio felsökningsverktyg (titta på, variabler, etc.) för att felsöka problemet.

> [!NOTE]
> Återskapa sammansättningen kod projekt varje gång när du har ändrat koden för att generera uppdaterade .pdb filer.

## <a name="resubmit-the-job"></a>Skicka jobbet

När du felsöker visar utdatafönstret följande meddelande om projektet är klar:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL-debug lyckas](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Att skicka det misslyckade jobbet:

1. Kopiera C#-kod för jobb med bakomliggande kod lösningar till källfilen bakomliggande kod (vanligtvis `Script.usql.cs`).

2. Högerklicka på projektet sammansättningen källa kod i Felsökning lösning för jobb med sammansättningar och registrera uppdaterade .dll-sammansättningar i din Azure Data Lake-katalog.

3. Skicka U-SQL-jobb.

## <a name="next-steps"></a>Nästa steg

- [U-SQL-Programmeringsguide](data-lake-analytics-u-sql-programmability-guide.md)
- [Utveckla U-SQL-användardefinierade operatörer för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testa och felsöka U-SQL-jobb med lokal körning och Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Så här felsöker du ett avvikande, återkommande jobb](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
