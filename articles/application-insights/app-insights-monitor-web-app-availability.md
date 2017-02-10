---
title: "Övervaka tillgänglighet och svarstider på valfri webbplats | Microsoft Docs"
description: "Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 003db6e1479be1007dd292555ce5997f1c138809
ms.openlocfilehash: c5c2742065536805cd032f2d814ad668b8ad3b6e


---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Övervaka tillgänglighet och svarstider på valfri webbplats
När du har distribuerat din webbapp eller webbplats till en server kan du konfigurera webbtester för att övervaka appens tillgänglighet och svarstider. [Azure Application Insights](app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Den varnar dig om programmet inte svarar eller svarar långsamt.

![Exempel på webbtest](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Du kan konfigurera webbtester för valfri HTTP- eller HTTPS-slutpunkt som kan nås från det offentliga Internet. Du behöver inte lägga till något till den webbplats som du testar. Det behöver inte ens vara din webbplats: du kan testa en REST API-tjänst som du är beroende av.

Det finns två typer av webbtester:

* [URL-pingtest](#create): Ett enkelt test som du kan skapa på Azure-portalen.
* [Webbtest i flera steg](#multi-step-web-tests): Ett test som du skapar i Visual Studio Ultimate eller Visual Studio Enterprise och laddar upp till portalen.

Du kan skapa upp till tio webbtester per programresurs.

## <a name="a-namecreatea1-create-a-resource-for-your-test-reports"></a><a name="create"></a>1. Skapa en resurs för dina testrapporter
Hoppa över det här steget om du redan har [konfigurerat en Application Insights-resurs][start] för det här programmet och om du vill visa tillgänglighetsrapporter på samma plats.

Registrera dig för [Microsoft Azure](http://azure.com), gå till [Azure-portalen](https://portal.azure.com) och skapa en Application Insights-resurs.

![Nytt > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Klicka på **alla resurser** för att öppna översiktsbladet för den nya resursen.

## <a name="a-namesetupa2-create-a-url-ping-test"></a><a name="setup"></a>2. Skapa ett URL-pingtest
Leta upp panelen Tillgänglighet i Application Insights-resursen. Klicka på panalen för att öppna bladet Webbtest för ditt program, och lägg till ett webbtest.

![Fyll åtminstone i URL:en för din webbplats](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **URL:en** måste vara synlig från det offentliga Internet. Den kan innehålla en frågesträng, vilket innebär att du kan arbeta med din databas om du vill. Om URL-adressen matchar en omdirigering följer vi den upp till tio omdirigeringar.
* **Parsa beroendebegäranden**: Bilder, skript, filer och andra resurser för sidan har begärts som en del av testet och den angivna svarstiden innehåller dessa tider. Testet misslyckas om dessa resurser inte kan laddas ned inom tidsgränsen för hela testet.
* **Aktivera återförsök**: Om testet misslyckas görs ett nytt försök efter en liten stund. Ett fel rapporteras endast om tre på varandra följande försök misslyckas. Efterföljande tester utförs sedan med den vanliga testfrekvensen. Återförsök pausas tillfälligt tills nästa lyckade test. Den här regeln tillämpas separat på varje testplats. (Vi rekommenderar den här inställningen. I genomsnitt försvinner ca 80 % av felen vid återförsök.)
* **Testfrekvens**: Anger hur ofta testet körs från varje testplats. Med en frekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.
* **Testplatser** är de platser som våra servrar skickar webbförfrågningar till din URL från. Välj mer än en så att du kan skilja mellan problem på din webbplats och nätverksproblem. Du kan välja upp till 16 platser.
* **Villkor för lyckad test**:

    **Timeout för test**: Minska det här värdet om du vill få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.

    **HTTP-svar**: Den returnerade statuskoden som räknas som ett lyckat test. 200 är koden som anger att en normal webbsida har returnerats.

    **Innehållsmatchning**: en sträng, t.ex. ”Välkommen!”. Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras.
* **Aviseringar** skickas som standard till dig om det uppstår fel på tre platser under en femminutersperiod. Ett fel på en enda plats är ofta ett nätverksproblem och inte ett problem med din webbplats. Men du kan ändra tröskelvärdet så att det är mer eller mindre känsligt, och du kan också ändra vem e-postmeddelandena ska skickas till.

    Du kan konfigurera en [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) som anropas när en avisering genereras. (Observera dock att frågeparametrar inte skickas som egenskaper för närvarande.)

### <a name="test-more-urls"></a>Testa fler URL:er
Lägg till fler test. Förutom att testa din hemsida kan du till exempel kontrollera att din databas körs genom att testa URL:en för en sökning.

## <a name="a-namemonitora3-see-your-web-test-results"></a><a name="monitor"></a>3. Se webbtestresultaten
Resultaten visas i bladet Webbtest efter 1 till 2 minuter.

![Sammanfattningsresultat på startbladet](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Klicka på valfri stapel i sammanfattningsdiagrammet om du vill visa en mer detaljerad vy av den aktuella tidsperioden.

Dessa diagram kombinerar resultaten för alla webbtester för det här programmet.

## <a name="a-namefailuresaif-you-see-failures"></a><a name="failures"></a>Om du ser fel
Klicka på en röd punkt.

![Klicka på en röd punkt](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

Eller rulla ned och klicka på ett test där du ser ett resultat som är mindre än 100 % lyckat.

![Klicka på ett specifikt webbtest](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Testresultatet öppnas.

![Klicka på ett specifikt webbtest](./media/app-insights-monitor-web-app-availability/16-1test.png)

Testet körs från flera platser: välj en där resultatet är mindre än 100 %.

![Klicka på ett specifikt webbtest](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)

Rulla ned till **Misslyckade tester** och välj ett resultat.

Klicka på resultatet för att utvärdera det på portalen och se orsaken till felet.

![Körningsresultat från webbtest](./media/app-insights-monitor-web-app-availability/18-availDetails.png)

Du kan också hämta resultatfilen och granska den i Visual Studio.

*Ser det okej ut trots att fel har rapporterats?* Kontrollera alla bilder, skript, formatmallar och andra filer som lästs in av sidan. Om någon av komponenterna inte kunde läsas in rapporteras testet som misslyckat, även om HTML-huvudsidan kan läsas in korrekt.

### <a name="open-the-server-request-and-exceptions"></a>Öppna serverbegäran och undantag

Från de detaljerade egenskaperna för ett visst test kan du öppna rapporten på serversidan av begäran och andra händelser, till exempel undantag.

![Körningsresultat från webbtest](./media/app-insights-monitor-web-app-availability/web-test-linked-to-server-telemetry.png)

Om du inte ser relaterade objekt kan det bero på en pågående [sampling](app-insights-sampling.md).

## <a name="multi-step-web-tests"></a>Webbtester med flera steg
Du kan övervaka ett scenario med en serie URL:er. Om du till exempel övervakar en försäljningswebbplats kan du testa att det går att lägga till objekt i kundvagnen korrekt.

> [!NOTE] 
> Det utgår en avgift för webbtester med flera steg. [Prisschema](http://azure.microsoft.com/pricing/details/application-insights/).
> 

Om du vill skapa ett test med flera steg spelar du in scenariot med hjälp av Visual Studio och laddar sedan upp inspelningen till Application Insights. Application Insights spelar upp scenariot i intervall och verifierar svaren.

Observera att du inte kan använda kodade funktioner i tester: stegen i scenariot måste finnas som ett skript i filen .webtest.

#### <a name="1-record-a-scenario"></a>1. Spela in ett scenario
Spela in en webbsession med Visual Studio Enterprise eller Ultimate.

1. Skapa ett testprojekt för webbprestanda.

    ![Skapa ett projekt i Visual Studio från mallen Webbprestanda- och inläsningstest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
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
1. Skapa ett nytt webbtest på Application Insights-portalen.

    ![Välj Lägg till på bladet Webbtest.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Välj ett flerstegstest och ladda upp filen .webtest.

    ![Välj webbtestet med flera steg.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Ange testplatserna, frekvensen och aviseringsparametrarna på samma sätt som för pingtest.

Visa testresultaten och eventuella fel på samma sätt som för tester av enstaka URL:er.

En vanlig orsak till fel är att testet körs för länge. Det får inte köras längre än två minuter.

Glöm inte att alla resurser på en sida måste läsas in korrekt för att testet ska lyckas, inklusive skript, formatmallar, bilder och så vidare.

Observera att webbtestet måste finnas i filen .webtest: du kan inte använda kodade funktioner i testet.

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

## <a name="a-nameedita-edit-or-disable-a-test"></a><a name="edit"></a>Redigera eller inaktivera ett test
Öppna ett enskilt test för att redigera eller inaktivera det.

![Redigera eller inaktivera ett webbtest](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Du kanske vill inaktivera webbtesterna när du utför underhåll av tjänsten.

## <a name="performance-tests"></a>Prestandatester
Du kan köra ett inläsningstest på din webbplats. Som med tillgänglighetstestet kan du skicka antingen enkla begäranden eller begäranden med flera steg från våra platser runtom i världen. Till skillnad från ett tillgänglighetstest skickas många begäranden, som simulerar flera samtidiga användare.

Öppna **Inställningar**, **Prestandatest** från bladet Översikt. När du skapar ett test uppmanas du att ansluta till eller att skapa ett konto för Visual Studio Team Services.

När testet är klart visas svarstiderna och slutförandefrekvens.

## <a name="automation"></a>Automation
* [Konfigurera ett webbtest automatiskt med hjälp av PowerShell-skript](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/).
* Konfigurera en [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) som anropas när en avisering genereras.

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
* *Kan jag anropa kod från mitt webbtest?*

    Nej. Stegen i testet måste finnas i filen .webtest. Och du kan inte anropa andra webbtester eller använda loopar. Men det finns flera plugin-program som kan vara användbara.
* *Stöds HTTPS?*

    Vi stöder TLS 1.1 och TLS 1.2.
* *Är det någon skillnad mellan ”webbtester” och ”tillgänglighetstester”?*

    Vi använder båda termerna synonymt.
* *Jag vill använda tillgänglighetstester på vår interna server som körs bakom en brandvägg.*

    Konfigurera din brandvägg att tillåta förfrågningar från [IP-adresserna för webbtestagenter](app-insights-ip-addresses.md).
* *Det går inte att överföra ett flerstegstest för webbplatser*

    Det finns en storleksgräns på 300 kB.

    Loopar stöds inte.

    Referenser till andra webbtester stöds inte.

    Datakällor stöds inte.
* *Mitt test med flera steg slutförs inte*

    Det finns en gräns på 100 förfrågningar per test.

    Testet stoppas om den körs längre än två minuter.
* *Hur kan jag köra ett test med klientcertifikat?*

    Det stöds tyvärr inte.

## <a name="a-namevideoavideo"></a><a name="video"></a>Video
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Monitoring-Availability-with-Application-Insights/player]
>
>

## <a name="a-namenextanext-steps"></a><a name="next"></a>Nästa steg
[Sök i diagnostikloggar][diagnostic]

[Felsökning][qna]

[IP-adresser för webbtestagenter](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md



<!--HONumber=Dec16_HO2-->


