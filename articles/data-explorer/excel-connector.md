---
title: Visualisera data med Azure Datautforskaren Connector för Microsoft Excel
description: I den här artikeln får du lära dig hur du använder Excel-anslutaren för Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849062"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualisera data med Azure Datautforskaren Connector för Excel

Azure Datautforskaren innehåller två alternativ för att ansluta till data i Excel: Använd den interna anslutningen eller importera en fråga från Azure Datautforskaren. Den här artikeln visar hur du använder den inbyggda anslutningen i Excel och ansluter till Azure Datautforskaren-klustret för att hämta och visualisera data.

Azure Datautforskaren Excel Native Connector ger möjlighet att exportera frågeresultat till Excel. Du kan också lägga till en KQL-fråga som en Excel-datakälla för ytterligare beräkningar eller visualiseringar.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definiera Kusto-fråga som en Excel-datakälla och läsa in data till Excel

1. Öppna **Microsoft Excel**.
1. På fliken **data** väljer du **Hämta data** > **från Azure** > **från Azure datautforskaren**.

    ![Hämta data från Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. I fönstret **Azure datautforskaren (Kusto)** fyller du i följande fält och väljer **OK**.

    ![Fönstret Azure Datautforskaren (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Fält   |Beskrivning |
    |---------|---------|
    |**Flernodskluster**   |   Namn på kluster (obligatoriskt)      |    
    |**Databas**     |    Namnet på databasen      |    
    |**Tabell namn eller Azure Datautforskaren fråga**    |     Namn på tabell eller Azure Datautforskaren fråga    | 
    
    **Avancerade alternativ:**

     |Fält   |Beskrivning |
    |---------|---------|
    |**Begränsa post nummer för frågeresultat**     |     Begränsa antalet inlästa poster till Excel  |    
    |**Begränsa data storlek för frågeresultat (byte)**    |    Begränsa data storleken      |   
    |**Inaktivera avtrunkering av resultat uppsättning**    |         |      
    |**Ytterligare set-instruktioner (avgränsade med semikolon)**    |    Lägg till `set`-instruktioner som ska tillämpas på data källan     |   

1.  I **navigerings** fönstret navigerar du till rätt tabell. I rutan tabell förhands granskning väljer du **transformera data** för att göra ändringar i dina data eller väljer **Läs in** för att läsa in den i Excel.

![Tabell förhands gransknings fönster](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Om **databas** -och/eller **tabell namn eller Azure Datautforskarens fråga** redan har angetts öppnas rätt tabell förhands gransknings fönster automatiskt. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analysera och visualisera data i Excel

När data har lästs in till Excel och är tillgängliga i ditt Excel-blad kan du analysera, sammanfatta och visualisera data genom att skapa relationer och visuella objekt. 

1.  På fliken **tabell design** väljer du **sammanfatta med pivottabell**. I fönstret **skapa pivottabell** väljer du relevant tabell och väljer **OK**.

    ![Skapa Pivot-tabell](media/excel-connector/create-pivot-table.png)

1. I fönstret **pivottabellfält** väljer du relevanta tabell kolumner för att skapa sammanfattnings tabeller. I exemplet nedan är **EventId** och **State** markerade.
    
    ![Välj pivottabellfält](media/excel-connector/pivot-table-pick-fields.png)

1. På fliken **analys av pivottabell** väljer du **pivotdiagram** för att skapa visuella objekt baserat på tabellen. 

    ![Pivot-diagram](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. I exemplet nedan använder du **händelse-ID**, **StartTime**och **EventType** för att visa mer information om väder händelser.

    ![Visualisera data](media/excel-connector/visualize-excel-data.png)

1. Skapa fullständiga instrument paneler för att övervaka dina data.

## <a name="next-steps"></a>Nästa steg

[Visualisera data med en Azure Datautforskaren Kusto-fråga som importer ATS till Microsoft Excel](excel-blank-query.md)