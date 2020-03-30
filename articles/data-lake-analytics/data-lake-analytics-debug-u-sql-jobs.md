---
title: Felsök C#-kod för Azure Data Lake U-SQL-jobb
description: I den här artikeln beskrivs hur du felsöker ett U-SQL-felhörande hörn med Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315803"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felsök användardefinierad C#-kod för misslyckade U-SQL-jobb

U-SQL ger en utökningsbarhetsmodell med C#. I U-SQL-skript är det enkelt att anropa C#-funktioner och utföra analytiska funktioner som SQL-liknande deklarativt språk inte stöder. Mer information om U-SQL utökningsbarhet finns i [U-SQL-programmerbarhetsguide](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

I praktiken kan valfri kod behöva felsöka, men det är svårt att felsöka ett distribuerat jobb med anpassad kod i molnet med begränsade loggfiler. [Azure Data Lake Tools för Visual Studio](https://aka.ms/adltoolsvs) innehåller en funktion som kallas **Misslyckad Vertex Debug**, som hjälper dig att lättare felsöka de fel som uppstår i din anpassade kod. När U-SQL-jobbet misslyckas behåller tjänsten feltillståndet och verktyget hjälper dig att hämta molnfelsmiljön till den lokala datorn för felsökning. Den lokala nedladdningen fångar hela molnmiljön, inklusive indata och användarkod.

Följande video visar misslyckade vertex debug i Azure Data Lake Tools för Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio kräver följande två uppdateringar för att använda den här funktionen: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) och [Universal C Runtime för Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Hämtning misslyckades med hörn till lokal dator

När du öppnar ett misslyckat jobb i Azure Data Lake Tools för Visual Studio visas ett gult varningsfält med detaljerade felmeddelanden på felfliken.

1. Klicka på **Hämta** om du vill hämta alla nödvändiga resurser och indataströmmar. Om hämtningen inte slutförs klickar du på **Försök igen**.

2. Klicka på **Öppna** när hämtningen är klar för att generera en lokal felsökningsmiljö. En ny felsökningslösning öppnas, och om du har en befintlig lösning öppen i Visual Studio, se till att spara och stänga den innan felsökning.

![Azure Data Lake Analytics U-SQL felsökning visual studio nedladdning vertex](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurera felsökningsmiljön

> [!NOTE]
> Innan du felsöker bör du kontrollera **undantag för vanliga språkkörningar** i fönstret Undantagsinställningar (**Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL-felsökning av visual studio-inställning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

I den nya lanserade Visual Studio-instansen kanske du inte hittar den användardefinierade C#-källkoden:

1. [Jag kan hitta min källkod i lösningen](#source-code-is-included-in-debugging-solution)

2. [Jag kan inte hitta min källkod i lösningen](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Källkod ingår i felsökningslösningen

Det finns två fall som C# källkoden fångas:

1. Användarkoden definieras i den bakomkodade `Script.usql.cs` filen (vanligtvis namngiven i ett U-SQL-projekt).

2. Användarkoden definieras i C#-klassbiblioteksprojektet för U-SQL-program och registreras som sammansättning med **felsökningsinformation**.

Om källkoden importeras till lösningen kan du använda felsökningsverktygen i Visual Studio (klocka, variabler osv.) för att felsöka problemet:

1. Starta felsökningen genom att trycka på **F5**. Koden körs tills den stoppas av ett undantag.

2. Öppna källkodsfilen och ange brytpunkter och tryck sedan på **F5** för att felsöka koden steg för steg.

    ![Undantag för Azure Data Lake Analytics U-SQL-felsökning](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Källkod ingår inte i felsökningslösningen

Om användarkoden inte ingår i kod-bakom-filen, eller om du inte registrerade sammansättningen med **felsökningsinformation,** inkluderas inte källkoden automatiskt i felsökningslösningen. I det här fallet behöver du extra steg för att lägga till källkoden:

1. Högerklicka på **Lösning "VertexDebug" > Lägg till > befintligt projekt...** för att hitta sammansättningskällakoden och lägga till projektet i felsökningslösningen.

    ![Azure Data Lake Analytics U-SQL-felsökningssökningsprojekt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Hämta sökvägen till projektmappen för **Projektet FailedVertexDebugHost.** 

3. Högerklicka på **det tillagda monteringskällkodprojektet > Egenskaper,** markera fliken **Bygg** till vänster och klistra in den kopierade sökvägen som slutar med \bin\debug som **utdata > utdatasökvägen**. Den slutliga utdatabanan är som `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Azure Data Lake Analytics U-SQL-felsökningsuppsättning pdb-sökväg](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Efter dessa inställningar börjar du felsöka med **F5** och brytpunkter. Du kan också använda felsökningsverktygen i Visual Studio (klocka, variabler osv.) för att felsöka problemet.

> [!NOTE]
> Återskapa sammansättningens källkodsprojekt varje gång efter att du har ändrat koden för att generera uppdaterade PDB-filer.

## <a name="resubmit-the-job"></a>Skicka jobbet igen

Om utdatafönstret har felsökts visas följande meddelande om projektet har slutförts:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL-felsökning lyckas](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Så här skickar du det misslyckade jobbet igen:

1. För jobb med bakomkodningslösningar kopierar du C#-koden `Script.usql.cs`till den bakomgående källkoden (vanligtvis ).

2. För jobb med sammansättningar högerklickar du på monteringskällkodprojektet i felsökningslösningen och registrerar de uppdaterade DLL-sammansättningarna i din Azure Data Lake-katalog.

3. Skicka in U-SQL-jobbet igen.

## <a name="next-steps"></a>Nästa steg

- [U-SQL-programmerbarhetsguide](data-lake-analytics-u-sql-programmability-guide.md)
- [Utveckla U-SQL-användardefinierade operatorer för Azure Data Lake Analytics-jobb](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testa och felsöka U-SQL-jobb med lokal körning och Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Felsöker ett onormalt återkommande jobb](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
