---
title: Skapa en rapport från en data uppsättning – Power BI arbets ytans samlingar
description: Power BI arbets ytans samlings rapporter kan nu skapas från en data uppsättning i ditt eget program.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bcc6044d0f0f5270f81a619e4d1ad71ea35cc170
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427062"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Skapa en ny rapport från en data uppsättning i Power BI samlingar för arbets ytor

Power BI arbets ytans samlings rapporter kan nu skapas från en data uppsättning i ditt eget program.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Autentiseringsmetoden liknar den för inbäddning av en rapport. Den baseras på åtkomsttoken som är speciella för en data uppsättning. Token som används för PowerBI.com utfärdas av Azure Active Directory (AAD). Token för samling med Power BI-arbetsytor utfärdas av ditt eget program.

När du skapar en inbäddad rapport är de token som utfärdas för en speciell data uppsättning. Tokens ska associeras med inbäddnings-URL: en i samma element för att säkerställa att varje har en unik token. För att kunna skapa en inbäddad rapport, *data uppsättning. Read och arbetsyte. Report. Create* -scope måste anges i åtkomsttoken.

## <a name="create-access-token-needed-to-create-new-report"></a>Skapa en åtkomsttoken som krävs för att skapa en ny rapport

Power BI arbets ytans samlingar använder en embed-token, som är HMAC-signerad JSON-webbtoken. Token är signerade med åtkomst nyckeln från din Power BI-arbetsyta samling. Bädda in tokens används som standard för att ge skrivskyddad åtkomst till en rapport som ska bäddas in i ett program. Embed-token utfärdas för en viss rapport och bör associeras med en inbäddnings-URL.

Åtkomsttoken ska skapas på servern som åtkomst nycklar används för att signera/kryptera tokens. Information om hur du skapar en åtkomsttoken finns i [autentisera och auktorisera med Power BI arbets ytans samlingar](app-token-flow.md). Du kan också granska [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) -metoden. Här är ett exempel på hur det kan se ut som att använda .NET SDK för Power BI.

I det här exemplet har vi vårt data uppsättnings-ID som vi vill skapa den nya rapporten på. Vi måste också lägga till omfattningarna för *data uppsättningen. Read och arbetsyte. Report. Create*.

*PowerBIToken-klassen* kräver att du installerar [Power BI Core NuGut-paketet](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Installera NuGet-paket**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#rikt**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Skapa en ny tom rapport

För att du ska kunna skapa en ny rapport ska skapa-konfigurationen tillhandahållas. Detta bör inkludera åtkomsttoken, embedURL och datasetID som vi vill skapa rapporten mot. Detta kräver att du installerar NuGet- [Power BI JavaScript-paketet](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). EmbedUrl kommer bara att https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Du kan använda JavaScript-rapporten för att [bädda in exempel](https://microsoft.github.io/PowerBI-JavaScript/demo/) för att testa funktionen. Det ger också kod exempel för de olika åtgärder som är tillgängliga.

**Installera NuGet-paket**

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

När du anropar *PowerBI. createReport ()* visas en tom arbets yta i redigerings läget i *div* -elementet.

![Ny tom rapport](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Spara nya rapporter

Rapporten skapas inte förrän du anropar **Spara som** -åtgärden. Detta kan göras från menyn Arkiv eller från Java Script.

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
> En ny rapport skapas endast efter att **sparats som** kallas. När du har sparat kommer arbets ytan fortfarande att visa data uppsättningen i redigerings läge och inte i rapporten. Du måste läsa in den nya rapporten på samma sätt som andra rapporter.

![Arkiv-menyn – Spara som](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Läs in den nya rapporten

För att kunna interagera med den nya rapporten måste du bädda in den på samma sätt som programmet bäddar in en vanlig rapport, vilket innebär att en ny token måste utfärdas specifikt för den nya rapporten och sedan anropa metoden embed.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatisera Spara och Läs in en ny rapport med hjälp av händelsen "Sparad"

För att automatisera processen för "Spara som" och sedan läsa in den nya rapporten kan du använda händelsen "Sparad". Den här händelsen utlöses när Spara-åtgärden har slutförts och returnerar ett JSON-objekt som innehåller det nya Rapportid, rapport namnet, den gamla Rapportid (om det fanns en) och om åtgärden sparades eller sparades.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Om du vill automatisera processen kan du lyssna på händelsen "Sparad", ta den nya Rapportid, skapa den nya token och bädda in den nya rapporten med den.

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
[Power BI Core NuGut-paket](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript-paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)
