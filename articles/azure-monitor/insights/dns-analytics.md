---
title: DNS Analytics-lösningen i Azure Log Analytics | Microsoft Docs
description: Konfigurera och använda DNS Analytics-lösningen i Log Analytics för att samla in insikter om DNS-infrastrukturen på säkerhet, prestanda och åtgärder.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: 5641792c058d10bba1bfd468f3c5c90b9217226e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187661"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Samla in information om DNS-infrastrukturen med förhandsversionen av DNS Analytics-lösning

![Symbol för DNS-analys](./media/dns-analytics/dns-analytics-symbol.png)

Den här artikeln beskriver hur du ställer in och använda Azure DNS Analytics-lösning i Azure Log Analytics för att samla in insikter om DNS-infrastrukturen på säkerhet, prestanda och åtgärder.

DNS Analytics hjälper dig att:

- Identifiera klienter som försöker matcha skadliga domännamn.
- Identifiera inaktuella poster.
- Identifiera ofta efterfrågade domännamn och pratsam DNS-klienter.
- Visa belastning för begäranden på DNS-servrar.
- Visa dynamiska DNS-registreringsfel.

Lösningen samlar in, analyserar och korrelerar Windows DNS analytiska och granskningsloggar och andra relaterade data från dina DNS-servrar.

## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| **Ansluten källa** | **Support** | **Beskrivning** |
| --- | --- | --- |
| [Windows-agenter](../../azure-monitor/platform/agent-windows.md) | Ja | Lösningen samlar in DNS-information från Windows-agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nej | Lösningen samlar inte in DNS-information från direkt Linux-agenter. |
| [System Center Operations Manager-hanteringsgrupp](../../azure-monitor/platform/om-agents.md) | Ja | Lösningen samlar in DNS-information från agenter i en ansluten hanteringsgrupp för Operations Manager. En direktanslutning från Operations Manager-agenten till Log Analytics krävs inte. Data vidarebefordras från hanteringsgruppen till Log Analytics-arbetsytan. |
| [Azure Storage-konto](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nej | Azure storage används inte av lösningen. |

### <a name="data-collection-details"></a>Information om insamling av data

Lösningen samlar in DNS-inventering och DNS-händelse-relaterade data från DNS-servrar där en Log Analytics-agenten är installerad. Dessa data överförs till Log Analytics sedan och visas i instrumentpanelen för lösningen. Inventeringsrelaterade data, till exempel antalet DNS-servrar, zoner och resursposter som samlas in genom att köra DNS PowerShell-cmdlets. Data uppdateras en gång varannan dag. Händelse-relaterade data samlas nära realtid från den [analytiska och granskningsloggar](https://technet.microsoft.com/library/dn800669.aspx#enhanc) tillhandahålls av förbättrad DNS-loggning och diagnostik i Windows Server 2012 R2.

## <a name="configuration"></a>Konfiguration

Använd följande information för att konfigurera lösningen:

- Du måste ha en [Windows](../../azure-monitor/platform/agent-windows.md) eller [Operations Manager](../../azure-monitor/platform/om-agents.md) agenten på varje DNS-server som du vill övervaka.
- Du kan lägga till DNS Analytics-lösningen i Log Analytics-arbetsytan från den [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Du kan också använda metoden som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).

Lösningen börjar samla in data utan behov av ytterligare konfiguration. Du kan dock använda följande konfiguration för att anpassa datainsamling.

### <a name="configure-the-solution"></a>Konfigurera lösningen

Klicka på instrumentpanelen för lösningen **Configuration** att öppna sidan konfiguration av DNS Analytics. Det finns två typer av konfigurationsändringar som du kan göra:

- **Godkänd domännamn**. Lösningen bearbetar inte sökning-frågor. Den upprätthåller en lista över tillåtna domännamnssuffix. Sökning-frågor som matchar de domännamn som matchar domännamnssuffixen i den här listan bearbetas inte av lösningen. Icke-bearbetning av listan över godkända domännamn som hjälper dig för att optimera de data som skickas till Log Analytics. Standardlistan över tillåtna objekt omfattar populära offentliga domännamn, t.ex www.google.com och www.facebook.com. Du kan visa listan över slutförts genom att bläddra.

 Du kan ändra listan för att lägga till alla domänens namnsuffix som du vill visa sökning insikter för. Du kan också ta bort alla domänens namnsuffix som du inte vill visa sökning insikter för.

- **Tröskelvärde för pratsam klient**. DNS-klienter som överskrider tröskelvärdet för antalet uppslagsförfrågningar är markerade på den **DNS-klienter** bladet. Standardtröskeln är 1 000. Du kan redigera tröskelvärdet.

    ![Godkänd domännamn](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Hanteringspaket

Om du använder Microsoft Monitoring Agent för att ansluta till Log Analytics-arbetsytan installeras följande hanteringspaket:

- Microsoft DNS-Data Collector-informationspaketet (Microsft.IntelligencePacks.Dns)

Om din hanteringsgrupp för Operations Manager är ansluten till Log Analytics-arbetsytan kan installeras följande hanteringspaket i Operations Manager när du lägger till den här lösningen. Det finns ingen konfiguration som krävs eller underhåll av dessa hanteringspaket:

- Microsoft DNS-Data Collector-informationspaketet (Microsft.IntelligencePacks.Dns)
- Konfiguration av Microsoft System Center Advisor DNS Analytics (Microsoft.IntelligencePack.Dns.Configuration)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Använda DNS Analytics-lösningen

Det här avsnittet beskrivs alla instrumentpanelen funktioner och hur de används.

När du har lagt till lösningen till din arbetsyta, översikt över Log Analytics-sidan på Azure portal innehåller ett **visa lösningar** länk till en snabb sammanfattning av DNS-infrastrukturen. Den innehåller antalet DNS-servrar där data samlas in. Den innehåller också antalet begäranden som görs av klienter du löser skadliga domäner under de senaste 24 timmarna. När du klickar på panelen öppnas instrumentpanelen för lösningen.

![DNS Analytics-ikonen](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Instrumentpanel för lösningen

Lösningens instrumentpanel visar sammanfattningsinformation för de olika funktionerna i lösningen. Den innehåller också länkar till den detaljerade vyn för kriminalteknisk analys och diagnostik. Som standard visas data för de senaste sju dagarna. Du kan ändra intervallet för datum och tid genom att använda den **val av datum / tid-kontroll**, enligt följande bild:

![Kontroll för val av tid](./media/dns-analytics/dns-time.png)

Lösningens instrumentpanel visar följande blad:

**DNS-säkerhet**. Rapporter om DNS-klienter som försöker kommunicera med skadliga domäner. Med hjälp av Microsoft hotinformation identifiera DNS Analytics klientens IP-adresser som försöker få åtkomst till skadliga domäner. I många fall kan uppringning enheter som infekterats av skadlig kod ”” till ”kommando och kontroll” mitten av skadliga domänen genom att lösa domännamn för skadlig kod.

![Bladet DNS-säkerhet](./media/dns-analytics/dns-security-blade.png)

När du klickar på en klientens IP-adress i listan Log Search öppnas och visar information om respektive frågan sökning. I följande exempel visas DNS Analytics upptäckte att kommunikationen gjordes med en [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Loggsökningen som visar ircbot](./media/dns-analytics/ircbot.png)

Informationen hjälper dig att identifiera den:

- Klientens IP-adress som initierade kommunikationen.
- Domännamnet som matchas till skadlig IP-adress.
- IP-adresser som matchar domännamnet.
- Skadliga IP-adress.
- Problemets allvarlighetsgrad.
- Orsaken till att godkänna skadlig IP-adress.
- Tidpunkten för identifieringen.

**Domäner som efterfrågas**. Innehåller de vanligaste domännamn som efterfrågas av DNS-klienter i din miljö. Du kan visa listan över alla domännamn som efterfrågas. Du kan också granska nedåt i Frågedetaljer lookup för ett specifikt domännamn i Loggsökning.

![Domäner sökt bladet](./media/dns-analytics/domains-queried-blade.png)

**DNS-klienter**. Rapporterar klienterna *brott mot tröskelvärdet* för antal frågor under den valda tidsperioden. Du kan visa listan över DNS-klienter och information om de frågor som gjorts av dem i Loggsökning.

![Bladet DNS-klienter](./media/dns-analytics/dns-clients-blade.png)

**Dynamiska DNS-registreringar**. Rapporter namnge registreringsfel. Alla registreringsfel för adress [resursposter](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A och AAAA) är markerade tillsammans med klienten IP-adresser som gjorts i registreringsbegäranden. Du kan sedan använda den här informationen för att hitta orsaken till felet registreringen genom att följa dessa steg:

1. Hitta de zoner som är auktoritära för namn som klienten försöker att uppdatera.

1. Använd lösningen för att kontrollera inventeringsinformation för zonen.

1. Kontrollera att dynamisk uppdatering för zonen är aktiverad.

1. Kontrollera om zonen är konfigurerad för säker dynamisk uppdatering eller inte.

    ![Bladet för dynamisk DNS-registreringar](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Namnge registreringsbegäranden**. Den övre panelen visas en trendlinje av begäranden om lyckade och misslyckade DNS-dynamiska uppdateringar. Lägre panelen visar de översta 10 klienter som skickar begäranden om indexuppdatering av misslyckade DNS till DNS-servrar, sorterade efter antalet fel.

![Namn på registrering begäranden bladet ](./media/dns-analytics/name-reg-req-blade.png)

**Exempel på DDI analysfrågor**. Innehåller en lista över de vanligaste sökfrågorna som hämtar raw analysdata direkt.


![Exempelfrågor](./media/dns-analytics/queries.png)

Du kan använda de här frågorna som utgångspunkt för att skapa egna frågor för anpassad rapportering. Frågorna länka till sidan DNS Analytics-Loggsökning där resultatet visas:

- **Lista över DNS-servrar**. Visar en lista över alla DNS-servrar med deras associerade FQDN, domännamn, skogsnamnet och server IP-adresser.
- **Lista över DNS-zoner**. Visar en lista över alla DNS-zoner med associerade zonnamnet, status för dynamisk uppdatering, DNS-servrar och DNSSEC-signering.
- **Oanvända resursposter**. Visar en lista över alla de oanvända/föråldrade resursposterna. Den här listan innehåller resursnamn för posten, typ av resurspost, associerade DNS-server, registrera Genereringstid och zonnamnet. Du kan använda den här listan för att identifiera de DNS-poster som inte längre används. Baserat på den här informationen kan du kan sedan ta bort dessa poster från DNS-servrar.
- **Frågeinläsning i DNS-servrar**. Visar information så att du kan få en översikt över DNS-belastningen på dina DNS-servrar. Den här informationen kan hjälpa dig att planera kapacitet för servrarna. Du kan gå till den **mått** flik för att ändra vyn till en grafisk visualisering. Den här vyn hjälper dig att förstå hur DNS-belastningen fördelas över DNS-servrar. DNS-fråga visar trender i frekvensen för varje server.

    ![DNS-servrar log search-frågeresultat](./media/dns-analytics/dns-servers-query-load.png)

- **Frågeinläsning i DNS-zoner**. Visar DNS-zon fråga per sekund statistik över alla zoner på DNS-servrar som hanteras av lösningen. Klicka på den **mått** flik för att ändra vyn från detaljerad poster i en grafisk visualisering av resultaten.
- **Konfigurationshändelser**. Visar alla DNS-konfigurationsändringshändelserna och tillhörande meddelanden. Sedan kan du filtrera händelserna baserat på tid för händelse, händelse-ID, DNS-server eller aktivitetskategori. Med hjälp av data kan du granska ändringar som gjorts till specifika DNS-servrar vid specifika tidpunkter.
- **DNS-Analyslogg**. Visar alla händelser som analytiska på alla DNS-servrar som hanteras av lösningen. Sedan kan du filtrera händelserna baserat på tid för händelse, händelse-ID, DNS-server, klient-IP som gjorts lookup fråga och kategori för aktiviteten fråga. DNS-server-analyshändelser aktivera Aktivitetsspårning på DNS-servern. En analytiska händelse loggas varje gång servern skickar eller tar emot DNS-information.

### <a name="search-by-using-dns-analytics-log-search"></a>Sök med hjälp av DNS Analytics-Loggsökning

På sidan Log Search kan du skapa en fråga. Du kan filtrera sökresultaten genom att använda aspekten kontroller. Du kan också skapa avancerade frågor för att transformera, filtrera och rapporten på dina resultat. Starta med hjälp av följande frågor:

1. I den **fråga sökrutan**, typ `DnsEvents` att visa alla DNS-händelser som genererats av DNS-servrar som hanteras av lösningen. I resultatlistan loggdata för alla händelser som rör sökning-frågor, dynamiska registreringar och ändringar i konfigurationen.

    ![DnsEvents loggsökning](./media/dns-analytics/log-search-dnsevents.png)  

    a. Om du vill visa loggdata för sökning-frågor, Välj **LookUpQuery** som den **undertyp** filter från aspekten kontrollen till vänster. En tabell som visar alla händelser för lookup-fråga för den valda tidsperioden visas.

    b. Om du vill visa loggdata för dynamiska registreringar, Välj **DynamicRegistration** som den **undertyp** filter från aspekten kontrollen till vänster. En tabell som listar alla dynamiska registreringshändelser för den valda tidsperioden visas.

    c. Om du vill visa loggdata för konfigurationsändringar, Välj **ConfigurationChange** som den **undertyp** filter från aspekten kontrollen till vänster. En tabell som listar alla konfigurationsändringshändelserna för den valda tidsperioden visas.

1. I den **fråga sökrutan**, typ `DnsInventory` att visa alla DNS-inventeringsrelaterade data för DNS-servrar som hanteras av lösningen. I resultatlistan loggdata för DNS-servrar, DNS-zoner och resursposter.

    ![DnsInventory loggsökning](./media/dns-analytics/log-search-dnsinventory.png)

## <a name="feedback"></a>Feedback

Det finns två sätt som du kan ge feedback:

- **UserVoice**. Publicera idéer om DNS Analytics-funktioner att arbeta med. Gå till den [Log Analytics UserVoice-sida](https://aka.ms/dnsanalyticsuservoice).
- **Ansluta till vår kohort**. Vi är alltid intresserade med nya kunder ansluta till vår kohorter för att få snabb åtkomst till nya funktioner och hjälp oss att förbättra DNS-analys. Om du vill ansluta till vår kohorter fyller [den här snabba undersökningen](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Nästa steg

[Söka loggarna](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerad DNS-poster.
