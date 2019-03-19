---
title: Kom igång med Azure Data Lake Analytics med hjälp av Visual Studio
description: Lär dig hur du installerar Data Lake Tools för Visual Studio och hur du utvecklar och testar U-SQL-skript.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/13/2018
ms.openlocfilehash: b463946402eee40d0de0942eeaf37a6f9ea59990
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083070"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake och Stream Analytics Tools innehåller funktioner som är relaterade till två Azure-tjänster, Azure Data Lake Analytics och Azure Stream Analytics. Mer information om Azure Stream Analytics-scenarier finns i [Azure Stream Analytics-verktyg för Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Den här artikeln beskriver hur du använder Visual Studio för att skapa Azure Data Lake Analytics-konton, definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md), och skicka jobb till Data Lake Analytics-tjänsten. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Microsoft rekommenderar att du uppgraderar till Azure Data Lake Tools för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och är nu föråldrade. 
> 
> **Vad måste jag göra?**
> 
> 1. Kontrollera om du använder en tidigare version än 2.3.3000.4 för Azure Data Lake-verktyg för Visual Studio. 
> 
>    ![Kontrollera version av verktyget](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
> 2. Om din version är en tidigare version av 2.3.3000.4 uppdaterar du ditt Azure Data Lake-verktyg för Visual Studio genom att besöka hämtningscentret: 
>    - [För Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [För Visual Studio 2013 och 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Förutsättningar

* **Visual Studio**: Alla utgåvor utom Express stöds.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK för .NET** version 2.7.1 eller senare.  Installera den med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/platform.aspx).
* Ett **Data Lake Analytics**-konto. Information om hur du skapar ett konto finns i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installera Azure Data Lake Tools för Visual Studio

För den här självstudien krävs att Data Lake Tools för Visual Studio har installerats. Följ [installationsinstruktionerna](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Ansluta till ett Azure Data Lake Analytics-konto

1. Öppna Visual Studio.

2. Öppna Server Explorer genom att välja **Visa** > **Server Explorer**.

3. Högerklicka på **Azure**. Välj sedan **Anslut till Microsoft Azure-prenumeration** och följ sedan instruktionerna.

4. Välj **Azure** > **Data Lake Analytics** i Server Explorer. En lista över dina Data Lake Analytics-konton visas.

## <a name="write-your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande text är ett enkelt U-SQL-skript. Skriptet definierar ett liten datauppsättning och skriver den till standard-Data Lake Store som en fil med namnet `/data.csv`.

```
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Skicka ett Data Lake Analytics-jobb

1. Välj **Arkiv** > **Nytt** > **Projekt**.

2. Välj typen **U-SQL-projekt** och klicka sedan på **OK**. Visual Studio skapar en lösning med filen **Script.usql**.

3. Klistra in det tidigare skriptet i fönstret **Script.usql**.

4. Ange Data Lake Analytics-kontot längst upp till vänster i fönstret **Script.usql**.

    ![Skicka U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. Välj **Skicka** längst upp till vänster i fönstret **Script.usql**.

6. När jobbet har skickats öppnas fliken **Job view** (Jobbvy) där du kan följa jobbförloppet. Klicka på **Uppdatera** för att se senaste jobbstatus och uppdatera skärmen.

    ![Prestandadiagram för U-SQL Visual Studio Data Lake Analytics-jobbet](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Jobbsammanfattning** visar en sammanfattning av jobbet.   
   * **Jobbdiagram** visualiserar jobbförloppet.
   * **Metadataåtgärder** visar alla åtgärder som vidtagits för U-SQL-katalogen.
   * **Data** visar alla indata och utdata.
   * **State History** (Tillståndshistorik) visar tidslinjen och tillståndsinformation.
   * **AU-analys** visar hur många analysenheter som användes i jobbet och utforskar simuleringar av olika AU-allokeringsstrategier.
   * **Diagnostik** ger en avancerad analys för jobbkörning och prestandaoptimering.

## <a name="check-job-status"></a>Kontrollera jobbstatus

1. Välj **Azure** > **Data Lake Analytics** i Server Explorer.

2. Expandera Data Lake Analytics-kontonamnet.

3. Dubbelklicka på **Jobb**.

4. Välj det jobb som du skickade tidigare.

## <a name="see-the-job-output"></a>Visa jobbutdata

1. I Server Explorer bläddrar du till jobbet du skickade.

2. Klicka på fliken **Data**.

3. Välj filen `"/data.csv"` på fliken **Job Outputs** (Jobbutdata).

## <a name="next-steps"></a>Nästa steg

* [Kör U-SQL-skript på din egen arbetsstation för testning och felsökning](data-lake-analytics-data-lake-tools-local-run.md)
* [Felsöka C#-kod i U-SQL-jobb med hjälp av Azure Data Lake-verktyg för Visual Studio-kod](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
