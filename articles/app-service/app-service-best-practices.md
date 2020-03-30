---
title: Metodtips
description: Lär dig metodtips och vanliga felsökningsscenarier för din app som körs i Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: ded812d5d7a0440466e7284b56c90965ea00406e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768494"
---
# <a name="best-practices-for-azure-app-service"></a>Metodtips för Azure App Service
I den här artikeln sammanfattas metodtips för användning av [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a><a name="colocation"></a>Samlokalisering
När Azure-resurser som utgör en lösning, till exempel en webbapp och en databas, finns i olika regioner, kan den ha följande effekter:

* Ökad latens i kommunikationen mellan resurser
* Monetära avgifter för utgående dataöverföring mellan regioner som anges på [azure-prissidan](https://azure.microsoft.com/pricing/details/data-transfers).

Samlokalisering i samma region är bäst för Azure-resurser som utgör en lösning som en webbapp och en databas eller ett lagringskonto som används för att lagra innehåll eller data. När du skapar resurser, se till att de är i samma Azure-region om du inte har specifika affärs- eller designorsak för dem att inte vara. Du kan flytta en App Service-app till samma region som databasen med hjälp av [appen Tjänst kloningsfunktion](app-service-web-app-cloning.md) som för närvarande är tillgänglig för Premium App Service Plan-appar.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>När appar förbrukar mer minne än förväntat
När du märker att en app förbrukar mer minne än förväntat enligt övervaknings- eller tjänstrekommendationer bör du tänka på [funktionen Automatisk läkning av apptjänsten](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Ett av alternativen för funktionen Automatisk läkning är att vidta anpassade åtgärder baserat på ett minneströskel. Åtgärder sträcker sig över spektrumet från e-postmeddelanden till undersökning via minnesdump till begränsning på plats genom att återvinna arbetsprocessen. Automatisk läkning kan konfigureras via web.config och via ett användarvänligt gränssnitt som beskrivs på i detta blogginlägg för [App Service Support Site Extension](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>När appar förbrukar mer CPU än förväntat
När du märker att en app förbrukar mer cpu än förväntat eller får upprepade CPU-toppar som indikeras via övervaknings- eller tjänstrekommendationer, överväg att skala upp eller skala ut App Service-planen. Om ditt program är tillståndskänsligt är uppskalning det enda alternativet, medan om ditt program är tillståndslöst, ger skalning ut dig mer flexibilitet och potential i högre skala. 

Om du vill ha mer information om "tillståndskänsliga" eller "tillståndslösa" program kan du titta på den här videon: [Planera ett skalbart heltäckande program på Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Mer information om alternativ för apptjänstskalning och automatisk skalning finns [i Skala en webbapp i Azure App Service](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>När socketresurserna är
En vanlig orsak till utmattning utgående TCP-anslutningar är användningen av klientbibliotek, som inte implementeras för återanvändning av TCP-anslutningar, eller när ett protokoll på högre nivå som HTTP - Keep-Alive inte används. Granska dokumentationen för vart och ett av biblioteken som refereras av apparna i apptjänstplanen för att säkerställa att de är konfigurerade eller används i koden för effektiv återanvändning av utgående anslutningar. Följ också bibliotekets dokumentationsvägledning för korrekt skapande och frisläppning eller rensning för att undvika läckageanslutningar. Även om sådana klientbiblioteksundersökningar pågår kan påverkan minskas genom att skala ut till flera instanser.

### <a name="nodejs-and-outgoing-http-requests"></a>Nod.js och utgående http-begäranden
När du arbetar med Node.js och många utgående http-förfrågningar är det viktigt att hantera HTTP - Keep-Alive. Du kan använda [agentkeepalive paketet](https://www.npmjs.com/package/agentkeepalive) `npm` för att göra det lättare i din kod.

Hantera alltid `http` svaret, även om du inte gör något i handläggaren. Om du inte hanterar svaret på rätt sätt fastnar ditt program så småningom eftersom inga fler sockets är tillgängliga.

Till exempel när du `http` `https` arbetar med eller paketet:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Om du kör på App Service på Linux på en dator med flera kärnor är en annan bästa praxis att använda PM2 för att starta flera Node.js-processer för att köra ditt program. Du kan göra det genom att ange ett startkommando till din behållare.

Så här startar du till exempel fyra instanser:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>När appsäkerhetskopiorna börjar misslyckas
De två vanligaste orsakerna till att säkerhetskopiering av appar misslyckas är: ogiltiga lagringsinställningar och ogiltig databaskonfiguration. Dessa fel inträffar vanligtvis när det finns ändringar i lagrings- eller databasresurser, eller ändringar för hur du kommer åt dessa resurser (till exempel autentiseringsuppgifter som uppdaterats för den databas som valts i inställningarna för säkerhetskopiering). Säkerhetskopior körs vanligtvis enligt ett schema och kräver åtkomst till lagring (för utmatning av säkerhetskopierade filer) och databaser (för kopiering och läsning av innehåll som ska ingå i säkerhetskopian). Resultatet av att inte komma åt någon av dessa resurser skulle vara konsekvent säkerhetskopieringsfel. 

När säkerhetskopieringsfel inträffar granskar du de senaste resultaten för att förstå vilken typ av fel som inträffar. Om du vill ha fel vid lagringsåtkomst granskar och uppdaterar du lagringsinställningarna som används i säkerhetskopieringskonfigurationen. För fel på databasåtkomst granskar och uppdaterar du anslutningssträngarna som en del av appinställningarna. fortsätt sedan att uppdatera konfigurationen för säkerhetskopiering så att den korrekt inkluderar de nödvändiga databaserna. Mer information om appsäkerhetskopior finns [i Säkerhetskopiera en webbapp i Azure App Service](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>När nya Node.js-appar distribueras till Azure App Service
Standardkonfigurationen för Azure App Service för Node.js-appar är avsedd att bäst passa behoven hos de vanligaste apparna. Om konfigurationen för node.js-appen skulle dra nytta av anpassad justering för att förbättra prestanda eller optimera resursanvändningen för CPU/minne/nätverksresurser läser du [Metodtips och felsökningsguide för nodprogram i Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). I den här artikeln beskrivs de iisnodinställningar som du kan behöva konfigurera för node.js-appen, beskrivs de olika scenarier eller problem som appen kan ställas inför och hur du åtgärdar dessa problem.


## <a name="next-steps"></a>Efterföljande moment
Mer information om metodtips finns i [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) för att ta reda på användbara metodtips som är specifika för din resurs.

- Navigera till din webbapp i [Azure-portalen](https://portal.azure.com).
- Klicka på **Diagnostisera och lösa problem** i vänster navigering, som öppnar App Service Diagnostics.
- Välj startsida **för bästa praxis.**
- Klicka på **Metodtips för tillgänglighet & prestanda** eller **metodtips för optimal konfiguration om** du vill visa appens aktuella tillstånd när det gäller de här metodtipsen.

Du kan också använda den här länken för att `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`direkt öppna App Service Diagnostics för din resurs: .