---
title: DNS-Analytics lösning i Azure Log Analytics | Microsoft Docs
description: Konfigurera och använda DNS-Analytics-lösning i logganalys att samla in insikter om DNS-infrastrukturen på säkerhet, prestanda och åtgärder.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: 6a59cf8b9444fe7cb197501c51d10dae81acb027
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Samla in insikter om din DNS-infrastruktur med DNS-Analytics Preview-lösning

![DNS-Analytics symbol](./media/log-analytics-dns/dns-analytics-symbol.png)

Den här artikeln beskriver hur du ställer in och använda Azure DNS Analytics-lösning i Azure Log Analytics för att samla in insikter om DNS-infrastrukturen på säkerhet, prestanda och åtgärder.

DNS-analyser kan du:

- Identifiera klienter som försöker matcha skadliga domännamn.
- Identifiera inaktuella poster.
- Identifiera ofta efterfrågade domännamn och talkative DNS-klienter.
- Visa belastningen på DNS-servrar.
- Visa dynamiska DNS-registreringsfel.

Lösningen samlar in, analyseras och korrelerar Windows DNS analytiska och granskningsloggar och andra relaterade data från DNS-servrar.

## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| **Ansluten datakälla** | **Support** | **Beskrivning** |
| --- | --- | --- |
| [Windows-agenter](log-analytics-windows-agent.md) | Ja | Lösningen samlar in DNS-information från Windows-agenter. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej | Lösningen inte samlar in DNS-information direkt Linux-agenter. |
| [System Center Operations Manager-hanteringsgruppen](log-analytics-om-agents.md) | Ja | Lösningen samlar in DNS-information från agenter i en ansluten hanteringsgrupp för Operations Manager. En direkt anslutning från Operations Manager-agenten till Operations Management Suite krävs inte. Data skickas från hanteringsgruppen till Operations Management Suite-databasen. |
| [Azure Storage-konto](log-analytics-azure-storage.md) | Nej | Azure-lagring används inte av lösningen. |

### <a name="data-collection-details"></a>Information om samlingen

Lösningen samlar in DNS-inventerings- och DNS-händelse-relaterade data från DNS-servrar där en Log Analytics-agent har installerats. Dessa data överförs till logganalys sedan och visas i instrumentpanelen för lösningen. Lager-relaterad data, till exempel antal DNS-servrar, zoner och resursposter, samlas in genom att köra DNS-PowerShell-cmdlets. Data uppdateras en gång var två dagar. Händelse-relaterade data samlas in nära realtid från den [analytiska och granskningsloggar](https://technet.microsoft.com/library/dn800669.aspx#enhanc) tillhandahålls av utökade DNS-loggning och diagnostik i Windows Server 2012 R2.

## <a name="configuration"></a>Konfiguration

Använd följande information för att konfigurera lösningen:

- Du måste ha en [Windows](log-analytics-windows-agent.md) eller [Operations Manager](log-analytics-om-agents.md) agenten på varje DNS-server som du vill övervaka.
- Du kan lägga till DNS-Analytics lösningen till Operations Management Suite-arbetsyta från den [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Du kan också använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).

Lösningen börjar samla in data utan behov av ytterligare konfiguration. Du kan dock använda följande konfiguration för att anpassa datainsamling.

### <a name="configure-the-solution"></a>Konfigurera lösningen

Klicka på instrumentpanelen i lösningen **Configuration** att öppna sidan DNS-konfiguration. Det finns två typer av konfigurationsändringar som du kan göra:

- **Listan över godkända domännamn**. Lösningen bearbetar inte sökning-frågor. Det finns en godkänd lista över namnsuffix. Sökning-frågor som matchar de domännamn som matchar namnsuffix i den här listan över godkända bearbetas inte av lösningen. Icke-bearbetning av listan över godkända domännamn hjälper till att optimera data som skickas till logganalys. Standard-listan innehåller populära offentliga domännamn, till exempel www.google.com och www.facebook.com. Du kan visa den fullständiga listan genom att bläddra.

 Du kan ändra listan för att lägga till alla domänsuffix namn som du vill visa sökning insikter för. Du kan också ta bort alla domänens namnsuffix som du inte vill visa sökning insikter för.

- **Tröskelvärdet för talkative klienter**. DNS-klienter som överskrider tröskelvärdet för antalet sökningsbegäranden är markerade i den **DNS-klienter** bladet. Standardtröskeln är 1 000. Du kan redigera tröskelvärdet.

    ![Listan över godkända domännamn](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Hanteringspaket

Om du använder Microsoft Monitoring Agent för att ansluta till Operations Management Suite-arbetsyta installeras följande hanteringspaket:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Om din Operations Manager-hanteringsgrupp är ansluten till Operations Management Suite-arbetsyta, installeras följande hanteringspaket i Operations Manager när du lägger till den här lösningen. Det finns ingen konfiguration eller underhåll av dessa hanteringspaket:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Använda DNS-Analytics-lösning

Det här avsnittet beskriver alla instrumentpanelen funktioner och hur de används.

När du har lagt till lösningen till din arbetsyta, ger panelen lösning på översiktssidan Operations Management Suite en snabb översikt över DNS-infrastrukturen. Den innehåller antalet DNS-servrar där data som samlas in. Den innehåller också antalet begäranden som görs av klienter för att matcha skadliga domäner under de senaste 24 timmarna. När du klickar på ikonen öppnas lösning instrumentpanelen.

![DNS-Analytics sida vid sida](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Instrumentpanel för lösningen

Lösning instrumentpanelen visar sammanfattningsinformation för de olika funktionerna i lösningen. Den innehåller också länkar till den detaljerade vyn för kriminalteknisk analys och diagnos. Som standard visas data för de senaste sju dagarna. Du kan ändra intervallet datum och tid genom att använda den **tid markering**, enligt följande bild:

![Tid markering](./media/log-analytics-dns/dns-time.png)

Lösning instrumentpanelen visar följande blad:

**DNS-säkerhet**. Rapporter om DNS-klienter som försöker kommunicera med skadliga domäner. Med hjälp av Microsoft threat intelligence feeds identifiera DNS Analytics klientens IP-adresser som försöker komma åt skadliga domäner. I många fall uppringning enheter som infekterats av skadlig kod ”” till ”kommando- och” mitten av skadliga domänen genom att lösa domännamn skadlig kod.

![DNS-säkerhet bladet](./media/log-analytics-dns/dns-security-blade.png)

När du klickar på en klient-IP-adress i listan loggen sökning öppnas och visar information om respektive frågan sökning. I följande exempel visas DNS Analytics upptäckte att kommunikationen har utförts med en [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Loggen sökresultat visar ircbot](./media/log-analytics-dns/ircbot.png)

Informationen hjälper dig att identifiera den:

- Klientens IP-adress som initierade kommunikationen.
- Domännamnet som matchas till skadliga IP-Adressen.
- IP-adresser som motsvarar namnet på en domän.
- Skadliga IP-adress.
- Problemets allvarlighetsgrad.
- Orsak till att godkänna skadliga IP-Adressen.
- Identifieringstiden.

**Domäner som efterfrågas**. Innehåller de vanligaste domännamn som efterfrågas av DNS-klienter i din miljö. Du kan visa listan över alla domännamn som efterfrågas. Du kan också öka detaljnivån sökning begäran detaljer om ett visst domännamn i loggen sökningen.

![Domains Queried blade](./media/log-analytics-dns/domains-queried-blade.png)

**DNS-klienter**. Rapporterar klienter *brott mot tröskelvärdet* för antal frågor i den valda tidsperioden. Du kan visa listan över DNS-klienter och detaljerna för de frågor som gjorts av dem i loggen sökningen.

![Bladet för DNS-klienter](./media/log-analytics-dns/dns-clients-blade.png)

**Dynamiska DNS-registreringar**. Rapporter namnge registreringsfel. Alla registreringsfel för adress [resursposter](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A och AAAA) markeras tillsammans med klienten IP-adresser som gjort registrerings-begäranden. Du kan sedan använda den här informationen för att hitta orsaken till felet registreringen genom att följa dessa steg:

1. Hitta zonen som är auktoritär för namnet som klienten försöker uppdatera.

2. Använda lösningen för att kontrollera inventeringsinformation för zonen.

3. Kontrollera att dynamisk uppdatering för zonen är aktiverad.

4. Kontrollera om zonen är konfigurerat för säkra dynamiska uppdateringar eller inte.

    ![Bladet för dynamisk DNS-registrering](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Namnge förfrågningar om klientregistrering**. Den övre panelen visas en trendlinje för lyckade och misslyckade DNS dynamisk uppdatering. Den nedre panelen visas de översta 10 klienter som skickar misslyckade DNS-uppdatering begäranden till DNS-servrar, sorterade efter antalet fel.

![Namnet registrering begäranden bladet ](./media/log-analytics-dns/name-reg-req-blade.png)

**Exempel på DDI Analytics frågor**. Innehåller en lista över de vanligaste sökfrågor som hämta rådata analysdata direkt.


![Exempelfrågor](./media/log-analytics-dns/queries.png)

Du kan använda de här frågorna som utgångspunkt för att skapa egna frågor för anpassad rapportering. Länken frågor till DNS-Analytics loggen söksidan där resultat visas:

- **Lista över DNS-servrar**. Visar en lista över alla DNS-servrar med deras associerade FQDN, domännamn, skogsnamnet och servern IP-adresser.
- **Lista över DNS-zoner**. Visar en lista över alla DNS-zoner med associerade zonnamnet, status för dynamisk uppdatering, namnservrar och DNSSEC-signering.
- **Oanvända resursposter**. Visar en lista över alla oanvända/inaktuell resursposterna. Den här listan innehåller resurs postens namn, typ av resurspost, associerade DNS-server, registrera Genereringstid och zonnamnet. Du kan använda den här listan för att identifiera de DNS-poster som inte längre används. Baserat på den här informationen kan du kan sedan ta bort dessa poster från DNS-servrar.
- **DNS-servrar fråga belastningen**. Visar information så att du kan få ett perspektiv DNS-belastningen på din DNS-servrar. Den här informationen kan hjälpa dig att planera kapaciteten för servrarna. Du kan gå till den **mått** att ändra vyn till en grafisk visualisering. Den här vyn hjälper dig att förstå hur DNS belastningen distribueras över DNS-servrar. DNS-fråga visar trender i frekvensen för varje server.

    ![Sökresultat för DNS-servrar frågan logg](./media/log-analytics-dns/dns-servers-query-load.png)

- **DNS-zoner fråga belastningen**. Visar DNS-zonen frågan per sekund statistik över alla zoner på DNS-servrar som hanteras av lösningen. Klicka på den **mått** att ändra vyn från detaljerad poster till en grafisk visualisering av resultaten.
- **Konfigurationshändelser**. Visar alla DNS-konfigurationsändringshändelserna och associerade meddelanden. Därefter kan du filtrera händelserna baserat på tid för händelse, händelse-ID, DNS-servern eller aktivitetskategori. Med hjälp av data kan du granska ändringar som gjorts i specifika DNS-servrar vid specifika tidpunkter.
- **DNS-analytiska loggen**. Visar alla händelser som analytiska på alla DNS-servrar som hanteras av lösningen. Därefter kan du filtrera händelserna baserat på tid för händelse, händelse-ID, DNS-servern klientens IP-Adressen som gjorts sökning frågan och fråga kategori för aktiviteten. DNS-server-analyshändelser aktivera Aktivitetsspårning på DNS-servern. En analytiska händelse loggas varje gång servern skickar och tar emot DNS-information.

### <a name="search-by-using-dns-analytics-log-search"></a>Sök med hjälp av DNS-Analytics loggen sökning

På sidan Logga, kan du skapa en fråga. Du kan filtrera sökresultaten genom att använda aspekten kontroller. Du kan också skapa avancerade frågor för att transformera, filter och rapporten på dina resultat. Starta med hjälp av följande frågor:

1. I den **frågan sökrutan**, typen `DnsEvents` att visa alla DNS-händelser som genererats av DNS-servrar som hanteras av lösningen. Loggdata för alla händelser relaterade till sökning-frågor, dynamiska registreringar och konfigurationsändringar resultatlistan.

    ![DnsEvents loggen Sök](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Om du vill visa loggdata för sökning-frågor, Välj **LookUpQuery** som den **undertyp** filter från aspekten kontrollen till vänster. En tabell som visar alla händelser för sökning-frågan för den valda tidsperioden visas.

    b. Om du vill visa loggdata för dynamiska registreringar **DynamicRegistration** som den **undertyp** filter från aspekten kontrollen till vänster. En tabell som visar alla händelser som dynamisk registrering för den valda tidsperioden visas.

    c. Om du vill visa loggdata för konfigurationsändringar **ConfigurationChange** som den **undertyp** filter från aspekten kontrollen till vänster. En tabell som listar alla konfigurationsändringshändelserna för den valda tidsperioden visas.

2. I den **frågan sökrutan**, typen `DnsInventory` att visa alla DNS-inventeringsrelaterade data för de DNS-servrar som hanteras av lösningen. Loggdata för DNS-servrar, DNS-zoner och resursposter resultatlistan.

    ![DnsInventory loggen Sök](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Feedback

Det finns två sätt kan du ge feedback:

- **UserVoice**. Bokför idéer för DNS-Analytics funktioner fungerar på. Besök den [Operations Management Suite UserVoice sidan](https://aka.ms/dnsanalyticsuservoice).
- **Ansluta till vår kommittén**. Vi vill alltid med nya kunder ansluta till vår kohorter för att få snabb åtkomst till nya funktioner och hjälp oss att förbättra DNS Analytics. Om du vill ansluta till vår kohorter fylla [snabb undersökningen](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Nästa steg

[Söka i loggar](log-analytics-log-searches.md) att visa detaljerad DNS-poster.
