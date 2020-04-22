---
title: Aktivera diagnostikloggning
description: Lär dig hur du aktiverar diagnostikloggning och lägger till instrumentering i ditt program samt hur du kommer åt informationen som loggas av Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769991"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Aktivera diagnostikloggning för appar i Azure App Service
## <a name="overview"></a>Översikt
Azure tillhandahåller inbyggd diagnostik för att hjälpa till med felsökning av en [App Service-app](overview.md). I den här artikeln får du lära dig hur du aktiverar diagnostikloggning och lägger till instrumentering i ditt program samt hur du kommer åt informationen som loggas av Azure.

I den här artikeln används [Azure-portalen](https://portal.azure.com) och Azure CLI för att arbeta med diagnostikloggar. Information om hur du arbetar med diagnostikloggar med Visual Studio finns [i Felsöka Azure i Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Förutom loggningsinstruktionerna i den här artikeln finns det nya, integrerade loggningsfunktioner med Azure Monitoring. Du hittar mer om den här funktionen i avsnittet [Skicka loggar till Azure Monitor (förhandsversion).](#send-logs-to-azure-monitor-preview) 
>
>

|Typ|Plattform|Location|Beskrivning|
|-|-|-|-|
| Programloggning | Windows, Linux | App Service-filsystem och/eller Azure Storage-blobbar | Loggar meddelanden som genereras av din programkod. Meddelandena kan genereras av det webbramverk du väljer, eller från din programkod direkt med hjälp av standardloggningsmönstret för ditt språk. Varje meddelande tilldelas en av följande kategorier: **Kritisk**, **Fel**, **Varning**, **Info**, **Felsökning**och **Spårning**. Du kan välja hur utförlig du vill att loggningen ska vara genom att ange allvarlighetsgrad när du aktiverar programloggning.|
| Webbserverloggning| Windows | App Service-filsystem eller Azure Storage-blobbar| Rå HTTP-begärandedata i [W3C-utökat loggfilformat](/windows/desktop/Http/w3c-logging). Varje loggmeddelande innehåller data som HTTP-metoden, resurs-URI, klient-IP, klientport, användaragent, svarskod och så vidare. |
| Detaljerade felmeddelanden| Windows | App Service filsystem | Kopior av *.htm* .htm-felsidorna som skulle ha skickats till klientwebbläsaren. Av säkerhetsskäl bör detaljerade felsidor inte skickas till klienter i produktion, men App Service kan spara felsidan varje gång ett programfel inträffar som har HTTP-kod 400 eller mer. Sidan kan innehålla information som kan hjälpa till att avgöra varför servern returnerar felkoden. |
| Spårning av misslyckade begäranden | Windows | App Service filsystem | Detaljerad spårningsinformation om misslyckade begäranden, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och den tid det tar i varje komponent. Det är användbart om du vill förbättra webbplatsens prestanda eller isolera ett visst HTTP-fel. En mapp genereras för varje misslyckad begäran, som innehåller XML-loggfilen, och XSL-formatmallen som loggfilen ska visas med. |
| Loggning av distribution | Windows, Linux | App Service filsystem | Loggar för när du publicerar innehåll i en app. Distributionsloggning sker automatiskt och det finns inga konfigurerbara inställningar för distributionsloggning. Det hjälper dig att avgöra varför en distribution misslyckades. Om du till exempel använder ett [anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)kan du använda distributionsloggning för att avgöra varför skriptet misslyckas. |

> [!NOTE]
> App Service tillhandahåller ett dedikerat, interaktivt diagnostikverktyg som hjälper dig att felsöka ditt program. Mer information finns i [diagnostiköversikten för Azure App Service](overview-diagnostics.md).
>
> Dessutom kan du använda andra Azure-tjänster för att förbättra loggnings- och övervakningsfunktionerna i din app, till exempel [Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Aktivera programloggning (Windows)

> [!NOTE]
> Programloggning för blob-lagring kan bara använda lagringskonton i samma region som App-tjänsten

Om du vill aktivera programloggning för Windows-appar i [Azure-portalen](https://portal.azure.com)navigerar du till din app och väljer **App Service-loggar**.

Välj **På** för antingen **programloggning (filsystem)** eller **programloggning (Blob)** eller båda. 

**Alternativet Filsystem** är för tillfällig felsökning och stängs av på 12 timmar. **Blob-alternativet** är för långsiktig loggning och behöver en blob-lagringsbehållare för att skriva loggar till.  **Blob-alternativet** innehåller också ytterligare information i loggmeddelandena, till exempel ID för`InstanceId`ursprungs-VM-instansen för loggmeddelandet ( ), tråd-ID (`Tid`) och en mer detaljerad tidsstämpel ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)).

> [!NOTE]
> För närvarande kan endast .NET-programloggar skrivas till blob-lagringen. Java, PHP, Node.js, Python programloggar kan endast lagras på App Service filsystemet (utan kodändringar för att skriva loggar till extern lagring).
>
> Om du [återskapar åtkomstnycklarna för ditt lagringskonto](../storage/common/storage-create-storage-account.md)måste du återställa respektive loggningskonfiguration för att kunna använda de uppdaterade åtkomstnycklarna. Gör så här:
>
> 1. På fliken **Konfigurera** ställer du in respektive loggningsfunktion på **Av**. Spara inställningen.
> 2. Aktivera loggning till lagringskontobloben igen. Spara inställningen.
>
>

Välj **nivå**eller informationsnivå att logga. I följande tabell visas de loggkategorier som ingår i varje nivå:

| Nivå | Inkluderade kategorier |
|-|-|
|**Disabled** | Ingen |
|**Fel** | Fel, kritiskt |
|**Varning** | Varning, fel, kritisk|
|**Information** | Info, Varning, Fel, Kritisk|
|**Verbose** | Spåra, felsöka, Info, Varning, Fel, Kritisk (alla kategorier) |

När du är klar väljer du **Spara**.

## <a name="enable-application-logging-linuxcontainer"></a>Aktivera programloggning (Linux/Container)

Om du vill aktivera programloggning för Linux-appar eller anpassade behållarappar i [Azure-portalen](https://portal.azure.com)navigerar du till din app och väljer **App Service-loggar**.

Välj **Filsystem**i **Programloggning**.

I **Kvot (MB)** anger du diskkvoten för programloggarna. I **Kvarhållningsperiod (dagar)** anger du hur många dagar loggarna ska behållas.

När du är klar väljer du **Spara**.

## <a name="enable-web-server-logging"></a>Aktivera webbserverloggning

Om du vill aktivera webbserverloggning för Windows-appar i [Azure-portalen](https://portal.azure.com)navigerar du till din app och väljer **App Service-loggar**.

För **webbserverloggning**väljer du **Lagring** för att lagra loggar på blob-lagring eller **Filsystem** för att lagra loggar på App Service-filsystemet. 

I **Kvarhållningsperiod (dagar)** anger du hur många dagar loggarna ska behållas.

> [!NOTE]
> Om du [återskapar åtkomstnycklarna för ditt lagringskonto](../storage/common/storage-create-storage-account.md)måste du återställa respektive loggningskonfiguration för att kunna använda de uppdaterade nycklarna. Gör så här:
>
> 1. På fliken **Konfigurera** ställer du in respektive loggningsfunktion på **Av**. Spara inställningen.
> 2. Aktivera loggning till lagringskontobloben igen. Spara inställningen.
>
>

När du är klar väljer du **Spara**.

## <a name="log-detailed-errors"></a>Logga detaljerade fel

Om du vill spara felsidan eller spårning av misslyckade begäranden för Windows-appar i [Azure-portalen](https://portal.azure.com)navigerar du till din app och väljer **App Service-loggar**.

Under **Detaljerad felloggning** eller **Spårning av misslyckade begäranden**väljer du **På**och väljer sedan **Spara**.

Båda typerna av loggar lagras i App Service-filsystemet. Upp till 50 fel (filer/mappar) behålls. När antalet HTML-filer överstiger 50 tas de äldsta 26 felen bort automatiskt.

## <a name="add-log-messages-in-code"></a>Lägga till loggmeddelanden i kod

I din programkod använder du de vanliga loggningslätterna för att skicka loggmeddelanden till programloggarna. Ett exempel:

- ASP.NET program kan använda klassen [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) för att logga information till programdiagnostikloggen. Ett exempel:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Som standard använder ASP.NET Core [loggningsleverantören Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Mer information finns [i ASP.NET Core-loggning i Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Strömningsloggar

Innan du streamar loggar i realtid aktiverar du den loggtyp du vill använda. All information som skrivs till filer som slutar på .txt, .log eller .htm som lagras i katalogen */LogFiles* (d:/home/logfiles) strömmas av App Service.

> [!NOTE]
> Vissa typer av loggningsbuffert skriv till loggfilen, vilket kan resultera i i oordning händelser i flödet. En programloggpost som inträffar när en användare besöker en sida kan till exempel visas i flödet före motsvarande HTTP-loggpost för sidbegäran.
>

### <a name="in-azure-portal"></a>I Azure-portalen

Om du vill strömma loggar i [Azure-portalen](https://portal.azure.com)navigerar du till din app och väljer **Loggström**. 

### <a name="in-cloud-shell"></a>I Cloud Shell

Så här streamar du loggar som finns i [Cloud Shell](../cloud-shell/overview.md)och använder följande kommando:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Om du vill filtrera specifika händelser, till exempel fel, använder du parametern **--Filter.** Ett exempel:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Om du vill filtrera specifika loggtyper, till exempel HTTP, använder du parametern **--Path.** Ett exempel:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>I lokal terminal

Om du vill strömma loggar i den lokala konsolen [installerar du Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och loggar in på ditt [konto](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). När du har loggat in, följde [instruktionerna för Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Komma åt loggfiler

Om du konfigurerar alternativet Azure Storage blobbar för en loggtyp behöver du ett klientverktyg som fungerar med Azure Storage. Mer information finns i [Azure Storage Client Tools](../storage/common/storage-explorers.md).

För loggar som lagras i App Service filsystemet, är det enklaste sättet att ladda ner ZIP-filen i webbläsaren på:

- Linux/behållarappar:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-appar:`https://<app-name>.scm.azurewebsites.net/api/dump`

För Linux/behållarappar innehåller ZIP-filen konsolutdataloggar för både docker-värden och docker-behållaren. För en utskalad app innehåller ZIP-filen en uppsättning loggar för varje instans. I apptjänstfilsystemet är dessa loggfiler innehållet i katalogen */home/logfiles.*

För Windows-appar innehåller ZIP-filen innehållet i katalogen *D:\Home\LogFiles* i apptjänstfilsystemet. Den har följande struktur:

| Typ av logg | Katalog | Beskrivning |
|-|-|-|
| **Programloggar** |*/LogFiles/Application/* | Innehåller en eller flera textfiler. Formatet på loggmeddelandena beror på vilken loggningsleverantör du använder. |
| **Misslyckade spårningar av begäranden** | */LogFiles/W3SVC###########/* | Innehåller XML-filer och en XSL-fil. Du kan visa de formaterade XML-filerna i webbläsaren. |
| **Detaljerade felloggar** | */LogFiles/DetailedErrors/* | Innehåller HTM-felfiler. Du kan visa HTM-filerna i webbläsaren.<br/>Ett annat sätt att visa misslyckade spårningar av begäran är att navigera till din appsida i portalen. På den vänstra menyn väljer du **Diagnostisera och lösa problem**och söker sedan efter spårade **logger för misslyckade begäranden**och klickar sedan på ikonen för att bläddra och visa den spårning du vill använda. |
| **Webbserverloggar** | */LogFiles/http/RawLogs/* | Innehåller textfiler som är formaterade med [w3c-utökat loggfilsformat](/windows/desktop/Http/w3c-logging). Den här informationen kan läsas med hjälp av en textredigerare eller ett verktyg som [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>App Service stöder inte `s-computername` `s-ip`fälten `cs-version` , eller . |
| **Distributionsloggar** | */LogFiles/Git/* och */deployments/* | Innehåller loggar som genereras av interna distributionsprocesser samt loggar för Git-distributioner. |

## <a name="send-logs-to-azure-monitor-preview"></a>Skicka loggar till Azure Monitor (förhandsversion)

Med den nya [Azure Monitor-integreringen](https://aka.ms/appsvcblog-azmon)kan du [skapa diagnostikinställningar (förhandsversion)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) för att skicka loggar till lagringskonton, händelsehubbar och logganalys. 

> [!div class="mx-imgBorder"]
> ![Diagnostikinställningar (förhandsgranskning)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Loggtyper som stöds

I följande tabell visas de loggtyper och beskrivningar som stöds: 

| Typ av logg | Windows-stöd | Stöd för Linux (Docker) | Beskrivning |
|-|-|-|
| AppServiceConsoleLogs | Tba | Ja | Standardutdata och standardfel |
| AppServiceHTTPLogs | Ja | Ja | Webbserverloggar |
| AppServiceMiljöPlattformar | Ja | Ja | App Service-miljö: skalning, konfigurationsändringar och statusloggar|
| AppServiceAuditLogs | Ja | Ja | Inloggningsaktivitet via FTP och Kudu |
| AppServiceFileAuditLogs | Ja | TBD | Filändringar via FTP och Kudu |
| AppServiceAppLogs | Tba | Java SE & Tomcat | Programloggar |

## <a name="next-steps"></a><a name="nextsteps"></a>Nästa steg
* [Frågeloggar med Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Övervaka Azure App Service](web-sites-monitor.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera apploggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
