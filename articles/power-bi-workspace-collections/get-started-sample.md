---
title: Kom igång med ett exempel
description: I den här artikeln lär dig till Power BI-Arbetsytesamlingar get igång exemplet.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 36566d72de1505cd5689aaad737d7948b80801ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110556"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Kom igång med Power BI-Arbetsytesamlingar exempel

Med **Microsoft Power BI-Arbetsytesamlingar**, kan du integrera Power BI-rapporter direkt i dina webb- och mobilprogram. I den här artikeln har vi introducerar du den **Power BI-Arbetsytesamlingar** igång get-exemplet.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Innan vi går vidare, som du vill spara följande resurser: De hjälper dig att när de integrerar Power BI-rapporter i exempelappen och dina egna appar för.

* [Exempelwebbappen för arbetsytan](https://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI-arbetsyta samlingar API-referens](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK](https://go.microsoft.com/fwlink/?LinkId=746472) (tillgängligt via NuGet)
* [Rapport för JavaScript-Inbäddningsexempel](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Innan du kan konfigurera och kör Power BI-Arbetsytesamlingar få igång exemplet, måste du skapa minst en **Arbetsytesamling** i Azure-prenumerationen. Lär dig hur du skapar en **Arbetsytesamling** i Azure-portalen finns i [komma igång med Power BI-Arbetsytesamlingar](get-started.md).

## <a name="configure-the-sample-app"></a>Konfigurera exempelappen

Låt oss gå genom att ställa in din utvecklingsmiljö i Visual Studio för att få åtkomst till de komponenter som behövs för att köra exempelappen.

1. Hämta och packa upp den [Power BI-Arbetsytesamlingar – integrera en rapport i en webbapp](https://go.microsoft.com/fwlink/?LinkId=761493) i GitHub.
2. Öppna **PowerBI-embedded.sln** i Visual Studio. Du kan behöva köra den **uppdateringspaketet** i NuGet Package Manager-konsolen för att uppdatera de paket som används i den här lösningen.
3. Skapa lösningen.
4. Kör den **ProvisionSample** konsolapp. I konsolen exempelapp etablerar en arbetsyta och importera en PBIX-fil.
5. Att etablera en ny **arbetsytan**, Välj alternativ 1, **hantering av**, och välj sedan alternativet 6, **etablera en ny arbetsyta**
6. Importera en ny **rapporten**, Välj alternativ 2, **rapportera management**, och välj sedan alternativ 3, **importera PBIX Desktop-fil till en arbetsyta**.

7. Ange din **Arbetsytesamling** namn, och **åtkomstnyckel**. Du kan hämta dessa i den **Azure-portalen**. Mer information om hur du hämtar din **åtkomstnyckeln**, finns i [visa åtkomstnycklar för Power BI API](get-started.md#view-power-bi-api-access-keys) på komma igång med Microsoft Power BI Embedded.

    ![Åtkomstnycklar i Azure-portalen](media/get-started-sample/azure-portal.png)
8. Kopiera och spara den nyligen skapade **arbetsyte-ID** att använda senare i den här artikeln. Efter den **arbetsyte-ID** är skapat kan du hittar den på **Azure-portalen**.

    ![Arbetsyte-ID på Azure portal](media/get-started-sample/workspace-id.png)
9. Importera en PBIX-fil i din **arbetsytan**, Välj alternativet **6. Importera PBIX Desktop-fil till en befintlig arbetsyta**. Om du inte har en PBIX filen till hands kan du hämta den [Retail Analysis-exemplet PBIX](https://go.microsoft.com/fwlink/?LinkID=780547).
10. Om du uppmanas, anger du ett eget namn för din **datauppsättning**.

Du bör se ett svar som:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Om din PBIX-fil innehåller alla directquery-anslutningar, kör du alternativet 7 om du vill uppdatera anslutningssträngarna.

Nu har du en rapport för Power BI PBIX som importeras till din **arbetsytan**. Nu ska vi titta på hur du kör den **Power BI-Arbetsytesamlingar** få igång exempelwebbappen.

## <a name="run-the-sample-web-app"></a>Kör exempelwebbappen

Web app-exemplet är ett exempelprogram som återger rapporter som importeras till din **arbetsytan**. Här är att konfigurera web app-exemplet.

1. I den **Power BI-inbäddad** Visual Studio-lösning högerklickar du på den **EmbedSample** webbprogrammet och välj **Ställ in som Startprojekt**.
2. I **web.config**i den **EmbedSample** webbprogram, redigera den **appSettings**: **AccessKey**, **WorkspaceCollection** namn, och **WorkspaceId**.

    ```xml
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Kör den **EmbedSample** webbprogram.

När du kör den **EmbedSample** webbprogrammet, den vänstra navigeringspanelen ska innehålla en **rapporter** menyn. Om du vill visa rapporten du importerat, expandera **rapporter**, och klicka på en rapport. Om du har importerat den [Retail Analysis-exemplet PBIX](https://go.microsoft.com/fwlink/?LinkID=780547), exempelwebbappen skulle se ut så här:

![Exemplet vänster navigeringsfält i exempelprogrammet](media/get-started-sample/sample-left-nav.png)

När du klickar på en rapport i **EmbedSample** webbprogram bör se ut ungefär detta:

![Exempelrapport som visar i programmet](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Utforska exempelkoden

Den **Microsoft Power BI-Arbetsytesamlingar** exemplet är en exempelwebbapp som visar hur du integrerar **Power BI** rapporter i din app. Använder en designmönstret Model-View-Controller (MVC) för att demonstrera bästa praxis. Det här avsnittet beskrivs delar av exempelkoden som du kan utforska inom den **Power BI-inbäddad** web applösning. Mönstret Model-View-Controller (MVC) avgränsar modellering av domänen, presentation och åtgärder baserat på användarindata i tre separata klasser: Modell, visa och kontroll. Läs mer om MVC i [Lär dig mer om ASP.NET](https://www.asp.net/mvc).

Den **Microsoft Power BI-Arbetsytesamlingar** exempelkoden avgränsas på följande sätt. Varje avsnitt innehåller namnet på filen i Power BI-embedded.sln lösningen så att du lätt kan hitta koden i exemplet.

> [!NOTE]
> Det här avsnittet är en sammanfattning av den exempelkod som visar hur koden har skrivits. Läsa in Power BI-embedded.sln-lösningen i Visual Studio om du vill visa hela exemplet.

### <a name="model"></a>Modell

Exemplet har en **ReportsViewModel** och **ReportViewModel**.

**ReportsViewModel.cs**: Representerar Power BI-rapporter.

```csharp
public class ReportsViewModel
{
    public List<Report> Reports { get; set; }
}
```

**ReportViewModel.cs**: Representerar en Power BI-rapport.

```csharp
public class ReportViewModel
{
    public IReport Report { get; set; }

    public string AccessToken { get; set; }
}
```

### <a name="connection-string"></a>Anslutningssträng

Anslutningssträngen måste ha följande format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Med hjälp av vanliga servern och databasen attribut misslyckas. Exempel: Server=TCP:myserver.Database.Windows.NET,1433;Database=MyDatabase,

### <a name="view"></a>Visa

Den **visa** hanterar visningen av Power BI **rapporter** och en Power BI **rapporten**.

**Reports.cshtml**: Iterera över **Model.Reports** att skapa en **ActionLink**. Den **ActionLink** består på följande sätt:

| En del | Beskrivning |
| --- | --- |
| Rubrik |Namnet på rapporten. |
| QueryString |En länk till rapporten-ID. |
```cshtml
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
```
Report.cshtml: Ange den **Model.AccessToken**, och Lambda-uttrycket för **PowerBIReportFor**.

```cshtml
@model ReportViewModel

...

<div class="side-body padding-top">
    @Html.PowerBIAccessToken(Model.AccessToken)
    @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
</div>
```

### <a name="controller"></a>Kontrollenhet

**DashboardController.cs**: Skapar en PowerBIClient skicka en **apptoken**. En JSON Web Token (JWT) genereras från den **signeringsnyckeln** att hämta den **autentiseringsuppgifter**. Den **autentiseringsuppgifter** används för att skapa en instans av **PowerBIClient**. När du har en instans av **PowerBIClient**, kan du anropa GetReports() och GetReportsAsync().


CreatePowerBIClient()

```csharp
private IPowerBIClient CreatePowerBIClient()
{
    var credentials = new TokenCredentials(accessKey, "AppKey");
    var client = new PowerBIClient(credentials)
    {
        BaseUri = new Uri(apiUrl)
    };

    return client;
}
```

ActionResult Reports()

```csharp
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
```

Uppgiften<ActionResult> rapporten (sträng reportId)

```csharp
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
```

### <a name="integrate-a-report-into-your-app"></a>Integrera en rapport i din app

När du har en **rapporten**, du använder en **IFrame** att bädda in Power BI **rapporten**. Här är ett kodstycke från powerbi.js i den **Microsoft Power BI-Arbetsytesamlingar** exemplet.

```javascript
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Filtrera rapporter som är inbäddad i ditt program

Du kan filtrera en inbäddad rapport med en URL-syntax. Om du vill göra detta måste du lägga till en **$filter** frågesträngparametern med en **eq** operatör till din iFrame-src-url med filtret angivet. Här är frågesyntaxen filter:

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

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)
