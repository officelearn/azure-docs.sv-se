---
title: Kopiera data enkelt med guiden Kopiera – Azure | Microsoft Docs
description: Läs mer om hur du använder Data Factory-Kopieringsguiden för att kopiera data från datakällor som stöds till mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90f78428601d7b039d00d39c1ca8339ab3ace9ba
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020361"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Kopiera eller flytta data enkelt med Azure Data Factory-Kopieringsguide
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten bör du gå igenom [självstudien för kopieringsaktiviteter](../quickstart-create-data-factory-dot-net.md). 


Azure Data Factory-Kopieringsguiden är att underlätta processen med att mata in data, vilket vanligtvis är ett första steg i ett scenario för integrering från slutpunkt till slutpunkt. När du går via Azure Data Factory-Kopieringsguiden behöver du inte förstår alla JSON-definitioner för länkade tjänster, datauppsättningar och pipeliner. Men när du har slutfört alla steg i guiden skapar guiden automatiskt en pipeline för att kopiera data från den valda datakällan till den angivna platsen. Dessutom kan guiden Kopiera hjälper dig att validera data som matas in vid tidpunkten för redigering, vilket sparar mycket av din tid, särskilt när du mata in data för första gången från datakällan. Starta guiden Kopiera, klicka på den **kopiera data** panelen på startsidan på din datafabrik.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>En intuitiv guide för att kopiera data
Den här guiden kan du enkelt flytta data från en mängd olika källor till mål i minuter. När du går igenom guiden, skapas automatiskt en pipeline med en Kopieringsaktivitet du tillsammans med beroende Data Factory-entiteter (länkade tjänster och datauppsättningar). Inga ytterligare åtgärder krävs för att skapa pipelinen.   

![Välja datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Se [Kopieringsguiden självstudien](data-factory-copy-data-wizard-tutorial.md) artikeln stegvisa anvisningar om hur du skapar en exempel-pipeline för att kopiera data från en Azure-blobb till en Azure SQL Database-tabell. 
> 
> 

Guiden har utformats med stordata i åtanke från starten. Det är enkelt och effektivt att skapa en Data Factory-pipeline som flyttar hundratals mappar, filer eller tabeller med hjälp av guiden kopieringsdata. Guiden stöder följande tre funktioner: Automatisk förhandsgranskning, schemat avbildning och mappning och filtrera data. 

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning
Kopieringsguiden kan du granska en del av data från den valda datakällan att verifiera om data är rätt data du vill kopiera. Dessutom tolkar källdata finns i en textfil, Kopieringsguiden textfilen Läs rad och kolumn avgränsare och schemat automatiskt. 

![Filformatinställningar](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schema-avbildning och mappning
Schemat för indata kanske inte matchar schemat för utdata i vissa fall. Du behöver mappa kolumner från datakällans schema till kolumner från målschema i det här scenariot. 

Kopieringsguiden mappar automatiskt kolumner i källans schema till kolumner i målschema. Du kan åsidosätta mappningarna med hjälp av nedrullningsbara listor (eller) Ange om en kolumn behöver hoppas över vid kopiering av data.   

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
Guiden kan du filtrera källdata för att välja endast de data som ska kopieras till datalager för destination/mottagare. Filtrering minskar mängden data som ska kopieras till de mottagande datalagren och därmed förbättrar dataflödet för kopieringen. Det ger ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL query language (eller) filer i en Azure blobbmapp med hjälp av [Data Factory-funktioner och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
I det här exemplet använder SQL-frågan i `Text.Format` funktion och `WindowStart` variabeln. 

![Verifiera uttryck](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering av data i en Azure blobbmapp
Du kan använda variabler i sökvägen till mappen för att kopiera data från en mapp som ska fastställas vid körning baserat på [systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Variablerna som stöds är: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}**, och **{anpassade}**. Exempel: inputfolder / {year} / {month} / {day}.

Anta att du har anger mappar i följande format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klicka på den **Bläddra** för **fil eller mapp**, bläddra till någon av dessa mappar (exempel: 2016 -> 03 -> 02-01 >), och klicka på **Välj**. Du bör se `2016/03/01/02` i textrutan. Nu kan ersätta **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** , och **02** med **{hour}**, och tryck på fliken. Du bör se listrutorna för att välja formatet för dessa fyra variabler:

![Använda systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

I följande skärmbild visas kan du också använda en **anpassade** variabeln och eventuella [stöds formatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Välj en mapp med denna struktur genom att använda den **Bläddra** knappen först. Ersätt ett värde med **{anpassade}**, och tryck på fliken för att se textrutan kan du ange Formatsträngen.     

![Med hjälp av anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Stöd för olika data och objekt av typen
Med hjälp av guiden Kopiera, kan du effektivt flytta hundratals mappar, filer eller tabeller.

![Välj tabeller som du vill kopiera data från](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen en gång eller enligt ett schema (varje timme, varje dag, och så vidare). Båda dessa alternativ kan användas för bredden av anslutningarna mellan lokala, moln och lokala skrivbord kopian.

Flyttning av data från en källa till ett mål kan bara en gång i en enstaka kopieringsåtgärd. Det gäller för data av valfri storlek och ett format som stöds. Schemalagd kopiering låter dig kopiera data på en föreskrivna upprepning. Du kan använda omfattande inställningar (t.ex. Försök igen, tidsgräns och aviseringar) för att konfigurera schemalagda kopia.

![Schemaläggning av egenskaper](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nästa steg
En snabb genomgång på hur du använder Data Factory-Kopieringsguiden för att skapa en pipeline med en Kopieringsaktivitet finns i [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

