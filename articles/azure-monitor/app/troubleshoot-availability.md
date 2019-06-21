---
title: Felsöka din tillgänglighetstester för Azure Application Insights | Microsoft Docs
description: Felsöka webbtester i Azure Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304946"
---
# <a name="troubleshooting"></a>Felsökning

Den här artikeln hjälper dig att felsöka vanliga problem som kan uppstå när du använder tillgänglighetsövervakning.

## <a name="ssltls-errors"></a>SSL/TLS-fel

|Symtom/felmeddelande| Möjliga orsaker|
|--------|------|
|Gick inte att skapa en säker SSL/TLS-kanal  | SSL version. Endast TLS 1.0, 1.1 och 1.2 stöds. **SSLv3 stöds inte.**
|TLSv1.2 poster lager: Varning (nivå: Allvarligt fel, beskrivning: Bad Record MAC)| Se StackExchange tråd för [mer](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL: en som inte fungerar som ett CDN (Content Delivery Network) | Detta kan orsakas av felaktig konfiguration i din CDN |  

### <a name="possible-workaround"></a>Möjlig lösning

* Om de webbadresser som upplever problemet ska alltid av beroende resurser, rekommenderar vi att inaktivera **parsa beroendebegäranden** för webbtestet.

## <a name="test-fails-only-from-certain-locations"></a>Testet misslyckas bara från vissa platser

|Symtom/felmeddelande| Möjliga orsaker|
|----|---------|
|Anslutningsförsöket misslyckades eftersom den anslutna parten inte svarade efter en viss tidsperiod  | Test-agenterna på vissa platser blockeras av en brandvägg.|
|    |Omdirigering av vissa IP-adresser sker via (belastningsutjämnare, Geo traffic Manager-hanterare, Azure Express Route.) 
|    |Om du använder Azure ExpressRoute, det finns scenarier där paket kan släppas i fall där [asymmetrisk routning sker](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Tillfälligt test misslyckades med ett protokollfel

|Symtom/felmeddelande| Möjliga orsaker|
|----|---------|
protokollet överträdelse CR måste följas av LF | Detta inträffar när felaktiga huvuden har identifierats. Mer specifikt vissa huvuden kanske inte använder CRLF för att ange radslut, vilket överskrider HTTP-specifikationen och därför misslyckas valideringen på .NET WebRequest-nivån.
 || Detta kan också orsakas av belastningsutjämnare eller CDN-nät.

> [!NOTE]
> URL: en kanske inte är felaktig på webbläsare som har en Avslappnad verifiering av HTTP-huvuden. I det här blogginlägget finns en detaljerad förklaring av felet: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Vanliga felsökningsfrågor

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Webbplatsen ser bra ut men visas testet är felaktigt? Varför är Application Insights Varna mig?

   * Har testet **parsa beroendebegäranden** aktiverad? Som resulterar i en strikt kontroll på resurser, till exempel skript, bilder osv. Dessa typer av fel kanske inte är märkbar i en webbläsare. Kontrollera alla bilder, skript, formatmallar och andra filer som lästs in av sidan. Om någon av dem inte kunde rapporteras testet som misslyckat, även om HTML-huvudsidan läses in utan problem. Om du vill desensitize testet till dessa resursfel, avmarkerar du helt enkelt parsa Beroendebegäranden från testkonfigurationen.

   * Se till att aktivera återförsök för misslyckade test configuration kontrolleras för att minska sannolikheten för brus från tillfälliga nätverkssignaler o.s.v. Du kan också testa från fler platser och hantera tröskelvärden för varningsregeln i enlighet med detta att förhindra platsspecifika problem som orsakar onödiga aviseringar.

   * Klicka på någon av de röda punkterna från tillgänglighet-upplevelsen eller underlåtenhet tillgänglighet från Sökutforskaren för att se information om varför vi rapporterade felet. Testresultat för tillsammans med serversidan korrelerad telemetri (om aktiverat) bör att förstå varför testet misslyckades. Vanliga orsaker till problem är problem med nätverket eller anslutning.

   * Gjorde timeout för test? Vi avbryta testerna efter två minuter. Om din ping eller test med flera steg tar längre tid än två minuter kan rapporterar vi det som ett fel. Kan du dela testet till flera värden som kan utföra i kortare varaktighet.

   * Alla platser för att rapportera fel eller bara vissa av dem? Om det bara vissa rapporterade fel kanske den på grund av problem med nätverket/CDN. Igen, klickar på de röda punkterna bör att förstå varför platsen rapporterade fel.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Jag fick inte ett e-postmeddelande när aviseringen utlöses eller matchas eller båda?

Kontrollera konfigurationen för klassiska aviseringar för att bekräfta din e-postadress anges direkt eller en distributionslista som du använder är konfigurerad för att ta emot meddelanden. Om det kontrollerar du lista för distributionskonfiguration att bekräfta att det kan ta emot externa e-postmeddelanden. Kontrollera också om din e-post-administratör kan ha alla principer som konfigurerats som kan orsaka det här problemet.

### <a name="i-did-not-receive-the-webhook-notification"></a>Jag fick ingen webhook-meddelande?

Kontrollera programmet som tar emot webhook-meddelande är tillgänglig och bearbetar webhook-begäranden. Se [detta](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) för mer information.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Tillfälligt test misslyckades med ett protokollfel?

Felet (”protokollfel... CR måste följas av LF ”) anger ett problem med servern (eller beroenden). Detta händer när felaktiga huvuden är inställda i svaret. Detta kan orsakas av lastbalanserare eller andra CDN-lösningar. Mer specifikt kan vissa huvuden inte använder CRLF för att ange radslut, vilket överskrider HTTP-specifikationen och därför misslyckas valideringen på .NET WebRequest-nivån. Kontrollera svaret för att hitta huvuden som kan vara felaktiga.

> [!NOTE]
> URL: en kanske inte är felaktig på webbläsare som har en Avslappnad verifiering av HTTP-huvuden. I det här blogginlägget finns en detaljerad förklaring av felet: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Jag ser inte alla relaterade serversidan telemetri för att diagnostisera testfel? *

Om du har konfigurerat Application Insights för din app på serversidan kan detta bero på att [sampling](../../azure-monitor/app/sampling.md) pågår. Välj en annan tillgänglighetszon resultat.

### <a name="can-i-call-code-from-my-web-test"></a>Kan jag anropa kod från mitt webbtest?

Nej. Stegen i testet måste finnas i filen .webtest. Och du kan inte anropa andra webbtester eller använda loopar. Men det finns flera plugin-program som kan vara användbara.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Är det någon skillnad mellan ”webbtester” och ”tillgänglighetstester”?

De är synonyma begrepp. Tillgänglighetstest är ett mer allmänt begrepp som innefattar tester med en URL-ping utöver webbtester i flera steg.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Jag vill använda tillgänglighetstester på vår interna server som körs bakom en brandvägg.

   Det finns två möjliga lösningar:

   * Konfigurera din brandvägg att tillåta inkommande förfrågningar från [IP-adresserna för webbtestagenter](../../azure-monitor/app/ip-addresses.md).
   * Skriv koden för att regelbundet testa din interna server. Kör koden i bakgrunden på en testserver bakom brandväggen. Testprocessen kan skicka resultaten till Application Insights med [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API i core-SDK-paketet. Detta kräver att din testserver har utgående åtkomst till Application Insights slutpunkt för inmatning, men detta utgör en mycket mindre säkerhetsrisk än alternativet att tillåta inkommande förfrågningar. Resultatet visas inte på bladen för webbtillgänglighetstester, men däremot visas det som tillgänglighetsresultat i Analytics, Sök och Metric Explorer.

### <a name="uploading-a-multi-step-web-test-fails"></a>Det går inte att överföra ett webbtest med flera steg

Några orsaker till detta kan inträffa:
   * Det finns en storleksgräns på 300 kB.
   * Loopar stöds inte.
   * Referenser till andra webbtester stöds inte.
   * Datakällor stöds inte.

### <a name="my-multi-step-test-doesnt-complete"></a>Mitt test med flera steg slutförs inte

Det finns en gräns på 100 förfrågningar per test. Testet stoppas även om den körs längre än två minuter.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hur kan jag köra ett test med klientcertifikat?

Detta stöds för närvarande inte.

## <a name="who-receives-the-classic-alert-notifications"></a>Vem som får aviseringar (klassisk)?

Det här avsnittet gäller för klassiska aviseringar endast och hjälper dig att optimera dina aviseringar till att säkerställa att endast dina önskade mottagare får meddelanden. Vill veta mer om skillnaden mellan [klassiska aviseringar](../platform/alerts-classic.overview.md)och det nya aviseringsgränssnittet referera till den [aviseringar översikten](../platform/alerts-overview.md). För att styra avisering meddelande i de nya aviseringarna uppstår Använd [åtgärdsgrupper](../platform/action-groups.md).

* Vi rekommenderar användning av specifika mottagare för klassiska aviseringar.

* För aviseringar i fel från X utanför Y platser, den **grupp/grupp** kryssrutan alternativet, om aktiverad, skickar till användare med administratör/medadministratör roller.  I stort sett _alla_ administratörer av den _prenumeration_ får meddelanden.

* För aviseringar i tillgänglighetsmått i **grupp/grupp** -alternativet om aktiverad, skickar till användare med ägare, deltagare eller läsare roller i prenumerationen. I praktiken _alla_ användare med åtkomst till prenumerationen Application Insights-resursen omfattas och ska ta emot meddelanden. 

> [!NOTE]
> Om du använder den **grupp/grupp** kryssrutan alternativet, och inaktivera det, kommer du inte kunna återställa ändringen.

Använd de nya upplevelse nära realtid/aviseringarna om du vill meddela användare baserat på deras roller. Med [åtgärdsgrupper](../platform/action-groups.md), du kan konfigurera e-postaviseringar till användare med någon av rollerna deltagare och ägare/läsare (inte kombineras tillsammans som ett alternativ).

## <a name="next-steps"></a>Nästa steg

* [Flerstegstest för webbplatser testning](availability-multistep.md)
* [URL-Pingtest](monitor-web-app-availability.md)
