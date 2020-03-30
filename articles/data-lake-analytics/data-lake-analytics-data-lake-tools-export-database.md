---
title: Exportera U-SQL-databas- Azure Data Lake Tools för Visual Studio
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att exportera en U-SQL-databas och automatiskt importera den till ett lokalt konto.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: e5a52de0342e864cb108d8d590583fe64f72e3b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315800"
---
# <a name="export-a-u-sql-database"></a>Exportera en U-SQL-databas

I den här artikeln kan du läsa om hur du använder [Azure Data Lake Tools för Visual Studio för](https://aka.ms/adltoolsvs) att exportera en U-SQL-databas som ett enda U-SQL-skript och hämtade resurser. Du kan importera den exporterade databasen till ett lokalt konto i samma process.

Kunder har vanligtvis flera miljöer för utveckling, test och produktion. Dessa miljöer finns både på ett lokalt konto, på en utvecklares lokala dator och i ett Azure Data Lake Analytics-konto i Azure. 

När du utvecklar och ställerr in U-SQL-frågor i utvecklings- och testmiljöer behöver utvecklare ofta återskapa sitt arbete i en produktionsdatabas. Guiden Exportera databas hjälper till att påskynda den här processen. Genom att använda guiden kan utvecklare klona den befintliga databasmiljön och exempeldata till andra DataSjöanalyskonton.

## <a name="export-steps"></a>Exportera steg

### <a name="step-1-export-the-database-in-server-explorer"></a>Steg 1: Exportera databasen i Server Explorer

Alla DataSjöanalyskonton som du har behörighet för visas i Server Explorer. Så här exporterar du databasen:

1. Expandera kontot som innehåller databasen som du vill exportera i Server Explorer.
2. Högerklicka på databasen och välj sedan **Exportera**. 
   
    ![Serverutforskaren – exportera en databas](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Om alternativet **Exportera** meny inte är tillgängligt måste du [uppdatera verktyget till den senast senaste versionen](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Steg 2: Konfigurera de objekt som du vill exportera

Om du bara behöver en liten del av en stor databas kan du konfigurera en delmängd av objekt som du vill exportera i exportguiden. 

Exportåtgärden slutförs genom att köra ett U-SQL-jobb. Därför medför export från ett Azure-konto en viss kostnad.

![Guiden Exportera databas – markera exportobjekt](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Steg 3: Kontrollera objektlistan och andra konfigurationer

I det här steget kan du verifiera de markerade objekten i **listrutan Exportera objekt.** Om det finns några fel väljer du **Föregående** om du vill gå tillbaka och korrekt konfigurera de objekt som du vill exportera.

Du kan också konfigurera andra inställningar för exportmålet. Konfigurationsbeskrivningar visas i följande tabell:

|Konfiguration|Beskrivning|
|-------------|-----------|
|Namn på mål|Det här namnet anger var du vill spara de exporterade databasresurserna. Exempel är sammansättningar, ytterligare filer och exempeldata. En mapp med det här namnet skapas under den lokala datarotmappen.|
|Projektkatalog|Den här sökvägen definierar var du vill spara det exporterade U-SQL-skriptet. Alla databasobjektdefinitioner sparas på den här platsen.|
|Endast schema|Om du väljer det här alternativet exporteras endast databasdefinitioner och resurser (som sammansättningar och ytterligare filer).|
|Schema och data|Om du väljer det här alternativet exporteras databasdefinitioner, resurser och data. De översta N-raderna i tabeller exporteras.|
|Importera till lokal databas automatiskt|Om du väljer det här alternativet importeras den exporterade databasen automatiskt till den lokala databasen när exporten är klar.|

![Guiden Exportera databas - listan Exportera objekt och andra konfigurationer](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Steg 4: Kontrollera exportresultaten

När exporten är klar kan du visa de exporterade resultaten i loggfönstret i guiden. I följande exempel visas hur du hittar exporterade U-SQL-skript- och databasresurser, inklusive sammansättningar, ytterligare filer och exempeldata:

![Guiden Exportera databas – exportera resultat](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importera den exporterade databasen till ett lokalt konto

Det bekvämaste sättet att importera den exporterade databasen är att markera kryssrutan **Importera till lokal databas automatiskt** under exportprocessen i steg 3. Om du inte markerar den här rutan letar du först reda på det exporterade U-SQL-skriptet i exportloggen. Kör sedan U-SQL-skriptet lokalt för att importera databasen till ditt lokala konto.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importera den exporterade databasen till ett DataSjöanalyskonto

Så här importerar du databasen till olika DataSjöanalyskonto:

1. Ladda upp de exporterade resurserna, inklusive sammansättningar, ytterligare filer och exempeldata, till standardkontot för Azure Data Lake Store för datasjöanalyskontot som du vill importera till. Du hittar den exporterade resursmappen under den lokala datarotmappen. Ladda upp hela mappen till roten för standardkontot för DataSjölagring.
2. När överföringen är klar skickar du det exporterade U-SQL-skriptet till datasjöanalyskontot som du vill importera databasen till.

## <a name="known-limitations"></a>Kända begränsningar

Om du väljer alternativet **Schema och data** i steg 3 körs för närvarande verktyget ett U-SQL-jobb för att exportera data som lagras i tabeller. På grund av detta kan dataexportprocessen vara långsam och du kan ådra dig kostnader. 

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om U-SQL-databaser](/u-sql/data-definition-language-ddl-statements) 
* [Testa och felsöka U-SQL-jobb med lokal körning och Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)


