---
title: Felsöka Azure Application Insights-tillgänglighets test
description: Felsök webbtester i Azure Application insikter. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 04/28/2020
ms.reviewer: sdash
ms.openlocfilehash: 0ac8dd189bee1c1d4f5a7a4d0f7de68b085fbc56
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015340"
---
# <a name="troubleshooting"></a>Felsökning

Den här artikeln hjälper dig att felsöka vanliga problem som kan uppstå när du använder tillgänglighets övervakning.

## <a name="ssltls-errors"></a>SSL/TLS-fel

|Symtom/fel meddelande| Möjliga orsaker|
|--------|------|
|Det gick inte att skapa säker kanal för SSL/TLS  | SSL-version. Endast TLS 1,0, 1,1 och 1,2 stöds. **SSLv3 stöds inte.**
|TLSv 1.2-post lager: varning (nivå: allvarligt, beskrivning: felaktig post MAC)| [Mer information](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)finns i stackexchange-tråden.
|URL: en som inte fungerar är till ett CDN (Content Delivery Network) | Detta kan bero på en felaktig konfiguration i CDN |  

### <a name="possible-workaround"></a>Möjlig lösning

* Om de webb adresser som upplever problemet alltid är beroende av resurser, rekommenderar vi att du inaktiverar **parsa beroende begär Anden** för webb testet.

## <a name="test-fails-only-from-certain-locations"></a>Testet Miss lyckas endast från vissa platser

|Symtom/fel meddelande| Möjliga orsaker|
|----|---------|
|Ett anslutnings försök misslyckades på grund av att den anslutna parten inte svarade korrekt efter en tids period  | Test agenter på vissa platser blockeras av en brand vägg.|
|    |Omdirigering av vissa IP-adresser sker via (belastnings utjämning, geo Traffic Manager, Azure Express Route.) 
|    |Om du använder Azure ExpressRoute finns det scenarier där paket kan släppas i fall där [asymmetrisk routning sker](../../expressroute/expressroute-asymmetric-routing.md).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Test fel med ett protokoll fel

|Symtom/fel meddelande| Möjliga orsaker| Möjliga lösningar |
|----|---------|-----|
|Servern genomförde ett protokoll fel. Section = ResponseHeader detail = CR måste följas av LF | Detta inträffar när felaktiga rubriker identifieras. Mer specifikt kanske vissa huvuden inte använder CRLF för att ange slutet på raden, vilket strider mot HTTP-specifikationen. Application Insights tillämpar den här HTTP-specifikationen och Miss lyckas svar med felaktiga huvuden.| a. Kontakta värd leverantören för webbplatsen/CDN-providern för att åtgärda de felande servrarna. <br> b. Om de misslyckade förfrågningarna är resurser (t. ex. formatfiler, bilder, skript) kan du överväga att inaktivera parsningen av beroende begär Anden. Tänk på att om du gör detta kommer du att förlora möjligheten att övervaka tillgängligheten för dessa filer.

> [!NOTE]
> URL: en kan inte Miss lyckas i webbläsare som har en avslappnad verifiering av HTTP-huvuden. I det här blogginlägget finns en detaljerad förklaring av felet: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Vanliga fel söknings frågor

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Webbplatsen ser bra ut men testet är felaktigt? Varför är Application Insights Avisera mig?

   * Har testet en **analys av beroende begär Anden** aktiverade? Detta resulterar i en strikt kontroll av resurser, till exempel skript, bilder osv. Dessa typer av problem kanske inte märks i en webbläsare. Kontrollera alla bilder, skript, formatmallar och andra filer som lästs in av sidan. Om någon av dem Miss lyckas rapporteras testet som misslyckat, även om huvud-HTML-sidan läses in utan problem. Om du vill desensitize testet till sådana resurs haverier avmarkerar du bara parsa beroende begär Anden från test konfigurationen.

   * För att minska strider av brus från tillfälliga nätverks signaler osv. kontrol lera att aktivera återförsök för konfiguration av test Miss lyckas. Du kan också testa från fler platser och hantera tröskelvärdet för varnings regeln för att förhindra att platsspecifika problem orsakar onödiga aviseringar.

   * Klicka på någon av de röda punkterna från tillgänglighets upplevelsen eller eventuella tillgänglighets problem från Sök Utforskaren för att se information om varför vi rapporterade problemet. Test resultatet, tillsammans med den korrelerade Telemetrin på Server sidan (om det är aktiverat) bör hjälpa till att förstå varför testet misslyckades. Vanliga orsaker till tillfälliga problem är nätverks-eller anslutnings problem.

   * Gjorde tids gränsen för testet? Vi avbryter tester efter 2 minuter. Om ditt ping-eller multi-Step-Test tar längre tid än två minuter rapporterar vi det som ett haveri. Överväg att bryta testet till flera som kan slutföras inom kortare varaktighet.

   * Rapporterades alla platser eller bara några av dem? Om det bara finns rapporterade fel kan det bero på problem med nätverket/CDN. Återigen bör du ta reda på varför platsen rapporterade fel genom att klicka på de röda punkterna.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Jag fick inget e-postmeddelande när aviseringen utlöstes eller matchades eller båda?

Kontrol lera den klassiska aviserings konfigurationen för att bekräfta att ditt e-postmeddelande visas direkt eller att en distributions lista som du är på är konfigurerad för att ta emot meddelanden. Om så är fallet kontrollerar du konfigurationen av distributions listan för att bekräfta att den kan ta emot externa e-postmeddelanden. Kontrol lera också om din e-postadministratör kan ha några konfigurerade principer som kan orsaka det här problemet.

### <a name="i-did-not-receive-the-webhook-notification"></a>Jag fick inte webhook-meddelandet?

Kontrol lera att det program som tar emot webhook-meddelandet är tillgängligt och att webhook-begärandena bearbetas. Se [det här](../platform/alerts-log-webhook.md) för mer information.

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Jag får 403 förbjudna fel, vad innebär det?

Det här felet indikerar att du behöver lägga till brand Väggs undantag för att tillåta tillgänglighets agenter att testa mål-URL: en. En fullständig lista över vilka agent-IP-adresser som tillåts finns i [artikeln om IP-undantag](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Tillfälligt test misslyckades med ett protokollfel?

Felet (”protokollfel... CR måste följas av LF ”) anger ett problem med servern (eller beroenden). Detta händer när felaktiga huvuden är inställda i svaret. Detta kan orsakas av lastbalanserare eller andra CDN-lösningar. Mer specifikt kanske vissa huvuden inte använder CRLF för att indikera slut på rad, vilket strider mot HTTP-specifikationen och därför inte kan verifiera verifieringen på .NET-webbegäran-nivån. Kontrol lera svaret på dekor rubriker, vilket kan vara en överträdelse.

> [!NOTE]
> URL: en kan inte Miss lyckas i webbläsare som har en avslappnad verifiering av HTTP-huvuden. I det här blogginlägget finns en detaljerad förklaring av felet: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Jag kan inte se någon relaterad telemetri på Server sidan för att diagnostisera test fel? *

Om du har konfigurerat Application Insights för din app på serversidan kan detta bero på att [sampling](./sampling.md) pågår. Välj ett annat tillgänglighets resultat.

### <a name="can-i-call-code-from-my-web-test"></a>Kan jag anropa kod från mitt webbtest?

Nej. Stegen i testet måste finnas i filen .webtest. Och du kan inte anropa andra webbtester eller använda loopar. Men det finns flera plugin-program som kan vara användbara.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Är det någon skillnad mellan ”webbtester” och ”tillgänglighetstester”?

De är synonyma begrepp. Tillgänglighetstest är ett mer allmänt begrepp som innefattar tester med en URL-ping utöver webbtester i flera steg.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Jag vill använda tillgänglighetstester på vår interna server som körs bakom en brandvägg.

   Det finns två möjliga lösningar:

   * Konfigurera din brandvägg att tillåta inkommande förfrågningar från [IP-adresserna för webbtestagenter](./ip-addresses.md).
   * Skriv koden för att regelbundet testa din interna server. Kör koden i bakgrunden på en testserver bakom brandväggen. Testprocessen kan skicka resultaten till Application Insights med [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API i core-SDK-paketet. Detta kräver att din testserver har utgående åtkomst till Application Insights slutpunkt för inmatning, men detta utgör en mycket mindre säkerhetsrisk än alternativet att tillåta inkommande förfrågningar. Resultaten visas på bladet tillgänglighet för webbtester, även om upplevelsen kommer att vara något förenklad från vad som är tillgängligt för test som skapats via portalen. Tester av anpassad tillgänglighet visas också som tillgänglighets resultat i analys, sökning och mått.

### <a name="uploading-a-multi-step-web-test-fails"></a>Det går inte att överföra ett webbtest med flera steg

Några orsaker till detta kan inträffa:
   * Det finns en storleksgräns på 300 kB.
   * Loopar stöds inte.
   * Referenser till andra webbtester stöds inte.
   * Datakällor stöds inte.

### <a name="my-multi-step-test-doesnt-complete"></a>Mitt test med flera steg slutförs inte

Det finns en gräns på 100 förfrågningar per test. Testet stoppas också om det körs längre än två minuter.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hur kan jag köra ett test med klientcertifikat?

Detta stöds inte för närvarande.

## <a name="who-receives-the-classic-alert-notifications"></a>Vem får aviseringarna (klassisk)?

Det här avsnittet gäller endast för klassiska varningar och hjälper dig att optimera dina aviserings aviseringar så att endast dina mottagare får aviseringar. Om du vill veta mer om skillnaden mellan [klassiska aviseringar](../platform/alerts-classic.overview.md)och den nya aviserings upplevelsen läser du [artikeln aviserings översikt](../platform/alerts-overview.md). Om du vill kontrol lera aviseringar i den nya aviserings upplevelsen använder du [Åtgärds grupper](../platform/action-groups.md).

* Vi rekommenderar att du använder vissa mottagare för klassisk aviserings aviseringar.

* För aviseringar vid haverier från X av Y-platser visas kryss rutan **Mass-/grupp** alternativ, om aktive rad, skickas till användare med administratörs-och medadministratörs roller.  I princip får _alla_ administratörer av _prenumerationen_ meddelanden.

* För varningar om tillgänglighets mått är kryss rutan **Mass-/grupp** alternativ om aktive rad, skickas till användare med rollen ägare, deltagare eller läsare i prenumerationen. I praktiken är _alla_ användare som har åtkomst till prenumerationen Application Insightss resursen inom räckvidden och får meddelanden. 

> [!NOTE]
> Om du för närvarande använder alternativet **Mass-/grupp** incheckning, och inaktiverar det, kommer du inte att kunna återställa ändringen.

Använd aviseringarna nya aviseringar/nästan-real tid om du behöver meddela användarna baserat på deras roller. Med [Åtgärds grupper](../platform/action-groups.md)kan du konfigurera e-postaviseringar till användare med någon av rollerna deltagare/ägare/läsare (som inte kombineras tillsammans som ett enda alternativ).

## <a name="next-steps"></a>Nästa steg

* [Webb testning med flera steg](availability-multistep.md)
* [Ping-test för URL](monitor-web-app-availability.md)

