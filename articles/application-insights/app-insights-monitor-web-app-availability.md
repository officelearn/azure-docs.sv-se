---
title: Övervaka tillgänglighet och svarstider på valfri webbplats | Microsoft Docs
description: Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: cf5f85d4f7e9dbe1278e9dc4290967d781b398f3
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632833"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Övervaka tillgänglighet och svarstider på valfri webbplats
När du har distribuerat din webbapp eller webbplats till en server kan du konfigurera tester för att övervaka appens tillgänglighet och svarstider. [Azure Application Insights](app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Den varnar dig om programmet inte svarar eller svarar långsamt.

Du kan konfigurera tillgänglighetstester för valfri HTTP- eller HTTPS-slutpunkt som kan nås från det offentliga Internet. Du behöver inte lägga till något till den webbplats som du testar. Det behöver inte ens vara din webbplats: du kan testa en REST API-tjänst som du är beroende av.

Det finns två typer av tillgänglighetstester:

* [URL-pingtest](#create): Ett enkelt test som du kan skapa på Azure-portalen.
* [Flerstegstest för webbplatser](#multi-step-web-tests): Ett test som du skapar i Visual Studio Enterprise och laddar upp till portalen.

Du kan skapa upp till 100 tillgänglighetstester per programresurs.


## <a name="create"></a>Öppna en resurs för dina tillgänglighetstestrapporter

**Om du redan har konfigurerat Application Insights** för din webbapp öppnar du dess Application Insights-resurs i [Azure Portal](https://portal.azure.com).

**Eller om du vill se dina rapporter i en ny resurs** går du till [Azure Portal](https://portal.azure.com) och skapar en Application Insights-resurs.

![Nytt > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Klicka på **alla resurser** för att öppna översiktsbladet för den nya resursen.

## <a name="setup"></a>Skapa ett URL-pingtest
Öppna bladet Tillgänglighet och lägg till ett test.

![Fyll åtminstone i URL:en för din webbplats](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **URL: en** kan vara en webbsida som du vill testa, men den måste vara synlig från Internet. URL: en kan innehålla en frågesträng. Du kan arbeta med din databas om du vill. Om URL-adressen matchar en omdirigering följer vi den upp till tio omdirigeringar.
* **Parsa beroendebegäranden**: om det här alternativet är markerat begärs bilder, skript, filer och andra filer som ingår i webbsidan under testet. Den registrerade svarstiden innefattar den tid det tar att hämta dessa filer. Testet misslyckas om dessa resurser inte kan laddas ned inom tidsgränsen för hela testet. Om alternativet inte är markerat begärs endast filen på den URL som du har angett i testet.

* **Aktivera återförsök**: Om det här alternativet är markerat och testet misslyckas görs ett nytt försök efter en liten stund. Ett fel rapporteras endast om tre på varandra följande försök misslyckas. Efterföljande tester utförs sedan med den vanliga testfrekvensen. Återförsök pausas tillfälligt tills nästa lyckade test. Den här regeln tillämpas separat på varje testplats. Vi rekommenderar det här alternativet. I genomsnitt försvinner ca 80 % av felen vid återförsök.

* **Testfrekvens**: Anger hur ofta testet körs från varje testplats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.

* **Testplatser** är de platser som våra servrar skickar webbförfrågningar till din URL från. Välj mer än en så att du kan skilja mellan problem på din webbplats och nätverksproblem. Du kan välja upp till 16 platser.

> [!NOTE] 
> * Vi rekommenderar testning från flera platser, att förhindra falsklarm som följd av tillfälliga problem med en viss plats.
> * Aktivera alternativet ”parsa beroende begäranden” resulterar i en striktare kontroll. Testet misslyckas, i de fall som inte får märkbar när du bläddrar efter platsen manuellt.

* **Villkor för lyckad test**:

    **Timeout för test**: Minska det här värdet om du vill få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.

    **HTTP-svar**: Den returnerade statuskoden som räknas som ett lyckat test. 200 är koden som anger att en normal webbsida har returnerats.

    **Innehållsmatchning**: en sträng, t.ex. ”Välkommen!”. Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras.

## <a name="multi-step-web-tests"></a>Webbtester med flera steg
Du kan övervaka ett scenario med en serie URL:er. Om du till exempel övervakar en försäljningswebbplats kan du testa att det går att lägga till objekt i kundvagnen korrekt.

> [!NOTE] 
> Det utgår en avgift för webbtester med flera steg. [Prisschema](http://azure.microsoft.com/pricing/details/application-insights/).
> 

Om du vill skapa ett test med flera steg spelar du in scenariot med hjälp av Visual Studio Enterprise och laddar sedan upp inspelningen till Application Insights. Application Insights spelar upp scenariot i intervall och verifierar svaren.

> [!NOTE]
> * Du kan inte använda kodade funktioner eller loopar i dina tester. Testet måste ingå i .webtest-skriptet. Du kan dock använda standardplugin-program.
> * Endast engelska tecken stöds i webbtester med flera steg. Om du använder Visual Studio på andra språk måste du uppdatera definitionsfilen för webbtesten för att översätta/exkludera icke-engelska tecken.
>

#### <a name="1-record-a-scenario"></a>1. Spela in ett scenario
Spela in en webbsession med Visual Studio Enterprise.

1. Skapa ett testprojekt för webbprestanda.

    ![Skapa ett projekt i Visual Studio Enterprise från mallen Webbprestanda- och inläsningstest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *Kan du inte se mallen Webbprestanda- och inläsningstest?* - Stäng Visual Studio Enterprise. Öppna **installationsprogrammet för Visual Studio** för att ändra Visual Studio Enterprise-installationen. Välj **Web Performance and load testing tools** (Verktyg för webbprestanda- och inläsningstest) under **Individual Components** (Enskilda komponenter).

2. Öppna filen .webtest och börja inspelningen.

    ![Öppna filen .webtest och klicka på Spela in.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Utför de användaråtgärder som du vill simulera i testet: öppna webbplatsen, lägg till en produkt i kundvagnen och så vidare. Stoppa sedan testet.

    ![Inspelningsverktyget för webbtestet körs i Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Håll scenariot kort. Det finns en gräns på 100 steg och 2 minuter.
4. Redigera testet om du vill:

   * Lägg till valideringar för att kontrollera texten som tas emot och svarskoderna.
   * Ta bort eventuella överflödiga interaktioner. Du kan också ta bort beroende förfrågningar för bilder eller till annons- eller spårningswebbplatser.

     Kom ihåg att du bara kan redigera testskriptet – du kan inte lägga till anpassad kod eller anropa andra webbtester. Infoga inte loopar i testet. Du kan använda standard-plugin-program för webbtester.
5. Kör testet i Visual Studio för att kontrollera att det fungerar.

    En webbläsare öppnas och de åtgärder som du har spelat in upprepas. Kontrollera att det fungerar som förväntat.

    ![Öppna filen .webtest i Visual Studio och klicka på Kör.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Ladda upp webbtestet till Application Insights
1. Skapa ett webbtest på Application Insights-portalen.

    ![Välj Lägg till på bladet Webbtest.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Välj ett flerstegstest och ladda upp filen .webtest.

    ![Välj webbtestet med flera steg.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Ange testplatserna, frekvensen och aviseringsparametrarna på samma sätt som för pingtest.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Använda tid och slumptal i flerstegstest
Anta att du testar ett verktyg som hämtar tidsberoende data, till exempel aktier från ett externt flöde. När du spelar in webbtestet måste du ange specifika tider, men du anger dem som parametrar för testet, StartTime och EndTime.

![Ett webbtest med parametrar.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

När du kör testet vill du att EndTime alltid ska vara den aktuella tiden, och StartTime 15 minuter tidigare.

Du kan parameterisera tider med hjälp av webbtest-plugin-program.

1. Lägg till ett plugin-program för webbtester för varje variabelt parametervärde som du behöver. I verktygsfältet Webbtest väljer du **Lägg till plugin-program för webbtest**.

    ![Välj Lägg till plugin-program för webbtest och välj en typ.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    I det här exemplet ska vi använda två instanser av plugin-programmet för datum och tid. En instans är för ”15 minuter tidigare” och en annan för ”nu”.
2. Öppna egenskaperna för varje plugin-program. Lägg till ett namn och ange att den aktuella tiden ska användas. För den ena anger du Lägg till minuter = -15.

    ![Ange namn, Använd aktuell tid och Lägg till minuter.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Du refererar till plugin-namn genom att använd {{plugin-name}} i parametrarna för webbtestet.

    ![Använd {{plugin-name}} i testparametern.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Ladda upp testet till portalen. De dynamiska värdena används i varje testkörning.


## <a name="monitor"></a>Visa tillgänglighetstestresultat

Efter ett par minuter klickar du på **Uppdatera** för att visa testresultaten. 

![Sammanfattningsresultat på startbladet](./media/app-insights-monitor-web-app-availability/14-availSummary-3.png)

Spridningsdiagrammet visar exempel på testresultat som innehåller information om diagnostiska test. Testmotorn lagrar diagnosinformation för tester som innehåller fel. För lyckade tester lagras diagnosinformation för en delmängd av körningarna. För pekaren över någon av de gröna/röda punkterna för att visa tidsstämpel, varaktighet, plats och namn för testet. Klicka på någon av punkterna i spridningsdiagrammet för att visa detaljerad information om testresultatet.  

Välj ett visst test, en viss plats eller minska tidsperioden för att visa fler resultat för en viss tidsperiod som du är intresserad av. Använd Sökutforskaren för att visa resultat från alla körningar, eller använd analysfrågor om du vill köra anpassade rapporter för dessa data.

Förutom rådataresultat finns även två tillgänglighetsmått i Metrics Explorer: 

1. Tillgänglighet: antal procent av testerna som lyckades av alla testkörningar. 
2. Testets varaktighet: genomsnittlig tid för alla testkörningar.

Du kan använda filter för testnamn och plats om du vill analysera trender för ett visst test och/eller en viss plats.

## <a name="edit"></a>Granska och redigera tester

Välj ett specifikt test på sammanfattningssidan. Där du kan se specifika resultat för testet och redigera eller tillfälligt inaktivera det.

![Redigera eller inaktivera ett webbtest](./media/app-insights-monitor-web-app-availability/19-availEdit-3.png)

Du kanske vill inaktivera tillgänglighetstester eller varningsreglerna som är associerade med dem medan du utför underhåll på tjänsten. 

## <a name="failures"></a>Om du ser fel
Klicka på en röd punkt.

![Klicka på en röd punkt](./media/app-insights-monitor-web-app-availability/open-instance-3.png)

Från ett tillgänglighetstestresultat kan du se transaktionsinformation för alla komponenter. Här kan du:

* Kontrollera de svar som mottas från servern.
* Diagnostisera fel med korrelerad telemetri på serversidan som samlats in under bearbetning av misslyckade tillgänglighetstestet.
* Logga ett problem eller arbetsuppgift i Git eller VSTS för att spåra problemet. Buggen innehåller en länk till den här händelsen.
* Öppna resultatet av webbtestet i Visual Studio.

Lär dig mer om från slutpunkt till slutpunkt-transaktionsdiagnostik uppleva [här](app-insights-transaction-diagnostics.md).

Klicka på raden undantag om du vill se information om server sida undantaget som orsakade syntetiska tillgänglighetstestet misslyckas. Du kan också hämta den [felsökning för ögonblicksbilder](app-insights-snapshot-debugger.md) rikare kod på diagnostik.

![Diagnostik för Server-sida](./media/app-insights-monitor-web-app-availability/open-instance-4.png)

## <a name="alerts"></a> Tillgänglighet aviseringar
Du kan ha följande typer av Varningsregler på tillgänglighetsdata via den klassiska aviseringar:
1. X av Y-platser som rapporterar fel under en viss tidsperiod
2. Sammanställd tillgänglighet procent faller under ett tröskelvärde
3. Genomsnittlig varaktighet ökar utöver ett tröskelvärde

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Avisera om X av Y-platser som rapporterar fel
X utanför Y platser varningsregel är aktiverat som standard i den [nya aviseringar för enhetlig upplevelse](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), när du skapar en ny tillgänglighetstestet. Du kan välja bort genom att välja alternativet ”klassiska” eller välja att inaktivera varningsregeln.

![Skapa upplevelse](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

**Viktiga**: med den [nya enhetliga aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), varningsregel allvarlighetsgrad och notification-inställningar med [åtgärdsgrupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **måste vara** konfigurerats i den aviseringar upplevelse. Utan följande steg ska får du bara meddelanden i portalen. 

1. Klicka på namnet på nya test att gå till information om när du har sparat tillgänglighetstestet. Klicka på ”Redigera avisering” ![redigera efter spara](./media/app-insights-monitor-web-app-availability/editaftersave.png)

2. Ange den önskade allvarlighetsgraden, beskrivning av regel och viktigast av allt – åtgärdsgrupp som har felaviseringar som du vill använda för den här aviseringsregeln.
![Redigera efter spara](./media/app-insights-monitor-web-app-availability/setactiongroup.png)


> [!NOTE]
> * Konfigurera åtgärdsgrupper för att ta emot meddelanden när en avisering utlöses genom att följa stegen ovan. Utan det här steget endast visas i portalen meddelanden när regeln utlöses.
>
### <a name="alert-on-availability-metrics"></a>Avisera om tillgänglighetsmått
Med hjälp av den [nya enhetliga aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), du Avisera om segmenterade sammanställd tillgänglighet och testa samt varaktighet mått:

1. Välj en Application Insights-resurs i mått-upplevelsen och välj ett mått för tillgänglighet: ![tillgänglighet mått val](./media/app-insights-monitor-web-app-availability/selectmetric.png)

2. Konfigurera alternativ på menyn tar dig till den nya miljön där du kan välja specifika test eller platser för att ställa in varningsregel för aviseringar. Du kan också konfigurera åtgärdsgrupper för den här aviseringsregeln här.
    ![Konfiguration av tillgänglighet aviseringar](./media/app-insights-monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Avisera om anpassade analysfrågor
Med hjälp av den [nya enhetliga aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), du kan meddela på [anpassade loggfrågor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Med anpassade frågor kan du meddela på eventuella valfria villkor som hjälper dig att få den mest tillförlitliga signalen av tillgänglighetsproblem. Detta gäller även särskilt, om du skickar anpassade tillgänglighetsresultat med TrackAvailability SDK. 

> [!Tip]
> * Mått på tillgänglighetsdata omfattar alla anpassade tillgänglighetsresultat som du kan skicka genom att anropa vår TrackAvailability SDK. Du kan använda aviseringar vilka mått som stöds till en avisering för anpassade tillgänglighetsresultat.
>

## <a name="dealing-with-sign-in"></a>Hantera inloggning
Om användarna måste logga in i din app kan du välja mellan olika alternativ för att simulera inloggningen, så att du kan testa sidorna bakom inloggningen. Vilken metod du använder beror på vilken typ av säkerhet som tillhandahålls av appen.

I samtliga fall bör du skapa ett konto i ditt program som endast används för testning. Om möjligt begränsar du behörigheterna för det här testkontot så att webbtestningen inte påverkar riktiga användare.

### <a name="simple-username-and-password"></a>Enkelt användarnamn och lösenord
Spela in ett webbtest som vanligt. Ta bort cookies först.

### <a name="saml-authentication"></a>SAML-autentisering
Använd SAML-plugin-programmet som är tillgängligt för webbtester.

### <a name="client-secret"></a>Klienthemlighet
Om din app kräver inloggning med en klienthemlighet använder du det. Azure Active Directory (AAD) är ett exempel på en tjänst som erbjuder inloggning med klienthemligheter. I AAD är klienthemligheten appnyckeln.

Här är ett exempel på ett webbtest för en Azure-webbapp som använder en appnyckel:

![Exempel med en klienthemlighet](./media/app-insights-monitor-web-app-availability/110.png)

1. Hämta en token från AAD med hjälp av en klienthemlighet (AppKey).
2. Extrahera ägartoken från svaret.
3. Anropa API:et med hjälp av ägartoken i auktoriseringshuvudet.

Kontrollera att webbtestet är en riktig klient, dvs. att det har en egen app i AAD, och använd dess klient-ID och appnyckel. Tjänsten som testas har också sin egen app i AAD: appID-URI:n för den här appen visas i webbtestet i fältet ”resource”.

### <a name="open-authentication"></a>Öppen autentisering
Ett exempel på öppen autentisering är inloggning med ett Microsoft- eller Google-konto. Många appar som använder OAuth erbjuder möjligheten att använda en klienthemlighet, så det första du bör göra är att ta reda på detta.

Om testet måste logga in med OAuth är den allmänna riktlinjen att:

* Använda ett verktyg som Fiddler för att undersöka trafiken mellan webbläsaren, autentiseringswebbplatsen och din app.
* Utföra två eller flera inloggningar med olika datorer eller webbläsare, eller med långa intervall (så att token upphör att gälla).
* Genom att jämföra olika sessioner identifiera den token som skickas tillbaka från autentiseringswebbplatsen, som sedan skickas till din appserver efter inloggningen.
* Spela in ett webbtest med hjälp av Visual Studio.
* Parameterisera token genom att ange parametern när token returneras från autentiseraren och använda den i frågan till webbplatsen.
  (Visual Studio försöker parameterisera testet, men kan inte parameterisera token korrekt.)

## <a name="performance-tests"></a>Prestandatester
Du kan köra ett inläsningstest på din webbplats. Som med tillgänglighetstestet kan du skicka antingen enkla begäranden eller begäranden med flera steg från våra platser runtom i världen. Till skillnad från ett tillgänglighetstest skickas många begäranden, som simulerar flera samtidiga användare.

Öppna **Inställningar**, **Prestandatest** från bladet Översikt. När du skapar ett test uppmanas du att ansluta till eller skapa ett Azure DevOps-konto.

När testet är klart visas svarstiderna och slutförandefrekvens.


![Prestandatest](./media/app-insights-monitor-web-app-availability/perf-test.png)

> [!TIP]
> Om du vill se effekterna av ett prestandatest kan du använda [Live Stream](app-insights-live-stream.md) och [Profilerare](app-insights-profiler.md).
>

## <a name="automation"></a>Automation
* [Konfigurera ett tillgänglighetstest automatiskt med hjälp av PowerShell-skript](app-insights-powershell.md#add-an-availability-test).
* Konfigurera en [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) som anropas när en avisering genereras.

## <a name="qna"></a> VANLIGA FRÅGOR OCH SVAR

* *Webbplatsen ser bra ut men visas testet är felaktigt? Varför är Application Insights Varna mig?*

    * Har testet ”parsa beroende begäranden om” aktiverat? Som resulterar i en strikt kontroll på resurser, till exempel skript, bilder osv. Dessa typer av fel kanske inte är märkbar i en webbläsare. Kontrollera alla bilder, skript, formatmallar och andra filer som lästs in av sidan. Om någon av komponenterna inte kunde läsas in rapporteras testet som misslyckat, även om HTML-huvudsidan kan läsas in korrekt. Om du vill minska känsligheten för testet till dessa resursfel, avmarkerar du helt enkelt ”parsa beroende begäranden om” från testkonfigurationen. 

    * Kontrollera att konfigurationen ”aktivera återförsök för misslyckade test” är markerad för att minska sannolikheten för brus från tillfälliga nätverkssignaler o.s.v. Du kan också testa från fler platser och hantera tröskelvärden för varningsregeln i enlighet för att förhindra platsspecifika problem som orsakar onödiga aviseringar.

    * Klicka på någon av de röda punkterna från tillgänglighet-upplevelsen eller underlåtenhet tillgänglighet från Sökutforskaren för att se information om varför vi rapporterade felet. Testresultat för tillsammans med den korrelerad telemetrin på serversidan (om aktiverat) bör att förstå varför testet misslyckades. Vanliga orsaker till problem är problem med nätverket eller anslutning. 

    * Gjorde timeout för test? Vi avbryta testerna efter två minuter. Om din ping eller test med flera steg tar längre tid än två minuter kan rapporterar vi det som ett fel. Kan du dela testet till flera värden som kan utföra i kortare varaktighet.

    * Alla platser för att rapportera fel eller bara vissa av dem? Om det bara vissa rapporterade fel kanske den på grund av problem med nätverket/CDN. Igen, klickar på de röda punkterna bör att förstå varför platsen rapporterade fel.

* *Jag fick inte ett e-postmeddelande när aviseringen utlöses eller matchas eller båda?*

    Kontrollera konfigurationen för klassiska aviseringar för att bekräfta din e-postadress anges direkt eller en distributionslista som du använder är konfigurerad för att ta emot meddelanden. Om det kontrollerar du lista för distributionskonfiguration att bekräfta att det kan ta emot externa e-postmeddelanden. Kontrollera också om din e-post-administratör kan ha alla principer som konfigurerats som kan orsaka det här problemet.

* *Jag fick ingen webhook-meddelande?*

    Kontrollera programmet som tar emot webhook-meddelande är tillgänglig och bearbetar webhook-begäranden. Se [detta](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) för mer information.

* *Tillfälligt test misslyckades med ett protokollfel?*

    Felet (”protokollfel... CR måste följas av LF ”) anger ett problem med servern (eller beroenden). Detta händer när felaktiga huvuden är inställda i svaret. Detta kan orsakas av lastbalanserare eller andra CDN-lösningar. Mer specifikt kanske vissa huvuden inte använder CRLF för att ange radslut vilket överskrider HTTP-specifikationen och därför misslyckas valideringen på .NET WebRequest-nivån. Kontrollera svaret för att hitta huvuden som kan vara felaktiga.
    
    Obs: URL:en kanske inte är felaktig på webbläsare som har en avslappnad verifiering av HTTP-huvuden. I det här blogginlägget finns en detaljerad förklaring av felet: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
    
* *Jag ser inte någon relaterad telemetri på serversidan för att diagnostisera testfel?*
    
    Om du har konfigurerat Application Insights för din app på serversidan kan detta bero på att [sampling](app-insights-sampling.md) pågår. Välj en annan tillgänglighetszon resultat.

* *Kan jag anropa kod från mitt webbtest?*

    Nej. Stegen i testet måste finnas i filen .webtest. Och du kan inte anropa andra webbtester eller använda loopar. Men det finns flera plugin-program som kan vara användbara.

* *Stöds HTTPS?*

    Vi stöder TLS 1.1 och TLS 1.2.
* *Är det någon skillnad mellan ”webbtester” och ”tillgänglighetstester”?*

    De är synonyma begrepp. Tillgänglighetstest är ett mer allmänt begrepp som innefattar tester med en URL-ping utöver webbtester i flera steg.
    
* *Jag vill använda tillgänglighetstester på vår interna server som körs bakom en brandvägg.*

    Det finns två möjliga lösningar:
    
    * Konfigurera din brandvägg att tillåta inkommande förfrågningar från [IP-adresserna för webbtestagenter](app-insights-ip-addresses.md).
    * Skriv koden för att regelbundet testa din interna server. Kör koden i bakgrunden på en testserver bakom brandväggen. Testprocessen kan skicka resultaten till Application Insights med [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API i core-SDK-paketet. Detta kräver att din testserver har utgående åtkomst till Application Insights slutpunkt för inmatning, men detta utgör en mycket mindre säkerhetsrisk än alternativet att tillåta inkommande förfrågningar. Resultatet visas inte på bladen för webbtillgänglighetstester, men däremot visas det som tillgänglighetsresultat i Analytics, Sök och Metric Explorer.

* *Det går inte att överföra ett flerstegstest för webbplatser*

    Några orsaker till detta kan inträffa:
    * Det finns en storleksgräns på 300 kB.
    * Loopar stöds inte.
    * Referenser till andra webbtester stöds inte.
    * Datakällor stöds inte.

* *Mitt test med flera steg slutförs inte*

    Det finns en gräns på 100 förfrågningar per test. Testet stoppas även om den körs längre än två minuter.

* *Hur kan jag köra ett test med klientcertifikat?*

    Det stöds tyvärr inte.


## <a name="next"></a>Nästa steg
[Sök i diagnostikloggar][diagnostic]

[Felsökning][qna]

[IP-adresser för webbtestagenter](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
