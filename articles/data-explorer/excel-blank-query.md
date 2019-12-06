---
title: Visualisera data med en Azure Datautforskaren Kusto-fråga som importer ATS till Microsoft Excel
description: I den här artikeln får du lära dig hur du importerar en Azure Datautforskaren Kusto-fråga till Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849096"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualisera data med en Azure Datautforskaren Kusto-fråga som importer ATS till Microsoft Excel

Azure Datautforskaren innehåller två alternativ för att ansluta till data i Excel: Använd den interna anslutningen eller importera en fråga från Azure Datautforskaren. Den här artikeln visar hur du importerar en fråga från Azure Datautforskaren till Excel för att visualisera data. Lägg till Kusto-fråga som en Excel-datakälla för att utföra ytterligare beräkningar eller visualiseringar av data.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett e-postkonto i organisationen som är medlem i Azure Active Directory, så att du kan ansluta till [azure datautforskaren hjälp kluster](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>eller</br>
* Skapa [ett test kluster och en databas](create-cluster-database-portal.md) och logga in på [Azure datautforskaren Web UI-programmet](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definiera Kusto-fråga som en Excel-datakälla

1. Kör frågan i [Azure datautforskaren Web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples)och kontrol lera resultaten.

1. Välj fliken **dela** och välj **fråga för att Power BI**.

    ![Webb GRÄNSSNITTs fråga att Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Ett fönster med följande meddelande visas:

    ![Exportera fråga till Urklipp](media/excel-blank-query/query-exported-to-clipboard.png)

1. Öppna **Microsoft Excel**.

1. På fliken **data** väljer du **Hämta data** > **från andra källor** > en **Tom fråga**.

    ![Hämta data och välj tom fråga](media/excel-blank-query/get-data-blank-query.png)

1. Fönstret **Power Querys redigerare** öppnas. I fönstret väljer du **avancerad redigerare**.

    ![Power Query Editor-fönstret](media/excel-blank-query/power-query-editor.png)

1. I fönstret **avancerad redigerare** klistrar du in frågan som du exporterade till Urklipp och väljer sedan **Slutför**.

    ![Avancerad redigerings fråga](media/excel-blank-query/advanced-editor-query.png)    

1. För att autentisera väljer du **Redigera autentiseringsuppgifter**.

    ![Redigera autentiseringsuppgifter](media/excel-blank-query/edit-credentials.png)

1. Välj **organisations konto** och **Logga**in. Slutför inloggnings processen och välj sedan **Anslut**.

    ![Slutför inloggning](media/excel-blank-query/complete-sign-in.png)

    Upprepa föregående steg för att lägga till fler frågor. Du kan byta namn på frågorna till mer meningsfulla namn.

1. Välj knappen **stäng & Läs in** för att hämta dina data till Excel.

    ![Välj Stäng och Läs in](media/excel-blank-query/close-and-load.png)

1. Nu finns dina data i Excel. Klicka på knappen **Uppdatera** för att uppdatera frågan.

    ![Visa data i Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Nästa steg

[Visualisera data med Azure Datautforskaren Connector för Excel](excel-connector.md)