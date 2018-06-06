---
title: Felsöka användardefinierade C#-kod för misslyckade Azure Data Lake U-SQL-jobb
description: Den här artikeln beskriver hur du felsöka en misslyckad U-SQL-nod med hjälp av Azure Data Lake-verktyg för Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: d8926f0defdfcc0e045ae87de6fd82755a5a35eb
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736855"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felsöka användardefinierade C#-kod för misslyckade U-SQL-jobb

U-SQL ger en modellen för utökning med C#. Det är enkelt att anropa C#-funktioner och utföra analysfunktioner som SQL-liknande deklarativ språket inte stöder i U-SQL-skript. Mer information för U-SQL-utökningsbarhet finns [U-SQL-programmering guiden](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

I praktiken någon kod behöva felsökning, men det är svårt att felsöka en distribuerad jobbet med anpassad kod på molnet med begränsad loggfiler. [Azure Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs) innehåller en funktion som kallas **misslyckades Vertex felsöka**, som hjälper dig att du enkelt kan felsöka fel som uppstår i den anpassade koden. Om U-SQL-jobb misslyckas tjänsten håller i feltillstånd och verktyget hjälper dig att hämta fel molnmiljö till den lokala datorn för felsökning. Lokala nedladdningen fångar hela molnmiljön, inklusive alla indata och användarkod.

Följande videoklipp visar misslyckades Vertex Debug i Azure Data Lake-verktyg för Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio kräver följande två uppdateringar för att använda den här funktionen: [Microsoft Visual C++ 2015 Redistributable uppdatering 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) och [Universal C Runtime för Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Gick inte att hämta hörn till den lokala datorn

När du öppnar ett jobb som misslyckades i Azure Data Lake-verktyg för Visual Studio kan se du ett gult avisering fält med detaljerade felmeddelanden på fliken fel.

1. Klicka på **hämta** att hämta alla nödvändiga resurser och inkommande dataströmmar. Om hämtningen inte slutförs, klicka på **försök**.

2. Klicka på **öppna** När nedladdningen är klar för att generera en lokal felsökningsmiljö. Öppnas en ny lösning för felsökning och om du har befintliga lösningen öppnas i Visual Studio du se till att spara och Stäng den innan du felsökning.

![Azure Data Lake Analytics U-SQL felsökning i visual studio download vertex](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurera felsökning

> [!NOTE]
> Innan du felsökning, måste du kontrollera att **Common Language Runtime undantag** i fönstret undantag inställningar (**Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL felsökning i visual studio-inställning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

I den nya startade Visual Studio-instansen kan eller inte kan hitta den användardefinierade C#-källkoden:

1. [Jag kan hitta min källkoden i lösningen](#source-code-is-included-in-debugging-solution)

2. [Jag kan inte hitta min källkoden i lösningen](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Källkoden ingår i Felsökning lösning

Det finns två fall C#-källkod avbildas:

1. Användarkod anges i filen med bakomliggande kod (vanligtvis namnet `Script.usql.cs` i ett projekt U-SQL).

2. Användarkoden definieras i C# klassbiblioteksprojektet för U-SQL-program och registrerad som sammansättning med **debug info**.

Du kan använda Visual Studio-felsökningsverktygen (titta på, variabler, etc.) för att felsöka problemet om källkoden importeras till lösningen:

1. Tryck på **F5** att starta felsökningen. Koden körs förrän den har stoppats av ett undantagsfel.

2. Öppna källkodsfilen och ange brytpunkter, tryck på **F5** för felsökning av kod steg för steg.

    ![Azure Data Lake Analytics U-SQL debug-undantag](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Källkoden ingår inte i lösningen-felsökning

Om användarkoden ingår inte i filen med bakomliggande kod eller registrerades inte sammansättningen med **debug info**, och sedan källkoden inte ingår automatiskt i lösningen för felsökning. I det här fallet behöver du ytterligare åtgärder för att lägga till i källkoden:

1. Högerklicka på **lösning 'VertexDebug' > Lägg till > befintligt projekt...**  att hitta sammansättningen källkoden och lägger till projektet i Felsökning lösningen.

    ![Azure Data Lake Analytics U-SQL-debug lägga till projekt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Hämta projektet mappsökväg för **FailedVertexDebugHost** projekt. 

3. Högerklicka på **lagts till sammansättningen Kodprojekt > Egenskaper**väljer den **skapa** fliken längst till vänster och klistra in den kopierade sökvägen som slutar med \bin\debug som **utdata > Utdatasökvägen**. Sista Utdatasökvägen är som ”<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\".

    ![Ange sökväg till pdb för Azure Data Lake Analytics U-SQL-felsökning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

När dessa inställningar startar felsökning med **F5** och brytpunkter. Du kan också använda Visual Studio felsökningsverktyg (titta på, variabler, etc.) för att felsöka problemet.

> [!NOTE]
> Återskapa sammansättningsprojekt källa kod varje gång när du ändrar koden för att generera uppdaterade .pdb filer.

## <a name="resubmit-the-job"></a>Skicka jobbet

Efter felsökning visar utdatafönstret följande meddelande om projektet har slutförts:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL-debug lyckas](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Skicka om det misslyckade jobbet:

1. Kopiera C#-koden till bakomliggande kod källfilen för jobb med bakomliggande kod lösningar (vanligtvis `Script.usql.cs`).

2. För jobb med sammansättningar, högerklicka på sammansättningen Kodprojekt i lösningen för felsökning och registrera uppdaterade .dll sammansättningarna i Azure Data Lake-katalogen.

3. Skicka U-SQL-jobbet.

## <a name="next-steps"></a>Nästa steg

- [U-SQL-programmering guide](data-lake-analytics-u-sql-programmability-guide.md)
- [Utveckla U-SQL-användardefinierade operatorer för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testa och felsöka U-SQL-jobb med lokal körning och Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Så här felsöker du ett onormalt återkommande jobb](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
