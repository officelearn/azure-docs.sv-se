---
title: Kopiera data enkelt med kopieringsguiden - Azure
description: Lär dig mer om hur du använder guiden Kopia av Data Factory för att kopiera data från datakällor som stöds till sänkor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b872cc30ae66e83274f189138dad6d609e3f536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927056"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Kopiera eller flytta data enkelt med Azure Data Factory Copy Wizard
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten. 


Azure Data Factory Copy Wizard är att underlätta processen att inta data, vilket vanligtvis är ett första steg i ett scenario med integrering av data från slutna till slutna data. När du går igenom Azure Data Factory Copy Wizard behöver du inte förstå några JSON-definitioner för länkade tjänster, datauppsättningar och pipelines. När du har slutfört alla steg i guiden skapas dock automatiskt en pipeline för att kopiera data från den valda datakällan till det valda målet. Dessutom hjälper kopieringsguiden dig att validera de data som intas vid tidpunkten för redigering, vilket sparar mycket av din tid, särskilt när du intar data för första gången från datakällan. Om du vill starta kopieringsguiden klickar du på panelen **Kopiera data** på startsidan för datafabriken.

![Guiden Kopiera](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>En intuitiv guide för kopiering av data
Med den här guiden kan du enkelt flytta data från en mängd olika källor till destinationer på några minuter. När du har gått igenom guiden skapas en pipeline med en kopieringsaktivitet automatiskt för dig tillsammans med beroende datafabrikentiteter (länkade tjänster och datauppsättningar). Inga ytterligare steg krävs för att skapa pipelinen.   

![Välja datakälla](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Se [Självstudieartikel](data-factory-copy-data-wizard-tutorial.md) i copy wizard för steg-för-steg-instruktioner för att skapa en exempelpipeline för att kopiera data från en Azure-blob till en Azure SQL Database-tabell. 
> 
> 

Guiden är utformad med stordata i åtanke från början. Det är enkelt och effektivt att skapa Data Factory-pipelines som flyttar hundratals mappar, filer eller tabeller med hjälp av guiden Kopiera data. Guiden stöder följande tre funktioner: Automatisk förhandsgranskning av data, schemainsamling och mappning samt filtrering av data. 

## <a name="automatic-data-preview"></a>Automatisk förhandsgranskning av data
Med kopieringsguiden kan du granska en del av data från den valda datakällan så att du kan verifiera om de data som är rätt data som du vill kopiera. Om källdata finns i en textfil tolkar kopieringsguiden dessutom textfilen för att lära sig rad- och kolumnavgränsare och schema automatiskt. 

![Filformatinställningar](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Insamling och mappning av schema
Schemat för indata kanske inte matchar schemat för utdata i vissa fall. I det här fallet måste du mappa kolumner från källschemat till kolumner från målschemat. 

Kopieringsguiden mappar automatiskt kolumner i källschemat till kolumner i målschemat. Du kan åsidosätta mappningarna med hjälp av listrutorna (eller) ange om en kolumn måste hoppas över när data kopieras.   

![Schemamappning](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrera data
I guiden kan du filtrera källdata för att välja endast de data som behöver kopieras till datalagret för mål/diskbänk. Filtrering minskar volymen på de data som ska kopieras till diskbänksdatalagret och förbättrar därför kopieringsflödets dataflöde. Det ger ett flexibelt sätt att filtrera data i en relationsdatabas med hjälp av SQL-frågespråk (eller) filer i en Azure blob mapp med hjälp av [Data Factory funktioner och variabler](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrering av data i en databas
I exemplet använder SQL-frågan `Text.Format` funktionen `WindowStart` och variabeln. 

![Validera uttryck](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrering av data i en Azure-blobmapp
Du kan använda variabler i mappsökvägen för att kopiera data från en mapp som bestäms vid körning baserat på [systemvariabler](data-factory-functions-variables.md#data-factory-system-variables). Variablerna som stöds är: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** och **{custom}**. Exempel: inputfolder/{year}/{month}/{day}.

Anta att du har inmatningsmappar i följande format:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klicka på knappen **Bläddra** efter **Fil eller mapp**, bläddra till någon av dessa mappar (till exempel 2016->03->01->02) och klicka på **Välj**. Du bör `2016/03/01/02` se i textrutan. Ersätt nu **2016** med **{year}**, **03** med **{month}**, **01** med **{day}** och **02** med **{hour}** och tryck på Tabb. Du bör se listrutor för att välja format för dessa fyra variabler:

![Använda systemvariabler](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Som visas i följande skärmbild kan du också använda en **anpassad** variabel och eventuella [formatsträngar som stöds](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Om du vill välja en mapp med den strukturen använder du knappen **Bläddra** först. Ersätt sedan ett värde med **{custom}** och tryck på Tabb för att se textrutan där du kan skriva formatsträngen.     

![Använda anpassad variabel](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Stöd för olika data och objekttyper
Med hjälp av kopieringsguiden kan du effektivt flytta hundratals mappar, filer eller tabeller.

![Välj tabeller som data ska kopieras från](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Alternativ för schemaläggning
Du kan köra kopieringen en gång eller enligt ett schema (varje timme, varje dag och så vidare). Båda dessa alternativ kan användas för bredden på kopplingarna över lokal, moln och lokal skrivbordskopia.

En engångskopia gör det möjligt för data förflyttning från en källa till ett mål bara en gång. Det gäller data av alla storlekar och alla format som stöds. Med den schemalagda kopian kan du kopiera data på en föreskriven upprepning. Du kan använda avancerade inställningar (som återförsök, timeout och aviseringar) för att konfigurera den schemalagda kopian.

![Egenskaper för schemaläggning](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Nästa steg
En snabb genomgång av att använda guiden Kopia för datafabrik för att skapa en pipeline med kopieringsaktivitet finns i [Självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md).

