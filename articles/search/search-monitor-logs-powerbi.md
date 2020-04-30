---
title: Visualisera Azure Kognitiv sökning-loggar och mät värden med Power BI
description: Visualisera Azure Kognitiv sökning-loggar och mät värden med Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e01ac332e61f51909ff1617f1716cd719b67c319
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127874"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualisera Azure Kognitiv sökning-loggar och mät värden med Power BI
Med [Azure kognitiv sökning](https://docs.microsoft.com/azure/search/search-what-is-azure-search) kan du lagra åtgärds loggar och tjänste mått för din Sök tjänst i ett Azure Storage konto. Den här sidan innehåller instruktioner för hur du kan visualisera informationen via en Power BI Template-app. Appen innehåller detaljerade insikter om din Sök tjänst, inklusive information om Sök-, indexerings-, drift-och tjänst mått.

Du hittar Power BI Template app Azure- **kognitiv sökning: Analysera loggar och mått** på [Power BI-appar på marknaden](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Komma igång med appen

1. Aktivera mått-och resurs loggning för Sök tjänsten:

    1. Skapa eller identifiera ett befintligt [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) där du kan arkivera loggarna
    1. Gå till Azure Kognitiv sökning-tjänsten i Azure Portal
    1. Under avsnittet övervakning i den vänstra kolumnen väljer du **diagnostikinställningar**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Välj **+ Lägg till diagnostisk inställning**
    1. Markera **arkivera till ett lagrings konto**, ange din lagrings konto information och kontrol lera **OperationLogs** och **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Välj **Spara**

1. När loggning har Aktiver ATS använder du Sök tjänsten för att börja generera loggar och mått. Det tar upp till en timme innan behållarna visas i Blob Storage med dessa loggar. Du kommer att se en **Insights-logs-operationlogs** -behållare för Sök trafik loggar och en **insikts-mått – pt1m** container för mått.

1. Hitta Azure Kognitiv sökning Power BI-appen i [Power BI Apps Marketplace](https://appsource.microsoft.com/marketplace/apps) och installera den i en ny arbets yta eller en befintlig arbets yta. Appen kallas **Azure kognitiv sökning: Analysera loggar och mått**.

1. När du har installerat appen väljer du appen i listan med appar i Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Välj **Anslut** för att ansluta dina data

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Skriv in namnet på det lagrings konto som innehåller dina loggar och mät värden. Som standard kommer appen att titta på de senaste 10 dagarna av data, men det här värdet kan ändras med parametern **Days** .

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Välj **nyckel** som autentiseringsmetod och ange din lagrings konto nyckel. Välj **privat** som sekretess nivå. Klicka på logga In för att starta inläsningen.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Vänta tills data har uppdaterats. Detta kan ta lite tid beroende på hur mycket data du har. Du kan se om data fortfarande uppdateras baserat på nedanstående indikator.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. När data uppdateringen har slutförts väljer du **Azure kognitiv sökning rapporten** för att visa rapporten.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Se till att uppdatera sidan när du har öppnat rapporten så att den öppnas med dina data.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Ändra app-parametrarna
Om du vill visualisera data från ett annat lagrings konto eller ändra antalet dagar som data ska frågas, följer du stegen nedan för att ändra **dagar** och **StorageAccount** -parametrar.

1. Navigera till Power BI appar, hitta din Azure Kognitiv sökning-app och välj knappen **Redigera app** för att Visa arbets ytan.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Välj **Inställningar** från alternativ för data uppsättning.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. På fliken Data uppsättningar ändrar du parameter värden och väljer **tillämpa**. Om det uppstår ett problem med anslutningen uppdaterar du autentiseringsuppgifterna för data källan på samma sida.

1. Gå tillbaka till arbets ytan och välj **Uppdatera nu** från data uppsättnings alternativen.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Öppna rapporten om du vill visa uppdaterade data. Du kan också behöva uppdatera rapporten för att visa den senaste informationen.

## <a name="troubleshooting"></a>Felsökning
Om du upptäcker att du inte kan se dina data följer du dessa fel söknings steg:

1. Öppna rapporten och uppdatera sidan för att se till att du visar den senaste informationen. Det finns ett alternativ i rapporten för att uppdatera data. Välj det här alternativet för att hämta den senaste informationen.

1. Se till att det lagrings konto namn och åtkomst nyckel du angav är korrekt. Namnet på lagrings kontot ska motsvara det konto som kon figurer ATS med Sök tjänst loggarna.

1. Bekräfta att ditt lagrings konto innehåller behållar **insikter-logs-operationlogs** och **Insights-Metrics-pt1m** och att varje behållare har data. Loggarna och måtten kommer att finnas inom ett par lager mappar.

1. Kontrol lera om data uppsättningen fortfarande uppdateras. Indikatorn för uppdaterings status visas i steg 8 ovan. Om den fortfarande uppdateras väntar du tills uppdateringen är klar för att öppna och uppdatera rapporten.

## <a name="next-steps"></a>Nästa steg
[Läs mer om Azure Kognitiv sökning](https://docs.microsoft.com/azure/search/)

[Vad är Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Grundläggande begrepp för designers i Power BI-tjänsten](https://docs.microsoft.com/power-bi/service-basic-concepts)