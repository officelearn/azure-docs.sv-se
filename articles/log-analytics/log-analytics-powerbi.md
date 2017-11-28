---
title: Importera Azure logganalys data till Power BI | Microsoft Docs
description: "Powerbi är en molnbaserad business analytics-tjänst från Microsoft som innehåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  Den här artikeln beskriver hur du konfigurerar importera logganalys data till Power BI och konfigurera den att uppdateras automatiskt."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: bwren
ms.openlocfilehash: 163ac33af43a8cb7a23742f6336efca5fe7c4b4e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importera Azure logganalys data till Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är en molnbaserad business analytics-tjänst från Microsoft som innehåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  Du kan importera resultaten av en logganalys loggen till en Power BI-dataset så att du kan dra nytta av dess funktioner såsom combing data från olika källor och dela rapporter på webben och mobila enheter.

Den här artikeln innehåller information om importera logganalys data till Power BI och schemaläggning det uppdateras automatiskt.  Olika processer ingår för en [uppgraderas](#upgraded-workspace) och en [äldre](#legacy-workspace) arbetsytan.

## <a name="upgraded-workspace"></a>Uppgraderade arbetsytan


Importera data från en [uppgraderas logganalys-arbetsytan](log-analytics-log-search-upgrade.md) i Power BI kan du skapa en datamängd i Power BI baserat på en logg sökfråga i logganalys.  Frågan körs varje gång dataset uppdateras.  Du kan sedan skapa Power BI-rapporter som använder data från datamängden.  För att skapa datauppsättningen i Power BI, exportera frågan från logganalys till [Power Query (M) språk](https://msdn.microsoft.com/library/mt807488.aspx).  Du sedan används för att skapa en fråga i Power BI Desktop och publicera det till Power BI som en datamängd.  Information för den här processen beskrivs nedan.

![Logganalys till Powerbi](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>Exportera fråga
Börja med att skapa en [loggen Sök](log-analytics-log-search-new.md) som returnerar data från logganalys som du vill fylla i Power BI-datauppsättningen.  Sedan kan du exportera den frågan till [Power Query (M) språk](https://msdn.microsoft.com/library/mt807488.aspx) som kan användas av Power BI Desktop.

1. Skapa loggen sökningen i logganalys att extrahera data för datamängden.
2. Om du använder loggen Sök-portalen klickar du på **Power BI**.  Om du använder Analytics-portalen, Välj **exportera** > **Power BI fråga (M)**.  Båda dessa alternativ exportera frågan till en textfil med namnet **PowerBIQuery.txt**. 

    ![Exportera loggen Sök](media/log-analytics-powerbi/export-logsearch.png) ![Exportera loggen Sök](media/log-analytics-powerbi/export-analytics.png)

3. Öppna filen och kopiera innehållet.

### <a name="import-query-into-power-bi-desktop"></a>Importera frågan till Power BI Desktop
Power BI Desktop är ett skrivbordsprogram som hjälper dig att skapa datauppsättningar och rapporter som kan publiceras till Power BI.  Du kan också använda den för att skapa en fråga med Power Query-språket som exporterats från logganalys. 

1. Installera [Power BI Desktop](https://powerbi.microsoft.com/desktop/) om du inte redan har det och öppna programmet.
2. Välj **hämta Data** > **tom fråga** att öppna en ny fråga.  Välj sedan **avancerade Editor** och klistra in innehållet i den exporterade filen i frågan. Klicka på **Klar**.

    ![Power BI Desktop-fråga](media/log-analytics-powerbi/desktop-new-query.png)

5. Frågan körs, och resultatet visas.  Du kan uppmanas att autentiseringsuppgifter för att ansluta till Azure.  
6. Skriv ett beskrivande namn för frågan.  Standardvärdet är **Fråga1**. Klicka på **stängs och** att lägga till datamängden i rapporten.

    ![Power BI Desktop namn](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Publicera till Powerbi
När du publicerar till Power BI, skapas en datauppsättning och en rapport.  Om du skapar en rapport i Power BI Desktop kommer sedan detta att publiceras med dina data.  Om inte en tom rapport skapas.  Du kan ändra rapporten i Power BI eller skapa ett nytt baserat på datamängden.

8. Skapa en rapport baserad på dina data.  Använd [Power BI Desktop dokumentationen](https://docs.microsoft.com/power-bi/desktop-report-view) om du inte känner till den.  När du är redo att skicka den till Power BI klickar du på **publicera**.  När du uppmanas, Välj ett mål i Power BI-konto.  Om du inte har ett visst mål i åtanke, Använd **Min arbetsyta**.

    ![Publicera Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. När publiceringen är klar klickar du på **öppna i Power BI** att öppna Power BI med ditt nya dataset.


### <a name="configure-scheduled-refresh"></a>Konfigurera schemalagd uppdatering
Dataset som skapats i Power BI har samma data som du såg tidigare i Power BI Desktop.  Du måste uppdatera datauppsättningen regelbundet för att köra frågan igen och fylla det med den senaste informationen från logganalys.  

1. Klicka på arbetsytan där du har överfört rapporten och välj den **datauppsättningar** menyn. Välj snabbmenyn bredvid din ny datauppsättning och välj **inställningar**. Under **datakällans autentiseringsuppgifter** bör du ha ett meddelande att autentiseringsuppgifterna är ogiltiga.  Det beror på att du inte har angett autentiseringsuppgifterna för datamängden som används när den uppdaterar sina data.  Klicka på **redigera autentiseringsuppgifter** och ange autentiseringsuppgifter för åtkomst till logganalys.

    ![Power BI-schema](media/log-analytics-powerbi/powerbi-schedule.png)

5. Under **schemalagd uppdatering** aktiverar alternativet att **hålla data uppdaterade**.  Du kan ändra den **uppdateringsfrekvens** och en eller flera specifika gånger att köra uppdateringen.

    ![Power BI-uppdatering](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>Äldre arbetsytan
När du konfigurerar Power BI med en [äldre logganalys-arbetsytan](log-analytics-powerbi.md), du skapa loggen frågor som exporterar resultaten till motsvarande datamängder i Power BI.  Frågan och exportera fortsätter automatiskt enligt ett schema som du definierar för att hålla datauppsättningen uppdaterade med den senaste informationen som samlas in av logganalys.

![Logganalys till Powerbi](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Power BI-scheman
En *Power BI schema* innehåller en logg sökning som exporterar en uppsättning data från OMS-databasen till en motsvarande dataset i Power BI och ett schema som definierar hur ofta den här sökningen körs om du vill behålla det aktuella datamängden.

Fält i datamängden matchar egenskaperna för de poster som returneras av loggen sökningen.  Om sökningen innehåller olika typer av sedan tas datamängden alla egenskaper från var och en av typerna av inkluderade poster.  

### <a name="connecting-oms-workspace-to-power-bi"></a>Ansluter OMS-arbetsytan till Power BI
Innan du kan exportera från logganalys till Power BI måste du ansluta din OMS-arbetsyta till ditt Power BI-konto med följande procedur.  

1. I OMS-konsolen klickar du på den **inställningar** panelen.
2. Välj **konton**.
3. I den **arbetsyteinformation** Klicka på avsnittet **Anslut till Power BI-konto**.
4. Ange autentiseringsuppgifterna för ditt Power BI-konto.

### <a name="create-a-power-bi-schedule"></a>Skapa ett schema för Power BI
Skapa ett Power BI-schema för varje dataset med följande procedur.

1. I OMS-konsolen klickar du på den **loggen Sök** panelen.
2. Ange en ny fråga eller välj en sparad sökning som returnerar de data som du vill exportera till **Power BI**.  
3. Klicka på den **Power BI** längst upp på sidan för att öppna den **Power BI** dialogrutan.
4. Ange information i följande tabell och klicka på **spara**.

| Egenskap | Beskrivning |
|:--- |:--- |
| Namn |Namn för att identifiera schemat när du visar listan över Power BI-scheman. |
| Sparad sökning |Loggen genomsökas.  Du kan välja den aktuella frågan eller välj en befintlig sparad sökning i listrutan. |
| Schema |Hur ofta du kör den sparade sökningen och exportera till Power BI-datauppsättningen.  Värdet måste vara mellan 15 minuter och 24 timmar. |
| DataSet-namnet |Namnet på dataset i Power BI.  Den kommer att skapas om det inte finns och uppdateras om den finns. |

### <a name="viewing-and-removing-power-bi-schedules"></a>Visa och ta bort scheman för Power BI
Visa listan över befintliga Power BI-scheman med följande procedur.

1. I OMS-konsolen klickar du på den **inställningar** panelen.
2. Välj **Power BI**.

Förutom information om schemat visas status för den senaste synkroniseringen och antalet gånger som schemat har körts under den senaste veckan.  Om fel uppstod i synkroniseringen, klickar du på länken om du vill köra en logg Sök efter poster med information om felet.

Du kan ta bort ett schema genom att klicka på den **X** i den **ta bort kolumnen**.  Du kan inaktivera ett schema genom att välja **av**.  Om du vill ändra ett schema måste du ta bort den och återskapa den med de nya inställningarna.

![Power BI-scheman](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>Exempel genomgång
Följande avsnitt beskriver hur ett exempel för att skapa ett schema för Power BI och använda dess dataset för att skapa en enkel rapport.  I det här exemplet alla prestandadata för en uppsättning datorer som har exporterats till Power BI och sedan ett linjediagram skapas om du vill visa processorbelastning.

#### <a name="create-log-search"></a>Skapa loggen sökning
Vi börjar med att skapa en logg Sök efter de data som vi vill skicka till dataset.  I det här exemplet ska vi använda en fråga som returnerar alla prestandadata för datorer med ett namn som börjar med *srv*.  

![Power BI-scheman](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Skapa Power BI-sökning
Vi klickar på den **Power BI** knappen för att öppna dialogrutan Power BI och ange informationen som krävs.  Vi vill sökningen att köra en gång i timmen och skapa en datamängd som kallas *Contoso Perf*.  Eftersom det redan har Sök öppna som skapar de data som vi vill vi behåller du standardvärdet *Använd aktuell sökfråga* för **sparad sökning**.

![Power BI-sökningen](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Kontrollera Power BI-sökningen
Kontrollera att vi skapade schemat korrekt, vi visa listan över Power BI-sökningar under den **inställningar** panelen i OMS-instrumentpanelen.  Vi Vänta några minuter och uppdatera den här vyn förrän den rapporterar att synkroniseringen har körts.  Du kommer normalt planerar dataset uppdateras automatiskt.

![Power BI-sökningen](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Verifiera datauppsättningen i Power BI
Vi logga in på vår konto på [powerbi.microsoft.com](http://powerbi.microsoft.com/) och bläddrar till **datauppsättningar** längst ned i den vänstra rutan.  Vi kan se att den *Contoso Perf* datauppsättningen visas som anger att vår export har kunnat köras.

![Power BI dataset](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>Skapa rapport baserad på dataset
Vi väljer den **Contoso Perf** dataset och klicka sedan på **resultat** i den **fält** rutan till höger för att visa vilka fält som är en del av denna dataset.  Vi utföra följande åtgärder för att skapa ett linjediagram visar processorbelastning för varje dator.

1. Välj diagramvisualisering som rad.
2. Dra **ObjectName** till **rapporten nivån filter** och kontrollera **Processor**.
3. Dra **CounterName** till **rapporten nivån filter** och kontrollera **% processortid**.
4. Dra **CounterValue** till **värden**.
5. Dra **datorn** till **förklaring**.
6. Dra **TimeGenerated** till **axel**.

Vi kan se att det resulterande linjediagrammet visas med data från vår datauppsättning.

![Power BI-linjediagram](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>Spara rapporten
Vi sparar rapporten genom att klicka på knappen Spara längst upp på skärmen och verifiera att det visas nu i området rapporter i den vänstra rutan.

![Power BI-rapporter](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att skapa frågor som kan exporteras till Power BI.
* Lär dig mer om [Power BI](http://powerbi.microsoft.com) att skapa grafik baserad på logganalys exporter.
