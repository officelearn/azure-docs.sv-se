---
title: Växla mellan visnings-och redigerings läge för rapporter
description: Lär dig hur du växlar mellan visnings-och redigerings läge för dina rapporter i Power BI arbets ytans samlingar.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b2696560b5d5013fe337b51ec61cbfac9e512610
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357672"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Växla mellan visnings-och redigerings läge för rapporter i Power BI samlingar för arbets ytor

Lär dig hur du växlar mellan visnings-och redigerings läge för dina rapporter i Power BI arbets ytans samlingar.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Skapa en åtkomsttoken

Du måste skapa en åtkomsttoken som ger dig möjlighet att både Visa och redigera en rapport. Om du vill redigera och spara en rapport måste du ha behörigheten **Report. readwrite** -token. Mer information finns i [autentisera och auktorisera i Power BI arbets ytans samlingar](app-token-flow.md).

> [!NOTE]
> På så sätt kan du redigera och spara ändringar i en befintlig rapport. Om du också vill ha en funktion som stöder **Spara som**måste du ange ytterligare behörigheter. Mer information finns i [omfattningar](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Bädda in konfiguration

Du måste ange behörigheter och en viewMode för att kunna se Spara-knappen i redigerings läge. Mer information finns i [bädda in konfigurations information](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Till exempel i Java Script:

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
    </script>
```

Det innebär att du bäddar in rapporten i visnings läge baserat på att **viewMode** anges till **modeller. ViewMode. View**.

## <a name="view-mode"></a>Visnings läge

Du kan använda följande Java Script för att växla till visnings läge, om du är i redigerings läge.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Redigeringsläge

Du kan använda följande Java Script för att växla till redigerings läget, om du är i visnings läge.

```javascript
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
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp git-lagrings platsen](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-nod git lagrings platsen](https://github.com/Microsoft/PowerBI-Node)  

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)
