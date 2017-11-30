---
title: "Hur du exporterar U-SQL-databaser med hjälp av Azure Data Lake-verktyg för Visual Studio | Microsoft Docs"
description: "Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio för att exportera U-SQL-databas och importera dem till lokalt konto på samma gång."
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
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>Hur du exporterar U-SQL-databas

I detta dokument kan du lära dig hur du använder [Azure Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs) för export av U-SQL-databas som ett enda U-SQL-skript och hämtade resurser. Importera den exporterade databasen till lokalt konto stöds även i samma process.

Kunder Underhåll vanligtvis flera miljöer för utveckling, testning och produktion. Dessa miljöer finns i både lokalt konto på den lokala datorn för utvecklare och Azure Data Lake Analytics-konto i Azure. När du utvecklar och justera U-SQL-frågor för utvecklings- och testmiljöer, är det vanligt att utvecklare behöver återskapa allt i produktionsdatabas. **Databasen guiden Exportera** hjälper dig att öka takten på den här processen. Med hjälp av guiden kan utvecklare klona den befintliga databasmiljön och exempeldata till andra Azure Data Lake Analytics-konton.

## <a name="export-steps"></a>Exportera steg

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>Steg 1: Högerklicka på databasen i Server Explorer och klicka på ”Exportera...”

Alla Azure Data Lake Analytics-konton du har behörighet att visas i Server Explorer. Expandera som innehåller den databas du vill exportera och högerklicka på databasen för att välja **exportera...** . Om du inte hittar snabbmenyn kan du behöva [uppdaterar verktyget till lasted versionen](http://aka.ms/adltoolsvs).

![Data Lake Analytics verktyg Export-databas](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>Steg 2: Konfigurera de objekt som du vill exportera

En databas är ibland stort, men behöver du bara en liten del av den sedan kan du konfigurera delmängd av objekt som du vill exportera i guiden Exportera. Observera att export-åtgärden har slutförts genom att köra ett U-SQL-jobb och vissa kostnad uppkommer därför exportera från Azure-konto.

![Data Lake Analytics verktyg Export Database-guiden](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>Steg 3: Kontrollera listan med objekt och flera konfigurationer

I det här steget dubbelkolla du de valda objekten överst i dialogrutan. Om det finns några fel kan du klicka på Föregående om du vill gå tillbaka och konfigurera de objekt du vill exportera igen.

Du kan också göra andra konfigurationer om export mål, beskrivningar av de här konfigurationerna visas i tabellen nedan:

|Konfiguration|Beskrivning|
|-------------|-----------|
|Målnamn|Det här namnet anger där du vill spara de exporterade databasen resurserna, t.ex. sammansättningar, ytterligare filer och exempeldata. En mapp med det här namnet kommer att skapas under rotmappen för din lokala data.|
|Projektkatalogen|Den här sökvägen definierar där du vill spara exporterade U-SQL-skript som innehåller alla objektdefinitioner i databasen.|
|Schema Only|Det här alternativet resulterar i endast databasdefinitioner och resurser (t.ex. sammansättningar och ytterligare filer) som exporteras.|
|Schemat och Data|Det här alternativet resulterar i databasdefinitioner, resurser och data som ska exporteras. De översta N raderna i tabeller exporteras.|
|Importera till lokala databasen automatiskt|Om du markerar den här konfigurationen exporterade databasen kommer att importeras till den lokala databasen automatiskt efter exporten har slutförts.|

![Data Lake Analytics verktyg Export guiden databaskonfigurationen](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Steg 4: Kontrollera resultatet exporten

Efter alla dessa inställningar och export pågår hittar du exporterade resultaten från loggfönstret i guiden. Via loggen som markerats med röd rektangel i blåser bort skärmbilden, hittar du platsen för de exporterade U-SQL-skript och databasresurser inklusive sammansättningar, ytterligare filer och exempeldata.

![Data Lake Analytics verktyg Export Database-guiden slutförts](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>Importera den exporterade databasen till lokalt konto

Det enklaste sättet att göra detta importera kontrollerar **Import till databasen automatiskt lokala** under exporterar förloppet i steg3. Om du har glömt att göra det, kan du hitta det exporterade U-SQL-skriptet via exporterar loggen och köra U-SQL-skript lokalt för att importera databasen till det lokala kontot.

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>Importera den exporterade databasen till Azure Data Lake Analytics-konto

Om du vill importera databasen till andra Azure Data Lake Analytics-konto behöver du två steg:

1. Överför de exporterade resurserna, inklusive sammansättningar, ytterligare filer och exempeldata till Azure Data Lake Store-standardkontot för Azure Data Lake Analytics-konto som du vill importera till. Du kan hitta den exporterade resursmappen under rotmappen lokala data och överföra hela mappen till roten i store-standardkontot.
2. Skicka exporterade U-SQL-skript till Azure Data Lake Analytics-konto som du vill importera databasen till efter överföring slutförts.

## <a name="known-limitation"></a>Kända begränsningar

För närvarande, om du har valt **Schema- och** i guiden verktyget kör ett U-SQL-jobb för att exportera de data som lagras i tabeller. Det är därför data exporterar process kan gå långsamt och innebära kostnaden. 

## <a name="next-steps"></a>Nästa steg

* [Förstå U-SQL-databas](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Testa och felsöka U-SQL-jobb med hjälp av lokal körning och Azure Data Lake U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)


