---
title: 'Självstudiekurs: Felsöka med Azure Monitor'
description: Lär dig mer om hur Azure Monitor och Log Analytics hjälper dig att övervaka din App Service-webbapp. Azure Monitor maximerar tillgängligheten genom att leverera en omfattande lösning för övervakning av dina miljöer.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: d543a9364311b2cf5f0258fbf9185d27bb1bfb2f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399526"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Självstudiekurs: Felsöka en App Service-app med Azure Monitor

> [!NOTE]
> Azure Monitor-integrering med App Service är i [förhandsversion](https://aka.ms/appsvcblog-azmon).
>

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maximerar tillgängligheten och prestandan för dina program och tjänster genom att leverera en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer.

Den här självstudien visar hur du felsöker en app med [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview). Exempelappen innehåller kod avsedd att förbruka minne och orsaka HTTP 500-fel, så att du kan diagnostisera och åtgärda problemet med Azure Monitor.

När du är klar har du en exempelapp som körs på App Service på Linux integrerat med [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera en webbapp med Azure Monitor
> * Skicka konsolloggar till Log Analytics
> * Använda loggfrågor för att identifiera och felsöka fel i webbappfel

Du kan följa stegen i den här självstudien i macOS, Linux och Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

- [Azure-prenumeration](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Skapa Azure-resurser

Först kör du flera kommandon lokalt för att konfigurera en exempelapp som ska användas med den här självstudien. Kommandona klonar en exempelapp, skapar Azure-resurser, skapar en distributionsanvändare och distribuerar appen till Azure. Du uppmanas att ange lösenordet som en del av skapandet av distributionsanvändaren. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Konfigurera Azure Monitor (förhandsversion)

### <a name="create-a-log-analytics-workspace"></a>Skapa en logganalysarbetsyta

Nu när du har distribuerat exempelappen till Azure App Service konfigurerar du övervakningsfunktionen för att felsöka appen när problem uppstår. Azure Monitor lagrar loggdata på en Log Analytics-arbetsyta. En arbetsyta är en behållare som innehåller data- och konfigurationsinformation.

I det här steget skapar du en Log Analytics-arbetsyta för att konfigurera Azure Monitor med din app.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [För Azure Monitor Log Analytics betalar du för datainmatning och datalagring.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Skapa en diagnostikinställning

Diagnostikinställningar kan användas för att samla in mått för vissa Azure-tjänster i Azure Monitor Logs för analys med andra övervakningsdata med hjälp av loggfrågor. För den här självstudien aktiverar du webbservern och standardutdata-/felloggarna. Se [loggtyper som stöds](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) för en fullständig lista över loggtyper och beskrivningar.

Du kör följande kommandon för att skapa diagnostikinställningar för AppServiceConsoleLogs (standardutdata/fel) och AppServiceHTTPLogs (webbserverloggar). Ersätt _ \<appnamn>_ och _ \<arbetsytans namn>_ med dina värden. 

> [!NOTE]
> De två första `resourceID` kommandona och `workspaceID`är variabler `az monitor diagnostic-settings create` som ska användas i kommandot. Mer information om det här kommandot finns [i Skapa diagnostikinställningar med Azure CLI.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli)
>

```bash

resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'

```

## <a name="troubleshoot-the-app"></a>Felsöka appen

Bläddra till `http://<app-name>.azurewebsites.net`.

Exempelappen ImageConverter konverterar inkluderade `JPG` bilder `PNG`från till . Ett fel har avsiktligt placerats i koden för den här självstudien. Om du väljer tillräckligt många bilder skapar appen ett HTTP 500-fel under bildkonverteringen. Föreställ dig att det här scenariot inte beaktas under utvecklingsfasen. Du använder Azure Monitor för att felsöka felet.

### <a name="verify-the-app-is-works"></a>Verifiera att appen fungerar

Om du vill `Tools` konvertera `Convert to PNG`bilder klickar du på och väljer .

![Klicka på "Verktyg" och välj "Konvertera till PNG"](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Markera de två första `convert`bilderna och klicka på . Detta kommer att konverteras.

![Markera de två första bilderna](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Bryta appen

Nu när du har verifierat appen genom att konvertera två bilder framgångsrikt försöker vi konvertera de fem första bilderna.

![Konvertera de första fem bilderna](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Den här åtgärden `HTTP 500` misslyckas och skapar ett fel som inte har testats under utvecklingen.

![Konverteringen resulterar i ett HTTP 500-fel](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Använda loggfråga för att visa Azure Monitor-loggar

Nu ska vi se vilka loggar som är tillgängliga på log analytics-arbetsytan. 

Klicka på den här [länken för Logganalys för](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) att komma åt din arbetsyta i Azure-portalen.

Välj arbetsytan Log Analytics i Azure-portalen.

### <a name="log-queries"></a>Loggfrågor

Loggfrågor hjälper dig att fullt ut utnyttja värdet på de data som samlas in i Azure Monitor Logs. Du använder loggfrågor för att identifiera loggarna i både AppServiceHTTPLogs och AppServiceConsoleLogs. Mer information om loggfrågor finns i översikten över [loggfrågor.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

### <a name="view-appservicehttplogs-with-log-query"></a>Visa AppServiceHTTPLogs med loggfråga

Nu när vi har kommit åt appen ska vi visa data som `AppServiceHTTPLogs`är associerade med HTTP-begäranden som finns i .

1. Klicka `Logs` från vänsternavigering.

![Logga Anlytics Worksace Loggar](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Sök `appservice` efter och `AppServiceHTTPLogs`dubbelklicka på .

![Logganalysarbetsytatabeller](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klicka på `Run`.

![HTTP-loggar för logganalysarbetsyytjänst](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

Frågan `AppServiceHTTPLogs` returnerar alla begäranden under de senaste 24 timmarna. Kolumnen `ScStatus` innehåller HTTP-status. Om du `HTTP 500` vill diagnostisera `ScStatus` felen begränsar du till 500 och kör frågan, som visas nedan:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Visa AppServiceConsoleLogs med loggfråga

Nu när du har bekräftat HTTP 500s, låt oss ta en titt på standardutdata / fel från appen. Dessa loggar finns i "AppServiceConsoleLogs".

(1) `+` Klicka här om du vill skapa en ny fråga. 

(2) Dubbelklicka `AppServiceConsoleLogs` på `Run`tabellen och klicka på . 

Eftersom konvertering av fem bilder resulterar i serverfel kan du se om `ResultDescription` appen också skriver fel genom att filtrera efter fel, som visas nedan:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

I `ResultDescription` kolumnen visas följande fel:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Gå med i AppServiceHTTPLogs och AppServiceConsoleLogs

Nu när du har identifierat både HTTP 500s och standardfel måste du bekräfta om det finns ett samband mellan dessa meddelanden. Därefter sammanfogar du tabellerna tillsammans baserat `TimeGenerated`på tidsstämpeln .

> [!NOTE]
> En fråga har förberetts för dig som gör följande:
>
> - Filtrerar HTTPLogs för 500-fel
> - Konsolloggar för frågor
> - Ansluter till tabellerna`TimeGenerated`
>

Kör följande fråga:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

I `ResultDescription` kolumnen visas följande fel samtidigt som webbserverfel:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Meddelandet anger att minnet har uttömts på rad 20 `process.php`. Du har nu bekräftat att programmet skapade ett fel under HTTP 500-felet. Låt oss ta en titt på koden för att identifiera problemet.

## <a name="identify-the-error"></a>Identifiera felet

Öppna och titta på `process.php` linje 20 i den lokala katalogen. 

```php
imagepng($imgArray[$x], $filename);
```

Det första `$imgArray[$x]`argumentet, är en variabel som håller alla JPGs (i minnet) som behöver konvertering. Men `imagepng` behöver bara bilden konverteras och inte alla bilder. Förinläsning av bilder är inte nödvändigt och kan orsaka minnesutmattning, vilket leder till HTTP 500s. Låt oss uppdatera koden för att läsa in bilder på begäran för att se om det löser problemet. Därefter kommer du att förbättra koden för att lösa minnesproblemet.

## <a name="fix-the-app"></a>Åtgärda appen

### <a name="update-locally-and-redeploy-the-code"></a>Uppdatera lokalt och distribuera om koden

Du gör följande `process.php` ändringar för att hantera minnesutmattningen:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Spara ändringarna på Git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till `http://<app-name>.azurewebsites.net`. 

Om du konverterar bilder bör http 500-felen inte längre skapas längre.

![PHP-app som körs i Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Ta bort diagnostikinställningen med följande kommando:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Vad du lärt dig:

> [!div class="checklist"]
> * Konfigurerade en webbapp med Azure Monitor
> * Skickade loggar till Log Analytics
> * Använda loggfrågor för att identifiera och felsöka webbappfel

## <a name="next-steps"></a><a name="nextsteps"></a>Nästa steg
* [Frågeloggar med Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)
* [Felsöka Azure App Service i Visual Studio](../troubleshoot-dotnet-visual-studio.md)
* [Analysera apploggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
