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
ms.date: 06/06/2016
ms.author: ranjithr
ms.openlocfilehash: 8f39b5e6faf5f9121ec2abe347f50653c5c3e4f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Bästa praxis och felsökningsguiden för noden program på Azure-Webbappar
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här artikeln får du lära dig rekommenderade metoder och felsökning av [noden program](app-service-web-get-started-nodejs.md) körs på Azure-Webbappar (med [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Var försiktig när du använder för felsökning på webbplatsen produktion. Rekommendation är att felsöka appen på en icke-produktion konfiguration till exempel din mellanlagringsplatsen och när problemet har åtgärdats, byta ut din mellanlagringsplatsen med din produktionsplatsen.
> 
> 

## <a name="iisnode-configuration"></a>IISNODE-konfiguration
Detta [schemafilen](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) visar alla inställningar som kan konfigureras för iisnode. Några av de inställningar som kan användas för ditt program är:

* nodeProcessCountPerApplication
  
    Den här inställningen styr antalet noder processer som startas per IIS-program. Standardvärdet är 1. Du kan starta så många node.exe som din Virtuella core antal genom att ange detta till 0. Rekommenderat värde är 0 för de flesta program så att du kan använda alla kärnor på datorn. Node.exe är en enskild threaded så att en node.exe kommer att använda högst 1 kärna och få maximala prestanda utanför tillämpningsprogrammet nod som du vill använda alla kärnor.
* nodeProcessCommandLine
  
    Den här inställningen anger sökvägen till node.exe. Du kan ange ett värde så att den pekar till node.exe-version.
* maxConcurrentRequestsPerProcess
  
    Den här inställningen styr det maximala antalet samtidiga förfrågningar som skickas av iisnode till varje node.exe. På azure webbappar är standardvärdet för den här oändligt. Du behöver inte bry dig om den här inställningen. Standardvärdet är 1024 utanför azure webbappar. Du kanske vill konfigurera detta beroende på hur många begär programmet hämtar och hur snabbt programmet bearbetar varje begäran.
* maxNamedPipeConnectionRetry
  
    Den här inställningen styr det maximala antalet gånger som iisnode kommer att försöka att anslutningen på en namngiven pipe skicka begäran via till node.exe. Den här inställningen i kombination med namedPipeConnectionRetryDelay avgör den totala tidsgränsen för varje begäran inom iisnode. Standardvärdet är 200 på Azure-Webbappar. Totalt antal tidsgräns i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    Kontrollerar den här inställningen hur lång tid (i ms) iisnode ska vänta mellan varje försök att skicka begäran till node.exe via en namngiven pipe. Standardvärdet är 250ms.
    Totalt antal tidsgräns i sekunder = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    Som standard är den totala tidsgränsen i iisnode på azure webbappar 200 \* 250ms = 50 sekunder.
* logDirectory
  
    Den här inställningen anger den katalog där iisnode loggas stdout/stderr. Standardvärdet är iisnode som är i förhållande till katalogen huvudskriptet (katalog där huvudsakliga server.js finns)
* debuggerExtensionDll
  
    Den här inställningen styr vilken version av node-inspector iisnode ska använda vid felsökning av tillämpningsprogrammet nod. Iisnode-inspector-0.7.3.dll och iisnode inspector.dll för närvarande endast 2 giltiga värden för den här inställningen. Standardvärdet är iisnode-inspector-0.7.3.dll. iisnode-inspector-0.7.3.dll version använder node-inspector-0.7.3 och använder websockets, så du måste aktivera websockets på ditt azure webapp använda den här versionen. Se <http://www.ranjithr.com/?p=98> för mer information om hur du konfigurerar iisnode om du vill använda den nya node-inspector.
* flushResponse
  
    Standardbeteendet för IIS är den buffrar svarsdata för upp till 4MB innan eller till slutet av svar, beroende på vilket som kommer först. iisnode erbjuder en konfigurationsinställning kan åsidosätta detta beteende: Om du vill rensa ett fragment av text för svar entitet som iisnode tar emot det från node.exe, måste du ange den iisnode/@flushResponse attribut i web.config på 'true':
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    Genom att aktivera bokföring för varje fragment av text för svar entitet läggs prestanda försämras som minskar genomflödet i systemet med ~ 5% (från och med v0.1.13), så det är bäst att definiera omfattningen av den här inställningen bara till slutpunkter som kräver svar streaming (t.ex. med den <location> elementet i web.config)
  
    Utöver detta, behöver för direktuppspelning av program, du också ställa in responseBufferLimit för iisnode-hanteraren på 0.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Detta är en lista över filer som ska övervakas för ändringar som gäller avgränsas med semikolon. En ändring av en fil utlöser återvinning. Varje post består av ett valfritt katalognamnet samt nödvändiga filnamn som är i förhållande till den katalog där programmets startpunkt finns. Jokertecken är tillåtna i den fil del endast. Standardvärdet är ”\*. js;web.config”
* recycleSignalEnabled
  
    Standardvärdet är false. Om aktiverad, node-App kan ansluta till en namngiven pipe (miljövariabeln IISNODE\_kontrollen\_PIPE) och skicka ett meddelande om ”återvinning”. Detta innebär att w3wp att återvinna avslutas.
* idlePageOutTimePeriod
  
    Standardvärdet är 0 vilket betyder att den här funktionen är inaktiverad. När anger ett värde större än 0, kommer iisnode sidan ut alla dess underordnade processer varje 'idlePageOutTimePeriod' millisekunder. För att förstå vad sidan ut innebär, referera till den här [dokumentationen](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Den här inställningen kan vara användbart för program som använder mycket minne och vill pageout minne till disk ibland att frigöra vissa RAM-minne.

> [!WARNING]
> Var försiktig när du aktiverar följande konfigurationsinställningarna på program i produktion. Rekommendation är att inte aktivera dem på live produktionsprogram.
> 
> 

* debugHeaderEnabled
  
    Standardvärdet är false. Om värdet är true, iisnode lägger till en HTTP-svar huvud iisnode-debug alla HTTP-svar skickar så huvudvärde iisnode-debug är en URL. Enskilda delar diagnostisk information kan vara uppnår genom att titta på URL-fragment, men en mycket bättre visualisering uppnås genom att öppna URL: en i webbläsaren.
* loggingEnabled
  
    Den här inställningen styr loggning av stdout och stderr av iisnode. Iisnode ska avbilda stdout/stderr från noden processer som startas och skriva till den katalog som anges i inställningen 'logDirectory'. När detta är att aktivera programmet skriver loggar till filsystemet och beroende på mängden loggning som utförs av programmet, kan det finnas prestanda.
* devErrorsEnabled
  
    Standardvärdet är false. När värdet är true, iisnode visas HTTP-statuskod och Win32-felkod i webbläsaren. Win32-kod blir enklare att felsöka vissa typer av problem.
* debuggingEnabled (aktivera inte på live produktionsplatsen)
  
    Den här inställningen styr felsökningsfunktionen. Iisnode är integrerad med node-inspector. Genom att aktivera den här inställningen kan aktivera du felsökning av tillämpningsprogrammet nod. När den här inställningen är aktiverad kommer iisnode layout nödvändiga node-inspector-filer i 'debuggerVirtualDir'-katalogen på den första debug-begäranden till programmet nod. Du kan läsa in node-inspector genom att skicka en begäran till http://yoursite/server.js/debug. Du kan styra debug URL-segment med inställningen 'debuggerPathSegment'. Som standard debuggerPathSegment = 'debug ”. Du kan ange ett GUID, till exempel så att det är svårare att identifieras av andra.
  
    Kontrollera detta [länken](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) för mer information om hur du felsöker.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scenarier och rekommendationer/felsökning
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Node-App är att för många utgående anrop.
Många program kan upprätta utgående anslutningar som en del av deras regelbunden drift. Till exempel när en begäran kommer, kan appen nod kontakta en REST-API någon annanstans och hämta viss information vid bearbetning av begäran. Du kan använda en keep-alive agent när du anropar http eller https. Exempelvis kan också använda modulen agentkeepalive som agent keep-alive när du gör dessa utgående anrop. Detta säkerställer att sockets återanvänds på ditt azure webapp VM och minskar den för att skapa nya sockets för varje utgående begäran. Det gör också till att du använder mindre antal sockets för att göra många utgående förfrågningar och därför du inte överstiger maxsocket som allokeras per virtuell dator. Rekommendation om Azure-Webbappar att värdet agentKeepAlive maxsocket totalt 160 sockets per virtuell dator. Det innebär att om du har 4 node.exe körs på den virtuella datorn du vill ange agentKeepAlive maxsocket till 40 per node.exe som är 160 Totalt per virtuell dator.

Exempel [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfiguration:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Det här exemplet förutsätter att du har 4 node.exe körs på den virtuella datorn. Om du har ett annat antal node.exe körs på den virtuella datorn måste du ändra maxsocket ange därefter.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Min noden program förbrukar för mycket CPU.
Förmodligen visas en rekommendation från Azure-Webbappar på portalen om hög cpu-förbrukning. Du kan också konfigurera Övervakare kan du titta på vissa [mått](web-sites-monitor.md). När du kontrollerar CPU-användningen på den [Azure portalens instrumentpanel](../application-insights/app-insights-web-monitor-performance.md), kontrollera MAX värdena för CPU så att du inte missar ut de högsta värdena.
I fall där du tror att ditt program förbrukar för mycket CPU och du kan förklara varför behöver du profilen node-App.

### 
#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>Profilering av noden programmet på azure webbappar med V8-Profiler
Exempelvis kan säga du har en hello world-app som du vill profil enligt nedan:

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

Kommandotolken visas enligt nedan. Gå till din plats/wwwroot-katalog

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Kör kommandot ”npm installera v8 profiler”

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

Ändringarna kommer profilen WriteConsoleLog-funktionen och sedan skriva profil utdata till 'profile.cpuprofile' fil under din plats wwwroot. Skicka en begäran till programmet. Du ser en 'profile.cpuprofile'-fil som skapats under din plats wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Hämta den här filen och måste du öppna den här filen med Chrome F12 verktyg. Tryck på F12 på chrome, och klicka sedan på fliken ”profiler”. Klicka på ”belastningen”. Välj din profile.cpuprofile-fil som du precis har laddat ned. Klicka på den profil som du just läste in.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Du ser att 95% av tiden har förbrukats av WriteConsoleLog funktionen enligt nedan. Detta visar också exakt radnummer och källfiler som orsakar problemet.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Min noden program förbrukar för mycket minne.
Du kommer förmodligen en rekommendation från Azure-Webbappar på portalen om hög minnesförbrukning. Du kan också konfigurera Övervakare kan du titta på vissa [mått](web-sites-monitor.md). När du kontrollerar minnesanvändningen på de [Azure portalens instrumentpanel](../application-insights/app-insights-web-monitor-performance.md), kontrollera de högsta värdena för minne så att du inte missar ut de högsta värdena.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Identifiering av läcka och Heap Diffing för node.js
Du kan använda [nod memwatch](https://github.com/lloyd/node-memwatch) för att identifiera minne läcker.
Du kan installera memwatch precis som v8 profiler och redigera koden för att avbilda och diff heapar att identifiera minnet läcker i ditt program.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Min node.exe är komma avslutats slumpmässigt
Det finns ett par skäl till varför detta skulle kunna förekomma:

1. Programmet som utlöste undantagsfel utan felhantering – ta kontroll d:\\hem\\loggfiler\\programmet\\loggning errors.txt fil information på undantag utlöstes. Den här filen har stack-spårning så att du kan åtgärda baserat på det här programmet.
2. Programmet förbrukar för mycket minne som påverkar andra processer från att komma igång. Om den totala mängden virtuell minnet är nära 100%, din node.exe kan ange avslutar processen manager så att andra processer prova att utföra vissa uppgifter. Kontrollera att programmet inte läcker minne eller om du programmet verkligen behöver mycket minne, kontrollera att skala upp en större virtuell dator med mycket mer RAM-minne för att åtgärda detta genom.

### <a name="my-node-application-does-not-start"></a>Node-App startar inte
Om programmet returnerar 500 fel vid start, kan det finnas flera skäl:

1. Node.exe finns inte på rätt plats. Kontrollera nodeProcessCommandLine inställningen.
2. Huvudsakliga skriptfilen finns inte på rätt plats. Kontrollera web.config och kontrollera att namnet på den huvudsakliga skriptfilen i hanteraravsnittet matchar den huvudsakliga skriptfilen.
3. Web.config konfigurationen är inte korrekt – Kontrollera namn/värden för inställningar.
4. Kallstart – ditt program tar för lång tid att. Om ditt program tar längre än (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 sekunder iisnode returnerar fel 500. Öka värdena för dessa inställningar så att den matchar ditt program starttiden för att förhindra att iisnode från timeout och returnerar fel 500.

### <a name="my-node-application-crashed"></a>Min nod programmet kraschade
Programmet som utlöste undantagsfel utan felhantering – ta kontroll d:\\hem\\loggfiler\\programmet\\loggning errors.txt fil information på undantag utlöstes. Den här filen har stack-spårning så att du kan åtgärda baserat på det här programmet.

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>Min noden tar för lång tid att (kalla Start)
De vanligaste anledningen är att programmet har en stor mängd filer i noden\_moduler och programmet försöker läsa in de flesta av dessa filer vid start. Som standard eftersom filerna som finns på nätverksresursen på Azure-Webbappar kan inläsning av så många filer ta lite tid.
Vissa lösningar som gör detta snabbare är:

1. Kontrollera att du har en platt beroende struktur och inga dubbla beroenden genom att använda npm3 för att installera modulerna.
2. Försök att lazy läsa in noden\_moduler och inte att läsa in alla moduler vid start. Det innebär att anropet till require('module') bör utföras när du verkligen behöver det i funktionen som du försöker använda modulen.
3. Azure Webbappar innehåller en funktion som kallas lokala cache. Den här funktionen kopieras innehållet från nätverksresursen till den lokala disken på den virtuella datorn. Eftersom filerna är lokal kan inläsningstid för nod\_moduler är mycket snabbare.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE HTTP-status och understatus
Detta [källfilen](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) visas alla möjliga status/understatus kombination iisnode kan returnera vid fel.

Aktivera FREB för ditt program för att se win32-felkod (se till att du aktiverar FREB bara på icke-produktionsmiljöer av prestandaskäl).

| HTTP-Status | HTTP-status | Möjlig orsak? |
| --- | --- | --- |
| 500 |1000 |Det fanns vissa problem skickar ut begäran att IISNODE – kontrollera om node.exe har startats. Node.exe kunde har kraschat vid start. Kontrollera konfigurationen av web.config innehåller fel. |
| 500 |1001 |-Win32Error 0x2 - App inte svarar på URL: en. Kontrollera regler för URL-omskrivning eller om en uttrycklig app har rätt vägar som definierats. -Win32Error 0x6d – namngiven pipe är upptagen – Node.exe kan ta emot inte begäranden eftersom pipe är upptaget. Kontrollera hög cpu-användning. -Andra fel – kontrollera om node.exe kraschade. |
| 500 |1002 |Node.exe kraschat – Kontrollera d:\\hem\\loggfiler\\loggning errors.txt för stack-spårning. |
| 500 |1003 |Skicka konfiguration problemet – bör aldrig visas men om du gör en namngiven pipe-konfigurationen är felaktig. |
| 500 |1004-1018 |Ett fel uppstod när begäran skickades eller bearbeta svaret till eller från node.exe. Kontrollera om node.exe kraschade. Kontrollera d:\\hem\\loggfiler\\loggning errors.txt för stack-spårning. |
| 503 |1000 |Inte tillräckligt med minne för att allokera mer namngivna pipe-anslutningar. Kontrollera varför appen förbrukar för mycket minne. Kontrollera maxConcurrentRequestsPerProcess värdet. Om det är inte oändligt och du har många begäranden, öka värdet för att förhindra det här felet. |
| 503 |1001 |Begäran gick inte att sända node.exe eftersom programmet återvinning. När programmet har återvunnits ska begäranden omdirigeras normalt. |
| 503 |1002 |Kontrollera win32-felkod faktiska orsak – begäran kunde inte skickas till en node.exe. |
| 503 |1003 |Namngiven pipe är upptagen – kontrollera om noden förbrukar för mycket processortid |

Det finns en inställning i NODE.exe kallas nod\_väntar\_PIPE\_INSTANSER. Det här värdet är 4 som standard utanför azure webbappar. Det innebär att node.exe kan endast accepterar begäranden som 4 samtidigt på en namngiven pipe. Det här värdet anges till 5 000 och värdet ska vara bra att de flesta noden program som körs på azure-webbappar på Azure-Webbappar. Du bör inte se 503.1003 på azure webbappar eftersom vi har ett högt värde för NODEN\_väntar\_PIPE\_INSTANSER.  |

## <a name="more-resources"></a>Fler resurser
Följa dessa länkar om du vill veta mer om node.js-program i Azure App Service.

* [Kom igång med Node.js-webbappar i Azure App Service](app-service-web-get-started-nodejs.md)
* [Felsöka en Node.js-webbapp i Azure App Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Använda Node.js-moduler med Azure-program](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js Developer Center](../nodejs-use-node-modules-azure-apps.md)
* [Utforska superhemliga Kudu-felsökningskonsolen](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

