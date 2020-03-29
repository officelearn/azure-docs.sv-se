---
title: Visualisera data med hjälp av en Azure Data Explorer Kusto-fråga som importeras till Microsoft Excel
description: I den här artikeln får du lära dig hur du importerar en Azure Data Explorer Kusto-fråga till Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849096"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualisera data med hjälp av en Azure Data Explorer Kusto-fråga som importeras till Microsoft Excel

Azure Data Explorer innehåller två alternativ för att ansluta till data i Excel: använd den inbyggda kopplingen eller importera en fråga från Azure Data Explorer. Den här artikeln visar hur du importerar en fråga från Azure Data Explorer till Excel för att visualisera data. Lägg till Kusto-fråga som en Excel-datakälla för att göra ytterligare beräkningar eller visualiseringar på data.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett organisations-e-postkonto som är medlem i Azure Active Directory, så att du kan ansluta till [hjälpklustret för Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>eller</br>
* Skapa [ett testkluster och en databas](create-cluster-database-portal.md) och logga in på Azure Data Explorer Web [UI-programmet](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definiera Kusto-fråga som en Excel-datakälla

1. I [Azure Data Explorer Web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples)kör du frågan och kontrollerar resultaten.

1. Välj fliken **Dela** och välj **Fråga till Power BI**.

    ![Webbgränssnittsfråga till Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Ett fönster visas med följande meddelande:

    ![exportera fråga till Urklipp](media/excel-blank-query/query-exported-to-clipboard.png)

1. Öppna **Microsoft Excel**.

1. På fliken **Data** väljer du **Hämta data** > från**tom fråga**för andra**källor** > .

    ![Hämta data och välj tom fråga](media/excel-blank-query/get-data-blank-query.png)

1. Fönstret **Power Query Editor** öppnas. Välj **Avancerad redigerare**i fönstret .

    ![Fönstret Power Query Editor](media/excel-blank-query/power-query-editor.png)

1. I fönstret **Avancerad redigerare** klistrar du in frågan som du exporterade till Urklipp och väljer **Klar**.

    ![Avancerad redigeringsfråga](media/excel-blank-query/advanced-editor-query.png)    

1. Om du vill autentisera väljer du **Redigera autentiseringsuppgifter**.

    ![Redigera autentiseringsuppgifter](media/excel-blank-query/edit-credentials.png)

1. Välj **Organisationskonto** och **Logga in**. Slutför inloggningsprocessen och välj sedan **Anslut**.

    ![Fullständig inloggning](media/excel-blank-query/complete-sign-in.png)

    Upprepa föregående steg för att lägga till fler frågor. Du kan byta namn på frågorna till mer meningsfulla namn.

1. Välj knappen **Stäng & Läs in** för att hämta data till Excel.

    ![Välj stäng och ladda](media/excel-blank-query/close-and-load.png)

1. Nu finns dina data i Excel. Välj knappen **Uppdatera** om du vill uppdatera frågan.

    ![Visa data i Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Nästa steg

[Visualisera data med Azure Data Explorer-kopplingen för Excel](excel-connector.md)