---
title: "Guiden för data Factory Azure kopiera | Microsoft Docs"
description: "Läs mer om hur du använder guiden Kopiera Data Factory Azure för att kopiera data från datakällor som stöds till sänkor."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 3f0b296b0bfb762607fdb4b42151b77a9e388a18
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="azure-data-factory-copy-wizard"></a>Guiden för Azure Data Factory kopiera
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). 

Guiden Kopiera Azure Data Factory underlättar processen att vill föra in data, som vanligtvis är ett första steg i ett scenario för integrering av data för slutpunkt till slutpunkt. När du använder guiden Kopiera Azure Data Factory behöver du inte förstå alla JSON-definitioner för länkade tjänster, datauppsättningar och rörledningar. Guiden skapar automatiskt en pipeline för att kopiera data från den valda datakällan till den angivna platsen. Dessutom kan hjälper guiden Kopiera dig att validera data som inhämtas vid tidpunkten för redigering. Detta sparar tid, särskilt när du mata in data för första gången från datakällan. Starta guiden Kopiera, klicka på den **kopiera data** panelen på startsidan i din data factory.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Utformad för stora data
Den här guiden kan du enkelt kan flytta data från olika källor till mål i minuter. När du går igenom guiden skapas en pipeline med en kopieringsaktiviteten automatiskt, tillsammans med beroende Data Factory-enheter (länkade tjänster och datauppsättningar). Inga ytterligare åtgärder krävs för att skapa pipelinen.   

![Välj datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Stegvisa anvisningar att skapa en exempel-pipeline för att kopiera data från en Azure blob till Azure SQL Database-tabellen, finns det [guiden Kopiera kursen](data-factory-copy-data-wizard-tutorial.md).
>
>

Guiden är utformad med stordata i åtanke från början, med stöd för olika data och objekt av typen. Du kan skapa Data Factory pipelines som flyttar hundratals mappar, filer eller tabeller. Guiden stöder automatisk förhandsgranskning, schemat avbildning och mappning och filtrering av data.

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning
Du kan förhandsgranska en del av data från den valda datakällan för att kunna verifiera om du vill kopiera data. Om datakällan finns i en textfil, Parsar guiden Kopiera dessutom textfil Läs rad och kolumn avgränsare och schemat automatiskt.

![Inställningar för format](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schemat avbildning och mappning
Schemat för indata kan inte överens med schemat för utdata i vissa fall. I det här scenariot måste du koppla kolumner från datakällans schema till kolumner från mål-schemat.

> [!TIP]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet för, stöder Data Factory automatiskt skapande av tabell med datakällans schema. Mer information från [flytta data till och från Azure SQL Data Warehouse med Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Använda en listrutan för att markera en kolumn från käll-schema ska mappas till en kolumn i mål-schemat. Guiden Kopiera försöker förstå mönstret för kolumnmappningen. Det gäller samma mönster för resten av kolumn, så att du inte behöver välja varje kolumn individuellt för att slutföra schemamappning. Om du vill kan åsidosätta du dessa mappningar genom att använda i listrutorna för att mappa kolumnerna i taget. Mönstret blir mer exakt du mappa fler kolumner. Guiden Kopiera uppdaterar kontinuerligt mönstret och slutligen når rätt mönster för kolumnmappningen som du vill uppnå.     

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
Du kan filtrera källdata för att markera de data som ska kopieras till datalagret mottagare. Filtrering minskar mängden data som ska kopieras till datalagret sink och därför förbättrar genomflödet av kopieringen. Det är ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL-frågespråket eller filer i en Azure blob-mapp med hjälp av [Data Factory-funktioner och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
Följande skärmbild visar ett SQL-frågan med de `Text.Format` funktion och `WindowStart` variabeln.

![Validera uttryck](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering av data i Azure blob-mappen
Du kan använda variabler i sökvägen till mappen för att kopiera data från en mapp som ska fastställas vid körning baserat på [systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Variabler som stöds är: **{year}**, **{month}**, **{day}**, **{timme}**, **{minut}**, och  **{anpassade}** . Till exempel: inputfolder / {year} / {month} / {day}.

Anta att du har definierat mappar i följande format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klicka på den **Bläddra** knappen för **filen eller mappen**, bläddra till någon av dessa mappar (exempelvis 2016 -> 03 -> 02-01 >), och klicka på **Välj**. Du bör se `2016/03/01/02` i textrutan. Ersätt nu **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** , och **02** med **{timme}**, och tryck på den **fliken** nyckel. Du bör se listrutorna för att välja formatet för dessa fyra variabler:

![Med hjälp av systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

I följande skärmbild visas kan du också använda en **anpassade** variabeln och eventuella [stöds formatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Om du vill välja en mapp med denna struktur, Använd den **Bläddra** knappen först. Ersätt ett värde med **{anpassade}**, och tryck på den **fliken** för att se textrutan kan du ange Formatsträngen.     

![Med anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen när eller enligt ett schema (varje timme, varje dag, och så vidare). Båda dessa alternativ kan användas för bredden av kopplingar mellan miljöer, inklusive lokalt, molnet och lokala skrivbord kopian.

Flytt av data från en källa till ett mål kan bara en gång i en enstaka kopieringsåtgärd. Det gäller för data av valfri storlek och ett format som stöds. Den schemalagda kopian kan du kopiera data på en föreskrivna upprepning. Du kan använda omfattande inställningar (t.ex. Försök igen, tidsgräns och varningar) för att konfigurera schemalagda kopia.

![Egenskaper för schemaläggning](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nästa steg
En snabb genomgång av med guiden Kopiera Data Factory för att skapa en pipeline med Kopieringsaktiviteten finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).
