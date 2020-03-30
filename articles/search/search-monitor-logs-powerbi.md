---
title: Visualisera Azure Cognitive Search Loggar och mått med Power BI
description: Visualisera Azure Cognitive Search Loggar och mått med Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650182"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualisera Azure Cognitive Search Loggar och mått med Power BI
[Med Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) kan du lagra åtgärdsloggar och tjänstmått om din söktjänst i ett Azure Storage-konto. Den här sidan innehåller instruktioner för hur du kan visualisera informationen via en Power BI-mallapp. Appen innehåller detaljerade insikter om din söktjänst, inklusive information om mätvärden för sökning, indexering, operationer och tjänster.

Du hittar Power BI Template App **Azure Cognitive Search: Analysera loggar och mått** på Power BI [Apps-marknadsplatsen](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Så här kommer du igång med appen
1. Aktivera diagnostikloggning för söktjänsten:
    1. Skapa eller identifiera ett befintligt [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) där du kan arkivera loggarna
    1. Navigera till din Azure Cognitive Search-tjänst i Azure-portalen
    1. Under avsnittet Övervakning i den vänstra kolumnen väljer du **Diagnostikinställningar**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Välj **+ Lägg till diagnostikinställning**
    1. Kontrollera **Arkiv till ett lagringskonto,** ange din lagringskontoinformation och kontrollera **OperationLogs** och **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Välj **Spara**

1. När loggningen har aktiverats använder du söktjänsten för att börja generera loggar och mått. Det tar upp till en timme innan behållarna visas i Blob-lagring med dessa loggar. Du kommer att se en **insights-logs-operationlogs-behållare** för söktrafikloggar och en **insights-metrics-pt1m-behållare** för mått.

1. Hitta Azure Cognitive Search Power BI-appen på [Power BI Apps-marknadsplatsen](https://appsource.microsoft.com/marketplace/apps) och installera den på en ny arbetsyta eller en befintlig arbetsyta. Appen kallas **Azure Cognitive Search: Analysera loggar och mått**.

1. När du har installerat appen väljer du appen i listan över appar i Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Välj **Anslut** för att ansluta data

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Ange namnet på lagringskontot som innehåller dina loggar och mått. Som standard kommer appen att titta på de senaste 10 dagarnas data, men det här värdet kan ändras med parametern **Dagar.**

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Välj **Nyckel** som autentiseringsmetod och ange din lagringskontonyckel. Välj **Privat** som sekretessnivå. Klicka på logga In för att starta inläsningen.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Vänta tills data har uppdaterats. Detta kan ta lite tid beroende på hur mycket data du har. Du kan se om data fortfarande uppdateras baserat på indikatorn nedan.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. När datauppdateringen har slutförts väljer du **Azure Cognitive Search Report** för att visa rapporten.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Se till att uppdatera sidan när du har öppnat rapporten så att den öppnas med dina data.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Så här ändrar du appparametrarna
Om du vill visualisera data från ett annat lagringskonto eller ändra antalet dagar med data som ska frågas följer du stegen nedan för att ändra parametrarna **Dagar** och **StorageAccount.**

1. Navigera till dina Power BI-appar, hitta din Azure Cognitive Search-app och välj knappen **Redigera app** för att visa arbetsytan.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Välj **Inställningar** i alternativen För datauppsättning.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. När du använder fliken Datauppsättningar ändrar du parametervärdena och väljer **Använd**. Om det finns ett problem med anslutningen uppdaterar du autentiseringsuppgifterna för datakällan på samma sida.

1. Navigera tillbaka till arbetsytan och välj **Uppdatera nu** från datauppsättningsalternativen.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Öppna rapporten om du vill visa uppdaterade data. Du kan också behöva uppdatera rapporten för att visa de senaste data.

## <a name="troubleshooting"></a>Felsökning
Om du upptäcker att du inte kan se dina data följer du de här felsökningsstegen:

1. Öppna rapporten och uppdatera sidan för att se till att du visar de senaste uppgifterna. Det finns ett alternativ i rapporten för att uppdatera data. Välj det här om du vill hämta de senaste uppgifterna.

1. Kontrollera att lagringskontots namn och åtkomstnyckel som du angav är korrekta. Lagringskontonamnet ska motsvara det konto som konfigurerats med söktjänstloggarna.

1. Bekräfta att ditt lagringskonto innehåller **behållarnas insights-logs-operationlogs** och **insights-metrics-pt1m** och att varje behållare har data. Loggarna och måtten kommer att finnas i ett par lager av mappar.

1. Kontrollera om datauppsättningen fortfarande uppdateras. Indikatorn för uppdateringsstatus visas i steg 8 ovan. Om den fortfarande uppdateras väntar du tills uppdateringen är klar för att öppna och uppdatera rapporten.

## <a name="next-steps"></a>Nästa steg
[Läs mer om Azure Cognitive Search](https://docs.microsoft.com/azure/search/)

[Vad är Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Grundläggande begrepp för designers i Power BI-tjänsten](https://docs.microsoft.com/power-bi/service-basic-concepts)