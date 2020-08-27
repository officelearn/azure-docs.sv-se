---
title: Visualisera Azure Kognitiv sökning-loggar och mät värden med Power BI
description: Visualisera Azure Kognitiv sökning-loggar och mät värden med Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 2caf4c66cdbb11ed42c4131ef1584c372ec10332
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935117"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualisera Azure Kognitiv sökning-loggar och mät värden med Power BI
Med [Azure kognitiv sökning](./search-what-is-azure-search.md) kan du lagra åtgärds loggar och tjänste mått för din Sök tjänst i ett Azure Storage konto. Den här sidan innehåller instruktioner för hur du kan visualisera informationen via en Power BI Template-app. Appen innehåller detaljerade insikter om din Sök tjänst, inklusive information om Sök-, indexerings-, drift-och tjänst mått.

Du hittar Power BI Template app Azure- **kognitiv sökning: Analysera loggar och mått** på [Power BI-appar på marknaden](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Komma igång med appen

1. Aktivera mått-och resurs loggning för Sök tjänsten:

    1. Skapa eller identifiera ett befintligt [Azure Storage-konto](../storage/common/storage-account-create.md) där du kan arkivera loggarna
    1. Gå till Azure Kognitiv sökning-tjänsten i Azure Portal
    1. Under avsnittet övervakning i den vänstra kolumnen väljer du **diagnostikinställningar**

        ![Skärm bild som visar hur du väljer diagnostikinställningar i avsnittet övervakning i Azure Kognitiv sökning-tjänsten.](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Välj **+ Lägg till diagnostisk inställning**
    1. Markera **arkivera till ett lagrings konto**, ange din lagrings konto information och kontrol lera **OperationLogs** och **AllMetrics**

        ![Skärm bild som visar hur du gör val för mått och resurs loggning på sidan diagnostiska inställningar.](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Välj **Spara**

1. När loggning har Aktiver ATS använder du Sök tjänsten för att börja generera loggar och mått. Det tar upp till en timme innan behållarna visas i Blob Storage med dessa loggar. Du kommer att se en **Insights-logs-operationlogs** -behållare för Sök trafik loggar och en **insikts-mått – pt1m** container för mått.

1. Hitta Azure Kognitiv sökning Power BI-appen i [Power BI Apps Marketplace](https://appsource.microsoft.com/marketplace/apps) och installera den i en ny arbets yta eller en befintlig arbets yta. Appen kallas **Azure kognitiv sökning: Analysera loggar och mått**.

1. När du har installerat appen väljer du appen i listan med appar i Power BI.

    ![Skärm bild som visar Azure Kognitiv sökning-appen att välja från listan över appar.](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Välj **Anslut** för att ansluta dina data

    ![Skärm bild som visar hur du ansluter till dina data i Azure Kognitiv sökning-appen.](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Skriv in namnet på det lagrings konto som innehåller dina loggar och mät värden. Som standard kommer appen att titta på de senaste 10 dagarna av data, men det här värdet kan ändras med parametern **Days** .

    ![Skärm bild som visar hur du matar in lagrings kontots namn och antalet dagar att fråga på sidan Anslut till Azure Kognitiv sökning.](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Välj **nyckel** som autentiseringsmetod och ange din lagrings konto nyckel. Välj **privat** som sekretess nivå. Klicka på logga In för att starta inläsningen.

    ![Skärm bild som visar hur du anger autentiseringsmetoden, konto nyckeln och sekretess nivån på sidan Anslut till Azure Kognitiv sökning.](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Vänta tills data har uppdaterats. Detta kan ta lite tid beroende på hur mycket data du har. Du kan se om data fortfarande uppdateras baserat på nedanstående indikator.

    ![Skärm bild som visar hur du läser informationen på sidan för data uppdatering.](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. När data uppdateringen har slutförts väljer du **Azure kognitiv sökning rapporten** för att visa rapporten.

    ![Skärm bild som visar hur du väljer Azure Kognitiv sökning-rapporten på sidan för data uppdatering.](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Se till att uppdatera sidan när du har öppnat rapporten så att den öppnas med dina data.

    ![Skärm bild av Azure Kognitiv sökning Power BI-rapporten.](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Ändra app-parametrarna
Om du vill visualisera data från ett annat lagrings konto eller ändra antalet dagar som data ska frågas, följer du stegen nedan för att ändra **dagar** och **StorageAccount** -parametrar.

1. Navigera till Power BI appar, hitta din Azure Kognitiv sökning-app och välj knappen **Redigera app** för att Visa arbets ytan.

    ![Skärm bild som visar hur du väljer knappen Redigera app för Azure Kognitiv sökning-appen.](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Välj **Inställningar** från alternativ för data uppsättning.

    ![Skärm bild som visar hur du väljer Inställningar från alternativ för att söka efter data i Azure Congnitive.](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. På fliken Data uppsättningar ändrar du parameter värden och väljer **tillämpa**. Om det uppstår ett problem med anslutningen uppdaterar du autentiseringsuppgifterna för data källan på samma sida.

1. Gå tillbaka till arbets ytan och välj **Uppdatera nu** från data uppsättnings alternativen.

    ![Skärm bild som visar hur du väljer Uppdatera nu från Azure Congnitive Sök data uppsättnings alternativ.](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Öppna rapporten om du vill visa uppdaterade data. Du kan också behöva uppdatera rapporten för att visa den senaste informationen.

## <a name="troubleshooting"></a>Felsökning
Om du upptäcker att du inte kan se dina data följer du dessa fel söknings steg:

1. Öppna rapporten och uppdatera sidan för att se till att du visar den senaste informationen. Det finns ett alternativ i rapporten för att uppdatera data. Välj det här alternativet för att hämta den senaste informationen.

1. Se till att det lagrings konto namn och åtkomst nyckel du angav är korrekt. Namnet på lagrings kontot ska motsvara det konto som kon figurer ATS med Sök tjänst loggarna.

1. Bekräfta att ditt lagrings konto innehåller behållar **insikter-logs-operationlogs** och **Insights-Metrics-pt1m** och att varje behållare har data. Loggarna och måtten kommer att finnas inom ett par lager mappar.

1. Kontrol lera om data uppsättningen fortfarande uppdateras. Indikatorn för uppdaterings status visas i steg 8 ovan. Om den fortfarande uppdateras väntar du tills uppdateringen är klar för att öppna och uppdatera rapporten.

## <a name="next-steps"></a>Nästa steg
[Läs mer om Azure Kognitiv sökning](./index.yml)

[Vad är Power BI?](/power-bi/fundamentals/power-bi-overview)

[Grundläggande begrepp för designers i Power BI-tjänsten](/power-bi/service-basic-concepts)