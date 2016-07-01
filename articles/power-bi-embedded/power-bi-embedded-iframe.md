<properties
   pageTitle="Microsoft Power BI Embedded Preview – Bädda in en Power BI-rapport med en IFrame"
   description="Microsoft Power BI Embedded Preview – Viktig kod för att integrera en rapport i din app, hur man autentiserar med en Power BI-inbäddad apptoken och hur man genererar rapporter"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/16/2016"
   ms.author="derrickv"/>

# Bädda in en Power BI-rapport med  IFrame
I den här artikeln redovisas viktig kod för att använda **Power BI Embedded** REST-API, apptoken, en IFrame och vissa JavaScript för att integrera eller bädda in en rapport i din app.

I [Komma igång med Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md) får du veta mer om hur du konfigurerar en **arbetsytesamling** så att den rymmer en eller flera **arbetsytor** för ditt rapportinnehåll. I [exemplet i Komma igång med Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md) importerar du sedan en rapport till en **arbetsyta**.

Den här artikeln visar stegen för att bädda in en rapport i din app. Om du vill följa den här artikeln bör du hämta exemplet [Integrera en rapport med en IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) på GitHub. Det här exemplet är en enkel ASP.NET-webbformulärsapp som är avsedd för att illustrera viktig C#- och JavaScript-kod som du behöver för att integrera en rapport. Ett mer avancerat exempel som använder designmönstret Model-View-Controller (MVC) för att integrera en rapport finns i [Exempel på instrumentpanel för webbapp](http://go.microsoft.com/fwlink/?LinkId=761493) på GitHub.

Vi börjar med att beskriva hur du integrerar en **Power BI Embedded**-rapport i din app.

Nedan följer stegen för att integrera en rapport.

- Steg 1: [Hämta en rapport från en arbetsyta](#GetReport). I det här steget använder du ett apptokenflöde för att få en åtkomsttoken att anropa REST-åtgärden [Hämta rapporter](https://msdn.microsoft.com/library/mt711510.aspx). När du har hämtat en rapport från listan **Hämta rapporter** bäddar du in rapporten i en app med ett **IFrame**-element.
- Steg 2: [Bädda in en rapport i en app](#EmbedReport). I det här steget du använder en inbäddningstoken för en rapport, några JavaScript och en IFrame för att integrera, eller bädda in, en rapport till ett webbprogram.

Om du vill köra exemplet för att se hur du integrerar en rapport kan du hämta exemplet [Integrera en rapport med en IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) på GitHub och konfigurera tre Web.Config-inställningar:

- **AccessKey**: En **AccessKey** används för att generera en JSON-webbtoken (JWT) som används för att hämta rapporter och bädda in en rapport. Mer information om hur du får en **AccessKey** finns i [Komma igång med Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md).
- **WorkspaceName**: Mer information om hur du får ett **WorkspaceName** finns i [Komma igång med Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md).
- **WorkspaceId**: Mer information om hur du får tillgång till ett **WorkspaceId** finns i [Komma igång med Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md).

I nästa avsnitt visas den kod som du behöver integrera i en rapport.

<a name="GetReport"/>
## Hämta en rapport från en arbetsyta

Om du vill integrera en rapport i en app, behöver du **ID** och **embedUrl** för rapporten. För att hämta **ID** och **embedUrl** för rapporten anropar du REST-åtgärden [Hämta rapporter](https://msdn.microsoft.com/library/mt711510.aspx) och väljer en rapport i JSON-listan. I [Bädda in en rapport i en app](#EmbedReport) använder du ett **ID** och **embedUrl** för rapporten för att bädda in den i din app.

### Hämta JSON-svar för rapporter
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

För att anropa REST-åtgärden [Hämta rapporter](https://msdn.microsoft.com/library/mt711510.aspx) använder du en apptoken. Mer information om apptokenflödet finns i [Om apptokenflöde i Power BI Embedded](power-bi-embedded-app-token-flow.md). Följande kod beskriver hur du hämtar en JSON-lista över rapporter. Mer information om att bädda in en rapport finns i [Bädda in en rapport i en app](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## Bädda in en rapport i en app

Innan du kan bädda in en rapport i din app behöver du en inbäddningstoken för rapporter. Denna token liknar en apptoken som används för att anropa REST-åtgärder för **Power BI-Embedded** men genereras för en rapportresurs snarare än en REST-resurs. Nedan följer den kod som krävs för att hämta en apptoken för en rapport. Mer information om att använda rapportapptoken finns i [Bädda in rapporten i din app](#EmbedReportJS).

<a name="EmbedReportToken"/>
### Hämta en apptoken för en rapport

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Bädda in rapporten i din app

För att bädda in en **Power BI**-rapport i din app måste du använda en IFrame och JavaScript-kod. Följande är ett exempel med IFrame och JavaScript-kod för att bädda in en rapport. Mer information om att visa all exempelkod för inbäddning av en rapport finns i exemplet [Integrera en rapport med en IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) på GitHub.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
När du har en rapport som är inbäddad i din app kan du filtrera rapporten. I nästa avsnitt visas hur du filtrerar en rapport med en URL-syntax.

## Filtrera en rapport

Du kan filtrera en inbäddad rapport med en URL-syntax. Om du vill göra detta måste du lägga till en frågesträngsparameter till din iFrame-src-URL med filtret angivet. Du kan välja **Filtrera efter ett värde** eller **Dölj filterfönstret**.


**Filtrera efter ett värde**

Om du vill filtrera efter ett värde använder du en **$filter**-frågesyntax med en **eq**-operatör på följande sätt:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Du kan till exempel filtrera på butikskedjan ”Lindseys”. URL:ens filterdel skulle se ut så här:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} får inte innehålla blanksteg eller specialtecken. {fieldValue} accepterar ett enskilt kategoriskt värde.

**Dölj filterfönstret**

För att dölja **filterfönstret** lägger du till **filterPaneEnabled** i rapportens frågesträng enligt följande:

```
&filterPaneEnabled=false
```

## Sammanfattning

I den här artikeln har du fått lära dig mer om koden för att integrera en **Power BI**-rapport i din app. Hämta de här exemplen på GitHub för att komma igång snabbt med att integrera en rapport i en app:

- [Integrera en rapport med ett IFrame-exempel](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Exempel på instrumentpanel för webbapp](http://go.microsoft.com/fwlink/?LinkId=761493)

## Se även
- [Komma igång med Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)
- [Komma igång med exemplet](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [Hämta rapporter](https://msdn.microsoft.com/library/mt711510.aspx)



<!--HONumber=Jun16_HO2-->


