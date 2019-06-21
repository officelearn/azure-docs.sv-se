---
title: Övervaka tillgänglighet och svarstider på valfri webbplats | Microsoft Docs
description: Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303722"
---
# <a name="monitor-the-availability-of-any-website"></a>Övervaka tillgängligheten för alla webbplatser

När du har distribuerat din web app/webbplats, kan du konfigurera återkommande tester för att övervaka tillgänglighet och svarstider. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Det kan meddela dig om programmet inte svarar, eller om den svarar för långsamt.

Du kan konfigurera tillgänglighetstester för valfri HTTP- eller HTTPS-slutpunkt som kan nås från det offentliga Internet. Du behöver göra några ändringar på webbplatsen som du testar. I själva verket kan behöver det även inte vara en plats som du äger. Du kan testa tillgängligheten för ett REST-API som din tjänst beroende.

### <a name="types-of-availability-tests"></a>Typer av tillgänglighetstester:

Det finns tre typer av tillgänglighetstester:

* [URL-pingtest](#create-a-url-ping-test): Ett enkelt test som du kan skapa på Azure-portalen.
* [Flerstegstest för webbplatser](availability-multistep.md): En registrering av en sekvens av webbegäranden, som kan spelas upp för att testa mer komplicerade scenarier. Webbtester med flera steg skapas i Visual Studio Enterprise och laddat upp till portalen för körning.
* [Anpassade spåra Tillgänglighetstester](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): Den `TrackAvailability()` metoden kan användas för att skapa dina egna anpassade tillgänglighetstester.

**Du kan skapa upp till 100 tillgänglighetstester per Application Insights-resurs.**

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

För att skapa ett tillgänglighetstest, måste du först skapa en Application Insights-resurs. Om du redan har skapat en resurs kan fortsätta till nästa avsnitt för att [skapa ett URL-Pingtest](#create-a-url-ping-test).

Azure-portalen väljer du **skapa en resurs** > **utvecklarverktyg** > **Programinsikter** och [skapa en Application Insights-resurs](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Skapa ett URL-pingtest

Det namnet ”URL-pingtestet” är inte en misnomer helt. Kan förtydliga att det här testet inte all användning av ICMP (Internet Control Message Protocol) att kontrollera tillgängligheten för din webbplats. I stället använder den mer avancerade funktioner för HTTP-begäran för att verifiera om en slutpunkt som svarar. Den också mäter prestanda som är associerade med det svaret och lägger till möjligheten att ange anpassade framgångskriterier tillsammans med mer avancerade funktioner som parsa beroende begäranden och att tillåta för återförsök.

Öppna fönstret tillgänglighet för att skapa din första begäran för tillgänglighet, och välj **skapa Test**.

![Fyll åtminstone i URL:en för din webbplats](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Skapa ett test

|Inställning| Förklaring
|----|----|----|
|**URL** |  URL: en kan vara en webbsida som du vill testa, men den måste vara synlig från det offentliga internet. URL: en kan innehålla en frågesträng. Du kan arbeta med din databas om du vill. Om URL-adressen matchar en omdirigering följer vi den upp till tio omdirigeringar.|
|**Dela upp beroende begäranden**| Begärs bilder, skript, formatfiler och andra filer som ingår i webbsidan under testet. Den registrerade svarstiden innefattar den tid det tar att hämta dessa filer. Testet misslyckas om någon av dessa resurser inte kan laddas ned inom tidsgränsen för hela testet. Om alternativet inte är markerat begärs endast filen på den URL som du har angett i testet. Aktiverar det här alternativet resulterar i en striktare kontroll. Testet misslyckas, fall, vilket inte kanske är märkbar när du bläddrar efter platsen manuellt.
|**Aktivera återförsök**|Om testet misslyckas görs ett nytt efter en liten stund. Ett fel rapporteras endast om tre på varandra följande försök misslyckas. Efterföljande tester utförs sedan med den vanliga testfrekvensen. Återförsök pausas tillfälligt tills nästa lyckade test. Den här regeln tillämpas separat på varje testplats. **Vi rekommenderar det här alternativet**. I genomsnitt försvinner ca 80 % av felen vid återförsök.|
|**Testfrekvens**| Anger hur ofta testet körs från varje testplats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.|
|**Testplatser**| Är platserna som våra servrar skickar webbförfrågningar till din URL. **Vår minsta antal rekommenderade testplatser är fem** för att se till att du kan skilja mellan problem på din webbplats nätverksproblem. Du kan välja upp till 16 platser.

**Om URL: en inte är synlig från det offentliga internet, kan du selektivt öppna brandväggen att tillåta testtransaktioner via**. Mer information om brandväggsundantag för vår tillgänglighet testagenter läser den [IP-adress för](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Vi rekommenderar testning från flera platser med **minst fem platser**. Det här är att förhindra falsklarm som kan bero på tillfälliga problem med en viss plats. Dessutom har vi hittade att den bästa konfigurationen är att ha den **antalet testplatser vara lika med aviseringsplats + 2**.

### <a name="success-criteria"></a>Villkor för lyckat test

|Inställning| Förklaring
|----|----|----|
| **Timeout för test** |Minska det här värdet om du vill få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.|
| **HTTP-svar** | Den returnerade statuskoden som räknas som ett lyckat test. 200 är koden som anger att en normal webbsida har returnerats.|
| **Innehållsmatchning** | En sträng, t.ex. ”Välkommen”! Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras. **Endast engelska tecken stöds med innehållsmatchning** |

### <a name="alerts"></a>Aviseringar

|Inställning| Förklaring
|----|----|----|
|**Nära realtid (förhandsversion)** | Vi rekommenderar att du använder aviseringar i nära realtid. Konfigurera den här typen av avisering görs efter din tillgänglighetstestet har skapats.  |
|**Klassisk** | Vi rekommenderar inte längre med klassiska aviseringar för nya tillgänglighetstester.|
|**Aviseringsplats**|Vi rekommenderar minst 3/5 platser. Optimal relationen mellan aviseringsplats och antalet testplatser är **aviseringsplats** = **antal testplatser - 2, med minst fem testplatser.**|

## <a name="see-your-availability-test-results"></a>Visa tillgänglighetstestresultat

Tillgänglighetstestresultat kan visualiseras med både rad- och punktdiagram ritytans vyer.

Efter ett par minuter klickar du på **uppdatera** att se testresultaten.

![Rad-vy](./media/monitor-web-app-availability/availability-refresh-002.png)

Spridningsdiagrammet visar exempel på testresultat som innehåller information om diagnostiska test-steget. Testmotorn lagrar diagnosinformation för tester som innehåller fel. För lyckade tester lagras diagnosinformation för en delmängd av körningarna. Hovra över någon av de grön/röda punkterna testet kan du testa namn och plats.

![Rad-vy](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Välj ett visst test, en viss plats eller minska tidsperioden för att visa fler resultat för en viss tidsperiod som du är intresserad av. Använd Sökutforskaren för att visa resultat från alla körningar, eller använd analysfrågor om du vill köra anpassade rapporter för dessa data.

## <a name="inspect-and-edit-tests"></a>Granska och redigera tester

Om du vill redigera, tillfälligt inaktivera eller ta bort ett test klickar du på ellipsen bredvid ett testnamn. Det kan ta upp till 20 minuter för konfigurationsändringar sprids till alla testagenter efter att en ändring har gjorts.

![Visa test information. Redigera och inaktivera ett webbtest](./media/monitor-web-app-availability/edit.png)

Du kanske vill inaktivera tillgänglighetstester eller varningsreglerna som är associerade med dem medan du utför underhåll på tjänsten.

## <a name="if-you-see-failures"></a>Om du ser fel

Klicka på en röd punkt.

![Klicka på en röd punkt](./media/monitor-web-app-availability/open-instance-3.png)

Från ett tillgänglighetstestresultat kan du se transaktionsinformation för alla komponenter. Här kan du:

* Kontrollera de svar som mottas från servern.
* Diagnostisera fel med korrelerade serversidan telemetri som samlas in under bearbetning av misslyckade tillgänglighetstestet.
* Logga ett problem eller arbetsuppgift i Git eller Azure-system för att spåra problemet. Buggen innehåller en länk till den här händelsen.
* Öppna resultatet av webbtestet i Visual Studio.

Lär dig mer om från slutpunkt till slutpunkt-transaktionsdiagnostik uppleva [här](../../azure-monitor/app/transaction-diagnostics.md).

Klicka på raden undantag om du vill se information om serversidan undantaget som orsakade syntetiska tillgänglighetstestet misslyckas. Du kan också hämta den [felsökning för ögonblicksbilder](../../azure-monitor/app/snapshot-debugger.md) rikare kod på diagnostik.

![Diagnostik för serversidan](./media/monitor-web-app-availability/open-instance-4.png)

Förutom rådataresultat, du kan också visa två viktiga tillgänglighetsmått i [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Tillgänglighet: Procent av testerna som lyckades av alla testkörningar.
2. Testets varaktighet: Genomsnittlig testvaraktighet alla testkörningar.

## <a name="automation"></a>Automation

* [Konfigurera ett tillgänglighetstest automatiskt med hjälp av PowerShell-skript](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Konfigurera en [webhook](../../azure-monitor/platform/alerts-webhooks.md) som anropas när en avisering genereras.

## <a name="troubleshooting"></a>Felsökning

Dedikerad [felsökningsartikeln](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Tillgänglighet aviseringar](availability-alerts.md)
* [Webbtester med flera steg](availability-multistep.md)


