---
title: Visualisera data från Azure Data Explorer med en Power BI-importerad fråga
description: 'I den här artikeln får du lära dig hur du använder ett av de tre alternativen för att visualisera data i Power BI: importera en fråga från Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ff156ab3fe74115bce8f7d6bdd3ba47b514f5ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562487"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Visualisera data med hjälp av en fråga som importeras till Power BI

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Power BI är en lösning för företagsanalys där du kan visualisera dina data och dela resultaten i hela organisationen.

Azure Data Explorer ger tre alternativ för att ansluta till data i Power BI: använda den inbyggda anslutningsappen, importera en fråga från Azure Data Explorer eller använda en SQL-fråga. I den här artikeln visas hur du importerar en fråga så att du kan hämta data och visualisera den i en Power BI-rapport.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra den här artikeln:

* En organisations e-postkonto som är medlem i Azure Active directory, så att du kan ansluta till [Azure Data Explorer-hjälpklustret](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (välj **DOWNLOAD FREE**) (Ladda ned kostnadsfritt)

* [Azure Data Explorer-skrivbordsapp](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Hämta data från Azure Data Explorer

Först skapar du en fråga i Azure Data Explorer-skrivbordsappen och exporterar den för användning i Power BI. Sedan ansluter du till Azure Data Explorer-hjälpklustret, och hämtar du en delmängd av data från tabellen *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. I en webbläsare [https://help.kusto.windows.net/](https://help.kusto.windows.net/) går du till för att starta azure data explorer-skrivbordsappen.

1. I skrivbordsappen kopierar du följande fråga i det övre högra frågefönstret och kör den.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    De första raderna i resultatuppsättningen bör likna följande bild.

    ![Frågeresultat](media/power-bi-imported-query/query-results.png)

1. På fliken **Verktyg** väljer du **Query to Power BI** (Fråga till Power BI) och sedan **OK**.

    ![Exportera fråga](media/power-bi-imported-query/export-query.png)

1. I Power BI Desktop går du till fliken **Start**, väljer **Hämta data** och sedan **Tom fråga**.

    ![Hämta data](media/power-bi-imported-query/get-data.png)

1. På **startfliken** i Power Query Editor väljer du **Avancerad redigerare**.

1. I fönstret **Avancerad redigerare** klistrar du in frågan du exporterade och väljer **Klar**.

    ![Klistra in fråga](media/power-bi-imported-query/paste-query.png)

1. I Power Query Editor-fönstret väljer du **Redigera autentiseringsuppgifter**. Välj **Organisationskonto**, logga in och välj sedan **Anslut**.

    ![Redigera autentiseringsuppgifter](media/power-bi-imported-query/edit-credentials.png)

1. På fliken **Start** väljer du **Stäng och tillämpa**.

    ![Stäng och tillämpa](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualisera data i en rapport

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver rapporten som du skapade för den här artikeln tar du bort Power BI Desktop -filen (.pbix).

## <a name="next-steps"></a>Nästa steg

[Visualisera data med Azure Data Explorer-anslutningen för Power BI](power-bi-connector.md)