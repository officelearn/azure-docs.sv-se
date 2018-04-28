---
title: Bästa praxis och felsökningsguiden för noden program på Azure-Webbappar
description: Information om metodtips och felsökningssteg för noden program på Azure Web Apps.
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
ms.openlocfilehash: 860874ed49056e6b4695c060b06bf061820c390e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Bästa praxis och felsökningsguiden för noden program på Azure-Webbappar

I den här artikeln får du lära dig metodtips och felsökningssteg för [noden program](app-service-web-get-started-nodejs.md) körs på Azure-Webbappar (med [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Var försiktig när du använder för felsökning på webbplatsen produktion. Rekommendation är att felsöka appen på en icke-produktion konfiguration till exempel din mellanlagringsplatsen och när problemet har åtgärdats, byta ut din mellanlagringsplatsen med din produktionsplatsen.
>

## <a name="iisnode-configuration"></a>IISNODE-konfiguration

Detta [schemafilen](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) visar alla inställningar som du kan konfigurera för iisnode. Några av de inställningar som är användbara för programmet:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Den här inställningen styr antalet noder processer som startas per IIS-program. Standardvärdet är 1. Du kan starta så många node.exes som din Virtuella vCPU antal genom att ändra värdet till 0. Det rekommenderade värdet är 0 för de flesta program så att du kan använda alla vCPUs på din dator. Node.exe är enkla trådar så att en node.exe förbrukar högst 1 vCPU. För att få högsta prestanda utanför tillämpningsprogrammet nod som du vill använda alla vCPUs.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Den här inställningen anger sökvägen till node.exe. Du kan ange ett värde så att den pekar till node.exe-version.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Den här inställningen styr det maximala antalet samtidiga förfrågningar som skickas av iisnode till varje node.exe. Standardvärdet är oändligt på Azure Web Apps. När finns inte på Azure-Webbappar, är standard 1024. Du kan konfigurera värde beroende på hur många begär programmet tar emot och hur snabbt programmet bearbetar varje begäran.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Den här inställningen styr det maximala antalet gånger iisnode försök att göra anslutningen på en namngiven pipe för att skicka begäranden till node.exe. Den här inställningen i kombination med namedPipeConnectionRetryDelay avgör den totala tidsgränsen för varje begäran inom iisnode. Standardvärdet är 200 på Azure Web Apps. Totalt antal tidsgräns i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Den här inställningen styr hur lång tid (i ms) iisnode väntar mellan varje försök att skicka begäran till node.exe via en namngiven pipe. Standardvärdet är 250 ms.
Totalt antal tidsgräns i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Som standard är den totala tidsgränsen i iisnode på Azure Web Apps 200 \* 250 ms = 50 sekunder.

### <a name="logdirectory"></a>logDirectory

Den här inställningen anger den katalog där iisnode loggar stdout/stderr. Standardvärdet är iisnode som är i förhållande till katalogen huvudskriptet (katalog där huvudsakliga server.js finns)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Den här inställningen styr vilken version av node-inspector iisnode använder vid felsökning av tillämpningsprogrammet nod. Iisnode-inspector-0.7.3.dll och iisnode inspector.dll för närvarande bara två giltiga värden för den här inställningen. Standardvärdet är iisnode-inspector-0.7.3.dll. Iisnode-inspector-0.7.3.dll version använder node-inspector-0.7.3 och använder web sockets. Aktivera web sockets på ditt Azure webapp använda den här versionen. Se <http://ranjithblogs.azurewebsites.net/?p=98> för mer information om hur du konfigurerar iisnode om du vill använda den nya node-inspector.

### <a name="flushresponse"></a>flushResponse

Standardbeteendet för IIS är den buffrar svarsdata för upp till 4 MB innan eller till slutet av svar, beroende på vilket som kommer först. iisnode erbjuder en konfigurationsinställning kan åsidosätta detta beteende: Om du vill rensa ett fragment av text för svar entitet som iisnode tar emot det från node.exe, måste du ange den iisnode/@flushResponse attribut i web.config på 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Aktivera den lokaliseraren av varje fragmentet i svaret objektsinnehållet lägger till prestanda försämras som minskar genomflödet i systemet med ~ 5% (från och med v0.1.13). Bäst att definiera omfattningen av den här inställningen bara till slutpunkter som kräver svar för strömning (till exempel med hjälp av den `<location>` elementet i web.config)

Utöver detta, måste för direktuppspelning av program, du också ange responseBufferLimit för iisnode-hanteraren till 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Ett semikolon avgränsade lista över filer som övervakas för ändringar. Ändringar i en fil utlöser återvinning. Varje post består av ett valfritt katalognamn som en obligatorisk filnamn, som är i förhållande till den katalog där programmets startpunkt finns. Jokertecken är tillåtna i den fil del endast. Standardvärdet är `*.js;web.config`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Standardvärdet är false. Om aktiverad, node-App kan ansluta till en namngiven pipe (miljövariabeln IISNODE\_kontrollen\_PIPE) och skicka ett meddelande om ”återvinning”. Detta medför w3wp att återvinna avslutas.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Standardvärdet är 0, vilket innebär att funktionen är inaktiverad. När anger ett värde större än 0, kommer iisnode sidan ut alla dess underordnade processer varje 'idlePageOutTimePeriod' i millisekunder. Se [dokumentationen](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) att förstå vad sidan ut sätt. Den här inställningen är användbar för program som använder en hög mängd minne och vill sidan ut minne till disk ibland att frigöra RAM-minne.

> [!WARNING]
> Var försiktig när du aktiverar följande konfigurationsinställningarna på program i produktion. Det rekommenderas att inte aktivera dem på live produktionsprogram.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Standardvärdet är false. Om värdet är true, iisnode lägger till en HTTP-Svarsrubrik `iisnode-debug` till alla HTTP-svar som skickas av `iisnode-debug` huvudvärde är en URL. Enskilda delar diagnostisk information kan erhållas genom att titta på URL-fragment, men en visualisering är tillgängliga genom att öppna URL: en i en webbläsare.

### <a name="loggingenabled"></a>loggingEnabled

Den här inställningen styr loggning av stdout och stderr av iisnode. Iisnode avbildar stdout/stderr från noden processer den startar och skriver till katalogen som anges i inställningen 'logDirectory'. När den är aktiverad, ditt program skriver loggar till filsystemet och beroende på mängden loggning som utförs av programmet, kan det finnas prestanda.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Standardvärdet är false. Inställt på true, iisnode visar när HTTP-statuskoden och Win32-felkod i webbläsaren. Win32-kod är användbara vid felsökning av vissa typer av problem.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (aktivera inte på live produktionsplatsen)

Den här inställningen styr felsökningsfunktionen. Iisnode är integrerad med node-inspector. Genom att aktivera den här inställningen kan aktivera du felsökning av tillämpningsprogrammet nod. När du aktiverar den här inställningen skapar iisnode node-inspector filer i katalogen 'debuggerVirtualDir' på den första debug-begäranden till programmet nod. Du kan läsa in node-inspector genom att skicka en begäran om att http://yoursite/server.js/debug. Du kan styra debug URL-segment med inställningen 'debuggerPathSegment'. Som standard debuggerPathSegment = 'debug ”. Du kan ange `debuggerPathSegment` till ett GUID, till exempel så att det är svårare att identifieras av andra.

Läs [felsöka node.js-program på Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) för mer information om hur du felsöker.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenarier och rekommendationer/felsökning

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Mitt noden program anropar överdriven utgående

Många program kan upprätta utgående anslutningar som en del av deras regelbunden drift. Till exempel när en begäran kommer, kan appen nod kontakta en REST-API någon annanstans och hämta viss information vid bearbetning av begäran. Du kan använda en keep-alive agent när du anropar http eller https. Du kan använda modulen agentkeepalive som agent keep-alive när du gör dessa utgående anrop.

Modulen agentkeepalive säkerställer att sockets återanvänds på ditt Azure webapp VM. Skapa en ny socket för varje utgående begäran lägger till omkostnader för ditt program. Programmet återanvända sockets för utgående begäran försäkrar du dig om att programmet inte överskrider maxsocket som allokeras per virtuell dator. Rekommendationerna i Azure-webbprogram är att ange värdet agentKeepAlive maxsocket till totalt (4 instanser av node.exe \* 40 maxsocket-instans) 160 sockets per virtuell dator.

Exempel [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfiguration:

```nodejs
var keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Det här exemplet förutsätter att du har 4 node.exe körs på den virtuella datorn. Om du har ett annat antal node.exe körs på den virtuella datorn, måste du ändra maxsocket ange därefter.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Min noden program förbrukar för mycket CPU

Du kan få en rekommendation från Azure-Webbappar på portalen om hög cpu-förbrukning. Du kan också ställa in Övervakare kan du titta på vissa [mått](web-sites-monitor.md). När CPU-användningen på den [Azure Portal instrumentpanelen](../application-insights/app-insights-web-monitor-performance.md), kontrollera MAX värdena för CPU så att du inte missar högsta värden.
Om du tror att ditt program förbrukar för mycket CPU och du kan förklara varför profile du tillämpningsprogrammet nod för att ta reda på.

#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>Profilering av noden programmet på Azure-Webbappar med V8-Profiler

Anta exempelvis att du har en hello world-app som du vill profilen på följande sätt:

```nodejs
var http = require('http');
function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
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

Gå till webbplatsen Felsökningskonsolen https://yoursite.scm.azurewebsites.net/DebugConsole

Gå till katalogen plats/wwwroot. Du kan se en kommandotolk som visas i följande exempel:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Kör kommandot `npm install v8-profiler`.

Det här kommandot installerar v8 profileraren under noden\_moduler katalogen och alla dess beroenden.
Nu kan redigera din server.js för att profilen ditt program.

```nodejs
var http = require('http');
var profiler = require('v8-profiler');
var fs = require('fs');

function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
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

Föregående kod profilerna WriteConsoleLog fungerar och skriver sedan profilen utdata till filen 'profile.cpuprofile' under din plats wwwroot. Skicka en begäran till programmet. Du se en 'profile.cpuprofile'-fil som skapats under din plats wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Hämta den här filen och öppna den med Chrome F12 verktyg. Tryck på F12 på Chrome, och välj sedan den **profiler** fliken. Välj den **belastningen** knappen. Välj din profile.cpuprofile-fil som du hämtat. Klicka på den profil som du just läste in.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Du kan se att 95% av tiden har förbrukats av funktionen WriteConsoleLog. Utdata visar också exakt radnummer och källfiler som har orsakat problemet.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Min noden program förbrukar för mycket minne

Om ditt program förbrukar för mycket minne, visas ett meddelande från Azure-Webbappar på portalen om hög minnesförbrukning. Du kan ställa in Övervakare kan du titta på vissa [mått](web-sites-monitor.md). När minnesanvändningen på de [Azure portalens instrumentpanel](../application-insights/app-insights-web-monitor-performance.md), bör du kontrollera de högsta värdena för minne så att du inte missar högsta värden.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Identifiering av läcka och Heap Diff för node.js

Du kan använda [nod memwatch](https://github.com/lloyd/node-memwatch) för att identifiera minne läcker.
Du kan installera `memwatch` precis som v8 profiler och redigera koden för att avbilda och diff heapar för att identifiera minnesläckor i ditt program.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Min node.exe är komma avslutats slumpmässigt

Det finns ett par skäl till varför node.exe är avstängd slumpmässigt:

1. Programmet som utlöste undantagsfel utan felhantering – Kontrollera d:\\hem\\loggfiler\\programmet\\loggning errors.txt fil information på undantag utlöstes. Den här filen har stack-spårning för att felsöka och lösa ditt program.
2. Programmet förbrukar för mycket minne som påverkar andra processer från att komma igång. Om den totala mängden virtuell minnet är nära 100%, kan din node.exe avslutas av Processhanteraren för. Processen manager stoppar vissa processer för att låta andra processer prova att utföra vissa uppgifter. Profilen ditt program för minnesläckor för att åtgärda problemet. Om ditt program kräver stora mängder minne, kan du skala upp till en större VM (vilket ökar RAM-minne tillgängligt för den virtuella datorn).

### <a name="my-node-application-does-not-start"></a>Node-App startar inte

Om programmet returnerar 500 fel när den startar, kan det finnas flera skäl:

1. Node.exe finns inte på rätt plats. Kontrollera nodeProcessCommandLine inställningen.
2. Huvudsakliga skriptfilen finns inte på rätt plats. Kontrollera web.config och kontrollera att namnet på den huvudsakliga skriptfilen i hanteraravsnittet matchar den huvudsakliga skriptfilen.
3. Web.config konfigurationen är inte korrekt – Kontrollera namn/värden för inställningar.
4. Kallstart – ditt program tar för lång tid att starta. Om ditt program tar längre än (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 sekunder iisnode returnerar ett fel 500. Öka värdena för dessa inställningar så att den matchar ditt program starttiden för att förhindra att iisnode från timeout och returnerar fel 500.

### <a name="my-node-application-crashed"></a>Min nod programmet kraschade

Programmet som utlöste undantagsfel utan felhantering – Kontrollera `d:\\home\\LogFiles\\Application\\logging-errors.txt` fil information på undantag genererades. Den här filen har stack-spårning för att diagnostisera och åtgärda ditt program.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Min noden tar för lång tid att starta (kalla Start)

Den vanliga orsaken för länge programmet starttider är ett stort antal filer i noden\_moduler. Programmet försöker läsa in de flesta av dessa filer när du startar. Som standard eftersom dina filer lagras på nätverksresursen på Azure Web Apps kan inläsning av många filer ta tid.
Vissa lösningar för att göra den här processen snabbare är:

1. Var noga med att du har en platt beroende struktur och inga dubbla beroenden genom att använda npm3 för att installera modulerna.
2. Försök att lazy läsa in noden\_moduler och inte att läsa in alla moduler i början av programmet. Anropet till require('module') ska göras när du verkligen behöver modulen i funktionen innan den första körningen av modulen kod för till Lazy belastningen moduler.
3. Azure Web Apps innehåller en funktion som kallas lokala cache. Den här funktionen kopieras innehållet från nätverksresursen till den lokala disken på den virtuella datorn. Eftersom filerna är lokal kan inläsningstid för nod\_moduler är mycket snabbare.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE HTTP-status och understatus

Den `cnodeconstants` [källfilen](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) visar alla möjliga status/understatus kombinationer iisnode kan returnera på grund av ett fel.

Aktivera FREB för ditt program för att se win32-felkod (vara säker på att du aktiverar FREB bara på icke-produktionsmiljöer av prestandaskäl).

| Http-status | HTTP-status | Möjlig orsak? |
| --- | --- | --- |
| 500 |1000 |Det fanns vissa problem skickar ut begäran att IISNODE – kontrollera om node.exe startades. Har det gick kraschat node.exe vid start. Kontrollera konfigurationen av web.config innehåller fel. |
| 500 |1001 |-Win32Error 0x2 - App inte svarar på URL: en. Kontrollera regler för URL-omskrivning eller kontrollera om din expressapp har rätt vägar som definierats. -Win32Error 0x6d – namngiven pipe är upptagen – Node.exe kan ta emot inte begäranden eftersom pipe är upptaget. Kontrollera hög cpu-användning. -Andra fel – kontrollera om node.exe kraschade. |
| 500 |1002 |Node.exe kraschat – Kontrollera d:\\hem\\loggfiler\\loggning errors.txt för stack-spårning. |
| 500 |1003 |Pipe-konfigurationen problemet – namngiven pipe-konfigurationen är felaktig. |
| 500 |1004-1018 |Ett fel uppstod när begäran skickades eller bearbeta svaret till eller från node.exe. Kontrollera om node.exe kraschade. Kontrollera d:\\hem\\loggfiler\\loggning errors.txt för stack-spårning. |
| 503 |1000 |Inte tillräckligt med minne för att allokera mer namngivna pipe-anslutningar. Kontrollera varför appen förbrukar för mycket minne. Kontrollera maxConcurrentRequestsPerProcess värdet. Om du har många begäranden inte och öka värdet för att förhindra det här felet. |
| 503 |1001 |Begäran gick inte att sända node.exe eftersom programmet återvinning. När programmet har återvunnits ska begäranden omdirigeras normalt. |
| 503 |1002 |Kontrollera win32-felkod faktiska orsak – begäran kunde inte skickas till en node.exe. |
| 503 |1003 |Namngiven pipe är upptagen – kontrollera om node.exe förbrukar för mycket Processorkraft |

NODE.exe har en inställning som kallas `NODE_PENDING_PIPE_INSTANCES`. Som standard om inte har distribuerats på Azure Web Apps det här värdet är 4. Vilket innebär att node.exe kan endast accepterar begäranden som fyra samtidigt på en namngiven pipe. Det här värdet anges till 5 000 på Azure Web Apps. Det här värdet ska vara bra att de flesta noden program som körs på Azure Web Apps. Du bör inte se 503.1003 på Azure-Webbappar på grund av högt värde för den `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Fler resurser

Följa dessa länkar om du vill veta mer om node.js-program i Azure App Service.

* [Kom igång med Node.js-Webbappar i Azure App Service](app-service-web-get-started-nodejs.md)
* [Felsöka en Node.js-webbapp i Azure App Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center](../nodejs-use-node-modules-azure-apps.md)
* [Utforska superhemliga Kudu-felsökningskonsolen](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)