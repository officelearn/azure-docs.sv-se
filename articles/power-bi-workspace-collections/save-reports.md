---
title: Spara rapporter i Power BI-Arbetsytesamlingar | Microsoft Docs
description: Lär dig hur du kan spara rapporter i Power BI-Arbetsytesamlingar. Detta kräver behörighet för att kunna fungera.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bc3c9f71900abcbba440db259d92df53a4c7a586
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516458"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Spara rapporter i Power BI-Arbetsytesamlingar

Lär dig hur du kan spara rapporter i Power BI-Arbetsytesamlingar. Spara rapporter kräver behörighet för att kunna fungera.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Du kan redigera befintliga rapporter och spara dem i Power BI-Arbetsytesamlingar. Du kan också skapa en ny rapport och spara som en ny rapport för att skapa en.

För att spara en rapport, måste du först skapa en token för den specifika rapporten med rätt omfång:

* Om du vill aktivera spara Report.ReadWrite krävs omfattning
* Om du vill aktivera Spara som, krävs Report.Read och Workspace.Report.Copy områden
* Om du vill aktivera spara och spara som, måste Report.ReadWrite och Workspace.Report.Copy anges

Respektive för att aktivera rätt Spara/Spara som knappar i Arkiv-menyn som du måste ange rätt behörighet i bädda in konfigurationen när du bäddar in rapporten:

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> Ditt åtkomsttoken behöver också lämpliga omfattningar. Mer information finns i [scope](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Bädda in rapporten i redigeringsläge

Vi antar att du vill bädda in en rapport i redigeringsläge i din app att göra så här kommer bara skicka rätt egenskaper i bädda in konfiguration och anropa powerbi.embed(). Ange behörigheter och en viewMode för att kunna se spara och spara som knappar i redigeringsläget. Mer information finns i [bädda in konfigurationsinformation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Till exempel i JavaScript:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

En rapport är nu inbäddad i din app i redigeringsläge.

## <a name="save-report"></a>Spara rapport

Efter att bädda in rapporten i redigeringsläge med rätt token och behörigheter kan spara du rapporten från Arkiv-menyn eller från javascript:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Spara som

```javascript
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Endast när *Spara som* är en ny rapport skapas. När du spara visas arbetsytan fortfarande gamla rapporten i redigeringsläge och inte den nya rapporten. Bädda in den nya rapporten som har skapats. Bädda in den nya rapporten kräver en ny åtkomsttoken som har skapats per rapport.

Du måste sedan att läsa in den nya rapporten efter en *Spara som*. Läser in den nya rapporten liknar bädda in en rapport.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="see-also"></a>Se också

[Komma igång med exemplet](get-started-sample.md)  
[Bädda in en rapport](embed-report.md)  
[Skapa en ny rapport från en datauppsättning](create-report-from-dataset.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)

