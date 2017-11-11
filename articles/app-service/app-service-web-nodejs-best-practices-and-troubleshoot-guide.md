---
title: "Bästa praxis och felsökningsguiden för noden program på Azure-Webbappar"
description: "Information om metodtips och felsökningssteg för noden program på Azure Web Apps."
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.openlocfilehash: f7f3186ba93670e566a10f97dde86a977101e8fc
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Bästa praxis och felsökningsguiden för noden program på Azure-Webbappar
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här artikeln får du lära dig metodtips och felsökningssteg för [noden program](app-service-web-get-started-nodejs.md) körs på Azure-Webbappar (med [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Var försiktig när du använder för felsökning på webbplatsen produktion. Rekommendation är att felsöka appen på en icke-produktion konfiguration till exempel din mellanlagringsplatsen och när problemet har åtgärdats, byta ut din mellanlagringsplatsen med din produktionsplatsen.
> 
> 

## <a name="iisnode-configuration"></a>IISNODE-konfiguration
Detta [schemafilen](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) visar alla inställningar som du kan konfigurera för iisnode. Några av de inställningar som är användbara för programmet:

* nodeProcessCountPerApplication
  
    Den här inställningen styr antalet noder processer som startas per IIS-program. Standardvärdet är 1. Du kan starta så många node.exes som din Virtuella vCPU antal genom att ange detta till 0. Det rekommenderade värdet är 0 för de flesta program så att du kan använda alla vCPUs på din dator. Node.exe är enkla trådar så att en node.exe förbrukar högst 1 vCPU. För att få högsta prestanda utanför tillämpningsprogrammet nod som du vill använda alla vCPUs.
* nodeProcessCommandLine
  
    Den här inställningen anger sökvägen till node.exe. Du kan ange ett värde så att den pekar till node.exe-version.
* maxConcurrentRequestsPerProcess
  
    Den här inställningen styr det maximala antalet samtidiga förfrågningar som skickas av iisnode till varje node.exe. På Azure Web Apps är standardvärdet för den här oändligt. Du behöver inte bry dig om den här inställningen. Standardvärdet är 1024 utanför Azure Web Apps. Du kan konfigurera detta beroende på hur många begär programmet tar emot och hur snabbt programmet bearbetar varje begäran.
* maxNamedPipeConnectionRetry
  
    Den här inställningen styr det maximala antalet gånger iisnode försök att göra anslutningen på en namngiven pipe skicka begäran via till node.exe. Den här inställningen i kombination med namedPipeConnectionRetryDelay avgör den totala tidsgränsen för varje begäran inom iisnode. Standardvärdet är 200 på Azure Web Apps. Totalt antal tidsgräns i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    Den här inställningen styr hur lång tid (i ms) iisnode väntar mellan varje försök att skicka begäran till node.exe via en namngiven pipe. Standardvärdet är 250 ms.
    Totalt antal tidsgräns i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    Som standard är den totala tidsgränsen i iisnode på Azure Web Apps 200 \* 250 ms = 50 sekunder.
* logDirectory
  
    Den här inställningen anger den katalog där iisnode loggar stdout/stderr. Standardvärdet är iisnode som är i förhållande till katalogen huvudskriptet (katalog där huvudsakliga server.js finns)
* debuggerExtensionDll
  
    Den här inställningen styr vilken version av node-inspector iisnode använder vid felsökning av tillämpningsprogrammet nod. Iisnode-inspector-0.7.3.dll och iisnode inspector.dll för närvarande bara två giltiga värden för den här inställningen. Standardvärdet är iisnode-inspector-0.7.3.dll. Iisnode-inspector-0.7.3.dll version använder node-inspector-0.7.3 och använder websockets. Du måste aktivera websockets på ditt Azure webapp använda den här versionen. Se <http://www.ranjithr.com/?p=98> för mer information om hur du konfigurerar iisnode om du vill använda den nya node-inspector.
* flushResponse
  
    Standardbeteendet för IIS är den buffrar svarsdata för upp till 4 MB innan eller till slutet av svar, beroende på vilket som kommer först. iisnode erbjuder en konfigurationsinställning kan åsidosätta detta beteende: Om du vill rensa ett fragment av text för svar entitet som iisnode tar emot det från node.exe, måste du ange den iisnode/@flushResponse attribut i web.config på 'true':
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    Genom att aktivera bokföring för varje fragment av text för svar entitet läggs prestanda försämras som minskar genomflödet i systemet med ~ 5% (från och med v0.1.13), så det är bäst att definiera omfattningen av den här inställningen bara till slutpunkter som kräver svar strömning (till exempel använda den <location> elementet i web.config).
  
    Utöver detta, måste för direktuppspelning av program, du också ange responseBufferLimit för iisnode-hanteraren till 0.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Detta är en lista över filer som övervakas för ändringar som gäller avgränsas med semikolon. En ändring av en fil utlöser återvinning. Varje post består av ett valfritt katalognamnet plus ett obligatoriskt filnamn som är i förhållande till den katalog där programmets startpunkt finns. Jokertecken är tillåtna i den fil del endast. Standardvärdet är ”\*. js;web.config”
* recycleSignalEnabled
  
    Standardvärdet är false. Om aktiverad, node-App kan ansluta till en namngiven pipe (miljövariabeln IISNODE\_kontrollen\_PIPE) och skicka ett meddelande om ”återvinning”. Detta medför w3wp att återvinna avslutas.
* idlePageOutTimePeriod
  
    Standardvärdet är 0, vilket innebär att funktionen är inaktiverad. När anger ett värde större än 0, kommer iisnode sidan ut alla dess underordnade processer varje 'idlePageOutTimePeriod' i millisekunder. Se [dokumentationen](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) att förstå vad sidan ut sätt. Den här inställningen är användbar för program som använder mycket minne och vill sidan ut minne till disk ibland att frigöra vissa RAM-minne.

> [!WARNING]
> Var försiktig när du aktiverar följande konfigurationsinställningarna på program i produktion. Det rekommenderas att inte aktivera dem på live produktionsprogram.
> 
> 

* debugHeaderEnabled
  
    Standardvärdet är false. Om värdet är true, iisnode lägger till en HTTP-svar huvud iisnode-debug i alla HTTP-svar skickar huvudvärde iisnode-debug är en URL. Enskilda delar diagnostisk information kan vara uppnår genom att titta på URL-fragment, men en mycket bättre visualisering uppnås genom att öppna URL: en i webbläsaren.
* loggingEnabled
  
    Den här inställningen styr loggning av stdout och stderr av iisnode. Iisnode avbildar stdout/stderr från noden processer den startar och skriver till katalogen som anges i inställningen 'logDirectory'. När den är aktiverad, ditt program skriver loggar till filsystemet och beroende på mängden loggning som utförs av programmet, kan det finnas prestanda.
* devErrorsEnabled
  
   Standardvärdet är false. Inställt på true, iisnode visar när HTTP-statuskoden och Win32-felkod i webbläsaren. Win32-kod är användbara vid felsökning av vissa typer av problem.
* debuggingEnabled (aktivera inte på live produktionsplatsen)
  
    Den här inställningen styr felsökningsfunktionen. Iisnode är integrerad med node-inspector. Genom att aktivera den här inställningen kan aktivera du felsökning av tillämpningsprogrammet nod. När den här inställningen är aktiverad kommer iisnode utforma nödvändiga node-inspector-filer i 'debuggerVirtualDir'-katalogen på den första debug-begäranden till programmet nod. Du kan läsa in node-inspector genom att skicka en begäran till http://yoursite/server.js/debug. Du kan styra debug URL-segment med inställningen 'debuggerPathSegment'. Som standard debuggerPathSegment = 'debug ”. Du kan ange ett GUID, till exempel så att det är svårare att identifieras av andra.
  
    Kontrollera detta [länken](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) för mer information om hur du felsöker.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenarier och rekommendationer/felsökning
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Node-App är att för många utgående anrop.
Många program kan upprätta utgående anslutningar som en del av deras regelbunden drift. Till exempel när en begäran kommer, kan appen nod kontakta en REST-API någon annanstans och hämta viss information vid bearbetning av begäran. Du kan använda en keep-alive agent när du anropar http eller https. Exempelvis kan också använda modulen agentkeepalive som agent keep-alive när du gör dessa utgående anrop. Detta säkerställer att sockets återanvänds på ditt Azure webapp VM och minskar den för att skapa nya sockets för varje utgående begäran. Det gör också till att du använder mindre antal sockets för att göra många utgående förfrågningar och därför du inte överstiger maxsocket som allokeras per virtuell dator. Rekommendationerna i Azure-webbprogram är att värdet agentKeepAlive maxsocket totalt 160 sockets per virtuell dator. Det innebär att om du har fyra node.exe körs på den virtuella datorn du vill ange agentKeepAlive maxsocket till 40 per node.exe, vilket är 160 Totalt per virtuell dator.

Exempel [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfiguration:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Det här exemplet förutsätter att du har 4 node.exe körs på den virtuella datorn. Om du har ett annat antal node.exe körs på den virtuella datorn, måste du ändra maxsocket ange därefter.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Min noden program förbrukar för mycket CPU.
Du kommer förmodligen en rekommendation från Azure Web Apps på portalen om hög cpu-förbrukning. Du kan också ställa in Övervakare kan du titta på vissa [mått](web-sites-monitor.md). När CPU-användningen på den [Azure Portal instrumentpanelen](../application-insights/app-insights-web-monitor-performance.md), kontrollera MAX värdena för CPU så att du inte missar högsta värden.
I fall där du tror att ditt program förbrukar för mycket CPU och du kan förklara varför profile du tillämpningsprogrammet nod för att ta reda på.

### 
#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>Profilering av noden programmet på Azure-Webbappar med V8-Profiler
Anta exempelvis att du har en hello world-app som du vill profilen på följande sätt:

```
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

Gå till din scm plats https://yoursite.scm.azurewebsites.net/DebugConsole

Du kan se en kommandotolk som visas nedan. Gå till katalogen plats/wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Kör kommandot ”npm installera v8 profileraren”.

Detta ska installera v8 profileraren under noden\_moduler katalogen och alla dess beroenden.
Nu kan redigera din server.js för att profilen ditt program.

```
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

Du kan se att 95% av tiden har förbrukats av funktionen WriteConsoleLog. Detta visar också exakt radnummer och källfiler som har orsakat problemet.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Min noden program förbrukar för mycket minne
Om ditt program förbrukar för mycket minne, visas ett meddelande från Azure-Webbappar på portalen om hög minnesförbrukning. Du kan ställa in Övervakare kan du titta på vissa [mått](web-sites-monitor.md). När minnesanvändningen på de [Azure portalens instrumentpanel](../application-insights/app-insights-web-monitor-performance.md), bör du kontrollera de högsta värdena för minne så att du inte missar högsta värden.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Identifiering av läcka och Heap Diffing för node.js
Du kan använda [nod memwatch](https://github.com/lloyd/node-memwatch) för att identifiera minne läcker.
Du kan installera memwatch precis som v8 profiler och redigera koden för att avbilda och diff heapar att identifiera minnet läcker i ditt program.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Min node.exe är komma avslutats slumpmässigt
Det finns ett par skäl till varför detta skulle kunna förekomma:

1. Programmet som utlöste undantagsfel utan felhantering – Kontrollera d:\\hem\\loggfiler\\programmet\\loggning errors.txt fil information på undantag utlöstes. Den här filen har stack-spårning så att du kan åtgärda baserat på det här programmet.
2. Programmet förbrukar för mycket minne som påverkar andra processer från att komma igång. Om den totala mängden virtuell minnet är nära 100%, din node.exe kan ange avslutar processen manager så att andra processer prova att utföra vissa uppgifter. Kontrollera att programmet inte läcker minne eller om ditt program måste använda en stor mängd minne, skala upp till en större virtuell dator med mycket mer RAM-minne för att åtgärda detta.

### <a name="my-node-application-does-not-start"></a>Node-App startar inte
Om programmet returnerar 500 fel när den startar, kan det finnas flera skäl:

1. Node.exe finns inte på rätt plats. Kontrollera nodeProcessCommandLine inställningen.
2. Huvudsakliga skriptfilen finns inte på rätt plats. Kontrollera web.config och kontrollera att namnet på den huvudsakliga skriptfilen i hanteraravsnittet matchar den huvudsakliga skriptfilen.
3. Web.config konfigurationen är inte korrekt – Kontrollera namn/värden för inställningar.
4. Kallstart – ditt program tar för lång tid att starta. Om ditt program tar längre än (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 sekunder iisnode returnerar ett fel 500. Öka värdena för dessa inställningar så att den matchar ditt program starttiden för att förhindra att iisnode från timeout och returnerar fel 500.

### <a name="my-node-application-crashed"></a>Min nod programmet kraschade
Programmet som utlöste undantagsfel utan felhantering – ta kontroll d:\\hem\\loggfiler\\programmet\\loggning errors.txt fil information på undantag utlöstes. Den här filen har stack-spårning så att du kan åtgärda baserat på det här programmet.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Min noden tar för lång tid att starta (kalla Start)
Den vanligaste orsaken för ett program som tar för lång tid att starta är ett stort antal filer i noden\_moduler. Programmet försöker läsa in de flesta av dessa filer när du startar. Som standard eftersom filerna som finns på nätverksresursen på Azure Web Apps kan inläsning av många filer ta tid.
Vissa lösningar för att göra den här processen snabbare är:

1. Var noga med att du har en platt beroende struktur och inga dubbla beroenden genom att använda npm3 för att installera modulerna.
2. Försök att lazy läsa in noden\_moduler och inte att läsa in alla moduler i början av programmet. Det innebär att anropet till require('module') ska göras när du verkligen behöver det i funktionen försök med hjälp av modulen.
3. Azure Web Apps innehåller en funktion som kallas lokala cache. Den här funktionen kopieras innehållet från nätverksresursen till den lokala disken på den virtuella datorn. Eftersom filerna är lokal kan inläsningstid för nod\_moduler är mycket snabbare.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE HTTP-status och understatus
Detta [källfilen](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) visar alla möjliga status/understatus kombinationer iisnode kan gå tillbaka om ett fel inträffar.

Aktivera FREB för ditt program för att se win32-felkod (vara säker på att du aktiverar FREB bara på icke-produktionsmiljöer av prestandaskäl).

| HTTP-Status | HTTP-status | Möjlig orsak? |
| --- | --- | --- |
| 500 |1000 |Det fanns vissa problem skickar ut begäran att IISNODE – kontrollera om node.exe startades. Har det gick kraschat node.exe vid start. Kontrollera konfigurationen av web.config innehåller fel. |
| 500 |1001 |-Win32Error 0x2 - App inte svarar på URL: en. Kontrollera regler för URL-omskrivning eller kontrollera om din expressapp har rätt vägar som definierats. -Win32Error 0x6d – namngiven pipe är upptagen – Node.exe kan ta emot inte begäranden eftersom pipe är upptaget. Kontrollera hög cpu-användning. -Andra fel – kontrollera om node.exe kraschade. |
| 500 |1002 |Node.exe kraschat – Kontrollera d:\\hem\\loggfiler\\loggning errors.txt för stack-spårning. |
| 500 |1003 |Skicka konfiguration problemet – bör aldrig visas men om du gör en namngiven pipe-konfigurationen är felaktig. |
| 500 |1004-1018 |Ett fel uppstod när begäran skickades eller bearbeta svaret till eller från node.exe. Kontrollera om node.exe kraschade. Kontrollera d:\\hem\\loggfiler\\loggning errors.txt för stack-spårning. |
| 503 |1000 |Inte tillräckligt med minne för att allokera mer namngivna pipe-anslutningar. Kontrollera varför appen förbrukar för mycket minne. Kontrollera maxConcurrentRequestsPerProcess värdet. Om du har många begäranden inte och öka värdet för att förhindra det här felet. |
| 503 |1001 |Begäran gick inte att sända node.exe eftersom programmet återvinning. När programmet har återvunnits ska begäranden omdirigeras normalt. |
| 503 |1002 |Kontrollera win32-felkod faktiska orsak – begäran kunde inte skickas till en node.exe. |
| 503 |1003 |Namngiven pipe är upptagen – kontrollera om noden förbrukar för mycket Processorkraft |

Det finns en inställning i NODE.exe kallas nod\_väntar\_PIPE\_INSTANSER. Det här värdet är 4 som standard utanför Azure Web Apps. Det innebär att node.exe kan endast accepterar begäranden som fyra samtidigt på en namngiven pipe. Det här värdet anges till 5 000 på Azure Web Apps. Det här värdet ska vara bra att de flesta noden program som körs på Azure Web Apps. Du bör inte se 503.1003 på Azure-Webbappar på grund av högt värde för NODEN\_väntar\_PIPE\_INSTANSER.  |

## <a name="more-resources"></a>Fler resurser
Följa dessa länkar om du vill veta mer om node.js-program i Azure App Service.

* [Kom igång med Node.js-Webbappar i Azure App Service](app-service-web-get-started-nodejs.md)
* [Felsöka en Node.js-webbapp i Azure App Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center](../nodejs-use-node-modules-azure-apps.md)
* [Utforska superhemliga Kudu-felsökningskonsolen](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

