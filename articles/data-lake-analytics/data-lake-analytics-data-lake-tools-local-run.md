---
title: Köra Azure Data Lake U-SQL-skript på din lokala dator
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att köra U-SQL-jobb på din lokala dator.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "62113943"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Köra U-SQL-skript på din lokala dator

När du utvecklar U-SQL-skript kan du spara tid och kostnader genom att köra skripten lokalt. Azure Data Lake Tools för Visual Studio stöder körning av U-SQL-skript på din lokala dator. 

## <a name="basic-concepts-for-local-runs"></a>Grundläggande begrepp för lokala körningar

Följande diagram visar komponenterna för lokal körning och hur dessa komponenter mappas till molnkörning.

|Komponent|Lokal körning|Molnkörning|
|---------|---------|---------|
|Lagring|Rotmapp för lokala data|Standardkontot för Azure Data Lake Store|
|Compute|U-SQL lokal körning motor|Azure Data Lake Analytics-tjänst|
|Kör miljö|Arbetskatalog på lokal dator|Azure Data Lake Analytics-kluster|

De avsnitt som följer innehåller mer information om lokala körningskomponenter.

### <a name="local-data-root-folders"></a>Lokala datarotmappar

En lokal datarotmapp är ett **lokalt arkiv** för det lokala beräkningskontot. Alla mappar i det lokala filsystemet på den lokala datorn kan vara en lokal datarotmapp. Det är samma som standardkontot för Azure Data Lake Store för ett Data Lake Analytics-konto. Att byta till en annan datarotmapp är precis som att byta till ett annat standardlagringskonto. 

Datarotmappen används på följande sätt:
- Lagra metadata. Exempel är databaser, tabeller, tabellvärderade funktioner och sammansättningar.
- Slå upp in- och utdatabanorna som definieras som relativa sökvägar i U-SQL-skript. Genom att använda relativa sökvägar är det enklare att distribuera dina U-SQL-skript till Azure.

### <a name="u-sql-local-run-engines"></a>U-SQL lokala köra motorer

En U-SQL lokal körningsmotor är ett **lokalt beräkningskonto** för U-SQL-jobb. Användare kan köra U-SQL-jobb lokalt via Azure Data Lake Tools för Visual Studio. Lokala körningar stöds också via Azure Data Lake U-SQL SDK-kommandorads- och programmeringsgränssnitt. Läs mer om [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Fungerande kataloger

När du kör ett U-SQL-skript behövs en arbetskatalogmapp för att cachelagra kompileringsresultat, köra loggar och utföra andra funktioner. I Azure Data Lake Tools för Visual Studio är arbetskatalogen U-SQL-projektets arbetskatalog. Det ligger under `<U-SQL project root path>/bin/debug>`. Arbetskatalogen rensas varje gång en ny körning utlöses.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokala körningar i Microsoft Visual Studio

Azure Data Lake Tools för Visual Studio har en inbyggd lokal körningsmotor. Verktygen yta motorn som en lokal beräkningskonto. Om du vill köra ett U-SQL-skript lokalt väljer du kontot **Lokal eller** **Lokal projekt** i den nedrullningsbara menyn med redigerarmarginal för skriptet. Välj sedan **Skicka**.

![Skicka ett U-SQL-skript till ett lokalt konto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokala körningar med ett konto för lokal dator

Ett **konto på lokal dator** är ett delat lokalt beräkningskonto med en enda lokal datarotmapp som lokalt arkivkonto. Som standard finns datarotmappen på **C:\Användares\<användarnamn>\AppData\Local\USQLDataRoot**. Det är också konfigurerbart genom **Verktyg** > **Data Lake** > **Alternativ och inställningar**.

![Konfigurera en lokal datarotmapp](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Ett U-SQL-projekt krävs för en lokal körning. U-SQL-projektets arbetskatalog används för U-SQL-arbetskatalogen för lokala kör. Kompileringsresultat, körloggar och andra jobbkörningsrelaterade filer genereras och lagras under arbetskatalogmappen under den lokala körningen. Varje gång du kör skriptet igen rensas och återskapas alla filer i arbetskatalogen.

## <a name="local-runs-with-a-local-project-account"></a>Lokala körningar med ett lokalt projektkonto

Ett **lokalt projektkonto** är ett projektinsolerat lokalt beräkningskonto för varje projekt med en isolerad lokal datarotmapp. Varje aktivt U-SQL-projekt som öppnas i Solution `(Local-project: <project name>)` Explorer i Visual Studio har ett motsvarande konto. Kontona visas i både Server Explorer i Visual Studio och U-SQL-skriptredigerarens marginal.  

Kontot **För lokalt projekt** ger en ren och isolerad utvecklingsmiljö. Ett konto **på lokal dator** har en delad lokal datarotmapp som lagrar metadata och indata och utdata för alla lokala jobb. Men ett **lokalt projektkonto** skapar en tillfällig lokal datarotmapp under en U-SQL-projektarbetskatalog varje gång ett U-SQL-skript körs. Den här tillfälliga datarotmappen rensas när en ombyggnad eller repris inträffar. 

Ett U-SQL-projekt hanterar den isolerade lokala körmiljön genom en projektreferens och egenskap. Du kan konfigurera indatakällor för U-SQL-skript i både projektet och de refererade databasmiljöerna.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Hantera indatakällan för ett lokalt projektkonto 

Ett U-SQL-projekt skapar en lokal datarotmapp och ställer in data för ett **lokalt projektkonto.** En tillfällig datarotmapp rensas och återskapas under U-SQL-projektets arbetskatalog varje gång en ombyggnad och lokal körning inträffar. Alla datakällor som konfigureras av U-SQL-projektet kopieras till den här tillfälliga lokala datarotmappen innan det lokala jobbet körs. 

Du kan konfigurera rotmappen för dina datakällor. Högerklicka på**testdatakälla** **för U-SQL-projektegenskap** > **Property** > . När du kör ett U-SQL-skript på ett **lokalt projektkonto** kopieras alla filer och undermappar i mappen **Testdatakälla** till den tillfälliga lokala datarotmappen. Filer under undermappar ingår. När ett lokalt jobb har körts kan utdata också hittas under den tillfälliga lokala datarotmappen i projektets arbetskatalog. All denna utdata tas bort och rengöras när projektet byggs om och rengörs. 

![Konfigurera ett projekts testdatakälla](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Hantera en refererad databasmiljö för ett **lokalt projektkonto** 

Om en U-SQL-fråga använder eller frågar med U-SQL-databasobjekt måste du göra databasmiljöerna redo lokalt innan du kör U-SQL-skriptet lokalt. För ett **lokalt projektkonto** kan U-SQL-databasberoenden hanteras av U-SQL-projektreferenser. Du kan lägga till U-SQL-databasprojektreferenser i ditt U-SQL-projekt. Innan U-SQL-skript körs på ett **lokalt projektkonto** distribueras alla refererade databaser till den tillfälliga lokala datarotmappen. Och för varje körning rensas den tillfälliga datarotmappen som en ny isolerad miljö.

Se den här relaterade artikeln:
* Lär dig hur du hanterar U-SQL-databasdefinitioner och referenser i [U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Skillnaden mellan **konton för lokal och** lokal **projekt**

Ett konto på lokal dator simulerar ett Azure Data Lake **Analytics-konto** på användarnas lokala datorer. Den delar samma upplevelse med ett Azure Data Lake Analytics-konto. Ett **lokalt projektkonto** ger en användarvänlig lokal utvecklingsmiljö. Den här miljön hjälper användare att distribuera databasreferenser och indata innan de kör skript lokalt. Ett konto **på lokal dator** tillhandahåller en delad permanent miljö som kan nås via alla projekt. Ett **lokalt projektkonto** ger en isolerad utvecklingsmiljö för varje projekt. Det är utvilat för varje körning. Ett **lokalt projektkonto** ger en snabbare utvecklingsupplevelse genom att snabbt tillämpa nya ändringar.

Fler skillnader mellan **konton för lokal dator** och Lokalt **projekt** visas i följande tabell:

|Skillnadsvinkel|Lokal-maskin|Lokalt projekt|
|----------------|---------------|---------------|
|Lokal åtkomst|Kan nås av alla projekt.|Endast motsvarande projekt kan komma åt det här kontot.|
|Rotmapp för lokala data|En permanent lokal mapp. Konfigurerad via**Alternativ och inställningar för****Verktygsdatasjö** >  **Tools** > .|En tillfällig mapp som skapats för varje lokal körning under U-SQL-projektets arbetskatalog. Mappen rensas när en ombyggnad eller repris inträffar.|
|Indata för ett U-SQL-skript|Den relativa sökvägen under den permanenta lokala datarotmappen.|Ange genom > **testdatakälla för** **U-SQL-projektegenskap**. Alla filer och undermappar kopieras till den tillfälliga datarotmappen före en lokal körning.|
|Utdata för ett U-SQL-skript|Relativ sökväg under den permanenta lokala datarotmappen.|Utdata till den tillfälliga datarotmappen. Resultaten rengörs när en ombyggnad eller repris inträffar.|
|Refererad databasdistribution|Refererade databaser distribueras inte automatiskt när de körs mot ett **konto för lokal dator.** Det är samma sak för att skicka till ett Azure Data Lake Analytics-konto.|Refererade databaser distribueras automatiskt till kontot **Lokalt projekt** innan en lokal körning körs. Alla databasmiljöer rensas och distribueras om när en ombyggnad eller repris inträffar.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>En lokal körning med U-SQL SDK

Du kan köra U-SQL-skript lokalt i Visual Studio och även använda Azure Data Lake U-SQL SDK för att köra U-SQL-skript lokalt med kommandorads- och programmeringsgränssnitt. Genom dessa gränssnitt kan du automatisera U-SQL lokala körningar och tester.

Läs mer om [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md).
