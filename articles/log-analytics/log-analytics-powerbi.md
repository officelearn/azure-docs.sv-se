---
title: Exportera logganalys data till Power BI | Microsoft Docs
description: "Powerbi är en molnbaserad business analytics-tjänst från Microsoft som innehåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  Logganalys exportera kontinuerligt data från OMS-databasen till Power BI så att du kan utnyttja dess visualiseringar och verktyg för analys.  Den här artikeln beskriver hur du konfigurerar frågor i logganalys som exporterar till Power BI automatiskt med jämna mellanrum."
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
ms.date: 07/24/2017
ms.author: bwren
ms.openlocfilehash: 271747e25f319c76195ec643025d24c6b7cdc9c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="export-log-analytics-data-to-power-bi"></a>Exportera logganalys data till Power BI

>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), och sedan på den här processen för att exportera logganalys data till Power BI fungerar inte längre.  Alla befintliga scheman som du skapade innan du uppgraderar inaktiveras. Dessutom visas inte längre möjligheten att aktivera Power BI export-funktionen i inställningarna under förhandsgranskningsfunktioner, som den här funktionen fullt ut i uppgraderade arbetsytor. 
>
> Efter uppgraderingen, Azure Log Analytics använder samma plattform som Application Insights och du använder samma process för att exportera logganalys frågor till Power BI som [processen för att exportera Application Insights frågor till Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Du kan antingen exportera frågan med hjälp av konsolen Analytics enligt beskrivningen i artikeln eller kan du välja den **Power BI** längst upp på skärmen i loggen Sök-portalen.
>
> Användare behöver åtkomst till resursen i arbetsytan i Azure för att använda Power BI export capabilitiy i uppgraderade arbetsytor. Utan åtkomst till ser användarna ett felmeddelande när du importerar frågan till Power BI desktop som de inte har åtkomst.



[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är en molnbaserad business analytics-tjänst från Microsoft som innehåller omfattande visualiseringar och rapporter för analys av olika datauppsättningar.  Logganalys exportera automatiskt data från OMS-databasen till Power BI så att du kan utnyttja dess visualiseringar och verktyg för analys.

När du konfigurerar Power BI med logganalys skapa log-frågor som exporterar resultaten till motsvarande datamängder i Power BI.  Frågan och exportera fortsätter automatiskt enligt ett schema som du definierar för att hålla datauppsättningen uppdaterade med den senaste informationen som samlas in av logganalys.

![Logganalys till Powerbi](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Power BI-scheman
En *Power BI schema* innehåller en logg sökning som exporterar en uppsättning data från OMS-databasen till en motsvarande dataset i Power BI och ett schema som definierar hur ofta den här sökningen körs om du vill behålla det aktuella datamängden.

Fält i datamängden matchar egenskaperna för de poster som returneras av loggen sökningen.  Om sökningen innehåller olika typer av sedan tas datamängden alla egenskaper från var och en av typerna av inkluderade poster.  

> [!NOTE]
> Det är en bra idé att använda en logg sökfråga som returnerar rådata och utföra konsolidering med kommandon som [mått](log-analytics-search-reference.md#measure).  Du kan utföra någon aggregering och beräkningar i Power BI från rådata.
>
>

## <a name="connecting-oms-workspace-to-power-bi"></a>Ansluter OMS-arbetsytan till Power BI
Innan du kan exportera från logganalys till Power BI måste du ansluta din OMS-arbetsyta till ditt Power BI-konto med följande procedur.  

1. I OMS-konsolen klickar du på den **inställningar** panelen.
2. Välj **konton**.
3. I den **arbetsyteinformation** Klicka på avsnittet **Anslut till Power BI-konto**.
4. Ange autentiseringsuppgifterna för ditt Power BI-konto.

## <a name="create-a-power-bi-schedule"></a>Skapa ett schema för Power BI
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

## <a name="viewing-and-removing-power-bi-schedules"></a>Visa och ta bort scheman för Power BI
Visa listan över befintliga Power BI-scheman med följande procedur.

1. I OMS-konsolen klickar du på den **inställningar** panelen.
2. Välj **Power BI**.

Förutom information om schemat visas status för den senaste synkroniseringen och antalet gånger som schemat har körts under den senaste veckan.  Om fel uppstod i synkroniseringen, klickar du på länken om du vill köra en logg Sök efter poster med information om felet.

Du kan ta bort ett schema genom att klicka på den **X** i den **ta bort kolumnen**.  Du kan inaktivera ett schema genom att välja **av**.  Om du vill ändra ett schema måste du ta bort den och återskapa den med de nya inställningarna.

![Power BI-scheman](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>Exempel genomgång
Följande avsnitt beskriver hur ett exempel för att skapa ett schema för Power BI och använda dess dataset för att skapa en enkel rapport.  I det här exemplet alla prestandadata för en uppsättning datorer som har exporterats till Power BI och sedan ett linjediagram skapas om du vill visa processorbelastning.

### <a name="create-log-search"></a>Skapa loggen sökning
Vi börjar med att skapa en logg Sök efter de data som vi vill skicka till dataset.  I det här exemplet ska vi använda en fråga som returnerar alla prestandadata för datorer med ett namn som börjar med *srv*.  

![Power BI-scheman](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Skapa Power BI-sökning
Vi klickar på den **Power BI** knappen för att öppna dialogrutan Power BI och ange informationen som krävs.  Vi vill sökningen att köra en gång i timmen och skapa en datamängd som kallas *Contoso Perf*.  Eftersom det redan har Sök öppna som skapar de data som vi vill vi behåller du standardvärdet *Använd aktuell sökfråga* för **sparad sökning**.

![Power BI-sökningen](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Kontrollera Power BI-sökningen
Kontrollera att vi skapade schemat korrekt, vi visa listan över Power BI-sökningar under den **inställningar** panelen i OMS-instrumentpanelen.  Vi Vänta några minuter och uppdatera den här vyn förrän den rapporterar att synkroniseringen har körts.

![Power BI-sökningen](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Verifiera datauppsättningen i Power BI
Vi logga in på vår konto på [powerbi.microsoft.com](http://powerbi.microsoft.com/) och bläddrar till **datauppsättningar** längst ned i den vänstra rutan.  Vi kan se att den *Contoso Perf* datauppsättningen visas som anger att vår export har kunnat köras.

![Power BI dataset](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Skapa rapport baserad på dataset
Vi väljer den **Contoso Perf** dataset och klicka sedan på **resultat** i den **fält** rutan till höger för att visa vilka fält som är en del av denna dataset.  Vi utföra följande åtgärder för att skapa ett linjediagram visar processorbelastning för varje dator.

1. Välj diagramvisualisering som rad.
2. Dra **ObjectName** till **rapporten nivån filter** och kontrollera **Processor**.
3. Dra **CounterName** till **rapporten nivån filter** och kontrollera **% processortid**.
4. Dra **CounterValue** till **värden**.
5. Dra **datorn** till **förklaring**.
6. Dra **TimeGenerated** till **axel**.

Vi kan se att det resulterande linjediagrammet visas med data från vår datauppsättning.

![Power BI-linjediagram](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>Spara rapporten
Vi sparar rapporten genom att klicka på knappen Spara längst upp på skärmen och verifiera att det visas nu i området rapporter i den vänstra rutan.

![Power BI-rapporter](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att skapa frågor som kan exporteras till Power BI.
* Lär dig mer om [Power BI](http://powerbi.microsoft.com) att skapa grafik baserad på logganalys exporter.
