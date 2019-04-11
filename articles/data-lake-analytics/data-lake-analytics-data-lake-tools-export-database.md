---
title: Exportera en U-SQL-databas med Azure Data Lake Tools för Visual Studio
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att exportera en U-SQL-databas och importera dem automatiskt till ett lokalt konto.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: fe28aa8b88f557d4bbcdabf1de1c4bc6491743ce
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471417"
---
# <a name="export-a-u-sql-database"></a>Exportera en U-SQL-databas

I den här artikeln lär du dig hur du använder [Azure Data Lake Tools för Visual Studio](https://aka.ms/adltoolsvs) du exporterar en U-SQL-databas som ett enda U-SQL-skript och hämtade resurser. Du kan importera den exporterade databasen till ett lokalt konto i samma process.

Kunder Underhåll vanligtvis flera miljöer för utveckling, testning och produktion. Dessa miljöer finns på både ett lokalt konto, på en utvecklare lokala datorn och på ett Azure Data Lake Analytics-konto i Azure. 

När du utvecklar och justera U-SQL-frågor i utvecklings- och testmiljöer behöver utvecklare ofta skapa sitt arbete i en produktionsdatabas. Guiden Exportera hjälper dig att påskynda den här processen. Med hjälp av guiden, kan utvecklare klona befintliga databasmiljö och exempeldata till andra Data Lake Analytics-konton.

## <a name="export-steps"></a>Exportera steg

### <a name="step-1-export-the-database-in-server-explorer"></a>Steg 1: Exportera databasen i Server Explorer

Alla Data Lake Analytics-konton som du har behörigheter för finns i Server Explorer. Så här exporterar databasen:

1. I Server Explorer expanderar du det konto som innehåller den databas som du vill exportera.
2. Högerklicka på databasen och välj sedan **exportera**. 
   
    ![Server Explorer - Export en databas](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Om den **exportera** menyalternativet inte är tillgänglig, måste du [uppdatera verktyget till lasted versionen](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Steg 2: Konfigurera de objekt som du vill exportera

Om du behöver bara en liten del av en stor databas kan konfigurera du en delmängd av objekt som du vill exportera i exportguiden. 

Export-åtgärden har slutförts genom att köra ett U-SQL-jobb. Därför medför export från ett Azure-konto vissa kostnader.

![Guiden för databasexport - Välj Exportera objekt](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Steg 3: Kontrollera listan med objekt och andra konfigurationer

I det här steget ska du kontrollera de markerade objekten i den **Export objektlistan** box. Om det finns några fel, väljer **föregående** gå tillbaka och konfigurera korrekt objekt som du vill exportera.

Du kan också konfigurera andra inställningar för export-mål. Konfigurationen beskrivningar finns i följande tabell:

|Konfiguration|Beskrivning|
|-------------|-----------|
|Målnamn|Det här namnet anger där du vill spara de exporterade databasresurserna. Exempel är sammansättningar, ytterligare filer och exempeldata. En mapp med det här namnet skapas under rotmappen för din lokala data.|
|Projektkatalog|Den här sökvägen definierar där du vill spara det exporterade U-SQL-skriptet. Alla objektdefinitioner för databasen sparas på den här platsen.|
|Endast schema|Om du väljer det här alternativet exporteras endast databasdefinitioner och resurser (t.ex. sammansättningar och ytterligare filer).|
|Schema och Data|Om du väljer det här alternativet exporteras databasdefinitioner, resurser och data. De översta N raderna med tabeller exporteras.|
|Importera till lokal databas automatiskt|Om du väljer det här alternativet kan den exporterade databasen är importeras automatiskt till den lokala databasen när du exporterar har slutförts.|

![Guiden för databasexport - objekt exportlistan och andra konfigurationer](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Steg 4: Kontrollera exportresultat

När du exporterar är klar kan visa du exporterade resultat i loggfönstret i guiden. I följande exempel visas hur du hittar exporterade U-SQL-skript och databasresurser, inklusive sammansättningar, ytterligare filer och exempeldata:

![Guiden för databasexport - exportresultat](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importera exporterade databasen till ett lokalt konto

Det enklaste sättet att importera den exporterade databasen är att välja den **importera till lokala databas automatiskt** kryssrutan under exportprocessen i steg3. Om du inte markerar kryssrutan måste du först hitta exporterade U-SQL-skriptet i exportloggen för. Kör U-SQL-skript lokalt för att importera databasen till det lokala kontot.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importera exporterade databasen till ett Data Lake Analytics-konto

Att importera databasen till annat Data Lake Analytics-konto:

1. Ladda upp de exporterade resurser, inklusive sammansättningar, ytterligare filer och exempeldata till Azure Data Lake Store-standardkontot för Data Lake Analytics-konto som du vill importera till. Du kan hitta den exporterade resursmappen i rotmappen för lokala data. Ladda upp hela mappen till roten för Data Lake Store-kontot av standardtyp.
2. När överföringen är klar kan du skicka den exporterade U-SQL-skript för att det Data Lake Analytics-konto som du vill importera databasen till.

## <a name="known-limitations"></a>Kända begränsningar

För närvarande, om du väljer den **Schema och Data** alternativet i steg3, verktyget kör ett U-SQL-jobb för att exportera de data som lagras i tabeller. Därför data exporterar processen kan vara långsam och du kan medföra kostnader. 

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om U-SQL-databaser](/u-sql/data-definition-language-ddl-statements) 
* [Testa och felsöka U-SQL-jobb genom att använda lokala kör och Azure Data Lake U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)


