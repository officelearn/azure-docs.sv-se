---
title: Använda inmatning med ett klick för att använda data i en ny Azure Data Explorer-tabell
description: Intag av (inläsning) data i en ny Azure Data Explorer-tabell helt enkelt med hjälp av inmatning med ett klick.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 1e337a46d1d8ddda1b07bde6f12d4c1f7feda42f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549225"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-a-new-table-in-azure-data-explorer"></a>Använda inmatning med ett klick för att använda data till en ny tabell i Azure Data Explorer

Med inmatning med ett klick kan du snabbt få in data i JSON, CSV och andra format i en tabell. Med hjälp av webbgränssnittet i Azure Data Explorer kan du använda data från lagring, från en lokal fil eller från en behållare. 

I det här dokumentet beskrivs hur du använder den intuitiva guiden med ett klick för att förtära CSV-data från en behållare till en ny tabell. Du kan sedan redigera tabellen och köra frågor med webbgränssnittet i Azure Data Explorer. Du kan också ange kontinuerligt inmatning för att automatiskt förtära data i tabellen när källdata uppdateras.

Inmatning med ett klick är särskilt användbart när du inbetar data för första gången, eller när datas schema inte är bekant för dig. 

En översikt över inmatning med ett klick och en lista över förutsättningar finns [i Inmatning](ingest-data-one-click.md)med ett klick .
Information om hur du inbester data i en befintlig tabell i Azure Data Explorer finns [i Inmatning](one-click-ingestion-existing-table.md) med ett klick i en befintlig tabell

## <a name="ingest-new-data"></a>Övriga nya data

1. Högerklicka på en *databas* på den vänstra menyn i webbgränssnittet och välj **Ingest nya data (Förhandsgranska).**

    ![Markera inmatning med ett klick i webbgränssnittet](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. I fönstret **Ingest nya data (Förhandsgranska)** markeras fliken **Källa** automatiskt. 

1. Välj **Skapa ny tabell** och ange ett namn på den nya tabellen. Du kan använda alfanumeriska, bindestreck och understreck. Specialtecken stöds inte.

![Skapa en ny tabell](media/one-click-ingestion-new-table/create-new-table.png) 

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]

Välj **Redigera schema** om du vill visa och redigera konfigurationen av tabellkolumnen. Systemet väljer en av blobbar slumpmässigt och schemat genereras baserat på den bloben. Genom att titta på namnet på källan identifierar tjänsten automatiskt om den är komprimerad eller inte.

## <a name="edit-the-schema"></a>Redigera schemat

1. På fliken **Schema:**

    1. Välj **dataformat:**

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]

    1. Om du väljer **JSON**måste du också välja **JSON-nivåer**, från 1 till 10. Nivåerna påverkar tabellkolumndataavbildningen. 

    ![Välj JSON-nivåer](media/one-click-ingestion-new-table/json-levels.png)

    * Om du markerar ett annat format än JSON kan du markera kryssrutan **Inkludera kolumnnamn** om du vill ignorera filens rubrikrad.

        ![Välj Inkludera kolumnnamn](media/one-click-ingestion-new-table/non-json-format.png)
        
1. Ange ett mappningsnamn i fältet **Mappningsnamn.** Du kan använda alfanumeriska tecken och understreck. Blanksteg, specialtecken och bindestreck stöds inte.
    
    ![Namn på tabellmappning](media/one-click-ingestion-new-table/table-mapping.png)

## <a name="copy-and-paste-queries"></a>Kopiera och klistra in frågor

1. Öppna **redigeraren** genom att välja **v-knappen** ovanför redigeringsfönstret. I redigeraren kan du visa och kopiera de automatiska frågor som genereras från dina indata. 
1. I tabellen: 
    * Dubbelklicka på det nya kolumnnamnet som ska redigeras.
    * Markera nya kolumnrubriker och gör något av följande:
    
|Åtgärd         |Beskrivning                                  |
|-----------------|-------------------------------------------|
|Ändra datatyp |Ändra datatypen från den som automatiskt väljs av tjänsten till en av de andra [datatyperna som stöds](#edit-the-schema)|
|Byt namn på kolumn    |Ändra kolumnnamnet |
|Ny kolumn       |Lägga till en ny kolumn|
|Ta bort kolumn    |Ta bort den markerade kolumnen|
|Sortera stigande   |Sortera tabellen efter den markerade kolumnen i stigande ordning (endast befintliga kolumner)|
|Sortera fallande  |Sortera tabellen efter den markerade kolumnen i fallande ordning (endast befintliga kolumner) |

> [!Note]
> För tabellformat kan varje kolumn intas i en kolumn i Azure Data Explorer.
> Du kan skapa nya kolumner från olika JSON-nivåer.

[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Starta inmatning

Välj **Starta inmatning** om du vill skapa en tabell och mappning och påbörja datainmatning.
![Starta inmatning](media/one-click-ingestion-new-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Datainmatning har slutförts

I fönstret **Datainmatning slutförs** alla tre stegen med gröna bockar om datainmatningen har slutförts.
 
![Datainmatning med ett klick har slutförts](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

**Kontinuerligt intag**

Med kontinuerligt inmatning kan du skapa ett händelserutnät som lyssnar på källbehållaren. Alla nya blob som uppfyller kriterierna för de fördefinierade parametrarna (prefix, suffix och så vidare) förtärs automatiskt i måltabellen.

> [!Note]
> Kontinuerligt intag är endast relevant vid intag från en behållare.

1. Välj **Kontinuerligt inmatning för** att öppna Azure-portalen. Dataanslutningssidan öppnas med händelserutnätsdataanslutningen öppnad och med käll- och målparametrar redan angivna (källbehållare, tabeller och mappningar).

1. Välj **Skapa** om du vill skapa en dataanslutning som lyssnar efter ändringar i behållaren. 

## <a name="next-steps"></a>Nästa steg

* [Frågedata i webbgränssnittet i Azure Data Explorer](/azure/data-explorer/web-query-data)
* [Skriva frågor för Azure Data Explorer med Kusto Query Language](/azure/data-explorer/write-queries)
