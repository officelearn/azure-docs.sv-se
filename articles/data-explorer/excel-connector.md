---
title: Visualisera data med Azure Data Explorer-kopplingen för Microsoft Excel
description: I den här artikeln får du lära dig hur du använder Excel-anslutningsappen för Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849062"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualisera data med Azure Data Explorer-kopplingen för Excel

Azure Data Explorer innehåller två alternativ för att ansluta till data i Excel: använd den inbyggda kopplingen eller importera en fråga från Azure Data Explorer. Den här artikeln visar hur du använder den inbyggda anslutningen i Excel och ansluter till Azure Data Explorer-klustret för att hämta och visualisera data.

Den inbyggda Azure Data Explorer Excel-anslutningsappen ger möjlighet att exportera frågeresultat till Excel. Du kan också lägga till en KQL-fråga som en Excel-datakälla för ytterligare beräkningar eller visualiseringar.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definiera Kusto-fråga som en Excel-datakälla och läsa in data i Excel

1. Öppna **Microsoft Excel**.
1. På fliken **Data** väljer du **Hämta data** > **från Azure** > **från Azure Data Explorer**.

    ![Hämta data från Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. I fönstret **Azure Data Explorer (Kusto)** slutför du följande fält och väljer **OK**.

    ![Fönstret Azure Data Explorer (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Field   |Beskrivning |
    |---------|---------|
    |**Kluster**   |   Namn på kluster (obligatoriskt)      |    
    |**Databas**     |    Namnet på databasen      |    
    |**Tabellnamn eller Azure Data Explorer-fråga**    |     Namn på tabell- eller Azure Data Explorer-fråga    | 
    
    **Avancerade alternativ:**

     |Field   |Beskrivning |
    |---------|---------|
    |**Begränsa frågeresultatpostnummer**     |     Begränsa antalet poster som läses in i Excel  |    
    |**Begränsa frågeresultatdatastorlek (byte)**    |    Begränsa datastorleken      |   
    |**Inaktivera trunkering av resultatuppsättningar**    |         |      
    |**Ytterligare setsatser (avgränsade med semikolon)**    |    Lägga `set` till satser som ska tillämpas på datakälla     |   

1.  Navigera till rätt tabell i **fönstret Navigatör.** I förhandsgranskningsfönstret väljer du **Omforma data** om du vill göra ändringar i data eller välj **Läs** in för att läsa in dem i Excel.

![Förhandsgranskningsfönster för tabell](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Om **databas-** och/eller **tabellnamn eller Azure Data Explorer-fråga** redan har angetts öppnas rätt förhandsgranskningsfönster automatiskt. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analysera och visualisera data i Excel

När data läses in för att utmärka sig och är tillgängliga i Excel-bladet kan du analysera, sammanfatta och visualisera data genom att skapa relationer och visuella objekt. 

1.  Välj **Sammanfatta med pivottabell**på fliken **Tabelldesign** . Markera relevant tabell i fönstret **Skapa pivottabell** och välj **OK**.

    ![Skapa pivottabell](media/excel-connector/create-pivot-table.png)

1. I fönstret **Pivottabellfält** markerar du de relevanta tabellkolumnerna för att skapa sammanfattningstabeller. I exemplet nedan väljs **EventId** och **State.**
    
    ![Markera pivottabellfält](media/excel-connector/pivot-table-pick-fields.png)

1. På fliken **Analysera pivottabell** väljer du **Pivotdiagram** för att skapa visuella objekt baserat på tabellen. 

    ![Pivotdiagram](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. I exemplet nedan använder du **Händelse-ID,** **StartTid**och **EventType** för att visa ytterligare information om väderhändelserna.

    ![Visualisera data](media/excel-connector/visualize-excel-data.png)

1. Skapa fullständiga instrumentpaneler för att övervaka dina data.

## <a name="next-steps"></a>Nästa steg

[Visualisera data med hjälp av en Azure Data Explorer Kusto-fråga som importeras till Microsoft Excel](excel-blank-query.md)