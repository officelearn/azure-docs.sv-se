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
ms.openlocfilehash: 892147f3668ee811e3c43044478c650d2f37587a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630323"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Kör U-SQL-skript på den lokala datorn

När du utvecklar U-SQL-skript kan spara du tid och pengar genom att köra skripten lokalt. Azure Data Lake Tools för Visual Studio har stöd för U-SQL-skript som körs på den lokala datorn. 

## <a name="basic-concepts-for-local-runs"></a>Grundläggande begrepp för lokala körningar

I följande diagram visas komponenterna för lokal körning och hur de här komponenterna mappas till molnet som kör.

|Komponent|Lokal körning|Moln som kör|
|---------|---------|---------|
|Storage|Rotmapp för lokala data|Standard Azure Data Lake Store-konto|
|Compute|Lokala kör U-SQL-motor|Azure Data Lake Analytics-tjänsten|
|Körningsmiljön|Arbetskatalog på den lokala datorn|Azure Data Lake Analytics-kluster|

Avsnitten som följer ger mer information om lokala kör komponenter.

### <a name="local-data-root-folders"></a>Lokala data rotmappar

En rotmapp för lokala data är en **lokalt Arkiv** för lokalt compute-konto. Valfri mapp i det lokala filsystemet på den lokala datorn kan vara en rotmapp för lokala data. Det är samma som Azure Data Lake Store-standardkontot för ett Data Lake Analytics-konto. Växla till rotmappen för en annan data är precis som att byta till en annan store-standardkontot. 

Rotmappen data används på följande sätt:
- Store metadata. Exempel är databaser, tabeller, tabellvärdesfunktioner och sammansättningar.
- Leta upp de inkommande och utgående sökvägar som har definierats som relativa sökvägar i U-SQL-skript. Genom att använda relativa sökvägar, är det enklare att distribuera dina U-SQL-skript till Azure.

### <a name="u-sql-local-run-engines"></a>Lokal U-SQL kör motorer

Ett U-SQL lokalt kör motorn är en **lokala beräkningskonto** för U-SQL-jobb. Användare kan köra U-SQL-jobb lokalt via Azure Data Lake Tools för Visual Studio. Lokala körningar finns också tillgänglig via Azure Data Lake U-SQL SDK-gränssnitt för kommandoradsverktyg och programmeringsspråk. Läs mer om den [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Fungerande kataloger

När du kör ett U-SQL-skript, krävs en arbetsmapp för katalogen för att cachelagra kompileringsresultaten, kör loggar och utföra andra funktioner. I Azure Data Lake Tools för Visual Studio är arbetskatalogen arbetskatalog för U-SQL-projekt. Det finns under `<U-SQL project root path>/bin/debug>`. Arbetskatalogen är rensad varje gång ett nytt kör utlöses.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokal körs i Microsoft Visual Studio

Azure Data Lake Tools för Visual Studio har en inbyggd lokala kör motor. Verktyg för att visa upp motorn som en lokal beräkningskonto. Om du vill köra ett U-SQL-skript lokalt väljer den **lokal dator** eller **lokal – projekt** konto i listrutan i skriptets redigeraren marginal. Välj sedan **skicka**.

![Skicka ett U-SQL-skript för att ett lokalt konto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokala körs med ett lokalt datorkonto

En **lokal dator** konto är en delad lokal compute-konto med en enda lokal data rotmapp som lokala store-konto. Som standard rotmappen data finns på **C:\Users\<användarnamn > \AppData\Local\USQLDataRoot**. Det är också konfigureras via **verktyg** > **Datasjö** > **alternativ och inställningar**.

![Konfigurera en rotmapp för lokala data](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Det krävs ett U-SQL-projekt för en lokal körning. Arbetskatalog för U-SQL-projekt används för arbetskatalogen U-SQL lokalt kör. Kompilering resultat, köra loggar och andra jobb kör-relaterade filer genereras och lagras under directory arbetsmapp vid lokal körning. Varje gång du kör skriptet rensas alla filer i arbetskatalogen och återskapas.

## <a name="local-runs-with-a-local-project-account"></a>Lokala körs med ett konto för lokal-projekt

En **lokal – projekt** konto är en isolerad projekt lokal compute-konto för varje projekt med en isolerad lokala data-rotmappen. Alla aktiva U-SQL-projektet som öppnas i Solution Explorer i Visual Studio har ett motsvarande `(Local-project: <project name>)` konto. Kontona finns angivna i både Server Explorer i Visual Studio och U-SQL-skript redigeraren marginal.  

Den **lokal – projekt** konto tillhandahåller en ren och isolerad utvecklingsmiljö. En **lokal dator** konto har en rotmapp för delade lokala data som lagrar metadata och indata och utdata för alla lokala jobb. Men en **lokal – projekt** konto skapar en rotmappen för en tillfällig lokala data under en arbetskatalog för U-SQL-projekt varje gång ett U-SQL-skript körs. Denna rotmapp för tillfälliga data rensas när en återskapning eller kör händer. 

Ett U-SQL-projekt hanterar isolerade lokala körningsmiljön via en projektreferens för och egenskapen. Du kan konfigurera indatakällor för U-SQL-skript i både projektet och de refererade databasmiljöer.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Hantera inkommande datakällan för ett konto för lokal-projekt 

Ett U-SQL-projekt som skapar en rotmapp för lokala data och ställer in data för en **lokal – projekt** konto. En rotmapp för tillfälliga data rensas och återskapas under arbetskatalogen för U-SQL-projekt varje gång en återskapning och lokal körning sker. Alla datakällor som är konfigurerade med U-SQL-projekt kopieras till rotmappen för den här tillfälliga lokala data innan du kör lokala jobbet. 

Du kan konfigurera rotmappen för dina datakällor. Högerklicka på **U-SQL-projekt** > **egenskapen** > **testa datakällan**. När du kör ett U-SQL-skript på en **lokal – projekt** konto, alla filer och undermappar i den **testa datakällan** mappen kopieras till rotmappen temporära lokala data. Filer under undermappar ingår. När ett lokalt jobb har körts finns utdataresultat även under rotmappen temporära lokala data i arbetskatalogen för projektet. Dessa utdata tas bort och rensas när projektet hämtar återskapas och tas sedan bort. 

![Konfigurera datakälla för ett projekt test](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Hantera en refererad databas för en **lokal – projekt** konto 

Om en U-SQL fråga använder eller frågor med U-SQL database-objekt, måste du se miljöerna som databasen redo lokalt innan du kör U-SQL-skript lokalt. För en **lokal – projekt** U-SQL-databas som beroenden kan hanteras av projektreferenserna för U-SQL-konto. Du kan lägga till U-SQL-databas projektreferenserna till ditt U-SQL-projekt. Innan du kör U-SQL-skript på en **lokal – projekt** konto, alla refererade databaser har distribuerats till rotmappen temporära lokala data. Och för varje körning rotmappen för tillfälliga data rensas som en ny isolerad miljö.

Se den här relaterade artikeln:
* Lär dig hur du hanterar definitioner för U-SQL-databas och referenser i [U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Skillnaden mellan **lokal dator** och **lokal – projekt** konton

En **lokal dator** konto simulerar ett Azure Data Lake Analytics-konto på användarnas lokala datorer. Den delar samma upplevelse med ett Azure Data Lake Analytics-konto. En **lokal – projekt** konto tillhandahåller ett användarvänligt lokala utvecklingsmiljö. Den här miljön hjälper användare att distribuera referenser och inmatade data innan de kör skript lokalt. En **lokal dator** konto tillhandahåller en delad permanent miljö som kan nås via alla projekt. En **lokal – projekt** konto tillhandahåller en isolerad utvecklingsmiljö för varje projekt. Den uppdateras för varje körning. En **lokal – projekt** konto ger en snabbare utvecklingsupplevelse genom att snabbt tillämpa nya ändringar.

Mer skillnaderna mellan **lokal dator** och **lokal – projekt** konton visas i följande tabell:

|Skillnaden vinkel|Lokal dator|Lokal-projekt|
|----------------|---------------|---------------|
|Lokal åtkomst|Kan användas av alla projekt.|Endast motsvarande projektet kan komma åt det här kontot.|
|Rotmapp för lokala data|En permanent lokal mapp. Konfigurerade via **verktyg** > **Datasjö** > **alternativ och inställningar**.|En tillfällig mapp som skapas för varje lokal körning under U-SQL-projekt som arbetskatalog. Mappen hämtar rensas när en återskapning eller kör händer.|
|Indata för ett U-SQL-skript|Den relativa sökvägen under rotmappen permanent lokala data.|Ange via **U-SQL-projektegenskap** > **testa datakällan**. Alla filer och undermappar kopieras till rotmappen för tillfälliga data innan en lokal körning.|
|Utdata för ett U-SQL-skript|Relativ sökväg under rotmappen permanent lokala data.|Utdata till rotmappen för tillfälliga data. Resultaten rensas när en återskapning eller kör händer.|
|Distribution av refererade databaser|Refererade databaser inte distribuerats automatiskt när du kör mot ett **lokal dator** konto. Det är samma för att skicka till ett Azure Data Lake Analytics-konto.|Refererade databaser har distribuerats till den **lokal – projekt** konto automatiskt innan en lokal körning. Alla databasmiljöer rensade och omdistribueras när indexet återskapas eller kör händer.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>En lokal körning med U-SQL-SDK

Du kan köra U-SQL-skript lokalt i Visual Studio och även använda Azure Data Lake U-SQL SDK för att köra U-SQL-skript lokalt med kommandoradsverktyg och programmeringsspråk gränssnitt. Via dessa gränssnitt, kan du automatisera lokal U-SQL-körningar och tester.

Läs mer om den [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Nästa steg

- [Hur du ställer in en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md).
