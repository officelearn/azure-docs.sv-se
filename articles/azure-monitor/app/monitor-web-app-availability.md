---
title: Övervaka tillgänglighet och svarstider på valfri webbplats | Microsoft Docs
description: Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670040"
---
# <a name="monitor-the-availability-of-any-website"></a>Övervaka tillgängligheten för en webbplats

När du har distribuerat webbappen/webbplatsen kan du ställa in återkommande tester för att övervaka tillgänglighet och svarstider. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Det kan varna dig om ditt program inte svarar, eller om det svarar för långsamt.

Du kan konfigurera tillgänglighetstester för valfri HTTP- eller HTTPS-slutpunkt som kan nås från det offentliga Internet. Du behöver inte göra några ändringar på webbplatsen du testar. I själva verket behöver det inte ens vara en webbplats du äger. Du kan testa tillgängligheten för ett REST API som tjänsten är beroende av.

### <a name="types-of-availability-tests"></a>Typer av tillgänglighetstester:

Det finns tre typer av tillgänglighetstester:

* [URL-pingtest](#create-a-url-ping-test): Ett enkelt test som du kan skapa på Azure-portalen.
* [Webbtest i flera steg](availability-multistep.md): En inspelning av en sekvens av webbbegäranden, som kan spelas upp för att testa mer komplexa scenarier. Webbtester i flera steg skapas i Visual Studio Enterprise och överförs till portalen för körning.
* [Anpassade spårtillgänglighetstester:](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)Om du bestämmer dig för `TrackAvailability()` att skapa ett anpassat program för att köra tillgänglighetstester kan metoden användas för att skicka resultaten till Application Insights.

**Du kan skapa upp till 100 tillgänglighetstester per Application Insights-resurs.**

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

För att skapa ett tillgänglighetstest måste du först skapa en Application Insights-resurs. Om du redan har skapat en resurs går du vidare till nästa avsnitt för att [skapa ett URL Ping-test](#create-a-url-ping-test).

På Azure-portalen väljer du **Skapa en resurs** > **developer tools** > **application insights** och [skapar en Application Insights-resurs](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Skapa ett URL-pingtest

Namnet "URL ping test" är lite av en missvisande. För att vara tydlig, är detta test inte gör någon användning av ICMP (Internet Control Message Protocol) för att kontrollera webbplatsens tillgänglighet. I stället används mer avancerade HTTP-begärandefunktioner för att verifiera om en slutpunkt svarar. Den mäter också prestanda som är associerad med det svaret och lägger till möjligheten att ange anpassade framgångskriterier i kombination med mer avancerade funktioner som att tolka beroende begäranden och möjliggöra försök.

Om du vill skapa din första tillgänglighetsbegäran öppnar du fönstret Tillgänglighet och väljer **Skapa test**.

![Fyll åtminstone i URL:en för din webbplats](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Skapa ett test

|Inställning| Förklaring
|----|----|----|
|**URL** |  URL: en kan vara en webbsida som du vill testa, men den måste vara synlig från Internet. URL: en kan innehålla en frågesträng. Du kan arbeta med din databas om du vill. Om URL-adressen matchar en omdirigering följer vi den upp till tio omdirigeringar.|
|**Tolka beroende begäranden**| Test begär bilder, skript, formatfiler och andra filer som ingår i webbsidan under test. Den registrerade svarstiden innefattar den tid det tar att hämta dessa filer. Testet misslyckas om någon av dessa resurser inte kan hämtas inom tidsgränsen för hela testet. Om alternativet inte är markerat begärs endast filen på den URL som du har angett i testet. Om du aktiverar det här alternativet resulterar det i en striktare kontroll. Testet kan misslyckas i fall, vilket kanske inte märks när du bläddrar på webbplatsen manuellt.
|**Aktivera återförsök**|när testet misslyckas görs det på nytt efter ett kort intervall. Ett fel rapporteras endast om tre på varandra följande försök misslyckas. Efterföljande tester utförs sedan med den vanliga testfrekvensen. Återförsök pausas tillfälligt tills nästa lyckade test. Den här regeln tillämpas separat på varje testplats. **Vi rekommenderar det här alternativet**. I genomsnitt försvinner ca 80 % av felen vid återförsök.|
|**Testfrekvens**| Anger hur ofta testet körs från varje testplats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.|
|**Testplatser**| Är de platser där våra servrar skickar webbförfrågningar till din webbadress. **Vårt minsta antal rekommenderade testplatser är fem** för att försäkra att du kan skilja problem på din webbplats från nätverksproblem. Du kan välja upp till 16 platser.

**Om webbadressen inte visas från det offentliga internet kan du välja att selektivt öppna brandväggen så att endast testtransaktionerna tillåts via**. Mer information om brandväggsund undantagen för våra tillgänglighetstestagenter finns i [IP-adressguiden](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Vi rekommenderar starkt testning från flera platser med **minst fem platser.** Detta för att förhindra falsklarm som kan uppstå till följd av tillfälliga problem med en viss plats. Dessutom har vi funnit att den optimala konfigurationen är att ha **antalet testplatser vara lika med tröskelvärdet för varningsplats + 2**.

### <a name="success-criteria"></a>Kriterier för framgång

|Inställning| Förklaring
|----|----|----|
| **Tidsgränsen för testet** |Minska det här värdet för att få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.|
| **HTTP-svar** | Den returnerade statuskoden som räknas som en framgång. 200 är koden som anger att en normal webbsida har returnerats.|
| **Matchning av innehåll** | En sträng, som "Välkommen!" Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras. **Endast engelska tecken stöds med innehållsmatchning** |

### <a name="alerts"></a>Aviseringar

|Inställning| Förklaring
|----|----|----|
|**Nära realtid (förhandsgranskning)** | Vi rekommenderar att du använder varningar om nära realtid. Konfigurera den här typen av avisering görs när ditt tillgänglighetstest har skapats.  |
|**Klassisk** | Vi rekommenderar inte längre att du använder klassiska aviseringar för nya tillgänglighetstester.|
|**Tröskelvärde för variseringsplats**|Vi rekommenderar minst 3/5 platser. Det optimala förhållandet mellan tröskelvärdet för varningsplats och antalet testplatser är =  **tröskelvärdet för varningsplats****för testplatser - 2, med minst fem testplatser.**|

## <a name="see-your-availability-test-results"></a>Visa tillgänglighetstestresultat

Tillgänglighetstestresultat kan visualiseras med både linje- och punktdiagramvyer.

Efter några minuter klickar du på **Uppdatera** för att se dina testresultat.

![Linjevy](./media/monitor-web-app-availability/availability-refresh-002.png)

Spridningsvyn visar exempel på testresultat som har detaljerad diagnostikteststeg i dem. Testmotorn lagrar diagnosinformation för tester som innehåller fel. För lyckade tester lagras diagnosinformation för en delmängd av körningarna. Hovra över någon av de gröna/röda punkterna för att se testet, testnamnet och platsen.

![Linjevy](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Välj ett visst test, en viss plats eller minska tidsperioden för att visa fler resultat för en viss tidsperiod som du är intresserad av. Använd Sökutforskaren för att visa resultat från alla körningar, eller använd analysfrågor om du vill köra anpassade rapporter för dessa data.

## <a name="inspect-and-edit-tests"></a>Granska och redigera tester

Om du vill redigera inaktivera, tillfälligt inaktivera eller ta bort ett test klickar du på ellipserna bredvid ett testnamn. Det kan ta upp till 20 minuter innan konfigurationsändringar sprids till alla testagenter när en ändring har gjorts.

![Visa testinformation. Redigera och inaktivera ett webbtest](./media/monitor-web-app-availability/edit.png)

Du kanske vill inaktivera tillgänglighetstester eller varningsreglerna som är associerade med dem medan du utför underhåll på tjänsten.

## <a name="if-you-see-failures"></a>Om du ser fel

Klicka på en röd punkt.

![Klicka på en röd punkt](./media/monitor-web-app-availability/open-instance-3.png)

Från ett tillgänglighetstestresultat kan du se transaktionsinformationen för alla komponenter. Här kan du:

* Kontrollera de svar som mottas från servern.
* Diagnostisera fel med korrelerad telemetri på serversidan som samlats in när det misslyckade tillgänglighetstestet bearbetades.
* Logga ett problem eller ett arbetsobjekt i Git- eller Azure-anslagstavlor för att spåra problemet. Buggen innehåller en länk till den här händelsen.
* Öppna resultatet av webbtestet i Visual Studio.

Läs mer om upplevelsen av transaktionsdiagnostik från till [här](../../azure-monitor/app/transaction-diagnostics.md).

Klicka på undantagsraden för att se information om undantaget på serversidan som orsakade att testet för syntetisk tillgänglighet misslyckades. Du kan också hämta [felsökningsögonblicksbilden](../../azure-monitor/app/snapshot-debugger.md) för diagnostik på rikare kodnivå.

![Diagnostik på serversidan](./media/monitor-web-app-availability/open-instance-4.png)

Förutom råresultaten kan du även visa två viktiga tillgänglighetsmått i [Statistikutforskaren:](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

1. Tillgänglighet: antal procent av testerna som lyckades av alla testkörningar.
2. Testets varaktighet: genomsnittlig tid för alla testkörningar.

## <a name="automation"></a>Automation

* [Konfigurera ett tillgänglighetstest automatiskt med hjälp av PowerShell-skript](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Konfigurera en [webhook](../../azure-monitor/platform/alerts-webhooks.md) som anropas när en avisering genereras.

## <a name="troubleshooting"></a>Felsökning

Särskild [felsökningsartikel](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Tillgänglighetsaviseringar](availability-alerts.md)
* [Webbtester med flera steg](availability-multistep.md)


