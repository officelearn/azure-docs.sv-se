---
title: Azure Data Factory-verktyget kopieringsdata | Microsoft Docs
description: Innehåller information om verktyget kopieringsdata i Azure Data Factory-användargränssnitt
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787739"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Kopiera Data-verktyg i Azure Data Factory
Verktyget kopieringsdata för Azure Data Factory förenklar och optimerar processen att mata in data i en datasjö som vanligtvis är ett första steg i ett scenario för integrering från slutpunkt till slutpunkt.  Det sparar tid, särskilt när du använder Azure Data Factory att mata in data från en datakälla för första gången. Några av fördelarna med att använda det här verktyget är:

- När du använder verktyget kopieringsdata för Azure Data Factory kan behöver du inte förstår Data Factory-definitioner för länkade tjänster, datauppsättningar, pipelines, aktiviteter och utlösare. 
- Flödet av verktyget kopieringsdata är intuitivt för inläsning av data i en datasjö. Verktyget skapar automatiskt alla Data Factory-resurser för att kopiera data från den valda källdatalagringen till datalagret valda destination/mottagare. 
- Verktyget kopieringsdata hjälper dig att validera data som matas vid tidpunkten för redigering, vilket hjälper dig att undvika alla eventuella fel i början själva.
- Om du vill implementera komplexa affärslogiken för att läsa in data i en datasjö kan redigera du fortfarande Data Factory-resurser som skapas av verktyget kopieringsdata med hjälp av per aktivitet redigering i Användargränssnittet för Data Factory. 

Följande tabell innehåller information om när du ska använda verktyget kopieringsdata jämfört med per aktivitet redigering i Användargränssnittet för Data Factory: 

| Kopiera data-verktyg | Per aktivitet (kopieringsaktiviteten) Redigering |
| -------------- | -------------------------------------- |
| Du vill skapa enkelt en datainläsning utan lära dig mer om Azure Data Factory-entiteter (länkade tjänster, datauppsättningar, pipeliner osv.) | Du vill implementera komplexa och flexibla logik för att läsa in data i sjön. |
| Vill du snabbt läsa in ett stort antal dataartifakter i en datasjö. | Du vill länka Kopieringsaktivitet med efterföljande aktiviteter för rengöring eller bearbeta data. |

Starta verktyget kopieringsdata genom att klicka på den **kopieringsdata** panelen på startsidan på din datafabrik.

![Startsidan - länk till verktyget kopieringsdata](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitiv flödet för att läsa in data i en datasjö
Det här verktyget kan du enkelt flytta data från en mängd olika källor till mål på några minuter med en intuitiv flöde:  

1. Konfigurera inställningar för den **källa**.
2. Konfigurera inställningar för den **mål**. 
3. Konfigurera **avancerade inställningar** för kopieringen som kolumnmappning, prestandainställningarna och fault toleransinställningar. 
4. Ange en **schema** för datainläsning uppgift. 
5. Granska **sammanfattning** av Data Factory-entiteter som ska skapas. 
6. **Redigera** pipeline för att uppdatera inställningarna för kopieringsaktiviteten efter behov. 

   Verktyget har utformats med stordata i åtanke från starten med stöd för olika data och objekt av typen. Du kan använda den för att flytta hundratals mappar, filer eller tabeller. Verktyget stöder automatisk förhandsgranskning, schemat avbildning och automatisk mappning och data samt filtrering.

![Kopiera data-verktyg](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning
Du kan förhandsgranska vissa data från den valda källdatalager, där du kan verifiera data som ska kopieras. Om datakällan finns i en textfil, tolkar verktyget kopieringsdata dessutom textfil för att automatiskt identifiera rad- och Radavgränsare och schema.

![Filinställningar](./media/copy-data-tool/file-format-settings.png)

Efter identifiering:

![Identifierade filinställningar och preview](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Schema-avbildning och automatisk mappning
Schemat för datakällan kan inte vara detsamma som schemat för datamålet i många fall. Du behöver mappa kolumner från datakällans schema till kolumner från målschema i det här scenariot.

Verktyget kopieringsdata övervakar och lär sig dina beteende när du mappar kolumner mellan käll-och mål. När du väljer en eller några kolumner från källdatalagret och mappa den till målschema, startar verktyget kopieringsdata att analysera mönster för kolumn-par som du valt från båda sidor. Sedan gäller det samma mönster för resten av kolumnerna. Därför kan se du alla kolumnerna som har mappats till målet på ett sätt som du vill att bara när du har flera klick.  Om du inte är nöjd med valet av kolumnmappning som tillhandahålls av verktyget kopieringsdata kan du ignorera det och fortsätta med att mappa kolumnerna manuellt. Under tiden kan verktyget kopieringsdata lär sig ständigt och uppdaterar mönstret och slutligen når rätt mönstret för kolumnmappningen som du vill uppnå. 

> [!NOTE]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet för, stöder verktyget kopieringsdata skapandet av tabellen automatiskt med hjälp av datakällans schema. 

## <a name="filter-data"></a>Filtrera data
Du kan filtrera källdata för att välja endast de data som ska kopieras till de mottagande datalagren. Filtrering minskar mängden data som ska kopieras till de mottagande datalagren och därmed förbättrar dataflödet för kopieringen. Kopiera Data verktyget ger ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL-frågespråket eller filer i en Azure blobbmapp. 

### <a name="filter-data-in-a-database"></a>Filtrera data i en databas
I följande skärmbild visas en SQL-fråga för att filtrera data.

![Filtrera data i en databas](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrera data i en Azure blobbmapp
Du kan använda variabler i sökvägen till mappen för att kopiera data från en mapp. Variablerna som stöds är: **{year}**, **{month}**, **{day}**, **{hour}**, och **{minute}**. Till exempel: inputfolder / {year} / {month} / {day}. 

Anta att du har anger mappar i följande format: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicka på den **Bläddra** för **fil eller mapp**, bläddra till någon av dessa mappar (exempel: 2016 -> 03 -> 02-01 >), och klicka på **Välj**. Du bör se 2016/03/01/02 i textrutan. 

Ersätt sedan **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** , och **02** med **{hour}**, och tryck på den **fliken** nyckel. Du bör se listrutorna för att välja formatet för dessa fyra variabler:

![Filtret fil eller mapp](./media/copy-data-tool/filter-file-or-folder.png)

Verktyget kopieringsdata genererar parametrar med uttryck, funktioner och systemvariabler som kan användas för att representera {year}, {month} {day} {hour} och {minute} när du skapar en pipeline. Mer information finns i den [hur att läsa eller skriva partitionerade data](how-to-read-write-partitioned-data.md) artikeln.

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen en gång eller enligt ett schema (varje timme, varje dag, och så vidare). Dessa alternativ kan användas för anslutningarna mellan olika miljöer, inklusive lokala, moln och lokala skrivbord. 

Flyttning av data från en källa till ett mål kan bara en gång i en enstaka kopieringsåtgärd. Det gäller för data av valfri storlek och ett format som stöds. Schemalagd kopiering låter dig kopiera data på en upprepning som du anger. Du kan använda omfattande inställningar (t.ex. Försök igen, tidsgräns och aviseringar) för att konfigurera schemalagda kopia.

![Alternativ för schemaläggning](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Nästa steg
Prova de här självstudierna som använder du verktyget kopieringsdata:

- [Snabbstart: skapa en datafabrik med hjälp av verktyget kopieringsdata](quickstart-create-data-factory-copy-data-tool.md)
- [Självstudier: kopiera data i Azure med hjälp av verktyget kopieringsdata](tutorial-copy-data-tool.md) 
- [Självstudier: kopiera lokala data till Azure med hjälp av verktyget kopieringsdata](tutorial-hybrid-copy-data-tool.md)
