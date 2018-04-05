---
title: Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio | Microsoft Docs
description: Lär dig hur du installerar Data Lake Tools för Visual Studio och hur du utvecklar och testar U-SQL-skript.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2017
ms.author: saveenr, yanacai
ms.openlocfilehash: b94c035083a00867139b6c58bc8b2ded08e970b0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Lär dig hur du använder Visual Studio för att skapa Azure Data Lake Analytics-konton, definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-tjänsten. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

* **Visual Studio**: Alla utgåvor utom Express stöds.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK för .NET** version 2.7.1 eller senare.  Installera den med hjälp av [installationsprogrammet för webbplattformen](http://www.microsoft.com/web/downloads/platform.aspx).
* Ett **Data Lake Analytics**-konto. Information om hur du skapar ett konto finns i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installera Azure Data Lake Tools för Visual Studio

### <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Installera Azure Data Lake Tools för Visual Studio 2017

Azure Data Lake Tools för Visual Studio stöds i Visual Studio 2017, version 15.3 och senare. Verktyget är en del av arbetsbelastningarna **Lagring och bearbetning av data** och **Azure Development** i Visual Studio-installationsprogrammet. Aktivera någon av dessa två arbetsbelastningar som en del av Visual Studio-installationen.  

Aktivera arbetsbelastningen **Lagring och bearbetning av data** så här: ![Aktivera arbetsbelastningen Lagring och bearbetning av data](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Aktivera arbetsbelastningen **Azure Development** så här: ![Aktivera arbetsbelastningen Azure Development](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

### <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installera Azure Data Lake Tools för Visual Studio 2013 och 2015

Hämta och installera Azure Data Lake Tools för Visual Studio [från Download Center](http://aka.ms/adltoolsvs). Efter installationen kontrollerar du att:
* Noden **Server Explorer** > **Azure** innehåller en **Data Lake Analytics**-nod. 
* Alternativet **Data Lake** finns på **Verktyg**-menyn.

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Ansluta till ett Azure Data Lake Analytics-konto

1. Öppna Visual Studio.
2. Öppna Server Explorer genom att välja **Visa** > **Server Explorer**.
3. Högerklicka på **Azure**. Välj sedan **Anslut till Microsoft Azure-prenumeration** och följ sedan instruktionerna.
4. Välj **Azure** > **Data Lake Analytics** i Server Explorer. En lista över dina Data Lake Analytics-konton visas.


## <a name="write-your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande text är ett enkelt U-SQL-skript. Skriptet definierar ett liten datauppsättning och skriver den till standard-Data Lake Store som en fil med namnet `/data.csv`.

```
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

### <a name="submit-a-data-lake-analytics-job"></a>Skicka ett Data Lake Analytics-jobb

1. Välj **Arkiv** > **Nytt** > **Projekt**.

2. Välj typen **U-SQL-projekt** och klicka sedan på **OK**. Visual Studio skapar en lösning med filen **Script.usql**.

3. Klistra in det tidigare skriptet i fönstret **Script.usql**.

4. Ange Data Lake Analytics-kontot längst upp till vänster i fönstret **Script.usql**.

    ![Skicka U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. Välj **Skicka** längst upp till vänster i fönstret **Script.usql**.
6. Kontrollera **Analytics-kontot** och välj sedan **Skicka**. Resultatet visas i resultatfönstret för Data Lake Tools för Visual Studio när överföringen är klar.

    ![Skicka U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
7. Klicka på **Uppdatera** för att se senaste jobbstatus och uppdatera skärmen. När jobbet har slutförts visas **Jobbdiagram**, **Metadataåtgärder**, **Tillståndshistorik** och **Diagnostik**:

    ![Prestandadiagram för U-SQL Visual Studio Data Lake Analytics-jobbet](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Jobbsammanfattning** visar en sammanfattning av jobbet.   
   * **Jobbinformation** visar mer detaljerad information om jobbet, inklusive skript, resurser och hörn.
   * **Jobbdiagram** visualiserar jobbförloppet.
   * **Metadataåtgärder** visar alla åtgärder som vidtagits för U-SQL-katalogen.
   * **Data** visar alla indata och utdata.
   * **Diagnostik** ger en avancerad analys för jobbkörning och prestandaoptimering.

### <a name="to-check-job-state"></a>Om du vill kontrollera jobbstatus

1. Välj **Azure** > **Data Lake Analytics** i Server Explorer. 
2. Expandera Data Lake Analytics-kontonamnet.
3. Dubbelklicka på **Jobb**.
4. Välj det jobb som du skickade tidigare.

### <a name="to-see-the-output-of-a-job"></a>Visa utdata för ett jobb

1. I Server Explorer bläddrar du till jobbet du skickade.
2. Klicka på fliken **Data**.
3. Välj filen `"/data.csv"` på fliken **Job Outputs** (Jobbutdata).

## <a name="next-steps"></a>Nästa steg

* [Kör U-SQL-skript på din egen arbetsstation för testning och felsökning](data-lake-analytics-data-lake-tools-local-run.md)
* [Felsöka C#-kod i U-SQL-jobb med hjälp av Azure Data Lake-verktyg för Visual Studio-kod](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Använda Azure Data Lake Tools för Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
