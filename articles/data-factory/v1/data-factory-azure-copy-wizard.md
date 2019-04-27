---
title: Data Factory Azure Kopieringsguiden | Microsoft Docs
description: Läs mer om hur du använder guiden Kopiera Azure för Data Factory för att kopiera data från datakällor som stöds till mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567681"
---
# <a name="azure-data-factory-copy-wizard"></a>Azure Data Factory-Kopieringsguide
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Azure Data Factory-Kopieringsguiden förenklar processen att mata in data, vilket vanligtvis är ett första steg i ett scenario för integrering från slutpunkt till slutpunkt. När du använder Azure Data Factory-Kopieringsguiden, behöver du inte förstår alla JSON-definitioner för länkade tjänster, datauppsättningar och pipeliner. Guiden skapar automatiskt en pipeline för att kopiera data från den valda datakällan till den angivna platsen. Dessutom kan hjälper guiden Kopiera dig att validera data som matas in vid tidpunkten för redigering. Detta sparar tid, särskilt när du mata in data för första gången från datakällan. Starta guiden Kopiera, klicka på den **kopiera data** panelen på startsidan på din datafabrik.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Utformad för stordata
Den här guiden kan du enkelt flytta data från en mängd olika källor till mål i minuter. När du går igenom guiden skapas en pipeline med en Kopieringsaktivitet automatiskt åt dig, tillsammans med beroende Data Factory-entiteter (länkade tjänster och datauppsättningar). Inga ytterligare åtgärder krävs för att skapa pipelinen.   

![Välja datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Stegvisa anvisningar om hur du skapar en exempel-pipeline för att kopiera data från en Azure-blobb till en Azure SQL Database-tabell kan du läsa den [Kopieringsguiden självstudien](data-factory-copy-data-wizard-tutorial.md).
>
>

Guiden har utformats med stordata i åtanke från starten med stöd för olika data och objekt av typen. Du kan skapa en Data Factory-pipeline som flyttar hundratals mappar, filer eller tabeller. Guiden stöder automatisk förhandsgranskning, schemat avbildning och mappning och filtrering av data.

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning
Du kan förhandsgranska en del av data från den valda datakällan för att verifiera om data är vad du vill kopiera. Dessutom tolkar källdata finns i en textfil, guiden Kopiera textfilen Läs rad- och Radavgränsare och schemat automatiskt.

![Filformatinställningar](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schema-avbildning och mappning
Schemat för indata kanske inte matchar schemat för utdata i vissa fall. Du behöver mappa kolumner från datakällans schema till kolumner från målschema i det här scenariot.

> [!TIP]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet för, stöder Data Factory automatiskt när tabellen skulle skapas med datakällans schema. Läs mer i [flytta data till och från Azure SQL Data Warehouse med Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Använd en nedrullningsbar listruta för att markera en kolumn från käll-schema ska mappas till en kolumn i målschema. Guiden Kopiera försöker förstå mönstret för kolumnmappning. Det gäller samma mönster för resten av kolumn, så att du inte behöver välja varje kolumn individuellt för att slutföra schemat mappningen. Om du vill kan åsidosätta du dessa mappningar genom att använda de nedrullningsbara listorna för att mappa kolumner i taget. Mönstret blir mer exakta du mappa flera kolumner. Kopieringsguiden uppdaterar ständigt mönstret och slutligen når rätt mönstret för kolumnmappningen som du vill uppnå.     

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
Du kan filtrera källdata för att välja endast de data som ska kopieras till de mottagande datalagren. Filtrering minskar mängden data som ska kopieras till de mottagande datalagren och därmed förbättrar dataflödet för kopieringen. Den ger ett flexibelt sätt att filtrera data i en relationsdatabas genom att använda SQL-frågespråket eller filer i en Azure blobbmapp med hjälp av [Data Factory-funktioner och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
Följande skärmbild visar ett SQL-fråga med den `Text.Format` funktion och `WindowStart` variabeln.

![Verifiera uttryck](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering av data i en Azure blobbmapp
Du kan använda variabler i sökvägen till mappen för att kopiera data från en mapp som ska fastställas vid körning baserat på [systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Variablerna som stöds är: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}**, och **{anpassade}**. Till exempel: inputfolder / {year} / {month} / {day}.

Anta att du har anger mappar i följande format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klicka på den **Bläddra** för **fil eller mapp**, bläddra till någon av dessa mappar (exempel: 2016 -> 03 -> 02-01 >), och klicka på **Välj**. Du bör se `2016/03/01/02` i textrutan. Nu kan ersätta **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** , och **02** med **{hour}**, och tryck på den **fliken** nyckel. Du bör se listrutorna för att välja formatet för dessa fyra variabler:

![Använda systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

I följande skärmbild visas kan du också använda en **anpassade** variabeln och eventuella [stöds formatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Välj en mapp med denna struktur genom att använda den **Bläddra** knappen först. Ersätt ett värde med **{anpassade}**, och tryck på den **fliken** nyckel till textruta där du kan skriva Formatsträngen som visas.     

![Med hjälp av anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen en gång eller enligt ett schema (varje timme, varje dag, och så vidare). Båda dessa alternativ kan användas för anslutningarna mellan miljöer, inklusive lokala, moln och lokala skrivbord kopian bredd.

Flyttning av data från en källa till ett mål kan bara en gång i en enstaka kopieringsåtgärd. Det gäller för data av valfri storlek och ett format som stöds. Schemalagd kopiering låter dig kopiera data på en föreskrivna upprepning. Du kan använda omfattande inställningar (t.ex. Försök igen, tidsgräns och aviseringar) för att konfigurera schemalagda kopia.

![Schemaläggning av egenskaper](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nästa steg
En snabb genomgång på hur du använder Data Factory-Kopieringsguiden för att skapa en pipeline med en Kopieringsaktivitet finns i [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md).
