---
title: Felsöka tillgänglighetstester för Azure Application Insights
description: Felsöka webbtester i Azure Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 94b00a36445b0f4284caba218f6416db726611eb
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255455"
---
# <a name="troubleshooting"></a>Felsökning

Den här artikeln hjälper dig att felsöka vanliga problem som kan uppstå när du använder tillgänglighetsövervakning.

## <a name="ssltls-errors"></a>SSL/TLS-fel

|Meddelande om symptom/felmeddelande| Möjliga orsaker|
|--------|------|
|Det gick inte att skapa SSL/TLS Secure Channel  | SSL-version. Endast TLS 1.0, 1.1 och 1.2 stöds. **SSLv3 stöds inte.**
|TLSv1.2 Postlager: Alert (Nivå: Fatal, Beskrivning: Bad Record MAC)| Se StackExchange tråd för [mer information](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL som misslyckas är till en CDN (Content Delivery Network) | Detta kan bero på en felaktig konfiguration på cdn-skivan |  

### <a name="possible-workaround"></a>Möjlig lösning

* Om webbadresserna som har problem alltid är beroende resurser, rekommenderas att **inaktivera tolkningsberoende begäranden** för webbtestet.

## <a name="test-fails-only-from-certain-locations"></a>Testet misslyckas bara från vissa platser

|Meddelande om symptom/felmeddelande| Möjliga orsaker|
|----|---------|
|Ett anslutningsförsök misslyckades eftersom den anslutna parten inte svarade korrekt efter en viss tid  | Testagenter på vissa platser blockeras av en brandvägg.|
|    |Omdirigering av vissa IP-adresser sker via (belastningsutjämnare, geotrafikhanterare, Azure Express Route.) 
|    |Om du använder Azure ExpressRoute finns det scenarier där paket kan tas bort i fall där [asymmetrisk routning inträffar](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Testfel med ett fel vid fel vid fel vid fel vid protokollbrott

|Meddelande om symptom/felmeddelande| Möjliga orsaker| Möjliga lösningar |
|----|---------|-----|
|Servern har begått ett protokollfel. Section=ResponseHeader Detail=CR måste följas av LF | Detta inträffar när felaktiga rubriker upptäcks. Vissa rubriker kanske inte använder CRLF för att ange slutet av raden, vilket bryter mot HTTP-specifikationen. Application Insights tillämpar den här HTTP-specifikationen och misslyckas med svar med felaktiga rubriker.| a. Kontakta webbplats värd leverantör / CDN leverantör för att åtgärda felaktiga servrar. <br> b. Om misslyckade begäranden är resurser (t.ex. formatfiler, bilder, skript) kan du överväga att inaktivera tolkningen av beroende begäranden. Tänk på, om du gör detta kommer du att förlora möjligheten att övervaka tillgängligheten för dessa filer).

> [!NOTE]
> URL:en kanske inte misslyckas i webbläsare som har en avslappnad validering av HTTP-huvuden. I det här blogginlägget finns en detaljerad förklaring av felet: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Vanliga felsökningsfrågor

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Webbplatsen ser bra ut men testet är felaktigt? Varför varnar Application Insights mig?

   * Har ditt test **parsa beroende begäranden** aktiverat? Det resulterar i en strikt kontroll av resurser som skript, bilder etc. Dessa typer av fel kanske inte märks i en webbläsare. Kontrollera alla bilder, skript, formatmallar och andra filer som lästs in av sidan. Om någon av dem misslyckas rapporteras testet som misslyckat, även om huvud-HTML-sidan läses in utan problem. Om du vill inaktivera testet till sådana resursfel avmarkerar du helt enkelt parse-beroende begäranden från testkonfigurationen.

   * För att minska oddsen för buller från tillfälliga nätverkslypsar etc., se till att aktivera återförsök för testfel konfiguration kontrolleras. Du kan också testa från fler platser och hantera tröskelvärdet för varningsregel i enlighet med detta för att förhindra platsspecifika problem som orsakar onödiga aviseringar.

   * Klicka på någon av de röda punkterna från tillgänglighetsupplevelsen, eller något tillgänglighetsfel från sökutforskaren för att se information om varför vi rapporterade felet. Testresultatet, tillsammans med den korrelerade telemetrin på serversidan (om den är aktiverad) bör hjälpa till att förstå varför testet misslyckades. Vanliga orsaker till tillfälliga problem är nätverks- eller anslutningsproblem.

   * Tog testet time-out? Vi avbryter testerna efter 2 minuter. Om ditt ping- eller flerstegstest tar längre tid än 2 minuter rapporterar vi det som ett fel. Överväg att dela upp testet i flera som kan slutföras under kortare tid.

   * Rapporterade alla platser fel, eller bara några av dem? Om bara vissa rapporterade fel kan bero på problem med nätverk/CDN. Återigen, klicka på de röda prickarna bör hjälpa till att förstå varför platsen rapporterade fel.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Jag fick inte ett e-postmeddelande när aviseringen utlöses, eller löst eller båda?

Kontrollera konfigurationen för klassiska aviseringar för att bekräfta att din e-post visas direkt, eller så är en distributionslista som du är på konfigurerad för att ta emot meddelanden. Om så är fallet kontrollerar du distributionslistekonfigurationen för att bekräfta att den kan ta emot externa e-postmeddelanden. Kontrollera också om e-postadministratören kan ha konfigurerat principer som kan orsaka problemet.

### <a name="i-did-not-receive-the-webhook-notification"></a>Jag fick inte webhook anmälan?

Kontrollera att programmet som tar emot webhook-meddelandet är tillgängligt och bearbetar webhook-begäranden. Se [detta](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) för mer information.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Tillfälligt test misslyckades med ett protokollfel?

Felet (”protokollfel... CR måste följas av LF ”) anger ett problem med servern (eller beroenden). Detta händer när felaktiga huvuden är inställda i svaret. Detta kan orsakas av lastbalanserare eller andra CDN-lösningar. Vissa rubriker kanske inte använder CRLF för att ange slutet av raden, vilket bryter mot HTTP-specifikationen och därför misslyckas validering på .NET WebRequest-nivån. Kontrollera svaret på dekorhuvuden, som kan vara i strid.

> [!NOTE]
> URL:en kanske inte misslyckas i webbläsare som har en avslappnad validering av HTTP-huvuden. I det här blogginlägget finns en detaljerad förklaring av felet: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Jag ser ingen relaterad telemetri på serversidan för att diagnostisera testfel?*

Om du har konfigurerat Application Insights för din app på serversidan kan detta bero på att [sampling](../../azure-monitor/app/sampling.md) pågår. Välj ett annat tillgänglighetsresultat.

### <a name="can-i-call-code-from-my-web-test"></a>Kan jag anropa kod från mitt webbtest?

Nej. Stegen i testet måste finnas i filen .webtest. Och du kan inte anropa andra webbtester eller använda loopar. Men det finns flera plugin-program som kan vara användbara.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Är det någon skillnad mellan ”webbtester” och ”tillgänglighetstester”?

De är synonyma begrepp. Tillgänglighetstest är ett mer allmänt begrepp som innefattar tester med en URL-ping utöver webbtester i flera steg.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Jag vill använda tillgänglighetstester på vår interna server som körs bakom en brandvägg.

   Det finns två möjliga lösningar:

   * Konfigurera din brandvägg att tillåta inkommande förfrågningar från [IP-adresserna för webbtestagenter](../../azure-monitor/app/ip-addresses.md).
   * Skriv koden för att regelbundet testa din interna server. Kör koden i bakgrunden på en testserver bakom brandväggen. Testprocessen kan skicka resultaten till Application Insights med [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API i core-SDK-paketet. Detta kräver att din testserver har utgående åtkomst till Application Insights slutpunkt för inmatning, men detta utgör en mycket mindre säkerhetsrisk än alternativet att tillåta inkommande förfrågningar. Resultaten kommer att visas i tillgänglighet webbtester blad även om upplevelsen kommer att förenklas något från vad som är tillgängligt för tester som skapas via portalen. Anpassade tillgänglighetstester visas också som tillgänglighetsresultat i Analytics, Sök och Mått.

### <a name="uploading-a-multi-step-web-test-fails"></a>Det går inte att överföra ett webbtest med flera steg

Några orsaker till detta kan hända:
   * Det finns en storleksgräns på 300 kB.
   * Loopar stöds inte.
   * Referenser till andra webbtester stöds inte.
   * Datakällor stöds inte.

### <a name="my-multi-step-test-doesnt-complete"></a>Mitt test med flera steg slutförs inte

Det finns en gräns på 100 förfrågningar per test. Dessutom stoppas testet om det körs längre än två minuter.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hur kan jag köra ett test med klientcertifikat?

Detta stöds för närvarande inte.

## <a name="who-receives-the-classic-alert-notifications"></a>Vem får (klassiska) varningsmeddelanden?

Det här avsnittet gäller endast klassiska aviseringar och hjälper dig att optimera dina aviseringar för att säkerställa att endast önskade mottagare får aviseringar. Om du vill förstå mer om skillnaden mellan [klassiska aviseringar](../platform/alerts-classic.overview.md)och den nya aviseringar upplevelsen hänvisas till [varningsöversiktsartikeln](../platform/alerts-overview.md). Om du vill styra aviseringsmeddelandet i de nya aviseringarna använder du [åtgärdsgrupper](../platform/action-groups.md).

* Vi rekommenderar att du använder specifika mottagare för klassiska varningsmeddelanden.

* För aviseringar om fel från X från Y-platser skickas kryssrutan **bulk/grupp,** om det är aktiverat, till användare med administratörs-/medadministratörsroller.  I stort _sett alla_ administratörer av _prenumerationen_ kommer att få meddelanden.

* För aviseringar om tillgänglighetsmått skickas kryssrutan **bulk/grupp** om det är aktiverat, till användare med ägar-, deltagar- eller läsarroller i prenumerationen. I själva verket är _alla_ användare med åtkomst till prenumerationen Application Insights resurs i omfattning och kommer att få meddelanden. 

> [!NOTE]
> Om du för närvarande använder kryssrutan **bulk/grupp** och inaktiverar det kan du inte återställa ändringen.

Använd den nya aviseringsupplevelsen/aviseringar i nära realtid om du behöver meddela användarna baserat på deras roller. Med [åtgärdsgrupper](../platform/action-groups.md)kan du konfigurera e-postmeddelanden till användare med någon av rollerna deltagare/ägare/läsare (inte kombineras tillsammans som ett enda alternativ).

## <a name="next-steps"></a>Nästa steg

* [Webbtestning i flera steg](availability-multistep.md)
* [URL ping tester](monitor-web-app-availability.md)
