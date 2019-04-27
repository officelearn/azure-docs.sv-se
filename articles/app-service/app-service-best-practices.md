---
title: Metodtips – Azure App Service
description: Läs om metodtips och felsökning för Azure App Service.
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
ms.custom: seodec18
ms.openlocfilehash: fc3749a9ebfbf0319a57b471b6fce9f62042ba27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852690"
---
# <a name="best-practices-for-azure-app-service"></a>Metodtips för Azure App Service
Den här artikeln sammanfattas Metodtips för [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Samordning
När Azure-resurser skapas en lösning, till exempel en webbapp och en databas finns i olika regioner, kan den ha följande effekter:

* Ökad latens i kommunikation mellan resurser
* Monetära avgifter för utgående data transfer över flera regioner enligt vad som anges på den [prissättningssidan för Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Samordning i samma region passar bäst för Azure-resurser skapas en lösning, till exempel en webbapp och en databas eller storage-konto som används för att lagra innehåll eller data. När du skapar resurser kan du kontrollera att de är i samma Azure-region, såvida du inte har specifika eller utforma orsaken till dem ska inte. Du kan flytta en App Service-app till samma region som din databas med hjälp av den [Apptjänst kloning](app-service-web-app-cloning.md) för närvarande tillgängligt för Premium App Service-Plan appar.   

## <a name="memoryresources"></a>När appar förbruka mer minne än förväntat
När du ser en app förbrukar mer minne än förväntat som anges via övervakning eller tjänsten rekommendationer bör du överväga att den [App Service autoreparationsfunktionen](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Något av alternativen för funktionen för automatisk återställning tar anpassade åtgärder baserat på en minnets tröskelvärde. Åtgärder som sträcker sig över spektrum från e-postmeddelanden till undersökning via minnesdump till på platsen lösning genom att återanvända arbetsprocessen. Automatisk återställning kan konfigureras via web.config och via ett eget användargränssnitt enligt beskrivningen i i det här blogginlägget för den [Webbplatstillägg för App Service stöd](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>När appar använder mer Processorkraft än förväntat
När du upptäcker en app förbrukar mer Processorkraft än förväntat eller upplevelser upprepade processoranvändning som anges via övervakning eller tjänsten rekommendationer, Överväg att skala upp eller skala ut App Service-planen. Om ditt program är tillståndskänsliga, är skala upp det enda alternativet om programmet är tillståndslösa, skala ut ger större flexibilitet och högre skala potential. 

Mer information om ”tillståndskänsliga” vs ”tillståndslösa” program kan du titta på den här videon: [Planera en skalbar slutpunkt till slutpunkt-Flernivåapp i Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Mer information om alternativ för skalning och automatisk skalning av App Service finns i [skala en Webbapp i Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>När socketresurser slut
En vanlig orsak till förbrukar utgående TCP-anslutningar är användningen av klientbibliotek som implementeras inte återanvända TCP-anslutningar eller när en högre nivå protokoll som HTTP - Keep-Alive används inte. Läs dokumentationen för varje bibliotek som refereras av appar i din App Service-Plan att se till att de är konfigurerade eller används i koden för effektiv återanvändning av utgående anslutningar. Också följa riktlinjerna för biblioteket-dokumentationen för rätt skapande och versionen eller rensa för att undvika läcker anslutningar. Sådana klienten bibliotek undersökningar är pågår, kan du minimera effekten genom att skala ut till flera instanser.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js och utgående http-begäranden
När du arbetar med Node.js och många utgående http-begäranden kan är hantera HTTP - Keep-Alive viktigt. Du kan använda den [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` så att det blir enklare i din kod.

Hantera alltid den `http` svar, även om du inte gör något i hanteraren. Om du inte hantera svaret korrekt, fastnar ditt program så småningom eftersom inga fler sockets är tillgängliga.

Till exempel när du arbetar med den `http` eller `https` paketet:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Om du kör på App Service i Linux på en dator med flera kärnor, är en annan bästa praxis att använda PM2 för att starta flera Node.js-processer för att köra ditt program. Du kan göra det genom att ange ett startkommando till behållaren.

Till exempel att starta fyra instanser:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>När din app säkerhetskopiera börjar misslyckas
De två vanligaste orsakerna till varför app säkerhetskopiering misslyckas är: ogiltiga Lagringsinställningar och ogiltig konfiguration. Dessa fel sker vanligtvis när det finns ändringar till lagring eller databas resurser eller ändringar för hur du kommer åt dessa resurser (till exempel autentiseringsuppgifter som uppdateras för den markerade i säkerhetskopieringsinställningarna databasen). Säkerhetskopior normalt körs enligt ett schema och kräver åtkomst till (för att mata ut de säkerhetskopierade filerna) och databaser (för kopiera och läsning av innehållet som ska ingå i säkerhetskopieringen). Resultatet för att få åtkomst till någon av dessa resurser inte är konsekvent säkerhetskopieringen har misslyckats. 

Granska de senaste resultaten för att förstå vilken typ av fel som händer när Säkerhetskopieringsfel uppstår. För åtkomst av lagringsfel, granska och uppdatera lagringsinställningarna som används i konfigurationen för säkerhetskopiering. För åtkomst av databasfel, granska och uppdatera dina anslutningar strängar som en del av appen inställningar. Fortsätt sedan med att uppdatera din säkerhetskopieringskonfiguration korrekt med databaserna som krävs. Mer information om säkerhetskopior av appar finns i [säkerhetskopiera en webbapp i Azure App Service](manage-backup.md).

## <a name="nodejs"></a>När nya Node.js-appar har distribuerats till Azure App Service
Azure App Service-standardkonfigurationen för Node.js-appar är avsedd att de passar behoven hos de vanligaste appar. Om konfigurationen för din Node.js-app skulle ha nytta av anpassade justering för att förbättra prestanda eller Optimera resursanvändningen för CPU/minne/nätverksresurser kan du läsa [metodtips och felsökningsguide för nodprogram i Azure-App Tjänsten](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Den här artikeln beskriver iisnode-inställningar du kan behöva konfigurera för din Node.js-app, beskriver olika scenarier eller problem med att din app får och visar hur du kan lösa dessa problem.

