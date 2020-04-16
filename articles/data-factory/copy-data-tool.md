---
title: Kopiera dataverktyg Azure Data Factory
description: Innehåller information om verktyget Kopiera data i Azure Data Factory UI
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: df078673aed60086a88961ff64f9bfa596d96346
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414085"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Verktyget Kopiera data i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory Copy Data-verktyget underlättar och optimerar processen för att inta data i en datasjö, vilket vanligtvis är ett första steg i ett scenario med integrering av data från slutpunkt till slutpunkt.  Det sparar tid, särskilt när du använder Azure Data Factory för att få tillgång till data från en datakälla för första gången. Några av fördelarna med att använda det här verktyget är:

- När du använder azure data fabrikskopieringsdataverktyget behöver du inte förstå Data Factory-definitioner för länkade tjänster, datauppsättningar, pipelines, aktiviteter och utlösare. 
- Flödet av verktyget Kopiera data är intuitivt för att läsa in data i en datasjö. Verktyget skapar automatiskt alla nödvändiga datafabriksresurser för att kopiera data från det valda källdatalagret till det valda mål-/sink-datalagret. 
- Verktyget Kopiera data hjälper dig att validera de data som används vid tidpunkten för redigeringen, vilket hjälper dig att undvika eventuella fel i själva början.
- Om du behöver implementera komplex affärslogik för att läsa in data i en datasjö kan du fortfarande redigera datafabriksresurserna som skapats av verktyget Kopiera data med hjälp av redigering per aktivitet i datafabrikens användargränssnitt. 

I följande tabell ges vägledning om när verktyget Kopiera data ska användas jämfört med redigering per aktivitet i datafabrikens användargränssnitt: 

| Kopiera data-verktyg | Redigering per aktivitet (kopiera aktivitet) |
| -------------- | -------------------------------------- |
| Du vill enkelt skapa en datainläsningsuppgift utan att lära dig om Azure Data Factory-entiteter (länkade tjänster, datauppsättningar, pipelines osv.) | Du vill implementera komplex och flexibel logik för att läsa in data i sjön. |
| Du vill snabbt läsa in ett stort antal dataartefakter i en datasjö. | Du vill kedja kopiera aktivitet med efterföljande aktiviteter för rengöring eller bearbetning av data. |

Om du vill starta verktyget Kopiera data klickar du på panelen **Kopiera data** på startsidan för datafabriken.

![Sidan Kom igång – länk till verktyget Kopiera data](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitivt flöde för inläsning av data i en datasjö
Med det här verktyget kan du enkelt flytta data från en mängd olika källor till destinationer på några minuter med ett intuitivt flöde:  

1. Konfigurera inställningar för **källan**.
2. Konfigurera inställningar för **målet**. 
3. Konfigurera **avancerade inställningar** för kopieringsåtgärden, till exempel kolumnmappning, prestandainställningar och inställningar för feltolerans. 
4. Ange ett **schema** för datainläsningsaktiviteten. 
5. Granska **sammanfattningen** av datafabrikentiteter som ska skapas. 
6. **Redigera** pipelinen för att uppdatera inställningarna för kopieringsaktiviteten efter behov. 

   Verktyget är utformat med stordata i åtanke från början, med stöd för olika data och objekttyper. Du kan använda den för att flytta hundratals mappar, filer eller tabeller. Verktyget stöder automatisk dataförhandsgranskning, schemainsamling och automatisk mappning samt datafiltrering.

![Kopiera data-verktyg](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning av data
Du kan förhandsgranska en del av data från det valda källdatalagret, vilket gör att du kan validera de data som kopieras. Om källdata finns i en textfil tolkar verktyget Kopiera data dessutom textfilen så att rad- och kolumnavgränsare automatiskt identifieras och schemat identifieras.

![Filinställningar](./media/copy-data-tool/file-format-settings.png)

Efter detektionen:

![Identifierade filinställningar och förhandsgranskning](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Schemainsamling och automatisk mappning
Schemat för datakällan kanske inte är detsamma som schemat för datamålet i många fall. I det här fallet måste du mappa kolumner från källschemat till kolumner från målschemat.

Verktyget Kopiera data övervakar och lär sig ditt beteende när du mappar kolumner mellan käll- och målbutiker. När du har valt en eller några kolumner från källdatalagret och mappa dem till målschemat börjar verktyget Kopiera data analysera mönstret för kolumnpar som du har valt från båda sidor. Sedan gäller samma mönster för resten av kolumnerna. Därför ser du att alla kolumner har mappats till målet på ett sätt du vill ha precis efter flera klick.  Om du inte är nöjd med valet av kolumnmappning som tillhandahålls av verktyget Kopiera data kan du ignorera den och fortsätta med att manuellt mappa kolumnerna. Under tiden lär sig och uppdaterar kopieringsdata-verktyget hela tiden mönstret och når i slutändan rätt mönster för den kolumnmappning du vill uppnå. 

> [!NOTE]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet, stöder verktyget Kopiera data skapandet av tabellen automatiskt med hjälp av källschemat. 

## <a name="filter-data"></a>Filtrera data
Du kan filtrera källdata för att bara markera de data som behöver kopieras till sink-datalagret. Filtrering minskar volymen på de data som ska kopieras till diskbänksdatalagret och förbättrar därför kopieringsflödets dataflöde. Verktyget Kopiera data är ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL-frågespråket eller filer i en Azure-blobmapp. 

### <a name="filter-data-in-a-database"></a>Filtrera data i en databas
Följande skärmbild visar en SQL-fråga för att filtrera data.

![Filtrera data i en databas](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrera data i en Azure-blobmapp
Du kan använda variabler i mappsökvägen för att kopiera data från en mapp. Variablerna som stöds är: **{year}**, **{month}**, **{day}**, **{hour}** och **{minute}**. Till exempel: inputfolder/{year}/{month}/{day}. 

Anta att du har inmatningsmappar i följande format: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicka på knappen **Bläddra** efter **Fil eller mapp**, bläddra till någon av dessa mappar (till exempel 2016->03->01->02) och klicka på **Välj**. Du bör se 2016/03/01/02 i textrutan. 

Ersätt sedan **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** och **02** med **{hour}** och tryck på **Tabb.** Du bör se listrutor för att välja format för dessa fyra variabler:

![Filtrera fil eller mapp](./media/copy-data-tool/filter-file-or-folder.png)

Verktyget Kopiera data genererar parametrar med uttryck, funktioner och systemvariabler som kan användas för att representera {year}, {month}, {day}, {hour} och {minute} när pipeline skapas.

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen en gång eller enligt ett schema (varje timme, varje dag och så vidare). Dessa alternativ kan användas för anslutningsapparna i olika miljöer, inklusive lokala, moln och lokala skrivbord. 

En engångskopia gör det möjligt för data förflyttning från en källa till ett mål bara en gång. Det gäller data av alla storlekar och alla format som stöds. Med den schemalagda kopian kan du kopiera data på en upprepning som du anger. Du kan använda avancerade inställningar (som återförsök, timeout och aviseringar) för att konfigurera den schemalagda kopian.

![Alternativ för schemaläggning](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Nästa steg
Prova de här självstudierna som använder verktyget Kopiera data:

- [Snabbstart: skapa en datafabrik med verktyget Kopiera data](quickstart-create-data-factory-copy-data-tool.md)
- [Självstudiekurs: kopiera data i Azure med verktyget Kopiera data](tutorial-copy-data-tool.md) 
- [Självstudiekurs: kopiera lokala data till Azure med verktyget Kopiera data](tutorial-hybrid-copy-data-tool.md)
