<properties
    pageTitle="Övervaka tillgänglighet och svarstider på valfri webbplats | Microsoft Azure"
    description="Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/20/2016"
    ms.author="awills"/>

# Övervaka tillgänglighet och svarstider på valfri webbplats


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

När du har distribuerat din webbapp kan du konfigurera webbtester för att övervaka appens tillgänglighet och svarstider. Application Insights skickar webbbförfrågningar med jämna mellanrum från platser runtom i världen och kan varna dig om programmet svarar långsamt eller inte alls.

![Exempel på webbtest](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Du kan konfigurera webbtester för valfri HTTP- eller HTTPS-slutpunkt som kan nås från det offentliga Internet.

Det finns två typer av webbtester:

* [URL-pingtest](#set-up-a-url-ping-test): Ett enkelt test som du kan skapa på Azure-portalen.
* [Webbtest i flera steg](#multi-step-web-tests): Ett test som du skapar i Visual Studio Ultimate eller Visual Studio Enterprise och laddar upp till portalen.

Du kan skapa upp till tio webbtester per programresurs.


## Konfigurera ett URL-pingtest

### <a name="create"></a>1. Skapa en ny resurs?

Hoppa över det här steget om du redan har [konfigurerat en Application Insights-resurs][start] för det här programmet och om du vill visa tillgänglighetsdata på samma plats.

Registrera dig för [Microsoft Azure](http://azure.com), gå till [Azure-portalen](https://portal.azure.com) och skapa en ny Application Insights-resurs.

![Nytt > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Översiktsbladet för den nya resursen öppnas. Du hittar det när som helst på [Azure-portalen](https://portal.azure.com) genom att klicka på **Bläddra**.

### <a name="setup"></a>2. Skapa ett webbtest

Leta upp panelen Tillgänglighet i Application Insights-resursen. Klicka på panalen för att öppna bladet Webbtest för ditt program, och lägg till ett webbtest.

![Fyll åtminstone i URL:en för din webbplats](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **URL:en** måste vara synlig från det offentliga Internet. Den kan innehålla en frågesträng, vilket innebär att du kan arbeta med din databas om du vill. Om URL-adressen matchar en omdirigering följer vi den upp till tio omdirigeringar.
- **Tolka beroende begäranden**: Bilder, skript, formatfiler och andra resurser för sidan begärs som en del av testet. Testet misslyckas om dessa resurser inte kan laddas ned inom tidsgränsen för hela testet.
- **Aktivera återförsök**: Om testet misslyckas görs ett nytt försök efter en liten stund. Ett fel rapporteras endast om tre på varandra följande försök misslyckas. Efterföljande tester utförs sedan med den vanliga testfrekvensen. Återförsök pausas tillfälligt tills nästa lyckade test. Den här regeln tillämpas separat på varje testplats. (Vi rekommenderar den här inställningen. I genomsnitt försvinner ca 80 % av felen vid återförsök.)
- **Testfrekvens**: Anger hur ofta testet körs från varje testplats. Med en frekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.
- **Testplatser** är de platser som våra servrar skickar webbförfrågningar till din URL från. Välj mer än en så att du kan skilja mellan problem på din webbplats och nätverksproblem. Du kan välja upp till 16 platser.

- **Villkor för lyckad test**:

    **Timeout för test**: Minska det här värdet om du vill få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Dela upp beroende begäranden** måste alla bilderna, formatfilerna, skripten och andra beroende resurser ha tagits emot inom denna period.

    **HTTP-svar**: Den returnerade statuskoden som räknas som ett lyckat test. 200 är koden som anger att en normal webbsida har returnerats.

    **Innehållsmatchning**: en sträng, t.ex. ”Välkommen!”. Vi ska testa att den förekommer i varje svar. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras.


- **Aviseringar** skickas som standard till dig om det uppstår fel på tre platser under en femminutersperiod. Ett fel på en enda plats är ofta ett nätverksproblem och inte ett problem med din webbplats. Men du kan ändra tröskelvärdet så att det är mer eller mindre känsligt, och du kan också ändra vem e-postmeddelandena ska skickas till.

    Du kan konfigurera en [webhook](../azure-portal/insights-webhooks-alerts.md) som anropas när en avisering genereras.

#### Testa fler URL:er

Lägg till fler test. Förutom att testa din hemsida kan du till exempel kontrollera att din databas körs genom att testa URL:en för en sökning.


### <a name="monitor"></a>3. Visa tillgänglighetsrapporter

Klicka på **Uppdatera** på tillgänglighets-/webbtestbladet efter 1–2 minuter. (Det uppdateras inte automatiskt.)

![Sammanfattningsresultat på startbladet](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Klicka på valfri stapel i sammanfattningsdiagrammet överst om du vill visa en mer detaljerad vy av den tidsperioden.

Dessa diagram kombinerar resultaten för alla webbtester för det här programmet.

#### Komponenter på din webbsida

Bilder, formatmallar och skript och andra statiska komponenter på webbsidan som du testar begärs som en del av testet.  

Den registrerade svarstiden är den tid det tar för alla komponenter att slutföra inläsningen.

Om någon av komponenterna inte kan läsas in markeras testet som misslyckat.

## <a name="failures"></a>Om du ser fel …

Klicka på en röd punkt.

![Klicka på en röd punkt](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

Eller rulla ned och klicka på ett test där du ser ett resultat som är mindre än 100 % lyckat.

![Klicka på ett specifikt webbtest](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Nu visas resultaten för testet.

![Klicka på ett specifikt webbtest](./media/app-insights-monitor-web-app-availability/16-1test.png)

Testet körs från flera platser: välj en där resultatet är mindre än 100 %.

![Klicka på ett specifikt webbtest](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


Rulla ned till **Misslyckade tester** och välj ett resultat.

Klicka på resultatet för att utvärdera det på portalen och se orsaken till felet.

![Körningsresultat från webbtest](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


Du kan också hämta resultatfilen och granska den i Visual Studio.


*Ser det okej ut trots att fel har rapporterats?* Kontrollera alla bilder, skript, formatmallar och andra filer som lästs in av sidan. Om någon av komponenterna inte kunde läsas in rapporteras testet som misslyckat, även om HTML-huvudsidan kan läsas in korrekt.



## Webbtester med flera steg

Du kan övervaka ett scenario med en serie URL:er. Om du till exempel övervakar en försäljningswebbplats kan du testa att det går att lägga till objekt i kundvagnen korrekt.

Om du vill skapa ett test med flera steg spelar du in scenariot med hjälp av Visual Studio och laddar sedan upp inspelningen till Application Insights. Application Insights spelar upp scenariot i intervall och kontrollerar svaren.

Observera att du inte kan använda kodade funktioner i tester: stegen i scenariot måste finnas som ett skript i filen .webtest.

#### 1. Spela in ett scenario

Spela in en webbsession med Visual Studio Enterprise eller Ultimate. 

1. Skapa ett testprojekt för webbprestanda.

    ![Skapa ett nytt projekt i Visual Studio från mallen Webbprestanda- och inläsningstest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. Öppna filen .webtest och börja inspelningen.

    ![Öppna filen .webtest och klicka på Spela in.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. Utför de användaråtgärder som du vill simulera i testet: öppna webbplatsen, lägg till en produkt i kundvagnen och så vidare. Stoppa sedan testet.

    ![Inspelningsverktyget för webbtestet körs i Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Håll scenariot kort. Det finns en gräns på 100 steg och 2 minuter.

4. Redigera testet om du vill:
 - Lägg till valideringar för att kontrollera texten som tas emot och svarskoderna.
 - Ta bort eventuella överflödiga interaktioner. Du kan också ta bort beroende förfrågningar för bilder eller till annons- eller spårningswebbplatser.

    Kom ihåg att du bara kan redigera testskriptet – du kan inte lägga till anpassad kod eller anropa andra webbtester. Infoga inte loopar i testet. Du kan använda standard-plugin-program för webbtester.

5. Kör testet i Visual Studio för att kontrollera att det fungerar.

    En webbläsare öppnas och de åtgärder som du har spelat in upprepas. Kontrollera att det fungerar som förväntat.

    ![Öppna filen .webtest i Visual Studio och klicka på Kör.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)


#### 2. Ladda upp webbtestet till Application Insights

1. Skapa ett nytt webbtest på Application Insights-portalen.

    ![Välj Lägg till på bladet Webbtest.](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. Välj ett flerstegstest och ladda upp filen .webtest.

    ![Välj webbtestet med flera steg.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Ange testplatserna, frekvensen och aviseringsparametrarna på samma sätt som för pingtest.

Visa testresultaten och eventuella fel på samma sätt som för tester av enstaka URL:er.

En vanlig orsak till fel är att testet körs för länge. Det får inte köras längre än två minuter.

Glöm inte att alla resurser på en sida måste läsas in korrekt för att testet ska lyckas, inklusive skript, formatmallar, bilder och så vidare.

Observera att webbtestet måste finnas i filen .webtest: du kan inte använda kodade funktioner i testet.


### Använda tid och slumptal i flerstegstest

Anta att du testar ett verktyg som hämtar tidsberoende data, till exempel aktier från ett externt flöde. När du spelar in webbtestet måste du ange specifika tider, men du anger dem som parametrar för testet, StartTime och EndTime.

![Ett webbtest med parametrar.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

När du kör testet vill du att EndTime alltid ska vara den aktuella tiden, och StartTime 15 minuter tidigare.

Du kan göra detta på två sätt i plugin-program för webbtester.

1. Lägg till ett plugin-program för webbtester för varje variabelt parametervärde som du behöver. I verktygsfältet Webbtest väljer du **Lägg till plugin-program för webbtest**.

    ![Välj Lägg till plugin-program för webbtest och välj en typ.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    I det här exemplet ska vi använda två instanser av plugin-programmet för datum och tid. En instans är för ”15 minuter tidigare” och en annan för ”nu”.

2. Öppna egenskaperna för varje plugin-program. Lägg till ett namn och ange att den aktuella tiden ska användas. För den ena anger du Lägg till minuter = -15.

    ![Ange namn, Använd aktuell tid och Lägg till minuter.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. Du refererar till plugin-namn genom att använd {{plugin-name}} i parametrarna för webbtestet.

    ![Använd {{plugin-name}} i testparametern.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Ladda upp testet till portalen. De dynamiska värdena används i varje körning av testet.

## Hantera inloggning

Om användarna måste logga in i din app kan du välja mellan olika alternativ för att simulera inloggningen, så att du kan testa sidorna bakom inloggningen. Vilken metod du använder beror på vilken typ av säkerhet som tillhandahålls av appen.

I samtliga fall bör du skapa ett konto som endast är avsett för testning. Om möjligt begränsar du kontots behörigheter så att det är skrivskyddat.

* Enkelt användarnamn och lösenord: Spela bara in ett webbtest som vanligt. Ta bort cookies först.
* SAML-autentisering. För detta kan du använda SAML-plugin-programmet som är tillgängligt för webbtester.
* Klienthemlighet: Om din app kräver inloggning med en klienthemlighet använder du den. Detta tillhandahålls av Azure Active Directory. 
* Öppen autentisering, t.ex. inloggning med ditt Microsoft- eller Google-konto. Många appar som använder OAuth erbjuder möjligheten att använda en klienthemlighet, så det första du bör göra är att ta reda på detta. Om testet måste logga in med OAuth är den allmänna riktlinjen att:
 * Använda ett verktyg som Fiddler för att undersöka trafiken mellan webbläsaren, autentiseringswebbplatsen och din app. 
 * Utföra två eller flera inloggningar med olika datorer eller webbläsare, eller med långa intervall (så att token upphör att gälla).
 * Genom att jämföra olika sessioner identifiera den token som skickas tillbaka från autentiseringswebbplatsen, som sedan skickas till din appserver efter inloggningen. 
 * Spela in ett webbtest med hjälp av Visual Studio. 
 * Parameterisera token genom att ange parametern när token returneras från autentiseraren och använda den i frågan till webbplatsen.
 (Visual Studio försöker parameterisera testet, men kan inte parameterisera token korrekt.)


## <a name="edit"></a> Redigera eller inaktivera ett test

Öppna ett enskilt test för att redigera eller inaktivera det.

![Redigera eller inaktivera ett webbtest](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Du kanske vill inaktivera webbtesterna när du utför underhåll av tjänsten.

## Automation

* [Konfigurera ett webbtest automatiskt med hjälp av PowerShell-skript](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/). 
* Konfigurera en [webhook](../azure-portal/insights-webhooks-alerts.md) som anropas när en avisering genereras.

## Har du några frågor? Har du problem?

* *Kan jag anropa kod från mitt webbtest?*

    Nej. Stegen i testet måste finnas i filen .webtest. Och du kan inte anropa andra webbtester eller använda loopar. Men det finns ett antal plugin-program som kan vara användbara.

* *Stöds HTTPS?*

    För närvarande stöds SSL 3.0 och TLS 1.0.

* *Är det någon skillnad mellan ”webbtester” och ”tillgänglighetstester”?*

    Vi använder båda termerna synonymt.

* *Jag vill använda tillgänglighetstester på vår interna server som körs bakom en brandvägg.*

    Konfigurera brandväggen så att den tillåter förfrågningar från IP-adresserna i listan i slutet av den här artikeln.

* *Det går inte att överföra ett webbtest med flera steg*

    Det finns en storleksgräns på 300 kB.

    Loopar stöds inte.

    Referenser till andra webbtester stöds inte.

    Datakällor stöds inte.

    
* *Mitt test med flera steg slutförs inte*

    Det finns en gräns på 100 förfrågningar per test.

    Testet stoppas om det körs längre än två minuter.

* *Hur kan jag köra ett test med klientcertifikat?*

    Det stöds tyvärr inte.


## <a name="video"></a>Video

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Nästa steg

[Söka i diagnostikloggar][diagnostic]

[Felsökning][qna]


## IP-adresser för webbtester

Om du behöver öppna en brandvägg för att tillåta webbtester är det här den aktuella listan över IP-adresser. Den kan ändras då och då.

Öppna portarna 80 (http) och 443 (https).

```

213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.61
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.63
213.199.178.64
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.157
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.162
207.46.98.171
207.46.98.172
65.55.244.40
65.55.244.17
65.55.244.42
65.55.244.37
65.55.244.15
65.55.244.16
65.55.244.44
65.55.244.18
65.55.244.46
65.55.244.47
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.55
207.46.14.63
207.46.14.64
207.46.14.51
207.46.14.52
207.46.14.56
207.46.14.65
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.47
157.55.14.64
157.55.14.65
157.55.14.43
157.55.14.44
157.55.14.49
157.55.14.50
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
207.46.71.54
207.46.71.52
207.46.71.55
207.46.71.38
207.46.71.51
207.46.71.57
207.46.71.58
207.46.71.37
202.89.228.67
202.89.228.68
202.89.228.69
202.89.228.57
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.67
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.81
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48

```


<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md



<!--HONumber=jun16_HO2-->


