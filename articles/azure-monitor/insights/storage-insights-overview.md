---
title: Övervaka Azure Storage-tjänster med Azure Monitor for Storage (förhandsversion)| Microsoft-dokument
description: I den här artikeln beskrivs Azure Monitor for Storage-funktionen som ger lagringsadministratörer en snabb förståelse för prestanda- och användningsproblem med sina Azure Storage-konton.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662527"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Övervaka din lagringstjänst med Azure Monitor for Storage (förhandsversion)

Azure Monitor for Storage (förhandsversion) ger omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage-tjänsters prestanda, kapacitet och tillgänglighet. Du kan observera lagringskapacitet och prestanda på två sätt, visa direkt från ett lagringskonto eller en vy från Azure Monitor för att se över grupper av lagringskonton. 

Den här artikeln hjälper dig att förstå den erfarenhet som Azure Monitor for Storage (förhandsversion) ger för att härleda användbar kunskap om hälso- och prestanda för lagringskonton i stor skala, med möjlighet att fokusera på hotspots och diagnostisera svarstid, begränsning, och tillgänglighetsproblem.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Introduktion till Azure Monitor for Storage (förhandsversion)

Innan du dyker in i upplevelsen, bör du förstå hur den presenterar och visualiserar information. Oavsett om du väljer lagringsfunktionen direkt från ett lagringskonto eller från Azure Monitor, presenterar Azure Monitor for Storage en konsekvent upplevelse. 

Kombinerat det ger:

* **Vid skalningsperspektiv** som visar en ögonblicksbildvy av deras tillgänglighet baserat på hälsotillståndet för lagringstjänsten eller API-åtgärden, användning som visar det totala antalet begäranden som lagringstjänsten tar emot och svarstid som visar den genomsnittliga tiden lagringstjänsten eller API-åtgärdstypen tar för att bearbeta begäranden. Du kan också visa kapacitet efter blob, fil, tabell och kö.

* **Öka detaljnivån för** en viss lagringskonto för att diagnostisera problem eller utföra detaljerad analys efter kategori - tillgänglighet, prestanda, fel och kapacitet. Om du väljer något av dessa alternativ kan du visa mått på ett djupgående perspektiv.  

* **Anpassningsbar där** du kan ändra vilka mått du vill visa, ändra eller ange tröskelvärden som stämmer överens med dina gränser och spara som en egen arbetsbok. Diagram i arbetsboken kan fästas på Azure-instrumentpanelen.  

Den här funktionen kräver inte att du aktiverar eller konfigurerar något, lagringsmåtten från dina lagringskonton samlas in som standard. Om du inte känner till mått som är tillgängliga på Azure Storage kan du visa beskrivningen och definitionen i Azure Storage-mått genom att granska [Azure-lagringsmått](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Det finns ingen avgift för att komma åt den här funktionen och du debiteras endast för de grundläggande funktioner för Azure Monitor som du konfigurerar eller aktiverar, enligt beskrivningen på [prisinformationssidan för Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

>[!NOTE]
>Azure Monitor for Storage stöder inte [virtuella v1-konton för allmänt ändamål](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Visa från Azure Monitor

Från Azure Monitor kan du visa transaktions-, svarstids- och kapacitetsinformation från flera lagringskonton i din prenumeration och hjälpa dig att identifiera prestanda, kapacitetsproblem och fel.

Om du vill visa hur du använder och är tillgänglig för dina lagringskonton i alla dina prenumerationer utför du följande steg.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Övervaka** i det vänstra fönstret i Azure-portalen och välj **Lagringskonton (förhandsgranskning)** under avsnittet **Insikter** .

    ![Vy över flera lagringskonton](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Översikt arbetsbok

I **översiktsarbetsboken** för den valda prenumerationen visar tabellen interaktiva lagringsmått och tjänsttillgänglighetstillstånd för upp till 10 lagringskonton grupperade i prenumerationen. Du kan filtrera resultaten baserat på de alternativ du väljer i följande listrutor:

* **Prenumerationer** – endast prenumerationer som har lagringskonton visas.  

* **Lagringskonton** – som standard är 10 lagringskonton förvalda. Om du väljer alla eller flera lagringskonton i scopeväljaren returneras upp till 200 lagringskonton. Om du till exempel hade totalt 573 lagringskonton för tre prenumerationer som du har valt visas bara 200 konton. 

* **Tidsintervall** - som standard visar de senaste 4 timmarnas information baserat på motsvarande val som gjorts.

Räknarpanelen under listrutan visar det totala antalet lagringskonton i prenumerationen och visar hur många av de totala som väljs. Det finns villkorlig färgkodning eller heatmaps för kolumner i arbetsboken som rapporterar transaktionsmått eller fel. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena. För de felbaserade kolumnerna är värdet i rött och för de måttbaserade kolumnerna är värdet i blått.

Välj ett värde i kolumnerna **Tillgänglighet**, **E2E Latency,** **Server latency**och **transaktionsfeltyp/Fel** leder dig till en rapport som är anpassad till den specifika typen av lagringsmått som matchar kolumnen som valts för det lagringskontot. Mer information om arbetsböckerna för varje kategori finns i avsnittet [Detaljerade lagringsarbetsböcker](#detailed-storage-workbooks) nedan. 

>[!NOTE]
>Mer information om vilka fel som kan visas i rapporten finns i [schemat för svarstyp](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) och leta efter svarstyper som **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. Beroende på vilka lagringskonton som valts representeras alla andra fel under kategorin **Andra.**

Standardtröskeln **för tillgänglighet** är:

* Varning - 99%
* Kritisk - 90%

Om du vill ange ett tröskelvärde för tillgänglighet baserat på resultaten av din observation eller dina krav granskar du [ändra tillgänglighetströskeln](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Arbetsbok för kapacitet

Välj **Kapacitet** högst upp på sidan och arbetsboken **Kapacitet** öppnas. Den visar hur mycket totalt lagringsutrymme som används i kontot och kapaciteten som används av varje datatjänst i kontot för att identifiera över och under utnyttjad lagring.

![Arbetsbok för kapacitet för flera lagringskonton](./media/storage-insights-overview/storage-account-capacity-02.png) 

Det finns villkorlig färgkodning eller heatmaps för kolumner i arbetsboken som rapporterar kapacitetsmått med ett blått värde. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena.

När du väljer ett värde under någon av kolumnerna i arbetsboken går du ned detaljnivån till **kapacitetsarbetsboken** för lagringskontot. Mer information om detaljgranskningsrapporten beskrivs i avsnittet [Detaljerad lagringsarbetsböcker](#detailed-storage-workbooks) nedan. 

## <a name="view-from-a-storage-account"></a>Visa från ett lagringskonto

Så här kommer du åt Azure Monitor för virtuella datorer direkt från ett lagringskonto:

1. Välj Lagringskonton i Azure-portalen.

2. Välj ett lagringskonto i listan. Välj Insikter (förhandsgranskning) i avsnittet Övervakning.

    ![Översiktssida för markerat lagringskonto](./media/storage-insights-overview/storage-account-direct-overview-01.png)

I **översiktsarbetsboken** för lagringskontot visas flera mätvärden för lagringsprestanda som hjälper dig att snabbt bedöma:

* Hälsotillstånd för lagringstjänsten för att omedelbart se om ett problem utanför kontrollen påverkar lagringstjänsten i den region som den distribueras till, vilket anges under kolumnen **Sammanfattning.**

* Interaktiva prestandadiagram som visar de viktigaste detaljerna för lagringskapacitet, tillgänglighet, transaktioner och svarstid.  

* Mått- och statuspaneler som belyser tjänstens tillgänglighet, totalt antal transaktioner till lagringstjänsten, E2E-svarstid och serverfördröjning.

Om du väljer en av **knapparna**för Fel, **Prestanda,** **Tillgänglighet**och **Kapacitet** öppnas respektive arbetsbok. 

![Översiktssida för markerat lagringskonto](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Detaljerade lagringsarbetsböcker

Oavsett om du har valt ett värde i kolumnerna **Tillgänglighet**, **E2E-svarstid,** **Serverfördröjning**och **transaktionsfeltyp/Fel** från arbetsboken **Översikt** för flera lagringskonto, eller välja någon av knapparna för **Fel,** **Prestanda,** **Tillgänglighet**och **Kapacitet** från **översiktsarbetsboken** från ett visst lagringskonto, levererar var och en en uppsättning interaktiv lagringsrelaterad information som är skräddarsydd för den kategorin.  

* **Tillgänglighet** öppnar **arbetsboken Tillgänglighet.** Den visar det aktuella hälsotillståndet för Azure Storage-tjänsten, en tabell som visar det tillgängliga hälsotillståndet för varje objekt som kategoriseras efter datatjänst som definierats i lagringskontot med en trendlinje som representerar det valda tidsintervallet och ett trenddiagram för tillgänglighet för varje datatjänst i kontot.  

    ![Exempel på tillgänglighetsrapport](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E-svarstid** och **serverfördröjning** öppnar **prestandaarbetsboken.** Den innehåller en samlad statuspanel som visar E2E-svarstid och serverfördröjning, ett prestandadiagram över E2E kontra serverfördröjning och en tabell som bryter ned svarstiden för lyckade anrop av API som kategoriseras av datatjänst som definierats i lagringskontot.

    ![Exempel på prestandarapport](./media/storage-insights-overview/storage-account-performance-01.png)

* Om du väljer någon av felkategorierna i rutnätet öppnas arbetsboken **Fel.** Rapporten visar måttpaneler för alla andra klientfel utom beskrivna och lyckade begäranden, klientbegränsningsfel, ett prestandadiagram för dimensionsmåttet **för transaktionssvarstyp** som är specifika för attributet ClientOtherError och två tabeller - **Transaktioner efter API-namn** och Transaktioner efter **svarstyp**.

   ![Exempel på felrapport](./media/storage-insights-overview/storage-account-failures-01.png)

* **Kapacitet** öppnar **arbetsboken Kapacitet.** Den visar den totala mängden lagringsutrymme som används för varje lagringsdataobjekt i kontot i panelerna och diagrammet och hur många dataobjekt som lagras i kontot.  

    ![Vald sida för lagringskontokapacitet](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Fästa och exportera

Du kan fästa något av måttavsnitten på en Azure Dashboard genom att välja pushpin-ikonen längst upp till höger i avsnittet.

![Exempel på måttavsnittsnål på instrumentpanelen](./media/storage-insights-overview/workbook-pin-example.png)

**Arbetsböckerna Översikt** **över** flera prenumerationer och lagringskonto stöder export av resultaten i Excel-format genom att välja nedpilikonen till höger om pushpin-ikonen.

![Exempel på resultat i dokumentnätet för export](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Anpassa Azure Monitor för lagring (förhandsversion)

I det här avsnittet beskrivs vanliga scenarier för redigering av arbetsboken för att anpassa till stöd för dina dataanalysbehov:

* Scope arbetsboken så att den alltid väljer en viss prenumeration eller ett visst lagringskonto
* Ändra mått i rutnätet
* Ändra tröskelvärdet för tillgänglighet
* Ändra färgåtergivning

Anpassningarna sparas i en anpassad arbetsbok för att förhindra att standardkonfigurationen skrivs över i vår publicerade arbetsbok. Arbetsböcker sparas i en resursgrupp, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **Delade rapporter** som är tillgängligt för alla med åtkomst till resursgruppen. När du har sparat den anpassade arbetsboken måste du gå till arbetsboksgalleriet för att starta den.

![Starta arbetsboksgalleri från kommandofältet](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Ange ett prenumerations- eller lagringskonto

Du kan konfigurera arbetsböckerna **översikt** **över** flera prenumerationer och lagringskonto till scope till en viss prenumeration eller lagringskonto på varje körning, utför följande steg.

1. Välj **Övervaka** från portalen och välj sedan **Lagringskonton (förhandsgranskning)** i den vänstra rutan.

2. I kommandofältet i **kontrollarbetsboken** väljer du **Redigera**i kommandofältet .

3. Välj i listrutan **Prenumerationer** en eller flera prenumerationer som du vill att den ska vara som standard. Kom ihåg att arbetsboken stöder att välja upp till totalt 10 prenumerationer.  

4. Välj i listrutan **Lagringskonton** ett eller flera konton som du vill att det ska vara som standard. Kom ihåg att arbetsboken stöder att välja upp till totalt 200 lagringskonton. 

5. Välj **Spara som** i kommandofältet om du vill spara en kopia av arbetsboken med anpassningarna och klicka sedan på Klar **redigering** för att återgå till läsläge.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Ändra mått och färger i arbetsboken

De fördefinierade arbetsböckerna innehåller måttdata och du kan ändra eller ta bort någon av visualiseringarna och anpassa efter teamets specifika behov.

I vårt exempel arbetar vi med arbetsboken för kapacitet för flera prenumerationer och lagringskonto för att visa hur du:

* Ta bort ett mått
* Ändra färgåtergivning

Du kan utföra samma ändringar mot någon av de fördefinierade **fel,** **prestanda,** **tillgänglighet**och **kapacitet** arbetsböcker.

1. Välj **Övervaka** från portalen och välj sedan **Lagringskonton (förhandsgranskning)** i den vänstra rutan.

2. Välj **Kapacitet om** du vill växla till kapacitetsarbetsboken och välj **Redigera** i kommandofältet i kommandofältet i kommandofältet.

    ![Välj redigera om du vill ändra en arbetsbok](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Välj Redigera bredvid avsnittet mått **.**

    ![Välj Redigera om du vill ändra kapacitetsarbetsboksmått](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Vi kommer att ta bort kolumnen **Kontoanvänd kapacitet tidslinje,** så välj **Kolumninställningar** i måttrutnätet.

    ![Redigera kolumninställningar](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. I fönstret **Redigera kolumninställningar** väljer du under avsnittet **Kolumner** **microsoft.storageaccounts-Capacity-UsedCapacity Timeline$| Kontoanvänd kapacitet Tidslinje$** och under listrutan **Kolumnrendare** väljer du **Dold**.

6. Välj **Spara och stäng** för att genomföra ändringen.

Nu ska vi ändra färgtemat för kapacitetsmåtten i rapporten så att det används grönt i stället för blått.

1. Välj **Kolumninställningar** i måttrutnätet.

2. I fönstret **Redigera kolumninställningar** välj under avsnittet **Kolumner** **microsoft.storageaccounts-Capacity-UsedCapacity$|microsoft.storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storageaccounts/fileservices-Capacity-FileCapacity$|microsoft.storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storageaccounts/tableservices-Capacity-TableCapacity$**. Välj **Grön**under listrutan **Färgpalett**.

3. Välj **Spara och stäng** för att genomföra ändringen.

4. Välj **Spara som** i kommandofältet om du vill spara en kopia av arbetsboken med anpassningarna och klicka sedan på Klar **redigering** för att återgå till läsläge.  

### <a name="modify-the-availability-threshold"></a>Ändra tröskelvärdet för tillgänglighet

I det här exemplet arbetar vi med arbetsboken för lagringskontokapacitet och visar hur du ändrar tillgänglighetströskeln. Som standard konfigureras panel- och rutnätsrapporteringsprocenttillgängligheten med ett lägsta tröskelvärde på 90 och ett högsta tröskelvärde på 99. Vi kommer att ändra det lägsta tröskelvärdet **för tillgänglighet %** i rutnätet Tillgänglighet med **API-namn** till 85 %, vilket innebär att hälsotillståndet ändras till kritiskt om tröskelvärdet är mindre än 85 procent. 

1. Välj **Lagringskonton** från portalen och välj sedan ett lagringskonto i listan.

2. Välj **Insikter (förhandsgranskning)** i den vänstra rutan.

3. I arbetsboken väljer du **Tillgänglighet** för att växla till tillgänglighetsarbetsboken och väljer sedan **Redigera** i kommandofältet. 

4. Bläddra ned till sidans nederkant och välj **Redigera**till vänster bredvid rutnätet **Tillgänglighet via API** .

    ![Redigera tillgänglighet efter inställningar för API-namnrutnät](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Välj **Kolumninställningar** och välj sedan Tillgänglighet **Columns** (%) i fönstret Redigera **kolumninställningar** ** (Tröskelvärden + Formaterade)**.

6. Ändra värdet för tillståndet **Kritisk** hälsotillstånd från **90** till **85** och klicka sedan på **Spara och stäng**.

    ![Ändra tröskelvärdet för tillgänglighet för kritiskt tillstånd](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Välj **Spara som** i kommandofältet om du vill spara en kopia av arbetsboken med anpassningarna och klicka sedan på Klar **redigering** för att återgå till läsläge.

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet hjälper dig med diagnos och felsökning av några av de vanliga problem som kan uppstå när du använder Azure Monitor for Storage (förhandsversion). Använd listan nedan för att hitta den information som är relevant för ditt specifika problem.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Lösa prestanda-, kapacitets- eller tillgänglighetsproblem

Information om hur du felsöker alla lagringsrelaterade problem som du identifierar med Azure Monitor for Storage (förhandsversion) finns i [felsökningsvägledningen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)för Azure Storage .  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Varför kan jag bara se 200 lagringskonton?

Antalet valda lagringskonton har en gräns på 200, oavsett antalet prenumerationer som har valts.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Vad händer när jag klickar på en nyligen fäst panel på instrumentpanelen?

* Om du klickar någonstans på panelen kommer den att ta dig till fliken där panelen var fäst från. Om du till exempel fäster ett diagram i fliken Översikt över lagringskonto öppnas den sparade kopians vy när du klickar på panelen på instrumentpanelen.
* Filterikonen längst upp till vänster i titeln öppnar fliken "Konfigurera panelinställningar".
* Ellipsikonen längst upp till höger ger dig alternativen att "Anpassa titeldata", "anpassa", "uppdatera" och "ta bort från instrumentpanelen".

### <a name="what-happens-when-i-save-a-workbook"></a>Vad händer när jag sparar en arbetsbok?

* När du sparar en arbetsbok kan du skapa en ny kopia av arbetsboken med dina redigeringar och ändra titeln. Spara inte skriva över arbetsboken, kommer den aktuella arbetsboken alltid vara standardvyn.
* En **arbetsbok som inte sparats** är bara standardvyn.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Varför ser jag inte alla mina prenumerationer i portalen?

Portalen visar endast data för valda prenumerationer vid portalstart. Om du vill ändra vilka prenumerationer som väljs går du längst upp till höger och klickar på anteckningsboken med en filterikon. Detta visar fliken Katalog + prenumerationer.

![Katalog + prenumeration](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Hur ändrar man färg och tröskelvärde för tillgänglighet?

I avsnittet [Ändra tröskelvärdet](storage-insights-overview.md#modify-the-availability-threshold) för tillgänglighet finns detaljerade steg om hur du ändrar färg och tröskelvärden för tillgänglighet.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Hur man analyserar och felsöker data som visas i Azure Monitor for Storage?

 Mer information om hur du analyserar och felsöker Azure Storage-data som visas i Azure Monitor for Storage finns i artikeln [Monitor, diagnose och felsöka](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) Microsoft Azure Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Varför ser jag inte alla typer av fel i mätvärden?

För närvarande visas upp till tre olika typer av fel och resten av felen grupperas tillsammans i en enda bucket. Den styrs med splitByLimit och kan ändras. Så här ändrar du den här egenskapen:

1. Klicka på redigera arbetsbok.
2. Gå till mått, klicka på redigera och välj sedan **Transaktioner, Summa** eller vilka mått du vill redigera.

    ![Gå till mått och klicka på redigera sedan på "Transaktioner, summor"](./media/storage-insights-overview/fqa7.png)

1. Ändra sedan antalet delningar.

    ![Välj måttparametrar"](./media/storage-insights-overview/fqa7-2.png)

Om du vill se n olika typer av fel än ange splitByLimit som n +1, 1 extra för resten av felen.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Jag sparade arbetsboken på vissa lagringskonto. Varför kan jag inte hitta den nu?

Varje arbetsbok sparas i lagringskontot som du sparade den i. Försök att hitta det specifika lagringskonto där användaren sparade arbetsboken. Annars finns det inget sätt att hitta en specifik arbetsbok utan att känna till resursen (lagringskonto).

### <a name="what-is-time-range"></a>Vad är tidsintervall?

Tidsintervallet visar data från en viss tidsram. Om tidsintervallet till exempel är 24 timmar visas data från de senaste 24 timmarna.

### <a name="what-is-time-granularity-time-grain"></a>Vad är tidsgranularitet (tidskorn)?

Tidsgranularitet är tidsskillnaden mellan två datapunkter. Om till exempel tidskornet är inställt på 1 sekund betyder det att mått samlas in varje sekund.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Vad är tidsgranulariteten när vi fäster någon del av arbetsböckerna på en instrumentpanel?

Standardtidsgranulariteten är inställd på automatisk, den kan för närvarande inte ändras just nu.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Hur ändrar jag tidsintervallet/tidsintervallet för arbetsboken på instrumentpanelen?

Som standard är tidsintervallet/tidsintervallet på instrumentpanelen inställt på 24 timmar, om du vill ändra det här klicket på ellipserna längst upp till höger, välj **Anpassa paneldata**, markera rutan "Åsidosätt instrumentpanelens tidsinställningar på rubriknivå" och välj sedan ett tidsintervall med hjälp av rullgardinsmenyn.  

![Markera ellipserna i det högra hörnet av panelen och välj Anpassa dessa data](./media/storage-insights-overview/fqa-data-settings.png)

![I Konfigurera panelinställningar väljer du listrutan tidsintervall för att ändra tidsintervallet/tidsintervallet](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Hur ändrar jag arbetsbokens rubrik eller ett arbetsbokssteg som jag fäster på en instrumentpanel?

Titeln på arbetsboken eller arbetsbokssteget som är fäst vid en instrumentpanel behåller samma namn som den hade i arbetsboken. Om du vill ändra titeln måste du spara en egen kopia av arbetsboken. Då kan du namnge arbetsboken innan du trycker på Spara.

![Välj spara högst upp om du vill spara en kopia av arbetsboken och ändra namnet på den](./media/storage-insights-overview/fqa-change-workbook-name.png)

Om du vill ändra namnet på ett steg i den sparade arbetsboken väljer du redigera under steget och väljer sedan växeln längst ned i inställningarna.

![Välj redigera längst ned i ett arbetsbokssteg för att öppna inställningarna](./media/storage-insights-overview/fqa-edit.png)
![I inställningarna väljer du växeln längst ned, för att kunna ändra stegnamnet](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Nästa steg

* Konfigurera [måttaviseringar](../platform/alerts-metric.md) och [tjänsthälsomeddelanden](../../service-health/alerts-activity-log-service-notifications.md) för att konfigurera automatisk avisering för att åtgärda problem.

* Lär dig scenarier arbetsböcker är utformade för att stödja, hur du skapar nya och anpassa befintliga rapporter och mer genom att granska [Skapa interaktiva rapporter med Azure Monitor arbetsböcker](../app/usage-workbooks.md).

* En detaljerad guide om hur du använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka Problem med Azure Storage finns i [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
