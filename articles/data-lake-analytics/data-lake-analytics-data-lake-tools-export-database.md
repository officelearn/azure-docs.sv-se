---
title: "Exportera en U-SQL-databas med Azure Data Lake-verktyg för Visual Studio | Microsoft Docs"
description: "Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio för att exportera en U-SQL-databas och importera det automatiskt till ett lokalt konto."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 441606258f9541c9552925e7c0cbc9b3a9effb4d
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="export-a-u-sql-database"></a>Exportera en U-SQL-databas

I den här artikeln lär du dig hur du använder [Azure Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs) att exportera en U-SQL-databas som ett enda U-SQL-skript och hämtade resurser. Du kan importera den exporterade databasen till ett lokalt konto i samma process.

Kunder Underhåll vanligtvis flera miljöer för utveckling, testning och produktion. Dessa miljöer finns i både ett lokalt konto, på en utvecklare lokala datorn och på ett Azure Data Lake Analytics-konto i Azure. 

När du utvecklar och finjustera U-SQL-frågor i utvecklings- och testmiljöer behöver utvecklare ofta Återskapa arbetet i en produktionsdatabas. Databasexport kan påskynda den här processen. Med hjälp av guiden kan utvecklare klona den befintliga databasmiljön och exempeldata till andra Data Lake Analytics-konton.

## <a name="export-steps"></a>Exportera steg

### <a name="step-1-export-the-database-in-server-explorer"></a>Steg 1: Exportera databasen i Server Explorer

Alla Data Lake Analytics-konton som du har behörigheter för finns i Server Explorer. Att exportera databasen:

1. I Server Explorer expanderar du det konto som innehåller den databas som du vill exportera.
2. Högerklicka på databasen och välj sedan **exportera**. 
   
    ![Server Explorer - exportera ett databasobjekt](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Om den **exportera** menyalternativet är inte tillgänglig, måste du [uppdaterar verktyget till lasted versionen](http://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Steg 2: Konfigurera de objekt som du vill exportera

Om du behöver bara en liten del av en stor databas kan konfigurera du en delmängd av objekt som du vill exportera i guiden Exportera. 

Export-åtgärden har slutförts genom att köra ett U-SQL-jobb. Därför exportera från ett Azure-konto vissa kostnad uppkommer.

![Guiden för databasexport - Välj Exportera objekt](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Steg 3: Kontrollera listan med objekt och andra konfigurationer

I det här steget kontrollerar du de markerade objekten i den **Export objektlistan** rutan. Om det finns några fel, väljer **föregående** gå tillbaka och konfigurerar de objekt som du vill exportera.

Du kan också konfigurera andra inställningar för export-mål. Beskrivningar av konfiguration finns i följande tabell:

|Konfiguration|Beskrivning|
|-------------|-----------|
|Målnamn|Det här namnet anger där du vill spara de exporterade databasen resurserna. Exempel är sammansättningar, ytterligare filer och exempeldata. En mapp med det här namnet skapas under rotmappen för din lokala data.|
|Projektkatalogen|Den här sökvägen definierar där du vill spara det exporterade U-SQL-skriptet. Alla objektdefinitioner för databasen sparas på den här platsen.|
|Schema Only|Om du väljer det här alternativet exporteras endast databasdefinitioner och resurser (t.ex. sammansättningar och ytterligare filer).|
|Schemat och Data|Om du väljer det här alternativet exporteras databasdefinitioner, resurser och data. De översta N raderna i tabeller exporteras.|
|Importera till lokala databasen automatiskt|Om du väljer det här alternativet är den exporterade databasen importeras automatiskt till den lokala databasen när du exporterar är klar.|

![Guiden för databasexport - Export objektlistan och andra konfigurationer](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Steg 4: Kontrollera resultatet exporten

När exporten har slutförts kan du visa exporterade resultat i loggfönstret i guiden. I följande exempel visas hur du hittar exporterade U-SQL-skript och databasen resurser, inklusive sammansättningar, ytterligare filer och exempeldata:

![Guiden för Export av databas - exportera resultaten](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importera exporterade databasen till ett lokalt konto

Det enklaste sättet att importera den exporterade databasen är att välja den **importera till lokala databasen automatiskt** kryssrutan under exportprocessen i steg3. Om du inte markerar kryssrutan, hitta först exporterade U-SQL-skriptet i exportloggen för. Kör U-SQL-skript lokalt för att importera databasen till det lokala kontot.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importera exporterade databasen till ett Data Lake Analytics-konto

Importera databasen till olika Data Lake Analytics-konto:

1. Ladda upp de exporterade resurser, inklusive sammansättningar, ytterligare filer och exempeldata till Azure Data Lake Store-standardkontot för Data Lake Analytics-konto som du vill importera till. Du kan hitta den exporterade resursmappen under rotmappen lokala data. Ladda upp hela mappen till roten på Data Lake Store-standardkontot.
2. Skicka exporterade U-SQL-skript för Data Lake Analytics-kontot som du vill importera databasen till när du överför är klar.

## <a name="known-limitations"></a>Kända begränsningar

För närvarande, om du väljer den **Schema- och** alternativet i steg3, verktyget kör ett U-SQL-jobb för att exportera de data som lagras i tabeller. Därför exportera processen data kan gå långsamt och du kan innebära kostnader. 

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om U-SQL-databaser](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Testa och felsöka U-SQL-jobb med lokal körning och Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)


