---
title: Övervaka en SharePoint-webbplats med Application Insights
description: Börja övervaka ett nytt program med en ny instrumentationsnyckel
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 392c0e0ee46e8acd540d498cbda1d240611d182e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326536"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Övervaka en SharePoint-webbplats med Application Insights

Azure Application Insights övervakar dina appars tillgänglighet, prestanda och användning. Här lär du dig att ställa in det för en SharePoint-webbplats.

> [!NOTE]
> På grund av säkerhets problem kan du inte direkt lägga till skriptet som beskrivs i den här artikeln på dina webb sidor i SharePoint modern UX. Alternativt kan du använda [SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) för att bygga ett anpassat tillägg som du kan använda för att installera Application Insights på dina SharePoint-webbplatser. Mer information finns i [så här skapar du en lösning för SPFx-tillägg med AppInsights installerat från början](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO#how-to-create-a-spfx-extension-solution-with-appinsights-installed-from-scratch) eller [visar exemplet](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO/AppInsightsExtensionSolutionSample). 

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs
Skapa en ny Application Insights-resurs på [Azure Portal](https://portal.azure.com). Välj ASP.NET som programtypen.

![Klicka på Egenskaper, markera nyckeln och tryck på CTRL + C.](./media/sharepoint/001.png)

Fönstret som öppnas är den plats där du kan se prestanda- och användningsdata om din app. Om du vill komma tillbaka till det nästa gång du loggar in på Azure bör du hitta en panel för det på startskärmen. Du kan också klicka på Bläddra för att hitta det.

## <a name="add-the-script-to-your-web-pages"></a>Lägga till skriptet på dina webbsidor

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Infoga skriptet precis före/Head- &lt; &gt; taggen på varje sida som du vill spåra. Om din webbplats har en huvud sida kan du Spara skriptet där. Till exempel, i ett ASP.NET MVC-projekt, placerar du det i View\Shared\_Layout.cshtml

Skriptet innehåller instrumenteringsnyckeln som dirigerar telemetri till din Application Insights-resurs.

### <a name="add-the-code-to-your-site-pages"></a>Lägg till kod på dina webbsidor
#### <a name="on-the-master-page"></a>På huvudsidan
Om du kan redigera webbplatsens huvudsida kan du övervaka alla sidor på webbplatsen.

Spara huvudsidan och redigera den med hjälp av SharePoint Designer eller någon annan redigerare.

![Skärm bild som visar hur du redigerar huvud sidan med Serviceadministratör designer eller någon annan redigerare.](./media/sharepoint/03-master.png)

Lägg till koden precis före </head> taggredigerare. 

![Skärm bild som visar var du kan lägga till koden på din webbplats sida.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Eller på enskilda sidor
Lägg till skript på varje enskild sida som du vill övervaka. 

Infoga en webbdel och bädda in kodfragmentet i den.

![Skärm bild som visar hur du lägger till skriptet för att övervaka en begränsad uppsättning sidor.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Visa data om din app
Distribuera om din app.

Gå tillbaka till ditt programblad i [Azure Portal](https://portal.azure.com).

De första händelserna visas i sökningen. 

![Skärm bild som visar nya data som du kan visa i appen.](./media/sharepoint/09-search.png)

Klicka på Uppdatera efter några sekunder om du väntade dig mer data.

## <a name="capturing-user-id"></a>Hämta användar-Id
Kodfragmentet för standardwebbplatsen hämtar inte användar-ID från SharePoint, men du kan göra detta med en liten ändring.

1. Kopiera din apps instrumentationsnyckeln från Essentials listrutan i Application Insights. 

    ![Skärm bild som visar hur du kopierar appens Instrumentation från den grundläggande List rutan i Application Insights.](./media/sharepoint/02-props.png)

1. Ersätta instrumentationsnyckeln 'XXXX' i kodfragmentet nedan. 
2. Bädda in skriptet i en SharePoint-app i stället för utdraget som du får från portalen.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Nästa steg
* [Webbtest](./monitor-web-app-availability.md) för att övervaka tillgängligheten för webbplatsen.
* [Application Insights](./app-insights-overview.md) för andra typer av appar.

<!--Link references-->

