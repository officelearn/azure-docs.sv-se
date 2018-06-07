---
title: Kopiera data enkelt med guiden Kopiera - Azure | Microsoft Docs
description: Läs mer om hur du använder guiden Kopiera Data Factory för att kopiera data från datakällor som stöds till sänkor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ef94f5cfa91422dddf9c684c2dc1b6eada754f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621300"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Kopiera eller flytta data med guiden för Azure Data Factory kopiera
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder version 2 av Data Factory-tjänsten, som finns tillgänglig som förhandsversion. 


Guiden Kopiera Azure Data Factory är att underlätta processen med vill föra in data, som vanligtvis är ett första steg i ett scenario för integrering av data för slutpunkt till slutpunkt. När du använder guiden Kopiera Azure Data Factory behöver du inte förstå alla JSON-definitioner för länkade tjänster, datauppsättningar och rörledningar. Men när du har slutfört alla steg i guiden skapar guiden automatiskt en pipeline för att kopiera data från den valda datakällan till den angivna platsen. Dessutom kan guiden Kopiera hjälper dig att validera data som inhämtas vid tidpunkten för redigering, vilket sparar mycket tid, särskilt när du mata in data för första gången från datakällan. Starta guiden Kopiera, klicka på den **kopiera data** panelen på startsidan i din data factory.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>En intuitiv guiden för att kopiera data
Den här guiden kan du enkelt kan flytta data från olika källor till mål i minuter. Efter att gå igenom guiden skapas automatiskt en pipeline med en kopia-aktivitet du tillsammans med beroende Data Factory-enheter (länkade tjänster och datauppsättningar). Inga ytterligare åtgärder krävs för att skapa pipelinen.   

![Välj datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Se [guiden Kopiera kursen](data-factory-copy-data-wizard-tutorial.md) artikel för stegvisa instruktioner för att skapa en exempel-rörledning för att kopiera data från ett Azure blob till Azure SQL Database-tabellen. 
> 
> 

Guiden är utformad med stordata i åtanke från början. Det är enkelt och effektivt att skriva Data Factory pipelines som flyttar hundratals mappar, filer eller tabeller med hjälp av guiden kopieringsdata. Guiden stöder följande tre funktioner: automatisk förhandsgranskning, schemat avbilda mappning och att filtrera data. 

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning
Guiden Kopiera kan du granska en del av data från den valda datakällan för att kontrollera huruvida data är rätt data du vill kopiera. Om datakällan finns i en textfil, Parsar guiden Kopiera dessutom textfil Läs rad och kolumn avgränsare och schemat automatiskt. 

![Filformatinställningar](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schemat avbildning och mappning
Schemat för indata kan inte överens med schemat för utdata i vissa fall. I det här scenariot måste du koppla kolumner från datakällans schema till kolumner från mål-schemat. 

Guiden Kopiera mappar automatiskt kolumner i datakällans schema till kolumnerna i mål-schemat. Du kan åsidosätta mappningar genom att använda listrutorna (eller) om en kolumn måste hoppas över vid kopiering av data.   

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
Guiden kan du filtrera källdata för att markera de data som ska kopieras till mål-sink-datalagret. Filtrering minskar mängden data som ska kopieras till datalagret sink och därför förbättrar genomflödet av kopieringen. Det ger ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL query language (eller) filer i en mapp i Azure blob [Data Factory-funktioner och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
I det här exemplet använder SQL-frågan i `Text.Format` funktion och `WindowStart` variabeln. 

![Validera uttryck](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering av data i Azure blob-mappen
Du kan använda variabler i sökvägen till mappen för att kopiera data från en mapp som ska fastställas vid körning baserat på [systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Variabler som stöds är: **{year}**, **{month}**, **{day}**, **{timme}**, **{minut}**, och **{anpassade}**. Exempel: inputfolder / {year} / {month} / {day}.

Anta att du har definierat mappar i följande format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klicka på den **Bläddra** knappen för **filen eller mappen**, bläddra till någon av dessa mappar (exempelvis 2016 -> 03 -> 02-01 >), och klicka på **Välj**. Du bör se `2016/03/01/02` i textrutan. Ersätt nu **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** , och **02** med **{timme}**, och tryck på Tab. Du bör se listrutorna för att välja formatet för dessa fyra variabler:

![Med hjälp av systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

I följande skärmbild visas kan du också använda en **anpassade** variabeln och eventuella [stöds formatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Om du vill välja en mapp med denna struktur, Använd den **Bläddra** knappen först. Ersätt ett värde med **{anpassade}**, och tryck på fliken för att se textrutan kan du ange Formatsträngen.     

![Med anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Stöd för olika data och objekt av typen
Med hjälp av guiden Kopiera kan flytta du effektivt hundratals mappar, filer eller tabeller.

![Välj tabeller som du vill kopiera data från](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen när eller enligt ett schema (varje timme, varje dag, och så vidare). Båda dessa alternativ kan användas för bredden av anslutningarna mellan lokala molnet och lokala skrivbord kopian.

Flytt av data från en källa till ett mål kan bara en gång i en enstaka kopieringsåtgärd. Det gäller för data av valfri storlek och ett format som stöds. Den schemalagda kopian kan du kopiera data på en föreskrivna upprepning. Du kan använda omfattande inställningar (t.ex. Försök igen, tidsgräns och varningar) för att konfigurera schemalagda kopia.

![Egenskaper för schemaläggning](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nästa steg
En snabb genomgång av med guiden Kopiera Data Factory för att skapa en pipeline med Kopieringsaktiviteten finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

