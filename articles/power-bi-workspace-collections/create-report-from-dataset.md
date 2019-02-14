---
title: Skapa en ny rapport från en datauppsättning i Power BI-Arbetsytesamlingar | Microsoft Docs
description: Power BI-Arbetsytesamling rapporter kan nu skapas från en datauppsättning i ditt eget program.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 78834f1f12d2c748cb885e437496f2acf11b69ee
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232795"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Skapa en ny rapport från en datauppsättning i Power BI-Arbetsytesamlingar

Power BI-Arbetsytesamling rapporter kan nu skapas från en datauppsättning i ditt eget program.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Autentiseringsmetoden som liknar den för att bädda in en rapport. Den baseras på åtkomst-token som är specifika för en datauppsättning. Token som används för PowerBI.com utfärdas av Azure Active Directory (AAD). Power BI-Arbetsytesamling token utfärdas av ditt eget program.

När du skapar en inbäddad rapport, är de token som utfärdas för en specifik datauppsättning. Token ska associeras med bädda in Webbadressen på samma element att se till att var och en har en unik token. För att skapa en inbäddad rapport *Dataset.Read och Workspace.Report.Create* omfång måste anges i åtkomst-token.

## <a name="create-access-token-needed-to-create-new-report"></a>Skapa åtkomst-token som behövs för att skapa ny rapport

Power BI-Arbetsytesamlingar använda en inbäddad token, vilket är HMAC signerade JSON Web token. Token är signerade med åtkomstnyckeln från din Power BI-Arbetsytesamling. Inbäddningstokens som standard, som används för att ge skrivskyddad åtkomst till en rapport för att bädda in i ett program. Bädda in token utfärdas för en viss rapport och ska associeras med en inbäddad URL.

Åtkomsttoken ska skapas på servern som åtkomstnycklar används för att logga/kryptera token. Information om hur du skapar en åtkomst-token finns i [autentisering och auktorisering med Power BI-Arbetsytesamlingar](app-token-flow.md). Du kan också granska den [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metod. Här är ett exempel på hur detta skulle se ut med .NET SDK för Power BI.

I det här exemplet har vi vår datauppsättnings-ID som vi vill skapa den nya rapporten på. Vi måste också lägga till scope för *Dataset.Read och Workspace.Report.Create*.

Den *PowerBIToken klass* kräver att du installerar den [Power BI Core NuGut paketet](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Installation av NuGet-paketet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#-kod**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Skapa en ny tom rapport

Skapa konfiguration ska anges för att skapa en ny rapport. Detta ska inkludera åtkomsttoken, embedURL och datasetID som vi vill skapa rapporten mot. Detta kräver att du installerar nuget [Power BI JavaScript-paketet](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). EmbedUrl blir https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Du kan använda den [JavaScript Rapportinbäddningsexemplet](https://microsoft.github.io/PowerBI-JavaScript/demo/) att testa funktionerna. Du får också kodexempel för olika åtgärder som är tillgängliga.

**Installation av NuGet-paketet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-kod**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

Anropa *powerbi.createReport()* gör en tom arbetsyta i redigeringsläge visas inom den *div* element.

![Ny tom rapport](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Spara nya rapporter

Rapporten skapas inte förrän du anropar den **Spara som** igen. Detta kan göras från Arkiv-menyn eller JavaScript.

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
> En ny rapport skapas först efter **Spara som** anropas. När du har sparat visas fortfarande datauppsättningen i redigeringsläge och inte i rapporten i arbetsytan. Du måste du uppdatera den nya rapporten precis som andra rapporter.

![Arkiv-menyn - Spara som](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Läsa in den nya rapporten

För att kunna interagera med den nya rapporten som du behöver för att bädda in den på samma sätt som programmet bäddar in en vanlig rapport, vilket innebär att en ny token utfärdas specifikt för den nya rapporten och sedan anropa metoden Bädda in.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatisera spara och läsa in av en ny rapport med händelsen ”sparade”

För att automatisera processen med ”Spara som” och sedan läsa in den nya rapporten du använda händelsens ”sparade”. Den här händelsen utlöses när spara åtgärden har slutförts och den returnerar ett Json-objekt som innehåller den nya reportId, rapportnamn, gamla reportId (om det finns) och om åtgärden Spara eller spara.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

För att automatisera processen kan du lyssna på händelsen ”sparade”, ta nya reportId, skapa en ny token och bädda in den nya rapporten med den.

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>Se också

[Komma igång med exemplet](get-started-sample.md)  
[Spara rapporter](save-reports.md)  
[Bädda in en rapport](embed-report.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI Core NuGut paketet](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript-paketet](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)
