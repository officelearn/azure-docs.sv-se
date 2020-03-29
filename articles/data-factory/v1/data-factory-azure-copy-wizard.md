---
title: Guiden Kopiera datafabriken
description: Lär dig mer om hur du använder guiden Kopia för Data Factory Azure för att kopiera data från datakällor som stöds till sänkor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fef9059700e2bd94029c40bb819870a7174e0812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930119"
---
# <a name="azure-data-factory-copy-wizard"></a>Guiden Kopiera Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Azure Data Factory Copy Wizard underlättar processen att inta data, vilket vanligtvis är ett första steg i ett scenario med integrering av data från slutna till slutna data. När du går igenom Azure Data Factory Copy Wizard behöver du inte förstå några JSON-definitioner för länkade tjänster, datauppsättningar och pipelines. I guiden skapas automatiskt en pipeline för att kopiera data från den valda datakällan till det valda målet. Dessutom hjälper kopieringsguiden dig att validera de data som intas vid tidpunkten för redigeringen. Detta sparar tid, särskilt när du intar data för första gången från datakällan. Om du vill starta kopieringsguiden klickar du på panelen **Kopiera data** på startsidan för datafabriken.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Utformad för stordata
Med den här guiden kan du enkelt flytta data från en mängd olika källor till destinationer på några minuter. När du har gått igenom guiden skapas en pipeline med en kopieringsaktivitet automatiskt åt dig, tillsammans med beroende datafabrikentiteter (länkade tjänster och datauppsättningar). Inga ytterligare steg krävs för att skapa pipelinen.   

![Välja datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Stegvisa instruktioner för att skapa en exempelpipeline för att kopiera data från en Azure-blob till en Azure SQL Database-tabell finns i [självstudiekursen i kopiaguiden](data-factory-copy-data-wizard-tutorial.md).
>
>

Guiden är utformad med stordata i åtanke från början, med stöd för olika data och objekttyper. Du kan skapa Data Factory-pipelines som flyttar hundratals mappar, filer eller tabeller. Guiden stöder automatisk dataförhandsgranskning, schemainsamling och mappning samt datafiltrering.

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning av data
Du kan förhandsgranska en del av data från den valda datakällan för att verifiera om data är vad du vill kopiera. Om källdata finns i en textfil tolkar kopiera guiden dessutom textfilen så att rad- och kolumnavgränsarna och schemat automatiskt lärs ut.

![Filformatinställningar](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Insamling och mappning av schema
Schemat för indata kanske inte matchar schemat för utdata i vissa fall. I det här fallet måste du mappa kolumner från källschemat till kolumner från målschemat.

> [!TIP]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet, stöder Data Factory automatisk skapande av tabeller med hjälp av källans schema. Läs mer från [Flytta data till och från Azure SQL Data Warehouse med Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Använd en listruta för att välja en kolumn från källschemat för att mappa till en kolumn i målschemat. I kopieringsguiden försöker du förstå ditt mönster för kolumnmappning. Det gäller samma mönster på resten av kolumnerna, så att du inte behöver välja var och en av kolumnerna individuellt för att slutföra schemamappningen. Om du vill kan du åsidosätta dessa mappningar genom att använda listrutorna för att mappa kolumnerna en efter en. Mönstret blir mer exakt när du kartlägger fler kolumner. Kopieringsguiden uppdaterar mönstret kontinuerligt och når i slutändan rätt mönster för den kolumnmappning du vill uppnå.     

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
Du kan filtrera källdata för att bara markera de data som behöver kopieras till sink-datalagret. Filtrering minskar volymen på de data som ska kopieras till diskbänksdatalagret och förbättrar därför kopieringsflödets dataflöde. Det ger ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL-frågespråket eller filer i en Azure-blobmapp med hjälp av [datafabriksfunktioner och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
Följande skärmbild visar en `Text.Format` SQL-fråga med hjälp av funktionen och `WindowStart` variabeln.

![Validera uttryck](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering av data i en Azure-blobmapp
Du kan använda variabler i mappsökvägen för att kopiera data från en mapp som bestäms vid körning baserat på [systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Variablerna som stöds är: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** och **{custom}**. Till exempel: inputfolder/{year}/{month}/{day}.

Anta att du har inmatningsmappar i följande format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klicka på knappen **Bläddra** efter **Fil eller mapp**, bläddra till någon av dessa mappar (till exempel 2016->03->01->02) och klicka på **Välj**. Du bör `2016/03/01/02` se i textrutan. Ersätt nu **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** och **02** med **{hour}** och tryck på **Tabb.** Du bör se listrutor för att välja format för dessa fyra variabler:

![Använda systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Som visas i följande skärmbild kan du också använda en **anpassad** variabel och eventuella [formatsträngar som stöds](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Om du vill välja en mapp med den strukturen använder du knappen **Bläddra** först. Ersätt sedan ett värde med **{custom}** och tryck på **Tabb** för att se textrutan där du kan skriva formatsträngen.     

![Använda anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen en gång eller enligt ett schema (varje timme, varje dag och så vidare). Båda dessa alternativ kan användas för bredden på kopplingarna i olika miljöer, inklusive lokal, moln och lokal skrivbordskopia.

En engångskopia gör det möjligt för data förflyttning från en källa till ett mål bara en gång. Det gäller data av alla storlekar och alla format som stöds. Med den schemalagda kopian kan du kopiera data på en föreskriven upprepning. Du kan använda avancerade inställningar (som återförsök, timeout och aviseringar) för att konfigurera den schemalagda kopian.

![Egenskaper för schemaläggning](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nästa steg
En snabb genomgång av att använda guiden Kopia för datafabrik för att skapa en pipeline med kopieringsaktivitet finns i [Självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md).
