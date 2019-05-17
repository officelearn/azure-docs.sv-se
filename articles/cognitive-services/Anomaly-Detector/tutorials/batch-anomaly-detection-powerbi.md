---
title: Visualisera avvikelser som batch-identifiering och Power BI
titlesuffix: Azure Cognitive Services
description: Använd Avvikelseidentifiering detektor API och Power BI för att visualisera avvikelser i tidsseriedata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 48257782bd71f6145adc7107a5fe70b4cc4f8305
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790511"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Självstudier: Visualisera avvikelser som batch-identifiering och Power BI

Använd den här självstudiekursen för att identifiera avvikelser i en time series datauppsättningen som en batch. Med Power BI desktop, ska du ta en Excel-fil, förbereda data för API: T för Avvikelseidentifiering detektor och visualisera statistiska avvikelser i hela den.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Använd Power BI Desktop för att importera och ändra en time series-datauppsättning
> * Integrera Power BI Desktop med Avvikelseidentifiering detektor API: et för batch-avvikelseidentifiering
> * Visualisera avvikelser i dina data, inklusive förväntade och sågs värden och gränser för identifiering av avvikelser.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)är tillgängligt utan kostnad.
* En excel-fil (.xlsx) som innehåller tidsseriedata pekar. Exempeldata för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Läsa in och formatera time series-data

Kom igång genom att öppna Power BI Desktop och läsa in time series-data som du laddade ned från kraven. Den här excel-filen innehåller en serie Coordinated Universal Time (UTC) tidsstämpel och värdepar.  

> [!NOTE]
> Powerbi kan använda data från en mängd olika källor, till exempel CSV-filer, SQL-databaser, Azure blob-lagring och mer.  

I huvudfönstret i Power BI Desktop klickar du på den **Start** menyfliksområdet. I den **externa data** på menyfliken, öppna den **hämta Data** nedrullningsbara menyn och klicka på **Excel**.

![En bild av knappen ”Hämta Data” i Power BI](../media/tutorials/power-bi-get-data-button.png)

När dialogrutan visas, navigera till mappen där du laddade ned exempel .xlsx-fil och markera den. Efter den **Navigator** dialogrutan visas klickar du på **Blad1**, och sedan **redigera**.

![En bild av skärmen datakälla ”Navigator” i Power BI](../media/tutorials/navigator-dialog-box.png)

Powerbi konverterar tidsstämplar i den första kolumnen till en `Date/Time` datatyp. Dessa tidsstämplar måste konverteras till text för att skickas till API: T för Avvikelseidentifiering detektor. Om Power Query editor inte automatiskt öppnas, klickar du på **redigera frågor** på Start-fliken. 

Klicka på den **transformera** menyfliksområdet i Power Query Editor. I den **valfri kolumn** grupp, öppna den **datatyp:** nedrullningsbara menyn och välj **Text**.

![En bild av skärmen datakälla ”Navigator” i Power BI](../media/tutorials/data-type-drop-down.png)

När du får ett meddelande om hur du ändrar kolumntypen, klickar du på **Ersätt aktuella**. Klicka därefter på **Stäng och tillämpa** eller **tillämpa** i den **Start** menyfliksområdet. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Skapa en funktion för att skicka data och formatera svaret

Om du vill formatera och skicka filen till API: T för Avvikelseidentifiering detektor kan anropa du en fråga i tabellen som skapades ovan. I Power Query Editor från den **Start** menyfliksområdet, öppna den **ny källa** nedrullningsbara menyn och klicka på **tom fråga**.

Kontrollera att den nya frågan är markerad och klicka sedan på **Avancerad redigerare**. 

![En bild av knappen ”Avancerad redigerare” i Power BI](../media/tutorials/advanced-editor-screen.png)

Använda Power Query M följande kodavsnitt inom Avancerad redigerare att extrahera kolumner från tabellen och skicka den till API: et. Frågan kommer därefter skapa en tabell från JSON-svar och lämna tillbaka. Ersätt den `apiKey` variabeln med din giltig Avvikelseidentifiering detektor API-nyckel och `endpoint` med slutpunkten. När du har angett frågan i Avancerad redigerare, klickar du på **klar**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect,
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Anropa frågan på databladet genom att välja `Sheet1` nedan **ange parametern**, och klicka på **Invoke**. 

![En bild av knappen ”Avancerad redigerare”](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Datasekretess för källa och autentisering

> [!NOTE]
> Tänk på organisationens principer för datasekretess och åtkomst. Se [sekretessnivåer i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) för mer information.

Du får ett varningsmeddelande när du försöker köra frågan eftersom den använder en extern datakälla. 

![En bild som visar en varning som skapats av Power BI](../media/tutorials/blocked-function.png)

Lös problemet genom att klicka på **filen**, och **alternativ och inställningar**. Klicka sedan på **alternativ**. Nedan **aktuell fil**väljer **sekretess**, och **Ignorera sekretessnivåerna och förbättra eventuellt prestandan**. 

Dessutom kan du få ett meddelande där du uppmanas att ange hur du vill ansluta till API: et.

![En bild på en begäran om att ange autentiseringsuppgifter](../media/tutorials/edit-credentials-message.png)

Lös problemet genom att klicka på **redigera autentiseringsuppgifter** i meddelandet. När dialogrutan öppnas, Välj **anonym** ansluter anonymt-API: et. Klicka sedan på **Anslut**. 

Klicka därefter på **Stäng och tillämpa** i den **Start** menyfliksområdet för att tillämpa ändringarna.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualisera Avvikelseidentifiering detektor API-svar

Börja använda de frågor som skapades ovan för att visualisera data på huvudskärmen i Power BI. Välj först **linjediagram** i **visualiseringar**. Lägg sedan till tidsstämpeln från anropad funktion till linjediagrammet **axel**. Högerklicka på den och välj **tidsstämpel**. 

![Att högerklicka på tidsstämpelvärdet](../media/tutorials/timestamp-right-click.png)

Lägg till följande fält från den **anropas funktionen** i diagrammet **värden** fält. Använd den skärmbilden för att skapa diagrammet nedan.

    * Value
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![En bild av skärmen nytt snabbmått](../media/tutorials/chart-settings.png)

När du lägger till fält, klicka på diagrammet och ändra storlek på den för att visa alla datapunkter. Ditt diagram ser ut ungefär som på skärmbilden nedan:

![En bild av skärmen nytt snabbmått](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Visa datapunkter för avvikelseidentifiering

På höger sida av Power BI-fönstret under den **fält** fönstret högerklickar du på **värdet** under den **anropas funktionsfrågan**, och klicka på **nya quick Måttet**.

![En bild av skärmen nytt snabbmått](../media/tutorials/new-quick-measure.png)

På skärmen som visas, markerar **filtrerat värde** som beräkningen. Ange **basera värdet** till `Sum of Value`. Dra sedan `IsAnomaly` från den **anropas funktionen** fält till **Filter**. Välj `True` från den **Filter** nedrullningsbara menyn.

![En bild av skärmen nytt snabbmått](../media/tutorials/new-quick-measure-2.png)

När du klickar på **Ok**, får du en `Value for True` fältet längst ned i listan över dina fält. Högerklicka på den och Byt namn på den till **Avvikelseidentifiering**. Lägga till den i diagrammets **värden**. Välj sedan den **Format** för och ange vilket x-axeln **Kategoriskt**.

![En bild av skärmen nytt snabbmått](../media/tutorials/format-x-axis.png)

Använda färger som ditt diagram genom att klicka på den **Format** verktyget och **datafärger**. Diagrammet bör se ut ungefär så här:

![En bild av skärmen nytt snabbmått](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Avvikelseidentifiering med Azure Databricks för direktuppspelning](anomaly-detection-streaming-databricks.md)
