---
title: Kopieringsdata verktyget Azure Data Factory | Microsoft Docs
description: Innehåller information om verktyget kopieringsdata i Azure Data Factory-Gränssnittet
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 5180a490a543b6cfc2a8129423f0e663de2e06e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619430"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Kopiera verktyget Data i Azure Data Factory
Verktyget Azure Data Factory kopieringsdata underlättar och optimerar hur du vill föra in data i en datasjö som vanligtvis är ett första steg i ett scenario för integrering av data för slutpunkt till slutpunkt.  Det sparar tid, särskilt när du använder Azure Data Factory att mata in data från en datakälla för första gången. Några av fördelarna med att använda det här verktyget är:

- När du använder verktyget Azure Data Factory kopieringsdata kan behöver du inte förstår Data Factory definitioner för länkade tjänster, datauppsättningar, rörledningar, aktiviteter och utlösare. 
- Flödet av verktyget kopieringsdata är intuitivt för att läsa in data i en datasjö. Verktyget skapar automatiskt alla nödvändiga Data Factory-resurser för att kopiera data från datalagret markerade källan till den valda mål-sink-databasen. 
- Kopieringsdata verktyget hjälper dig att validera data som är som inhämtas vid tidpunkten för redigering, som hjälper dig att undvika alla eventuella fel i början sig själv.
- Du kan fortfarande redigera Data Factory-resurser som skapats av verktyget kopieringsdata med hjälp av per aktivitet redigering i Användargränssnittet för Data Factory om du behöver implementera komplicerad affärslogik för att läsa in data i en datasjö. 

Följande tabell innehåller råd om när du använder verktyget kopieringsdata kontra per aktivitet redigering i Data Factory-Användargränssnittet: 

| Kopiera data-verktyg | Per aktivitet (kopieringsaktiviteten) Redigering |
| -------------- | -------------------------------------- |
| Du vill skapa enkelt en datainläsning aktiviteten utan att lära dig mer om Azure Data Factory-enheter (länkade tjänster, datauppsättningar, pipelines osv.) | Du vill implementera komplexa och flexibla logik för att läsa in data i lake. |
| Du vill läsa in ett stort antal dataartifakter snabbt i en datasjö. | Vill du kedja kopieringsaktiviteten med efterföljande aktiviteter för rengöring eller bearbetning av data. |

Starta verktyget kopieringsdata, klicka på den **kopieringsdata** panelen på startsidan i din data factory.

![Få igång sida - länken för att kopieringsdata verktyget](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitiva flödet för att läsa in data i en datasjö
Det här verktyget kan du enkelt kan flytta data från olika källor till mål i minuter med ett intuitivt flöde:  

1. Konfigurera inställningar för den **källa**.
2. Konfigurera inställningar för den **mål**. 
3. Konfigurera **avancerade inställningar** för kopieringen som kolumnmappningen, prestandainställningar för och fel inställningarna. 
4. Ange en **schema** för datainläsning aktivitet. 
5. Granska **sammanfattning** Data Factory-enheter som ska skapas. 
6. **Redigera** pipeline att uppdatera inställningarna för kopieringsaktiviteten efter behov. 

 Verktyget är utformad med stordata i åtanke från början, med stöd för olika data och objekt av typen. Du kan använda den för att flytta hundratals mappar, filer eller tabeller. Verktyget stöder automatisk förhandsgranskning, schemat avbildning och automatisk mappning och data samt filtrering.

![Kopiera data-verktyg](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning
Du kan förhandsgranska en del av data från datalagret valda källplatsen där du kan validera data som kopieras. Om datakällan finns i en textfil, Parsar verktyget kopieringsdata dessutom textfil för att automatiskt identifiera rad och kolumn avgränsare och schema.

![Inställningar för](./media/copy-data-tool/file-format-settings.png)

Efter identifiering:

![Inställningar för identifierade och förhandsgranskning](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Schemat avbilda och automatisk mappning
Schemat för datakällan kan inte vara detsamma som schemat för datamål i många fall. I det här scenariot måste du koppla kolumner från datakällans schema till kolumner från mål-schemat.

Kopieringsdata verktyget övervakar och lär sig din beteende när du mappar kolumner mellan käll- och Arkiv. När du välja en eller några kolumner från datalagret för källa och mappa dem till mål-schemat, startar verktyget kopieringsdata att analysera mönster för kolumn-par som du valde från båda sidor. Den gäller sedan samma mönster för resten av kolumnerna. Därför visas alla kolumnerna som har mappats till målet på ett sätt som du vill ha precis efter flera gånger.  Om du inte är nöjd med valet av kolumnmappning tillhandahölls genom att kopieringsdata verktyget kan du ignorera det och fortsätt med manuellt mappa kolumnerna. Under tiden verktyget kopieringsdata hela tiden lär sig och uppdaterar mönstret och slutligen når rätt mönster för kolumnmappningen som du vill uppnå. 

> [!NOTE]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet för, stöder kopieringsdata verktyget skapandet av tabellen automatiskt med hjälp av källschemat. 

## <a name="filter-data"></a>Filtrera data
Du kan filtrera källdata för att markera de data som ska kopieras till datalagret mottagare. Filtrering minskar mängden data som ska kopieras till datalagret sink och därför förbättrar genomflödet av kopieringen. Kopiera Data verktyget är ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL-frågespråket eller filer i en Azure blob-mapp. 

### <a name="filter-data-in-a-database"></a>Filtrera data i en databas
Följande skärmbild visar en SQL-fråga för att filtrera data.

![Filtrera data i en databas](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrera data i en Azure blob-mapp
Du kan använda variabler i sökvägen till mappen för att kopiera data från en mapp. Variabler som stöds är: **{year}**, **{month}**, **{day}**, **{timme}**, och **{minut}**. Till exempel: inputfolder / {year} / {month} / {day}. 

Anta att du har definierat mappar i följande format: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicka på den **Bläddra** knappen för **filen eller mappen**, bläddra till någon av dessa mappar (exempelvis 2016 -> 03 -> 02-01 >), och klicka på **Välj**. Du bör se 2016/03/01/02 i textrutan. 

Ersätt sedan **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** , och **02** med **{timme}**, och tryck på den **fliken** nyckel. Du bör se listrutorna för att välja formatet för dessa fyra variabler:

![Filterfilen eller mappen](./media/copy-data-tool/filter-file-or-folder.png)

Kopieringsdata verktyget genererar parametrar med uttryck, funktioner, och systemvariabler som kan användas för att representera {year}, {month}, {day}, {timme} och {minut} när du skapar pipeline. Mer information finns i [så att läsa eller skriva partitionerad data](how-to-read-write-partitioned-data.md) artikel.

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen när eller enligt ett schema (varje timme, varje dag, och så vidare). Dessa alternativ kan användas för anslutningarna mellan olika miljöer, inklusive lokalt, molnet och lokala dator. 

Flytt av data från en källa till ett mål kan bara en gång i en enstaka kopieringsåtgärd. Det gäller för data av valfri storlek och ett format som stöds. Den schemalagda kopian kan du kopiera data på en upprepning som du anger. Du kan använda omfattande inställningar (t.ex. Försök igen, tidsgräns och varningar) för att konfigurera schemalagda kopia.

![Alternativ för schemaläggning](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Nästa steg
Prova dessa självstudiekurser som använder verktyget kopieringsdata:

- [Snabbstart: skapa en datafabrik med hjälp av verktyget kopieringsdata](quickstart-create-data-factory-copy-data-tool.md)
- [Självstudier: kopiera data i Azure med hjälp av verktyget kopieringsdata](tutorial-copy-data-tool.md) 
- [Självstudier: kopiera lokala data till Azure med hjälp av verktyget kopieringsdata](tutorial-hybrid-copy-data-tool.md)
