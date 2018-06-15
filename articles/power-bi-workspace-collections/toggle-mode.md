---
title: Växla mellan att visa och redigera läge för rapporter i Power BI arbetsytan samlingar | Microsoft Docs
description: Lär dig mer om att växla mellan att visa och redigera läge för rapporter i Power BI arbetsytan samlingar.
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
ms.openlocfilehash: 23a8c4f0dd626a623df56de9546258a23d549d1a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409959"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Växla mellan att visa och redigera läge för rapporter i Power BI arbetsytan samlingar

Lär dig mer om att växla mellan att visa och redigera läge för rapporter i Power BI arbetsytan samlingar.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Skapa en åtkomst-token

Du måste skapa en åtkomsttoken som ger dig möjlighet att både visa och redigera en rapport. Om du vill redigera och spara en rapport, måste den **Report.ReadWrite** token behörighet. Mer information finns i [Authenticating och auktorisera i Power BI arbetsytan samlingar](app-token-flow.md).

> [!NOTE]
> På så sätt kan du redigera och spara ändringar i en befintlig rapport. Om du vill också att funktionen stödja **Spara som**, måste du ange ytterligare behörighet. Mer information finns i [scope](app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Bädda in konfiguration

Du måste ange behörigheter och en viewMode för att visa spara knappen i redigeringsläget. Mer information finns i [bädda in konfigurationsinformation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
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

Anger om du vill bädda in rapporten i läget baserat på **viewMode** anges till **modeller. ViewMode.View**.

## <a name="view-mode"></a>Läget för

Du kan använda följande JavaScript för att växla till läget, om du är i redigeringsläge.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Redigeringsläge

Du kan använda följande JavaScript för att växla till redigeringsläget, om du är i läget.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Se också

[Komma igång med exemplet](get-started-sample.md)  
[Bädda in en rapport](embed-report.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-nod Git Repo](https://github.com/Microsoft/PowerBI-Node)  

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)
