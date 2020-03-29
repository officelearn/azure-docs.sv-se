---
title: Node.js metodtips och felsökning
description: Lär dig de bästa metoderna och felsökningsstegen för Node.js-program som körs i Azure App Service.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 682884d11b298a97e27056af3c10802dfd410e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430565"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Metodtips och felsökningsguide för nodprogram i Azure App Service Windows

I den här artikeln får du lära dig metodtips och felsökningssteg för [nodprogram](app-service-web-get-started-nodejs.md) som körs på Azure App Service (med [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Var försiktig när du använder felsökningssteg på produktionsplatsen. Rekommendationen är att felsöka din app på en icke-produktionsinställning till exempel din mellanlagringsplats och när problemet är åtgärdat byter du mellanlagringsplatsen med produktionsplatsen.
>

## <a name="iisnode-configuration"></a>IISNODE-konfiguration

Den här [schemafilen](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) visar alla inställningar som du kan konfigurera för iisnode. Några av de inställningar som är användbara för ditt program:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Den här inställningen styr antalet nodprocesser som startas per IIS-program. Standardvärdet är 1. Du kan starta lika många node.exes som din VM vCPU räkna genom att ändra värdet till 0. Det rekommenderade värdet är 0 för de flesta program så att du kan använda alla virtuella processorer på din dator. Node.exe är enkeltrådad så en node.exe förbrukar högst 1 vCPU. Om du vill få ut maximal prestanda av nodprogrammet vill du använda alla virtuella processorer.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Den här inställningen styr sökvägen till node.exe. Du kan ange att det här värdet ska peka på node.exe-versionen.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Den här inställningen styr det maximala antalet samtidiga begäranden som skickas av iisnode till varje node.exe. På Azure App Service är standardvärdet Oändligt. Du kan konfigurera värdet beroende på hur många begäranden ditt program tar emot och hur snabbt ditt program bearbetar varje begäran.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Den här inställningen styr det maximala antalet gånger iisnode försöker göra anslutningen på den namngivna pipe för att skicka begäranden till node.exe. Den här inställningen i kombination med namedPipeConnectionRetryDelay bestämmer den totala timeouten för varje begäran inom iisnode. Standardvärdet är 200 på Azure App Service. Total timeout i sekunder = (maxNamedPipeConnectionRetry \* heterPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Den här inställningen styr hur länge (i ms) iisnode väntar mellan varje nytt försök att skicka begäran till node.exe över den namngivna pipe. Standardvärdet är 250 ms.
Total timeout i sekunder = (maxNamedPipeConnectionRetry \* heterPipeConnectionRetryDelay) / 1000

Som standard är den totala timeouten i iisnode \* på Azure App Service 200 250 ms = 50 sekunder.

### <a name="logdirectory"></a>logDirectory

Den här inställningen styr katalogen där iisnode loggar stdout/stderr. Standardvärdet är iisnode, som är i förhållande till huvudskriptkatalogen (katalog där huvudserver.js finns)

### <a name="debuggerextensiondll"></a>felsökareUtstensionDll

Den här inställningen styr vilken version av nod-inspector iisnode som används när du felsöker nodprogrammet. För närvarande är iisnode-inspector-0.7.3.dll och iisnode-inspector.dll de enda två giltiga värdena för den här inställningen. Standardvärdet är iisnode-inspector-0.7.3.dll. Iisnode-inspector-0.7.3.dll-versionen använder nod-inspector-0.7.3 och använder webbuttag. Aktivera webbuttag på din Azure-webbapp för att använda den här versionen. Se <https://ranjithblogs.azurewebsites.net/?p=98> mer information om hur du konfigurerar iisnode för att använda den nya noden-inspektören.

### <a name="flushresponse"></a>flushResponse

Standardbeteendet för IIS är att det buffrar svarsdata upp till 4 MB före tömning, eller fram till slutet av svaret, beroende på vilket som inträffar först. iisnode erbjuder en konfigurationsinställning för att åsidosätta det här beteendet: för att rensa ett fragment av svarsenhetskroppen så snart iisnod tar emot det iisnode/@flushResponse från node.exe måste du ange attributet i web.config till 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Aktivera tömning av varje fragment av svartitetskroppen lägger till prestandakostnader som minskar systemets dataflöde med ~5 % (från och med v0.1.13). Det bästa till scope den här inställningen endast till slutpunkter `<location>` som kräver svarsströmning (till exempel med hjälp av elementet i web.config)

Utöver detta, för strömmande program, måste du också ställa in responseBufferLimit av din iisnode hanterare till 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>tittadeFiler

En semikolon separerad lista över filer som visas efter ändringar. Alla ändringar i en fil gör att programmet återvinns. Varje post består av ett valfritt katalognamn samt ett obligatoriskt filnamn, som är i förhållande till katalogen där huvudprogrammets startpunkt finns. Jokertecken är endast tillåtna i filnamnsdelen. Standardvärdet är `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>återvinnaSignalEnabled

Standardvärdet är false. Om det är aktiverat kan nodprogrammet ansluta till en namngiven pipe (miljövariabeln IISNODE\_CONTROL\_PIPE) och skicka ett "återvinna" meddelande. Detta gör att w3wp att återvinna graciöst.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Standardvärdet är 0, vilket innebär att den här funktionen är inaktiverad. När värdet är större än 0 kommer iisnode att söka ut alla sina underordnade processer i alla idlePageOutTimePeriod i millisekunder. Se [dokumentation](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) för att förstå vad page out betyder. Den här inställningen är användbar för program som förbrukar en stor mängd minne och vill söka ut minne till disk ibland för att frigöra RAM-minne.

> [!WARNING]
> Var försiktig när du aktiverar följande konfigurationsinställningar för produktionsprogram. Rekommendationen är att inte aktivera dem i live-produktionsapplikationer.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Standardvärdet är false. Om värdet är true läggs ett HTTP-svarshuvud `iisnode-debug` till i `iisnode-debug` varje HTTP-svar som skickas i sidhuvudvärdet. Enskilda delar av diagnostisk information kan erhållas genom att titta på URL-fragmentet, men en visualisering är tillgänglig genom att öppna webbadressen i en webbläsare.

### <a name="loggingenabled"></a>loggningEnabled

Den här inställningen styr loggningen av stdout och stderr av iisnode. Iisnode fångar stdout/stderr från nodprocesser som den startar och skriver till den katalog som anges i inställningen "logDirectory". När detta är aktiverat skriver ditt program loggar till filsystemet och beroende på hur mycket loggning som görs av programmet kan det finnas prestandakonsekvenser.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Standardvärdet är false. När den är inställd på true visar iisnode HTTP-statuskoden och Win32-felkoden i webbläsaren. Win32-koden är till hjälp för att felsöka vissa typer av problem.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>felsökningEnabled (aktivera inte på live-produktion)

Den här inställningen styr felsökningsfunktionen. Iisnode är integrerat med nodinspektör. Genom att aktivera den här inställningen aktiverar du felsökning av nodprogrammet. När du aktiverar den här inställningen skapar iisnode nod-inspektörsfiler i katalogen "debuggerVirtualDir" på den första felsökningsbegäran till nodprogrammet. Du kan läsa in nodinspektören `http://yoursite/server.js/debug`genom att skicka en begäran till . Du kan styra felsöknings-URL-segmentet med inställningen "debuggerPathSegment". Som standard felsökerPathSegment='debug'. Du kan `debuggerPathSegment` till exempel ställa in ett GUID så att det blir svårare att upptäckas av andra.

Läs [Felsökning node.js-program i Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) för mer information om felsökning.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenarier och rekommendationer/felsökning

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Nodprogrammet gör överdrivna utgående samtal

Många program skulle vilja göra utgående anslutningar som en del av deras ordinarie drift. När en begäran till exempel kommer in vill nodappen kontakta ett REST API någon annanstans och få information för att bearbeta begäran. Du skulle vilja använda en keep alive agent när du ringer http eller https-samtal. Du kan använda agentkeepalive modulen som din hålla vid liv agent när du gör dessa utgående samtal.

Agentkeepalive-modulen säkerställer att sockets återanvänds på din virtuella Azure-webbapp. Om du skapar en ny socket på varje utgående begäran läggs omkostnader till ditt program. Om du har dina program återanvändningssocklar för utgående begäranden säkerställer du att ditt program inte överskrider maxSockets som allokeras per virtuell dator. Rekommendationen på Azure App Service är att ange värdet agentKeepAlive maxSockets till totalt \* (4 instanser av node.exe 40 maxSockets/instance) 160 sockets per virtuell dator.

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
> Det här exemplet förutsätter att du har 4 node.exe som körs på den virtuella datorn. Om du har ett annat antal node.exe som körs på den virtuella datorn måste du ändra inställningen maxSockets i enlighet med detta.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Min nod ansökan förbrukar för mycket CPU

Du kan få en rekommendation från Azure App Service på din portal om hög cpu-förbrukning. Du kan också ställa in övervakare för att titta efter vissa [mått](web-sites-monitor.md). När du kontrollerar CPU-användningen på [Azure Portal Dashboard](../azure-monitor/app/web-monitor-performance.md)kontrollerar du MAX-värdena för CPU så att du inte missar toppvärdena.
Om du tror att ditt program förbrukar för mycket CPU och du inte kan förklara varför, kan du profilera din nod program för att ta reda på.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilera nodprogrammet på Azure App Service med V8-Profiler

Anta till exempel att du har en hello world-app som du vill profilera enligt följande:

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

Gå till webbplatsen För felsökningskonsol`https://yoursite.scm.azurewebsites.net/DebugConsole`

Gå in på din webbplats / wwwroot katalog. En kommandotolk visas i följande exempel:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Kör kommandot `npm install v8-profiler`.

Det här kommandot installerar v8-profiler\_under nodmoduler katalog och alla dess beroenden.
Nu redigerar du server.js för att profilera ditt program.

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

Den föregående koden profilerar Funktionen WriteConsoleLog och skriver sedan profilutdata till filen "profile.cpuprofile" under din webbplats wwwroot. Skicka en begäran till din ansökan. Du ser en "profile.cpuprofile" fil som skapats under din webbplats wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Ladda ner den här filen och öppna den med Chrome F12 Tools. Tryck på F12 i Chrome och välj **Load** sedan fliken **Profiler.** Välj filen profile.cpuprofile som du hämtade. Klicka på den profil du just läst.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Du kan se att 95% av tiden förbrukades av writeconsoleLog-funktionen. Utdata visar också de exakta radnummer och källfiler som orsakade problemet.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Nodprogrammet förbrukar för mycket minne

Om ditt program förbrukar för mycket minne visas ett meddelande från Azure App Service på portalen om hög minnesförbrukning. Du kan ställa in övervakare för att titta efter vissa [mått](web-sites-monitor.md). När du kontrollerar minnesanvändningen på [Azure Portal Dashboard](../azure-monitor/app/web-monitor-performance.md)måste du kontrollera MAX-värdena för minne så att du inte missar toppvärdena.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Läcksökning och Heap Diff för nod.js

Du kan använda [nod-memwatch](https://github.com/lloyd/node-memwatch) för att identifiera minnesläckor.
Du kan `memwatch` installera precis som v8-profiler och redigera din kod för att fånga och diff heaps att identifiera minnesläckor i ditt program.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Min node.exe's blir dödad slumpmässigt

Det finns några anledningar till att node.exe stängs av slumpmässigt:

1. Ditt program kastar odragna undantag\\\\- Kontrollera\\\\d: home LogFiles Application logging-errors.txt fil för information om undantaget kastas. Den här filen har stackspårningen för att felsöka och åtgärda ditt program.
2. Ditt program förbrukar för mycket minne, vilket påverkar andra processer från att komma igång. Om det totala vm-minnet är nära 100 % kan din node.exes ha dödats av processhanteraren. Process manager dödar vissa processer för att låta andra processer få en chans att göra en del arbete. För att åtgärda problemet, profilera ditt program för minnesläckor. Om ditt program kräver stora mängder minne skalar du upp till en större virtuell dator (vilket ökar RAM-minnet som är tillgängligt för den virtuella datorn).

### <a name="my-node-application-does-not-start"></a>Nodprogrammet startar inte

Om ditt program returnerar 500 fel när det startar kan det finnas några orsaker:

1. Node.exe finns inte på rätt plats. Kontrollera inställningen för nodeProcessCommandLine.
2. Huvudskriptfilen finns inte på rätt plats. Kontrollera web.config och kontrollera att namnet på huvudskriptfilen i avsnittet hanterare matchar huvudskriptfilen.
3. Web.config-konfigurationen är inte korrekt – kontrollera inställningarnas namn/värden.
4. Kallstart – Det tar för lång tid att starta din ansökan. Om ditt program tar längre tid än (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 sekunder returnerar iisnode ett 500-fel. Öka värdena för dessa inställningar så att de matchar programmets starttid för att förhindra att iisnoden tajmar ut och returnerar 500-felet.

### <a name="my-node-application-crashed"></a>Nodprogrammet kraschade

Ditt program kastar odragna `d:\\home\\LogFiles\\Application\\logging-errors.txt` undantag - Kontrollera filen för information om undantaget kastas. Den här filen har stackspårningen för att diagnostisera och åtgärda ditt program.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Min nod ansökan tar för mycket tid att starta (Cold Start)

Den vanligaste orsaken till långa programstarttider är ett\_stort antal filer i nodmodulerna. Programmet försöker läsa in de flesta av dessa filer när du startar. Eftersom dina filer lagras i nätverksresursen på Azure App Service kan det ta tid att läsa in många filer.
Några lösningar för att göra denna process snabbare är:

1. Se till att du har en platt beroendestruktur och inga dubbla beroenden genom att använda npm3 för att installera dina moduler.
2. Försök att lata\_ladda nodmoduler och inte ladda alla moduler vid programstart. Till Lazy load moduler, bör anropet att kräva ("modul") göras när du faktiskt behöver modulen i funktionen innan den första körningen av modulkod.
3. Azure App Service erbjuder en funktion som kallas lokal cache. Den här funktionen kopierar ditt innehåll från nätverksresursen till den lokala disken på den virtuella datorn. Eftersom filerna är lokala är laddningstiden\_för nodmoduler mycket snabbare.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http-status och understatus

`cnodeconstants` [Källfilen](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) listar alla möjliga kombinationer av status/understatus som iisnode kan returnera på grund av ett fel.

Aktivera FREB för ditt program för att se win32-felkoden (se till att du aktiverar FREB endast på icke-produktionsplatser av prestandaskäl).

| Http-status | Http-understatus | Möjlig orsak? |
| --- | --- | --- |
| 500 |1000 |Det uppstod ett visst problem med att skicka begäran till IISNODE – Kontrollera om node.exe startades. Node.exe kan ha kraschat när du startar. Kontrollera om det finns fel i konfigurationen web.config. |
| 500 |1001 |- Win32Error 0x2 - App svarar inte på webbadressen. Kontrollera om du skriver om url-reglerna eller kontrollera om din expressapp har rätt definierade vägar. - Win32Error 0x6d - namngiven rör är upptagen - Node.exe accepterar inte begäranden eftersom röret är upptagen. Kontrollera hög cpu-användning. - Andra fel – kontrollera om node.exe kraschade. |
| 500 |1002 |Node.exe kraschade –\\kontrollera\\d:\\home LogFiles logging-errors.txt för stackspårning. |
| 500 |1003 |Problem med pipe-konfiguration – Den namngivna rörkonfigurationen är felaktig. |
| 500 |1004-1018 |Det uppstod ett visst fel när begäran skickades eller bearbetades till/från node.exe. Kontrollera om node.exe har kraschat. kontrollera\\d:\\hem\\LogFiles loggning-errors.txt för stackspårning. |
| 503 |1000 |Det finns inte tillräckligt med minne för att allokera fler namngivna röranslutningar. Kontrollera varför appen förbrukar så mycket minne. Kontrollera maxConcurrentRequestsPerProcess inställningsvärde. Om det inte är oändligt och du har många begäranden ökar du det här värdet för att förhindra det här felet. |
| 503 |1001 |Det gick inte att skicka begäran till node.exe eftersom programmet återvinns. När programmet har återvunnits ska begäranden delges normalt. |
| 503 |1002 |Kontrollera win32-felkoden av faktisk anledning – Begäran kunde inte skickas till en node.exe. |
| 503 |1003 |Namngiven pipe är för upptagen – Kontrollera om node.exe förbrukar överdriven PROCESSOR |

NODE.exe har en `NODE_PENDING_PIPE_INSTANCES`inställning som heter . På Azure App Service är det här värdet inställt på 5000. Vilket innebär att node.exe kan acceptera 5000-begäranden åt gången på den namngivna pipe. Det här värdet bör vara tillräckligt bra för de flesta nodprogram som körs på Azure App Service. Du bör inte se 503.1003 på Azure App Service på grund av det höga värdet för`NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Fler resurser

Följ de här länkarna om du vill veta mer om node.js-program på Azure App Service.

* [Kom igång med Node.js-webbappar i Azure App Service](app-service-web-get-started-nodejs.md)
* [Felsöka en Node.js-webbapp i Azure Apptjänst](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center](../nodejs-use-node-modules-azure-apps.md)
* [Utforska superhemliga Kudu-felsökningskonsolen](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
