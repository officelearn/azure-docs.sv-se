---
title: Metodtips för Azure App Service
description: Läs om bästa praxis och felsöka problem med Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 7c5eb6190d4a4cdfa47779d2c4d7aadac5a2fb80
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
ms.locfileid: "27868326"
---
# <a name="best-practices-for-azure-app-service"></a>Metodtips för Azure App Service
Den här artikeln sammanfattar Metodtips för [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Samordning
När Azure-resurser genom att skriva en lösning, till exempel ett webbprogram och en databas finns i olika regioner, kan den ha följande effekter:

* Ökad latens kommunikationen mellan resurser
* Monetära kostnader för utgående data transfer cross-region som anges på den [Azure sida med priser](https://azure.microsoft.com/pricing/details/data-transfers).

Det är bäst för Azure-resurser genom att skriva en lösning, till exempel ett webbprogram och en databas eller lagring kontot som används för att lagra innehåll eller samordning i samma region. När du skapar resurser, se till att de finns i samma Azure-region om du inte har särskilda business eller utforma orsaken till dem ska inte. Du kan flytta en Apptjänst-app till samma region som din databas med hjälp av den [Apptjänst funktionen kloning](app-service-web-app-cloning.md) tillgängliga för Premium Apptjänstplan appar.   

## <a name="memoryresources"></a>När appar förbruka mer minne än väntat
När du upptäcker att en app förbrukar mer minne än förväntat som anges via övervakning eller tjänsten rekommendationer bör du överväga att den [App Service automatisk återställning funktionen](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Något av alternativen för funktionen för automatisk återställning tar anpassade åtgärder baserat på ett tröskelvärde för minne. Åtgärder span spektrumet från e-postmeddelanden till undersökningen via minnesdump till på platsen minskning av återvinning för arbetsprocessen. Automatisk återställning kan konfigureras via web.config och via ett eget användargränssnitt enligt beskrivningen i i det här blogginlägget för den [App webbtjänsttillägget Support webbplats](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>När appar konsumera mycket mer Processorkraft än väntat
När du upptäcker en app förbrukar mer CPU än förväntat eller upplevelser upprepade processoranvändning som anges via övervakning eller tjänsten rekommendationer Överväg att skala upp eller skala ut App Service-plan. Om ditt program är tillståndskänslig är skala upp det enda alternativet om ditt program är tillståndslös, skala ut ger mer flexibilitet och högre risk för skalan. 

Mer information om ”tillståndskänslig” eller ”tillståndslösa” program kan du titta på den här videon: [planera en skalbar slutpunkt till slutpunkt Flernivåapp på Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Mer information om alternativ för skalning och autoskalning i App Service finns [skala en Webbapp i Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>När socketen resurserna är uttömda
En vanlig orsak till lång körningstid förbrukar utgående TCP-anslutningar är användning av klientbibliotek implementeras inte återanvända TCP-anslutningar eller när en högre nivå protokoll, till exempel HTTP - Keep-Alive används inte. Granska dokumentationen för varje bibliotek som refereras av appar i din App Service-Plan så konfigureras eller komma åt i koden för effektiv användning av utgående anslutningar. Du kan även följa biblioteket dokumentationen vägledning för rätt skapas och versionen eller rensning för att undvika läcka anslutningar. När sådana klienten bibliotek utredningar som pågår, kan påverkan undvikas genom att skala ut till flera instanser.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js och utgående http-begäranden
När du arbetar med Node.js och många utgående http-begäranden är med HTTP - Keep-Alive viktigt. Du kan använda den [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` så att det blir enklare i koden.

Hantera alltid den `http` svar, även om du inte gör någonting i hanteraren. Om du inte hantera svaret korrekt, hämtar programmet fastnat slutligen eftersom ingen mer sockets är tillgängliga.

Till exempel när du arbetar med den `http` eller `https` paketet:

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Om du kör på Apptjänst i Linux på en dator med flera kärnor, är en annan bästa praxis att använda PM2 för att starta flera Node.js-processer för att köra programmet. Du kan göra det genom att ange ett startkommando för att din behållare.

Till exempel för att starta fyra instanser:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>När en app Säkerhetskopiera startar misslyckas
De två vanligaste orsakerna varför app säkerhetskopieringen misslyckas är: Ogiltig lagringsinställningarna och konfiguration av ogiltig databas. Dessa fel inträffa vanligtvis när det finns ändringar till lagring eller databasen resurser eller ändringar att få åtkomst till dessa resurser (till exempel autentiseringsuppgifter som uppdateras med den valda i inställningarna för säkerhetskopiering databasen). Säkerhetskopieringar normalt körs enligt ett schema och kräver åtkomst till lagring (för att mata ut de säkerhetskopierade filerna) och databaser (för kopiera och läsning av innehållet som ska ingå i säkerhetskopian). Resultat för att komma åt någon av dessa resurser inte är konsekvent säkerhetskopieringen har misslyckats. 

När det uppstår fel vid säkerhetskopiering, granska senaste resultat för att förstå vilken typ av fel händer. För åtkomst av lagringsfel, granska och uppdatera lagringsinställningarna som används i konfigurationen för säkerhetskopiering. För databasfel åtkomst, granska och uppdatera din anslutningar strängar som en del av appinställningar. Fortsätt sedan att uppdatera konfigurationen för säkerhetskopieringen korrekt med databaserna som krävs. Mer information om appen säkerhetskopior finns [säkerhetskopiera en webbapp i Azure App Service](web-sites-backup.md).

## <a name="nodejs"></a>När nya Node.js-appar distribueras till Azure App Service
Azure Apptjänst standardkonfigurationen för Node.js-appar är avsedd som bäst passar behoven hos de vanligaste appar. Om konfigurationen för Node.js-appen skulle dra nytta av anpassade inställning för att förbättra prestanda eller Optimera resursanvändningen för CPU/minne/nätverksresurser, se [metodtips och felsökningsguiden för noden program i Azure App Tjänsten](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Den här artikeln beskriver iisnode inställningar du kan behöva konfigurera för din Node.js-app, beskriver de olika scenarierna eller problem att din app kan hantera och visar hur du kan lösa dessa problem.

