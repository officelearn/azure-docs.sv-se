---
title: Kom igång med ett exempel
description: I den här artikeln lär dig att Power BI arbetsytan samlingar get igång exempel.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: 4daa675092bed1788025e7e3f7e7e11d02ef1671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Komma igång med Power BI arbetsytan samlingar exemplet

Med **Microsoft Power BI arbetsytan samlingar**, kan du integrera Power BI-rapporter direkt i ditt webb- eller mobila program. I den här artikeln lär dig mer om att den **Power BI arbetsytan samlingar** get igång exempel.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Innan vi gå längre något du vill spara följande resurser: de hjälpa dig när du integrerar för Power BI-rapporter i exempelappen och egna appar.

* [Exempelwebbapp för arbetsytan](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI arbetsytan samlingar API-referens](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472) (tillgänglig via NuGet)
* [JavaScript-rapporten bäddas in exempel](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Innan du kan konfigurera och kör Power BI arbetsytan samlingar Kom igång exempel, måste du skapa minst en **Arbetsytesamling** i din Azure-prenumeration. Mer information om hur du skapar en **Arbetsytesamling** i Azure-portalen finns [komma igång med Power BI arbetsytan samlingar](get-started.md).

## <a name="configure-the-sample-app"></a>Konfigurera sample-appen

Låt oss gå igenom ställa in din utvecklingsmiljö i Visual Studio för att få åtkomst till komponenter som behövs för att köra sample-appen.

1. Hämta och packa upp den [Power BI arbetsytan samlingar – integrera en rapport i en webbapp](http://go.microsoft.com/fwlink/?LinkId=761493) på GitHub.
2. Öppna **PowerBI embedded.sln** i Visual Studio. Du kan behöva köra den **uppdateringspaketet** i NuGet Package Manager-konsolen för att uppdatera de paket som används i den här lösningen.
3. Skapa lösningen.
4. Kör den **ProvisionSample** konsolapp. I konsolen exempelapp etablera en arbetsyta och importerar en PBIX-fil.
5. Att etablera en ny **arbetsytan**, Välj alternativ 1, **samling management**, och välj sedan alternativet 6, **etablera en ny arbetsyta**
6. Så här importerar du en ny **rapporten**, Välj alternativ 2, **rapportera management**, och välj sedan alternativ 3, **importera PBIX Desktop-fil i en arbetsyta**.

7. Ange din **Arbetsytesamling** namn, och **åtkomstnyckeln**. Du kan hämta dessa i den **Azure-portalen**. Mer information om hur du hämtar dina **åtkomstnyckeln**, finns [visa åtkomstnycklar för Power BI API](get-started.md#view-power-bi-api-access-keys) i komma igång med Microsoft Power BI Embedded.

    ![Snabbtangenter i Azure-portalen](media/get-started-sample/azure-portal.png)
8. Kopiera och spara den nyligen skapade **arbetsyte-ID** för senare användning i den här artikeln. Efter den **arbetsyte-ID** är skapat kan du kan hitta den **Azure-portalen**.

    ![Arbetsyte-ID i Azure-portalen](media/get-started-sample/workspace-id.png)
9. Du importerar en PBIX-fil i din **arbetsytan**, Välj alternativet **6. Importera PBIX Desktop-fil till en befintlig arbetsyta**. Om du inte har en PBIX-fil praktiska, kan du hämta den [exempel på detaljhandelsanalys PBIX](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Om du uppmanas ange ett eget namn för din **Dataset**.

Du bör se ett svar som:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Om PBIX-fil innehåller alla anslutningar direkt fråga, kör du alternativet 7 för att uppdatera anslutningssträngar.

Nu har du en PBIX för Power BI-rapport som importeras till din **arbetsytan**. Nu ska vi titta på hur du kör den **Power BI arbetsytan samlingar** Kom igång exempelwebbapp.

## <a name="run-the-sample-web-app"></a>Kör exempelwebbappen

Web app exempel är ett exempelprogram som återger rapporter som importeras till din **arbetsytan**. Här är hur du konfigurerar web app exemplet.

1. I den **PowerBI-inbäddade** Visual Studio-lösning högerklickar du på den **EmbedSample** webbprogram och välj **Ställ in som Startprojekt**.
2. I **web.config**i den **EmbedSample** webbprogrammet, redigera den **appSettings**: **AccessKey**, **WorkspaceCollection** namn, och **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Kör den **EmbedSample** webbprogram.

När du kör den **EmbedSample** webbprogrammet, vänstra navigeringsfönstret ska innehålla en **rapporter** menyn. Om du vill visa rapporten du importerat Expandera **rapporter**, och klicka på en rapport. Om du har importerat den [exempel på detaljhandelsanalys PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), exempelwebbapp skulle se ut så här:

![Exempel vänster navigeringsfält inom exempelprogrammet](media/get-started-sample/sample-left-nav.png)

När du klickar på en rapport i **EmbedSample** webbprogram bör se ut ungefär detta:

![Exempelrapporten som visas i programmet](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Utforska exempelkoden

Den **Microsoft Power BI arbetsytan samlingar** exempel är en exempelwebbapp som visar hur du integrerar **Power BI** rapporter i din app. Den använder en designmönstret Model-View-Controller (MVC) för att demonstrera bästa praxis. Det här avsnittet beskrivs delar av exempelkod som du kan utforska inom den **PowerBI-inbäddade** web app lösning. Mönstret Model-View-Controller (MVC) separerar modellering av domänen, presentationen och åtgärder baserat på användarindata i tre separata klasser: modell, visa och kontroll. Läs mer om MVC i [Lär dig mer om ASP.NET](http://www.asp.net/mvc).

Den **Microsoft Power BI arbetsytan samlingar** exempelkod avgränsas på följande sätt. Varje avsnitt innehåller filnamnet i PowerBI embedded.sln lösningen så att du lätt kan hitta koden i exemplet.

> [!NOTE]
> Det här avsnittet är en sammanfattning av exempelkod som visar hur koden har skrivits. Om du vill visa hela exemplet, Läs in PowerBI embedded.sln lösningen i Visual Studio.

### <a name="model"></a>Modell

Exemplet har en **ReportsViewModel** och **ReportViewModel**.

**ReportsViewModel.cs**: representerar Power BI-rapporter.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: representerar en Power BI-rapport.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Anslutningssträng

Anslutningssträngen måste ha följande format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Med hjälp av gemensamma server och databas attribut misslyckas. Till exempel: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Visa

Den **visa** hanterar visningen av Power BI **rapporter** och en Power BI **rapporten**.

**Reports.cshtml**: iterera över **Model.Reports** att skapa en **ActionLink**. Den **ActionLink** består på följande sätt:

| En del | Beskrivning |
| --- | --- |
| Namn |Namnet på rapporten. |
| QueryString |En länk till rapporten-ID. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Ange den **Model.AccessToken**, och Lambda-uttrycket för **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Kontrollenhet

**DashboardController.cs**: skapar ett PowerBIClient skicka en **apptoken**. En JSON-Webbtoken (JWT) skapas från den **signeringsnyckeln** att hämta den **autentiseringsuppgifter**. Den **autentiseringsuppgifter** används för att skapa en instans av **PowerBIClient**. När du har en instans av **PowerBIClient**, kan du anropa GetReports() och GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Uppgiften<ActionResult> rapporten (sträng reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Integrera en rapport i din app

När du har en **rapporten**, du använder en **IFrame** att bädda in Power BI **rapporten**. Här är ett kodstycke från powerbi.js i den **Microsoft Power BI arbetsytan samlingar** exempel.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<igrame style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Filtrera rapporter som är inbäddad i ditt program

Du kan filtrera en inbäddad rapport med en URL-syntax. Om du vill göra detta måste du lägga till en **$filter** frågesträngparametern med en **eq** operatorn för att din iFrame-src-url med filtret angivet. Här är frågesyntaxen filter:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} får inte innehålla blanksteg eller specialtecken. {fieldValue} accepterar ett enskilt kategoriskt värde.  

## <a name="see-also"></a>Se också

[Vanliga scenarier för Microsoft Power BI-Arbetsytesamling](scenarios.md)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Bädda in en rapport](embed-report.md)  
[Skapa en ny rapport från en datauppsättning](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)
