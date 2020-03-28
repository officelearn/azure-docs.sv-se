---
title: 'Självstudiekurs: Visualisera avvikelser med batchidentifiering och Power BI'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för avvikelsedetektor och Power BI för att visualisera avvikelser i hela tidsseriedata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 1b486aaf0ce33e31433c2c3d0f7a1ff2c7089132
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402662"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Självstudiekurs: Visualisera avvikelser med batchidentifiering och Power BI

Använd den här självstudien för att hitta avvikelser i en tidsseriedatauppsättning som en batch. Med Power BI-skrivbordet tar du en Excel-fil, förbereder data för API:et för avvikelsedetektor och visualiserar statistiska avvikelser i den.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Använda Power BI Desktop för att importera och omvandla en tidsseriedatauppsättning
> * Integrera Power BI Desktop med API:et för avvikelsedetektor för identifiering av batchavvikelse
> * Visualisera avvikelser som hittas i dina data, inklusive förväntade och sedda värden, och avvikelseidentifieringsgränser.

## <a name="prerequisites"></a>Krav
* En [Azure-prenumeration](https://azure.microsoft.com/free/)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), tillgänglig gratis.
* En Excel-fil (.xlsx) som innehåller datapunkter för tidsserier. Exempeldata för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)
* När du har din <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-prenumeration"  target="_blank">skapar Skapa en <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics-resurs en Text Analytics-resurs i Azure-portalen för att hämta din nyckel och slutpunkt. 
    * Du behöver nyckeln och slutpunkten från den resurs du skapar för att ansluta ditt program till Text Analytics API. Du gör detta senare i snabbstarten.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Läsa in och formatera tidsseriedata

För att komma igång öppnar du Power BI Desktop och läser in tidsseriedata som du har hämtat från förutsättningarna. Den här Excel-filen innehåller en serie utc-tidsstämpel och värdepar (Coordinated Universal Time).  

> [!NOTE]
> Power BI kan använda data från en mängd olika källor, till exempel CSV-filer, SQL-databaser, Azure-bloblagring med mera.  

Klicka på menyfliksområdet **Start** i huvudfönstret för Power BI-skrivbordet. Öppna listrutan **Hämta data** i **menyfliksområdets externa datagrupp** och klicka på **Excel**.

![En bild av knappen "Hämta data" i Power BI](../media/tutorials/power-bi-get-data-button.png)

När dialogrutan visas navigerar du till mappen där du hämtade exemplet .xlsx-fil och markerar den. När dialogrutan **Navigator** visas klickar du på **Blad1**och **redigerar**sedan .

![En bild av datakällan "Navigator" på skärmen "Navigator" i Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI konverterar tidsstämplarna `Date/Time` i den första kolumnen till en datatyp. Dessa tidsstämplar måste konverteras till text för att skickas till API:et för avvikelsedetektor. Om Power Query-redigeraren inte öppnas automatiskt klickar du på **Redigera frågor** på startfliken. 

Klicka på **menyfliksområdet Omforma** i Power Query Editor. Öppna den **nedrullningsbara** menyn Datatyp i gruppen **Valfri kolumn** och välj **Text**.

![En bild av datakällan "Navigator" på skärmen "Navigator" i Power BI](../media/tutorials/data-type-drop-down.png)

När du får ett meddelande om att ändra kolumntypen klickar du på **Ersätt aktuell**. Klicka sedan på **Stäng & Använd** eller **Använd** i menyfliksområdet **Start.** 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Skapa en funktion för att skicka data och formatera svaret

Om du vill formatera och skicka datafilen till API:et för avvikelsedetektor kan du anropa en fråga i tabellen som skapats ovan. Öppna den **nedrullningssvänliga** menyn Ny källa i menyren **För** Start i Power Query-redigeraren och klicka på **Tom fråga**.

Kontrollera att den nya frågan är markerad och klicka sedan på **Avancerad redigerare**. 

![En bild av knappen "Avancerad redigerare" i Power BI](../media/tutorials/advanced-editor-screen.png)

I Den avancerade redigeraren använder du följande Power Query M-kodavsnitt för att extrahera kolumnerna från tabellen och skicka det till API:et. Därefter skapar frågan en tabell från JSON-svaret och returnerar den. Ersätt `apiKey` variabeln med den giltiga API-nyckeln för avvikelsedetektor och `endpoint` med slutpunkten. När du har angett frågan i Den avancerade redigeraren klickar du på **Klar**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
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

Anropa frågan på databladet genom `Sheet1` att välja under **Ange parameter**och klicka på **Anropa**. 

![En bild av knappen "Avancerad redigerare"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Sekretess och autentisering av datakälla

> [!NOTE]
> Var medveten om organisationens policyer för datasekretess och åtkomst. Mer information finns i Sekretessnivåerna för [Power BI Desktop.](https://docs.microsoft.com/power-bi/desktop-privacy-levels)

Du kan få ett varningsmeddelande när du försöker köra frågan eftersom den använder en extern datakälla. 

![En bild som visar en varning som skapats av Power BI](../media/tutorials/blocked-function.png)

Lös detta genom att klicka på **Arkiv**och **Alternativ och inställningar**. Klicka sedan på **Alternativ**. Under **Aktuell fil**väljer du **Sekretess**och **Ignorera sekretessnivåerna och kan eventuellt förbättra prestanda**. 

Dessutom kan du få ett meddelande där du uppmanas att ange hur du vill ansluta till API:et.

![En bild som visar en begäran om att ange åtkomstautentiseringsuppgifter](../media/tutorials/edit-credentials-message.png)

Lös detta genom att klicka på **Redigera autentiseringsuppgifter** i meddelandet. När dialogrutan visas väljer du **Anonym** för att ansluta till API:et anonymt. Klicka sedan på **Anslut**. 

Efteråt klickar du på **Stäng & Använd** i **menyfliksområdet Start** för att tillämpa ändringarna.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualisera API-svaret för avvikelsedetektorn

Börja använda de frågor som skapats ovan på huvudskärmen i Power BI för att visualisera data. Välj först **Linjediagram** i **Visualiseringar**. Lägg sedan till tidsstämpeln från den anropade funktionen i linjediagrammets **Axel**. Högerklicka på den och välj **Tidsstämpel**. 

![Högerklicka på tidsstämpelvärdet](../media/tutorials/timestamp-right-click.png)

Lägg till följande fält från funktionen **Anropad** i diagrammets **fält Värden.** Använd nedanstående skärmdump för att bygga ditt diagram.

    * Värde
    * ÖvreMargins
    * LägreMargins
    * Förväntade värden

![En bild av den nya snabbåtgärdsskärmen](../media/tutorials/chart-settings.png)

När du har lagt till fälten klickar du på diagrammet och ändrar storlek på det för att visa alla datapunkter. Diagrammet liknar skärmbilden nedan:

![En bild av den nya snabbåtgärdsskärmen](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Visa avvikelsedatapunkter

Högerklicka på **Värde** under **frågan Aktiverad funktion**till höger i Power BI-fönstret och klicka på Nytt **snabbmått**. **FIELDS**

![En bild av den nya snabbåtgärdsskärmen](../media/tutorials/new-quick-measure.png)

Välj **Filtrerat värde** som beräkning på skärmen som visas. Ange **basvärde** till `Sum of Value`. Dra `IsAnomaly` sedan från fälten **Anropad funktion** till **Filter**. Välj `True` på den **nedrullningsbara** menyn Filter.

![En bild av den nya snabbåtgärdsskärmen](../media/tutorials/new-quick-measure-2.png)

När du **Ok**har klickat `Value for True` på Ok har du ett fält längst ned i listan över fälten. Högerklicka på den och byt namn på den till **Anomali**. Lägg till det i diagrammets **värden**. Välj sedan **formatverktyget** och ställ in X-axeltypen på **Kategorisk**.

![En bild av den nya snabbåtgärdsskärmen](../media/tutorials/format-x-axis.png)

Använd färger i diagrammet genom att klicka på **formatverktyget** och **datafärgerna**. Diagrammet ska se ut ungefär så här:

![En bild av den nya snabbåtgärdsskärmen](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Identifiering av strömmaravvikelse med Azure Databricks](anomaly-detection-streaming-databricks.md)
