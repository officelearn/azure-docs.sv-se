---
title: Spara rapporter i Power BI arbetsytan samlingar | Microsoft Docs
description: Lär dig mer om att spara rapporter i Power BI arbetsytan samlingar. Detta kräver att rätt behörighet för att fungera korrekt.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: c5512584531c9f5c8a13e9a50161eb6b5a1f8a7b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31411224"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Spara rapporter i Power BI arbetsytan samlingar

Lär dig mer om att spara rapporter i Power BI arbetsytan samlingar. Spara rapporter kräver behörighet för att fungera korrekt.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Du kan redigera befintliga rapporter och spara dem i Power BI arbetsytan samlingar. Du kan också skapa en ny rapport och spara som en ny rapport för att skapa en.

Om du vill spara en rapport måste du först skapa en token för den valda rapporten med rätt scope:

* Om du vill aktivera spara Report.ReadWrite krävs omfattning
* Om du vill aktivera Spara som, krävs Report.Read och Workspace.Report.Copy scope
* Om du vill aktivera spara och spara som, Report.ReadWrite och Workspace.Report.Copy krävs

Respektive för att aktivera höger save/Spara som knappar Arkiv-menyn som du behöver ge behörighet i konfiguration Embed när du bäddar in rapporten:

* modeller. Permissions.ReadWrite
* modeller. Permissions.Copy
* modeller. Permissions.All

> [!NOTE]
> Åtkomst-token måste också lämpliga omfattningar. Mer information finns i [scope](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Bädda in rapporten i redigeringsläge

Anta att du vill bädda in en rapport i redigeringsläge i din app att bara skicka rätt egenskaperna i konfiguration Embed och anropa powerbi.embed(). Ange behörigheter och en viewMode för att visa spara och spara som knappar i redigeringsläget. Mer information finns i [bädda in konfigurationsinformation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Till exempel i JavaScript:

```
   <div id="reportContainer"></div>

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
```

En rapport är inbäddat i din app i redigeringsläge.

## <a name="save-report"></a>Spara rapporten

Du kan spara rapporten från Arkivmenyn eller javascript efter bädda in rapporten i redigeringsläge med rätt token och behörigheter:

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Spara som

```
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Endast när *Spara som* är en ny rapport skapas. När du spara visas arbetsytan fortfarande gamla rapporten i redigeringsläge och inte den nya rapporten. Bädda in den nya rapporten skapades. Bädda in den nya rapporten kräver en ny åtkomsttoken som har skapats per rapport.

Sedan måste du läsa in den nya rapporten efter en *Spara som*. Läser in den nya rapporten liknar bädda in en rapport.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="see-also"></a>Se också

[Komma igång med exemplet](get-started-sample.md)  
[Bädda in en rapport](embed-report.md)  
[Skapa en ny rapport från en datauppsättning](create-report-from-dataset.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)

