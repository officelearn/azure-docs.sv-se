---
title: Bädda in en rapport i Azure Power BI arbetsytan samlingar | Microsoft Docs
description: Lär dig mer om att bädda in en rapport som är i Power BI arbetsytan samlingar till programmet.
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
ms.openlocfilehash: b6fa46b1cf3a251d6116e7de6ef41a9e6d265c29
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Bädda in en rapport i Power BI arbetsytan samlingar

Lär dig mer om att bädda in en rapport som är i Power BI arbetsytan samlingar till programmet.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Vi ska titta på hur du bäddar in en rapport till programmet. Detta under förutsättning att du redan har en rapport som finns i en arbetsyta i din arbetsytesamling. Om du inte gjort det steget ännu, se [Kom igång med Power BI arbetsytan samlingar](get-started.md).

Du kan använda .NET (C#) eller Node.js SDK, tillsammans med JavaScript, enkelt kan skapa ditt program med Power BI arbetsytan samlingar.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Använda åtkomstnycklarna för att använda REST API: er

Du kan överföra snabbtangent som du kan hämta från Azure portal för en given arbetsytesamling för att anropa REST-API. Mer information finns i [Kom igång med Power BI arbetsytan samlingar](get-started.md).

## <a name="get-a-report-id"></a>Hämta en rapport-ID

Varje åtkomsttoken är baserad på en rapport. Du måste hämta det angivna-id för den rapport som du vill bädda in. Detta kan göras baserat på anrop till den [hämta rapporter](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API. Detta returnerar rapport-id och bädda in URL: en. Detta kan göras med Power BI .NET SDK eller anropa REST-API direkt.

### <a name="using-the-power-bi-net-sdk"></a>Med Power BI .NET SDK

När du använder .NET SDK, måste du skapa en token autentiseringsuppgift som baseras på snabbtangent du hämta från Azure-portalen. Detta kräver att du installerar den [Power BI API NuGet-paketet](https://www.nuget.org/profiles/powerbi).

**NuGet-paketet installation**

```
Install-Package Microsoft.PowerBI.Api
```

**C#-kod**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Anrop av REST-API direkt

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Skapa en åtkomst-token

Power BI arbetsytan samlingar Använd bädda in tokens som HMAC signeras JSON Web token. Token som signerats med åtkomst till nyckeln från din Power BI-Arbetsytesamling. Bädda in tokens som standard, som används för att ge skrivskyddad åtkomst till en rapport för att bädda in i ett program. Bädda in token utfärdas för en viss rapport och ska vara associerat med en embed-URL.

Åtkomst-token ska skapas på servern som åtkomstnycklarna används för att logga/kryptera token. Information om hur du skapar en åtkomst-token finns [Authenticating och auktorisera med Power BI arbetsytan samlingar](app-token-flow.md). Du kan också granska den [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metod. Här är ett exempel på hur det skulle se ut med .NET SDK för Power BI.

Du kan använda rapporten-ID som du hämtade tidigare. När inbäddningstoken har skapats använder du sedan snabbtangenten för att generera token som du kan använda ur javascript. Den *PowerBIToken klassen* kräver att du installerar den [Power BI Core NuGut paketet](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet-paketet installation**

```
Install-Package Microsoft.PowerBI.Core
```

**C#-kod**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Lägga till scope behörighet att bädda in token

När du använder Embed token, kanske du vill begränsa användningen av du ger åtkomst till resurser. Därför kan du generera en token med begränsade behörigheter. Mer information finns i [scope](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Bädda in med hjälp av JavaScript

När du har den åtkomst-token och rapport-ID, kan vi bädda in rapporten med hjälp av JavaScript. Detta kräver att du installerar NuGet [Power BI JavaScript paketet](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). EmbedUrl blir https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Du kan använda den [JavaScript rapporten bäddas in provet](https://microsoft.github.io/PowerBI-JavaScript/demo/) att testa funktionen. Det ger även kodexempel för olika åtgärder som är tillgängliga.

**NuGet-paketet installation**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-kod**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Ange storleken på inbäddade element

Rapporten bäddas automatiskt baserat på storleken på dess behållare. Om du vill åsidosätta standardstorleken för det inbäddade objektet lägger du till en CSS-klassen attribut eller infogade-format för bredd och höjd.

## <a name="see-also"></a>Se också

[Komma igång med exemplet](get-started-sample.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript-paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI API NuGet-paketet](https://www.nuget.org/profiles/powerbi)
[Power BI Core NuGut paketet](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[PowerBI CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-nod Git Repo](https://github.com/Microsoft/PowerBI-Node)  

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)
