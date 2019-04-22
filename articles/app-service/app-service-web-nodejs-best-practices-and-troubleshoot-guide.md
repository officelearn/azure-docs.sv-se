---
title: Metodtips och felsökning för Node.js – Azure App Service
description: Lär dig metodtips och felsökningssteg för node.js-program på Azure App Service.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.custom: seodec18
ms.openlocfilehash: 321dbf891c77007952f01b32bb509a15c2ac3e6f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895791"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Metodtips och felsökningsguide för nodprogram i Azure App Service Windows

I den här artikeln får du lära dig metodtips och felsökning av [nodprogram](app-service-web-get-started-nodejs.md) som körs på Azure App Service (med [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Var försiktig när du använder felsökningssteg på din produktionsplats. Rekommendationen är att felsöka din app på en icke-produktion-konfiguration till exempel din mellanlagringsplats och när problemet är löst växla din mellanlagringsplats med din produktionsplatsen.
>

## <a name="iisnode-configuration"></a>IISNODE-konfiguration

Detta [schemafilen](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) visar alla inställningar som du kan konfigurera för iisnode. Några av de inställningar som är användbara för ditt program:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Den här inställningen styr antalet noden processer som startas per IIS-program. Standardvärdet är 1. Du kan starta så många node.exes din VM vCPU antal genom att ändra värdet till 0. Det rekommenderade värdet är 0 för de flesta program så att du kan använda alla de virtuella processorer på din dator. Node.exe är single-threaded så att en node.exe förbrukar högst 1 virtuell processor. Om du vill få maximala prestanda från node-programmet som du vill använda alla virtuella processorer.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Den här inställningen styr sökvägen till node.exe. Du kan ange ett värde så att den pekar till node.exe-versionen.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Den här inställningen styr det maximala antalet samtidiga begäranden som skickas av iisnode till varje node.exe. Standardvärdet är oändligt på Azure App Service. Du kan konfigurera värde, beroende på hur många begäranden som programmet tar emot och hur snabbt ditt program bearbetar varje begäran.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Den här inställningen styr det maximala antalet gånger iisnode återförsök som gör anslutningen på en namngiven pipe att skicka begäranden till node.exe. Den här inställningen i kombination med namedPipeConnectionRetryDelay avgör den totala tidsgränsen för varje begärande i iisnode. Standardvärdet är 200 på Azure App Service. Total Timeout in seconds = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Den här inställningen styr hur lång tid (i ms) iisnode väntar mellan varje nytt försök att skicka begäran till node.exe via en namngiven pipe. Standardvärdet är 250 ms.
Total Timeout in seconds = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Den totala tidsgränsen i iisnode på Azure App Service är som standard 200 \* 250 ms = 50 sekunder.

### <a name="logdirectory"></a>logDirectory

Den här inställningen styr den katalog där iisnode-loggar stdout/stderr. Standardvärdet är iisnode, vilket är i förhållande till huvudskriptet-katalogen (katalog där huvudsakliga server.js förekommer)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Den här inställningen styr vilken version av node-inspector iisnode använder när du felsöker nodprogrammet. Iisnode-inspector-0.7.3.dll och iisnode-inspector.dll är för närvarande bara två giltiga värden för den här inställningen. Standardvärdet är iisnode-inspector-0.7.3.dll. Iisnode-inspector-0.7.3.dll version använder node-inspector-0.7.3 och använder webbsockets. Aktivera webbsockets på din Azure webapp att använda den här versionen. Se <https://ranjithblogs.azurewebsites.net/?p=98> för mer information om hur du konfigurerar iisnode om du vill använda den nya node-inspector.

### <a name="flushresponse"></a>flushResponse

Standardbeteendet för IIS är det buffrar svarsdata för upp till 4 MB innan eller fram till slutet av svaret, beroende på vilket som kommer först. iisnode erbjuder en konfigurationsinställning kan åsidosätta detta beteende: för att tömma fragment av entiteten svarstexten när iisnode tar emot det från node.exe, måste du ange den iisnode/@flushResponse attribut i web.config till 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Aktivera den tömning av varje fragment av svaret huvuddelen lägger till prestanda försämras som minskar genomströmningen i systemet med ~ 5% (från och med v0.1.13). Bäst att definiera omfattningen av den här inställningen bara till slutpunkter som kräver svar strömning (till exempel med hjälp av den `<location>` elementet i web.config)

Utöver detta är måste för direktuppspelning av program, du också ange responseBufferLimit för iisnode-hanteraren till 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Ett semikolon avgränsade lista över filer som har bevakade för ändringar. En fil ändras utlöser Papperskorgen. Varje post består av ett valfritt katalognamnet samt ett obligatoriskt filnamn, som är i förhållande till katalogen där programmets startpunkt finns. Jokertecken är tillåtna i filnamnsdelen endast. Standardvärdet är `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Standardvärdet är FALSKT. Om aktiverad, node-programmet kan ansluta till en namngiven pipe (miljövariabeln IISNODE\_kontroll\_PIPE) och skicka ett meddelande om ”återvinning”. Detta leder till w3wp att återanvända ett smidigt sätt.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Standardvärdet är 0, vilket innebär att den här funktionen är inaktiverad. Om värdet är ett värde större än 0, kommer iisnode sidan ut alla dess underordnade processer var 'idlePageOutTimePeriod' i millisekunder. Se [dokumentation](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) att förstå vad sidan ut innebär. Den här inställningen är användbar för program som förbrukar en hög mängd minne och vill sidan ut minne till disk ibland för att frigöra RAM-minne.

> [!WARNING]
> Var försiktig när du aktiverar följande konfigurationsinställningarna på program i produktion. Rekommendationen är att inte aktivera dem på live produktionsprogram.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Standardvärdet är FALSKT. Om värdet är sant, iisnode lägger till en HTTP-Svarsrubrik `iisnode-debug` till alla HTTP-svar skickas den `iisnode-debug` huvudvärde är en URL. Enskilda diagnostiska uppgifter kan hämtas genom att titta på URL-fragment, men en visualisering är tillgängliga genom att öppna URL: en i en webbläsare.

### <a name="loggingenabled"></a>loggingEnabled

Den här inställningen styr loggning av stdout- och stderr av iisnode. Iisnode samlar in stdout/stderr från noden processer det startar och skriver till den katalog som anges i inställningen ”logDirectory”. När den är aktiverad, ditt program skriver loggar till filsystemet och beroende på mängden loggning som utförs av programmet, det kan vara prestanda.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Standardvärdet är FALSKT. När visar inställd på SANT, iisnode HTTP-statuskoden och Win32-felkoden i webbläsaren. Win32-koden är användbara vid felsökning av vissa typer av problem.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (aktivera inte på aktiva produktionsplatsen)

Den här inställningen styr felsökningsfunktionen. Iisnode är integrerad med node-inspector. Genom att aktivera den här inställningen kan aktivera du felsökning av nodprogrammet. När du aktiverar den här inställningen skapar iisnode node-inspector filer i katalogen 'debuggerVirtualDir' på den första debug-begäran till nodprogrammet. Du kan läsa in node-inspector genom att skicka en begäran om att `http://yoursite/server.js/debug`. Du kan styra debug URL-segment med ”debuggerPathSegment” inställningen. Som standard debuggerPathSegment = ”felsöka”. Du kan ange `debuggerPathSegment` GUID, till exempel så att det är svårare att identifieras av andra.

Läs [Felsök node.js-program på Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) för mer information om hur du felsöker.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenarier och rekommendationer/felsökning

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Min node-programmet gör överdriven utgående samtal

Många program bör du se utgående anslutningar som en del av deras vanliga funktion. Till exempel när en begäran kommer in, kan node-app kontakta en REST-API någon annanstans och hämta viss information för att bearbeta begäran. Du kan använda en agent för keep-alive när http eller https-anrop. Du kan använda modulen agentkeepalive ditt keep-alive ombud när du gör dessa utgående anrop.

Modulen agentkeepalive säkerställer att sockets återanvänds på din Azure webapp VM. Skapa en ny socket för varje utgående begäran lägger till overhead i ditt program. Programmets återanvända sockets för utgående begäranden försäkrar du dig att ditt program inte överstiger maxsocket som allokeras per virtuell dator. Rekommendationen på Azure App Service är att ange värdet agentKeepAlive maxsocket totalt (4 instanser av node.exe \* 40 maxsocket/instans) 160 sockets per virtuell dator.

Exempel [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfiguration:

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Det här exemplet förutsätter att du har 4 node.exe som körs på den virtuella datorn. Om du har ett annat antal node.exe som körs på den virtuella datorn, måste du ändra maxsocket ange därefter.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Min noden program förbrukar för mycket CPU

Du får en rekommendation från Azure App Service på din portal om hög cpu-förbrukning. Du kan också ställa in Övervakare kan du titta på vissa [mått](web-sites-monitor.md). Vid kontroll av CPU-användningen på den [instrumentpanelen för Azure-portalen](../azure-monitor/app/web-monitor-performance.md), kontrollerar du de högsta värdena för processor så att du inte missar högsta värden.
Du kan profilera node-programmet för att ta reda på om du tror att ditt program förbrukar för mycket CPU och du kan förklara varför.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilering nodprogrammet på Azure App Service med V8-Profiler

Anta exempelvis att du har en hello world-app som du vill profilera på följande sätt:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Gå till webbplatsen Felsökningskonsolen `https://yoursite.scm.azurewebsites.net/DebugConsole`

Gå till katalogen site/wwwroot. Du kan se en kommandotolk som visas i följande exempel:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Kör kommandot `npm install v8-profiler`.

Det här kommandot installerar v8-profiler under noden\_moduler katalogen och alla dess beroenden.
Nu kan redigera din server.js för att Profilera ditt program.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Föregående kod profilerna i WriteConsoleLog fungerar och skriver sedan profilen utdata till filen 'profile.cpuprofile' under din plats wwwroot. Skicka en begäran till programmet. Du ser en ”profile.cpuprofile”-fil som skapats under din plats wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Hämta den här filen och öppna den med Chrome F12 Tools. Tryck på F12 på Chrome och välj sedan den **profiler** fliken. Välj den **belastningen** knappen. Välj din profile.cpuprofile-fil som du laddade ned. Klicka på den profil som du just läste in.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Du kan se att 95% av tiden har förbrukats av funktionen WriteConsoleLog. Utdata visar även de exakta radnummer och källfiler som har orsakat problemet.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Min noden program förbrukar för mycket minne

Om ditt program förbrukar för mycket minne, visas ett meddelande i Azure App Service på din portal om hög minnesförbrukning. Du kan ställa in Övervakare kan du titta på vissa [mått](web-sites-monitor.md). Vid kontroll av minnesanvändningen på de [instrumentpanelen för Azure-portalen](../azure-monitor/app/web-monitor-performance.md), bör du kontrollera de högsta värdena för minne så att du inte missar högsta värden.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Upptäcka läckor och Heap Diff för node.js

Du kan använda [noden memwatch](https://github.com/lloyd/node-memwatch) för att identifiera minne läcker.
Du kan installera `memwatch` precis som v8-profiler och redigera din kod för att avbilda och diff heapar för att identifiera minnesläckor i ditt program.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Min node.exe är komma avslutades slumpmässigt

Det finns flera skäl till varför node.exe datorn stängs av slumpmässigt:

1. Programmet som utlöste undantagsfel utan felhantering – Kontrollera d:\\home\\LogFiles\\program\\loggning errors.txt-filen för att få information på undantag uppstod. Den här filen har stackspårningen för att felsöka och åtgärda ditt program.
2. Programmet förbrukar för mycket minne, vilket påverkar andra processer från att komma igång. Om den totala mängden minnet i virtuell dator är nära 100%, kan din node.exe avslutas av Processhanteraren för. Processhanteraren stoppar vissa processer så att andra processer som prova att göra en del arbete. Profilera ditt program för minnesläckor för att åtgärda problemet. Om ditt program kräver stora mängder minne, kan du skala upp till en större virtuell dator (vilket ökar den ram-MINNET som är tillgängliga för den virtuella datorn).

### <a name="my-node-application-does-not-start"></a>Mitt program för noden startar inte

Om programmet returnerar 500-fel när den startas, kan det finnas flera skäl till:

1. Node.exe finns inte på rätt plats. Kontrollera nodeProcessCommandLine inställningen.
2. Viktigaste skriptfil finns inte på rätt plats. Kontrollera web.config och kontrollera att namnet på den huvudsakliga skriptfilen i hanteraravsnittet matchar den huvudsakliga skriptfilen.
3. Web.config-konfigurationen är inte rätt – Kontrollera inställningarna för namn/värden.
4. Kallstart – programmet tar lång tid att starta. Om ditt program tar längre tid än (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 sekunder iisnode returnerar ett 500-fel. Öka värdena för dessa inställningar så att den matchar ditt program starttiden för att förhindra att iisnode från timeout och returnera 500 fel.

### <a name="my-node-application-crashed"></a>Min node-programmet kraschade

Programmet som utlöste undantagsfel utan felhantering – Kontrollera `d:\\home\\LogFiles\\Application\\logging-errors.txt` -filen för att få information på undantag kastades. Den här filen har stackspårningen för att diagnostisera och åtgärda ditt program.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Min node-programmet tar för lång tid att starta (kalla Start)

Den vanliga orsaken till länge application starttider är ett stort antal filer i noden\_moduler. Ett program försöker läsa in de flesta av dessa filer när du börjar. Som standard eftersom filerna lagras på nätverksresursen på Azure App Service, kan läser in många filer ta tid.
Vissa lösningar för att den här processen snabbare är:

1. Måste du ha en fast beroende struktur och inga dubbla beroenden med hjälp av npm3 för att installera modulerna.
2. Försök att lazy läsa in din nod\_moduler och inte att läsa in alla moduler vid programstart. Lazy belastningen modulerna ska anropet till require('module') tänka på när du verkligen behöver modul i funktionen innan den första körningen av modulen kod.
3. Azure App Service erbjuder en funktion som kallas lokala cachen. Den här funktionen kopierar innehållet från nätverksresursen till den lokala disken på den virtuella datorn. Eftersom filerna är local, inläsningstiden för noden\_moduler är mycket snabbare.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http-status och understatus

Den `cnodeconstants` [källfilen](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) listor som alla möjliga status/understatus kombinationer iisnode kan returnera pga ett fel.

Aktivera FREB för ditt program att se win32-felkoden (vara säker på att du aktiverar FREB bara på icke-produktion platser av prestandaskäl).

| Http-status | Understatus för http | Möjlig orsak? |
| --- | --- | --- |
| 500 |1 000 |Det uppstod några fel skicka förfrågan om att IISNODE – kontrollerar om node.exe har startat. Har det gick kraschat node.exe vid start. Kontrollera din konfiguration för web.config för fel. |
| 500 |1001 |-Win32Error 0x2 - App svarar inte på URL: en. Kontrollera URL-omskrivningsregler eller kontrollera om din uttryckliga app har rätt vägar definierade. -Win32Error 0x6d – namngiven pipe är upptagen – Node.exe tar inte emot begäranden eftersom pipe är upptaget. Kontrollera hög cpu-användning. -Övriga fel – kontrollera om node.exe kraschade. |
| 500 |1002 |Node.exe kraschat – Kontrollera d:\\home\\LogFiles\\loggning-errors.txt för stackspårning. |
| 500 |1003 |Pipe configuration problem – namngiven pipe-konfigurationen är felaktig. |
| 500 |1004-1018 |Vissa fel uppstod när begäran skickades eller bearbetningen av svaret till och från node.exe. Kontrollera om node.exe kraschade. Kontrollera d:\\home\\LogFiles\\loggning-errors.txt för stackspårning. |
| 503 |1 000 |Inte tillräckligt med minne för att allokera mer namngivna pipe-anslutningar. Kontrollera varför din app förbrukar för mycket minne. Kontrollera maxConcurrentRequestsPerProcess inställningens värde. Om inte du har många begäranden och öka värdet för att förhindra det här felet. |
| 503 |1001 |Begäran kunde inte skickas till node.exe eftersom programmet återanvänds. När programmet har återvunnits ska begäranden omdirigeras normalt. |
| 503 |1002 |Kontrollera win32-felkoden faktiska orsak – begäran kunde inte skickas till en node.exe. |
| 503 |1003 |Namngiven pipe är upptagen – kontrollera om node.exe förbrukar mycket Processorkraft |

NODE.exe har en inställning som kallas `NODE_PENDING_PIPE_INSTANCES`. Det här värdet anges till 5 000 på Azure App Service. Vilket innebär att node.exe kan acceptera 5000 begäranden i taget på en namngiven pipe. Det här värdet ska vara tillräckligt bra för de flesta node.js-program som körs på Azure App Service. Du bör inte se 503.1003 på Azure App Service på grund av högt värde för den `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Fler resurser

Du kan följa dessa länkar om du vill veta mer om node.js-program på Azure App Service.

* [Kom igång med Node.js-webbappar i Azure App Service](app-service-web-get-started-nodejs.md)
* [Felsöka en Node.js-webbapp i Azure App Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center](../nodejs-use-node-modules-azure-apps.md)
* [Utforska superhemliga Kudu-felsökningskonsolen](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
