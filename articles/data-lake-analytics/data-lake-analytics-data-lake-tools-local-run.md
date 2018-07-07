---
title: Kör Azure Data Lake U-SQL-skript på den lokala datorn | Microsoft Docs
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att köra U-SQL-jobb på din lokala dator.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888974"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>Kör U-SQL-skript på den lokala datorn

När du utvecklar U-SQL-skript, är det vanligt att köra U-SQL-skript lokalt som sparas kostnad och tid. Azure Data Lake Tools för Visual Studio har stöd för att köra U-SQL-skript på den lokala datorn. 

## <a name="basic-concepts-for-local-run"></a>Grundläggande begrepp för lokal körning

Under diagrammet visas komponenterna för lokal körning och hur de här komponenterna mappas till molnet som kör.

|Komponent|Lokal körning|Kör i molnet|
|---------|---------|---------|
|Storage|Lokala Data i rotmappen|Standard Azure Data Lake Store-konto|
|Compute|Lokala kör U-SQL-motor|Azure Data Lake Analytics-tjänsten|
|Körningsmiljö|Arbetskatalog på den lokala datorn|Azure Data Lake Analytics-kluster|

Fler förklaringar för lokala kör komponenter:

### <a name="local-data-root-folder"></a>Lokala Data i rotmappen

Lokala Data rotmappen är ett ”lokalt Arkiv” för den lokala beräkningskonto. Valfri mapp i det lokala filsystemet på den lokala datorn kan vara en lokal mapp i roten för Data. Det är lika med Azure Data Lake Store-standardkontot för ett Data Lake Analytics-konto. Växla till en annan mapp i roten för Data är precis som att byta till en annan store-standardkontot. 

Data rotmappen används för att:
- Store metadata, t.ex. databaser, tabeller, tabellvärdesfunktioner och sammansättningar.
- Leta upp de inkommande och utgående sökvägar som har definierats som relativa sökvägar i U-SQL-skript. Använda relativa sökvägar gör det enklare att distribuera dina U-SQL-skript till Azure.

### <a name="u-sql-local-run-engine"></a>Lokala kör U-SQL-motor

Lokala kör U-SQL-motorn är en ”lokal beräkningskonto” för U-SQL-jobb. Användare kan köra U-SQL-jobb lokalt via Azure Data Lake Tools för Visual Studio. Lokal körning är också tillgänglig via Azure Data Lake U-SQL SDK kommandoradsverktyg och programmeringsspråk gränssnitt. [Läs mer om Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directory"></a>Arbetskatalog

När du kör ett U-SQL-skript, krävs en arbetsmapp för katalogen för cachelagring kompilera resultat, loggarna för jobbkörning och så vidare. I Azure Data Lake Tools för Visual Studio, arbetskatalogen är arbetskatalogen för U-SQL-projekt (de finns vanligtvis `<U-SQL project root path>/bin/debug>`). Arbetskatalogen rensas varje gång en ny kör utlösta.

## <a name="local-run-in-visual-studio"></a>Lokal körning i Visual Studio

Azure Data Lake Tools för Visual Studio har en inbyggd lokal körning motor och hämtar den som lokala beräkningskonto. Att köra ett U-SQL-skript lokalt väljer (lokal dator) eller (Local-projekt)-konto i skriptets redigeraren marginal listrutan och klicka på **skicka**.

![Data Lake Tools för Visual Studio skicka skript för att lokalt konto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>Lokal körning med (lokal dator)-konto

(Lokal dator) är en delad lokala beräkningskonto med en enda lokal Data rotmapp som lokala store-konto. Data rotmappen är som standard finns i ”C:\Users\<användarnamn > \AppData\Local\USQLDataRoot”, det är också konfigureras via **Verktyg > Data Lake > Alternativ och inställningar**.

![Konfigurera lokala dataroten för data Lake Tools för Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Det krävs ett U-SQL-projekt för lokal körning. Arbetskatalog för U-SQL-projekt används för U-SQL arbetskatalog för lokal körning. Kompilera resultat, loggarna för jobbkörning och andra jobb körning-relaterade filer genereras och lagras under directory arbetsmapp vid lokal körning. Observera att varje gång du kör skriptet alla dessa filer i arbetskatalogen kommer att rensas och återskapas.

## <a name="local-run-with-local-project-account"></a>Lokal körning med (Local-projekt)-konto

(Local-projekt) är en isolerad projekt lokala beräkningskonto för varje projekt med isolerade lokala Data rotmappen. Varje aktiv U-SQL-projektet öppnar i Solution Explorer har ett motsvarande `(Local-project: <project name>)` konto visas både i Server Explorer och U-SQL-skript redigeraren marginal. 

Kontot (Local-projekt) ger utvecklare en ren och isolerad utvecklingsmiljö. Till skillnad från (lokal dator)-konton som har en delad lokala Data rotmapp lagring av metadata och indata/utdata för alla lokala jobb, skapas (Local-projekt)-konto en temporär lokal Dataroten mapp under arbetskatalog för U-SQL-projekt varje gång ett U-SQL-skript hämtar kör. Den här tillfälliga Data rotmappen hämtar rensas när återskapning eller kör händer. 

U-SQL-projekt är det bra om du vill hantera den här isolerade lokal körning miljö via projektreferens och egenskapen. Du kan både konfigurera indatakällor för U-SQL-skript i projektet, samt de refererade databasmiljöer.

### <a name="manage-input-data-source-for-local-project-account"></a>Hantera inkommande datakälla för kontot (Local-projekt)

U-SQL-projekt hand tar om lokala Dataroten mappen skapandet och data ställa in för kontot (Local-projekt). En tillfällig mapp i roten för Data rensas och återskapas under arbetskatalogen för U-SQL-projekt varje gång återskapning och lokal körning sker. Alla datakällor som konfigurerats av U-SQL-projekt kopieras till denna tillfälliga lokala Data rotmapp innan du lokala jobbkörningen. 

Du kan konfigurera rotmappen för dina datakällor via **högerklickar du på U-SQL-projekt > egenskapen > Testa datakällan**. När du kör U-SQL-skript (Local-projekt), alla filer och undermappar (inklusive filer under undermappar) **testa datakällan** mappen kopieras till den tillfälliga mappen för lokala Data rot. När lokal jobbkörning är klar finns utdataresultat även under temporära lokala Data rotmappen i arbetskatalogen för projektet. Observera att alla dessa utdata tas bort och rensas när projektet hämtar återskapas och tas sedan bort. 

![Data Lake Tools för Visual Studio konfigurera projektet test-datakälla](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>Hantera refererade databasmiljö för kontot (Local-projekt) 

Om en U-SQL fråga använder eller frågor med U-SQL database-objekt, måste du göra miljöerna som databasen redo lokalt innan du kör den här U-SQL-skript lokalt. U-SQL-databas beroenden kan hanteras av U-SQL-projektreferenserna för (Local-projekt)-konto. Du kan lägga till U-SQL-databas projektreferenserna till ditt U-SQL-projekt. Innan du kör U-SQL-skript på (Local-projekt)-konto, distribueras alla refererade databaser till den tillfälliga mappen för lokala Data rot. Och för varje körning tillfälliga Data rotmappen är klassas som ett nytt isolerad miljö.

Relaterade artiklar:
* [Lär dig att hantera definitionen av U-SQL-databas via U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [Lär dig hur du hanterar referens för U-SQL-databas i U-SQL-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>Skillnaden mellan (lokal dator) och (Local-projekt)

Konto för (lokal dator) syftar till att simulera ett Azure Data Lake Analytics-konto på användarens lokala dator. Den delar samma upplevelse med Azure Data Lake Analytics-konto. (Local-projekt) syftar till att skapa ett användarvänligt lokal utvecklingsmiljö som hjälper användare att distribuera databaser referenser och indata innan du kör skriptet lokalt. (Lokal dator)-konto tillhandahåller en delad permanent miljö, som kan nås via alla projekt. (Local-projekt)-konto tillhandahåller en isolerad utvecklingsmiljö för varje projekt och den uppdateras för varje körning. Baserat på ovan ger (Local-projekt) konto en snabbare utvecklingsupplevelse genom att tillämpa nya ändringar snabbt.

Du kan hitta mer skillnaden mellan (lokal dator) och (Local-projekt) i diagrammet på följande sätt:

|Skillnaden vinkel|(Lokal dator)|(Local-projekt)|
|----------------|---------------|---------------|
|Lokal åtkomst|Kan användas av alla projekt|Endast motsvarande projektet kan komma åt det här kontot|
|Rotmapp för lokala Data|En permanent lokal mapp. Konfigurerade via **Verktyg > Data Lake > Alternativ och inställningar**|En tillfällig mapp som skapas för varje lokal körning under U-SQL-projekt arbetskatalog. Mappen hämtar rensas när återskapning eller kör händer|
|Indata för U-SQL-skript|Relativ sökväg under permanent lokala Data rotmappen|Ange via **U-SQL-projektegenskap > Testa datakällan**. Alla filer och undermappar kopieras till den tillfälliga mappen Dataroten innan lokal körning|
|Utdata för U-SQL-skript|Relativ sökväg under permanent lokala Data rotmappen|Utdata till tillfälliga Data rotmappen. Resultaten rensas när återskapning eller kör händer.|
|Distribution av refererade databaser|Refererade databaser distribueras inte automatiskt när du kör mot konto (lokal dator). Samma för att skicka till Azure Data Lake Analytics-konto.|Refererade databaser har distribuerats till kontot (Local-projekt) automatiskt innan lokal körning. Alla databasmiljöer rensade och omdistribueras när återskapning eller kör händer.|

## <a name="local-run-with-u-sql-sdk"></a>Lokal körning med U-SQL SDK

Förutom för att köra U-SQL-skript lokalt i Visual Studio, kan du också använda Azure Data Lake U-SQL SDK: N för att köra U-SQL-skript lokalt med kommandoradsverktyg och programmeringsspråk gränssnitt. Du kan automatisera lokal U-SQL-körning och testa via dessa gränssnitt.

[Läs mer om Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Nästa steg

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [Hur du ställer in CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Använd U-SQL database-projekt för att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
