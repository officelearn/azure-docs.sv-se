---
title: DNS-analys lösning i Azure Monitor | Microsoft Docs
description: Konfigurera och Använd DNS-analys-lösningen i Azure Monitor för att samla in insikter om säkerhet, prestanda och åtgärder i DNS-infrastrukturen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657341"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Samla in information om DNS-infrastrukturen med förhandsversionen av DNS Analytics-lösning

![Symbol för DNS-analys](./media/dns-analytics/dns-analytics-symbol.png)

Den här artikeln beskriver hur du konfigurerar och använder Azure DNS Analytics-lösningen i Azure Monitor för att samla in insikter om säkerhet, prestanda och åtgärder i DNS-infrastrukturen.

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
| [Windows-agenter](../platform/agent-windows.md) | Ja | Lösningen samlar in DNS-information från Windows-agenter. |
| [Linux-agenter](../learn/quick-collect-linux-computer.md) | Nej | Lösningen samlar inte in DNS-information från direkt Linux-agenter. |
| [System Center Operations Manager hanterings grupp](../platform/om-agents.md) | Ja | Lösningen samlar in DNS-information från agenter i en ansluten hanteringsgrupp för Operations Manager. En direkt anslutning från Operations Manager agent till Azure Monitor krävs inte. Data vidarebefordras från hanteringsgruppen till Log Analytics-arbetsytan. |
| [Azure Storage-konto](../platform/collect-azure-metrics-logs.md) | Nej | Azure storage används inte av lösningen. |

### <a name="data-collection-details"></a>Information om insamling av data

Lösningen samlar in DNS-inventering och DNS-händelse-relaterade data från DNS-servrar där en Log Analytics-agenten är installerad. Dessa data överförs sedan till Azure Monitor och visas i instrument panelen för lösningen. Inventeringsrelaterade data, till exempel antalet DNS-servrar, zoner och resursposter som samlas in genom att köra DNS PowerShell-cmdlets. Data uppdateras en gång varannan dag. Händelse-relaterade data samlas in nära real tid från de [analytiska och gransknings loggar](https://technet.microsoft.com/library/dn800669.aspx#enhanc) som tillhandahålls av förbättrad DNS-loggning och diagnostik i Windows Server 2012 R2.

## <a name="configuration"></a>Konfiguration

Använd följande information för att konfigurera lösningen:

- Du måste ha en [Windows](../platform/agent-windows.md) -eller [Operations Manager](../platform/om-agents.md) -agent på varje DNS-server som du vill övervaka.
- Du kan lägga till DNS-analys-lösningen till din Log Analytics-arbetsyta från [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Du kan också använda processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](solutions.md).

Lösningen börjar samla in data utan behov av ytterligare konfiguration. Du kan dock använda följande konfiguration för att anpassa datainsamling.

### <a name="configure-the-solution"></a>Konfigurera lösningen

På instrument panelen för lösningen klickar du på **konfiguration** för att öppna sidan DNS-analys konfiguration. Det finns två typer av konfigurationsändringar som du kan göra:

- **Vit listas domän namn**. Lösningen bearbetar inte sökning-frågor. Den upprätthåller en lista över tillåtna domännamnssuffix. Sökning-frågor som matchar de domännamn som matchar domännamnssuffixen i den här listan bearbetas inte av lösningen. Om du inte bearbetar vit listas domän namn kan du optimera data som skickas till Azure Monitor. Standardlistan över tillåtna objekt omfattar populära offentliga domännamn, t.ex www.google.com och www.facebook.com. Du kan visa listan över slutförts genom att bläddra.

  Du kan ändra listan för att lägga till alla domänens namnsuffix som du vill visa sökning insikter för. Du kan också ta bort alla domänens namnsuffix som du inte vill visa sökning insikter för.

- **Tröskelvärde för pratsam-klient**. DNS-klienter som överskrider tröskelvärdet för antalet uppslags förfrågningar är markerade på bladet **DNS-klienter** . Standardtröskeln är 1 000. Du kan redigera tröskelvärdet.

    ![Godkänd domännamn](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Hanteringspaket

Om du använder Microsoft Monitoring Agent för att ansluta till Log Analytics-arbetsytan installeras följande hanteringspaket:

- Microsoft DNS data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Om din hanteringsgrupp för Operations Manager är ansluten till Log Analytics-arbetsytan kan installeras följande hanteringspaket i Operations Manager när du lägger till den här lösningen. Det finns ingen konfiguration som krävs eller underhåll av dessa hanteringspaket:

- Microsoft DNS data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Konfiguration av Microsoft System Center Advisor DNS Analytics (Microsoft.IntelligencePack.Dns.Configuration)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Använda DNS Analytics-lösningen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Panelen DNS innehåller antalet DNS-servrar där data samlas in. Den innehåller också antalet begäranden som görs av klienter du löser skadliga domäner under de senaste 24 timmarna. När du klickar på panelen öppnas instrumentpanelen för lösningen.

![DNS Analytics-ikonen](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Instrumentpanel för lösningen

Lösningens instrumentpanel visar sammanfattningsinformation för de olika funktionerna i lösningen. Den innehåller också länkar till den detaljerade vyn för kriminalteknisk analys och diagnostik. Som standard visas data för de senaste sju dagarna. Du kan ändra datum-och tidsintervallet med hjälp av **kontrollen för datum-och tids markering**, som du ser i följande bild:

![Kontroll för val av tid](./media/dns-analytics/dns-time.png)

Lösningens instrumentpanel visar följande blad:

**DNS-säkerhet**. Rapporter om DNS-klienter som försöker kommunicera med skadliga domäner. Med hjälp av Microsoft hotinformation identifiera DNS Analytics klientens IP-adresser som försöker få åtkomst till skadliga domäner. I många fall kan uppringning enheter som infekterats av skadlig kod ”” till ”kommando och kontroll” mitten av skadliga domänen genom att lösa domännamn för skadlig kod.

![Bladet DNS-säkerhet](./media/dns-analytics/dns-security-blade.png)

När du klickar på en klientens IP-adress i listan Log Search öppnas och visar information om respektive frågan sökning. I följande exempel har DNS-analys identifierat att kommunikationen har gjorts med en [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Loggsökningen som visar ircbot](./media/dns-analytics/ircbot.png)

Informationen hjälper dig att identifiera den:

- Klientens IP-adress som initierade kommunikationen.
- Domännamnet som matchas till skadlig IP-adress.
- IP-adresser som matchar domännamnet.
- Skadliga IP-adress.
- Problemets allvarlighetsgrad.
- Orsaken till att godkänna skadlig IP-adress.
- Tidpunkten för identifieringen.

**Efterfrågade domäner**. Innehåller de vanligaste domännamn som efterfrågas av DNS-klienter i din miljö. Du kan visa listan över alla domännamn som efterfrågas. Du kan också granska nedåt i Frågedetaljer lookup för ett specifikt domännamn i Loggsökning.

![Domäner sökt bladet](./media/dns-analytics/domains-queried-blade.png)

**DNS-klienter**. Rapporterar klienter som *bryter mot tröskelvärdet* för antalet frågor under den valda tids perioden. Du kan visa listan över DNS-klienter och information om de frågor som gjorts av dem i Loggsökning.

![Bladet DNS-klienter](./media/dns-analytics/dns-clients-blade.png)

**Dynamiska DNS-registreringar**. Rapporter namnge registreringsfel. Alla registrerings fel för adress [resurs poster](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A och AAAA) är markerade tillsammans med de klient-IP: er som gjorde registrerings förfrågningarna. Du kan sedan använda den här informationen för att hitta orsaken till felet registreringen genom att följa dessa steg:

1. Hitta de zoner som är auktoritära för namn som klienten försöker att uppdatera.

1. Använd lösningen för att kontrollera inventeringsinformation för zonen.

1. Kontrollera att dynamisk uppdatering för zonen är aktiverad.

1. Kontrollera om zonen är konfigurerad för säker dynamisk uppdatering eller inte.

    ![Bladet för dynamisk DNS-registreringar](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Begär Anden om namn registrering**. Den övre panelen visas en trendlinje av begäranden om lyckade och misslyckade DNS-dynamiska uppdateringar. Lägre panelen visar de översta 10 klienter som skickar begäranden om indexuppdatering av misslyckade DNS till DNS-servrar, sorterade efter antalet fel.

![Namn på registrering begäranden bladet](./media/dns-analytics/name-reg-req-blade.png)

**Exempel på DDI Analytics-frågor**. Innehåller en lista över de vanligaste sökfrågorna som hämtar raw analysdata direkt.


![Exempelfrågor](./media/dns-analytics/queries.png)

Du kan använda de här frågorna som utgångspunkt för att skapa egna frågor för anpassad rapportering. Frågorna länka till sidan DNS Analytics-Loggsökning där resultatet visas:

- **Lista över DNS-servrar**. Visar en lista över alla DNS-servrar med deras associerade FQDN, domännamn, skogsnamnet och server IP-adresser.
- **Lista över DNS-zoner**. Visar en lista över alla DNS-zoner med associerade zonnamnet, status för dynamisk uppdatering, DNS-servrar och DNSSEC-signering.
- **Oanvända resurs poster**. Visar en lista över alla de oanvända/föråldrade resursposterna. Den här listan innehåller resursnamn för posten, typ av resurspost, associerade DNS-server, registrera Genereringstid och zonnamnet. Du kan använda den här listan för att identifiera de DNS-poster som inte längre används. Baserat på den här informationen kan du kan sedan ta bort dessa poster från DNS-servrar.
- **Fråga om inläsning av DNS-servrar**. Visar information så att du kan få en översikt över DNS-belastningen på dina DNS-servrar. Den här informationen kan hjälpa dig att planera kapacitet för servrarna. Du kan gå till fliken **mått** om du vill ändra vyn till en grafisk visualisering. Den här vyn hjälper dig att förstå hur DNS-belastningen fördelas över DNS-servrar. DNS-fråga visar trender i frekvensen för varje server.

    ![DNS-servrar log search-frågeresultat](./media/dns-analytics/dns-servers-query-load.png)

- **Inläsning av DNS-zoner fråga**. Visar DNS-zon fråga per sekund statistik över alla zoner på DNS-servrar som hanteras av lösningen. Klicka på fliken **mått** om du vill ändra vyn från detaljerade poster till en grafisk visualisering av resultaten.
- **Konfigurations händelser**. Visar alla DNS-konfigurationsändringshändelserna och tillhörande meddelanden. Sedan kan du filtrera händelserna baserat på tid för händelse, händelse-ID, DNS-server eller aktivitetskategori. Med hjälp av data kan du granska ändringar som gjorts till specifika DNS-servrar vid specifika tidpunkter.
- **DNS-analytisk logg**. Visar alla händelser som analytiska på alla DNS-servrar som hanteras av lösningen. Sedan kan du filtrera händelserna baserat på tid för händelse, händelse-ID, DNS-server, klient-IP som gjorts lookup fråga och kategori för aktiviteten fråga. DNS-server-analyshändelser aktivera Aktivitetsspårning på DNS-servern. En analytiska händelse loggas varje gång servern skickar eller tar emot DNS-information.

### <a name="search-by-using-dns-analytics-log-search"></a>Sök med hjälp av DNS Analytics-Loggsökning

På sidan Log Search kan du skapa en fråga. Du kan filtrera sökresultaten genom att använda aspekten kontroller. Du kan också skapa avancerade frågor för att transformera, filtrera och rapporten på dina resultat. Starta med hjälp av följande frågor:

1. I **rutan Sök fråga**skriver du `DnsEvents` för att visa alla DNS-händelser som genererats av DNS-servrarna som hanteras av lösningen. I resultatlistan loggdata för alla händelser som rör sökning-frågor, dynamiska registreringar och ändringar i konfigurationen.

    ![DnsEvents loggsökning](./media/dns-analytics/log-search-dnsevents.png)  

    a. Om du vill visa loggdata för Sök frågor väljer du **LookUpQuery** som under **typs** filter från fasett-kontrollen till vänster. En tabell som visar alla händelser för lookup-fråga för den valda tidsperioden visas.

    b. Om du vill visa loggdata för dynamiska registreringar väljer du **DynamicRegistration** som under **typs** filter från fasett-kontrollen till vänster. En tabell som listar alla dynamiska registreringshändelser för den valda tidsperioden visas.

    c. Om du vill visa logg data för konfigurations ändringar väljer du **ConfigurationChange** som under **typs** filter från fasett-kontrollen till vänster. En tabell som listar alla konfigurationsändringshändelserna för den valda tidsperioden visas.

1. I **rutan Sök fråga**skriver du `DnsInventory` för att visa alla DNS-relaterade data för DNS-inventeringen för DNS-servrarna som hanteras av lösningen. I resultatlistan loggdata för DNS-servrar, DNS-zoner och resursposter.

    ![DnsInventory loggsökning](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Felsökning

Vanliga fel söknings steg:

1. DNS-sökningar saknas – för att felsöka det här problemet kan du försöka med att återställa konfigurationen eller läsa in konfigurations sidan en gång i portalen. För att återställa, ändra bara en inställning till ett annat värde och ändra sedan tillbaka till det ursprungliga värdet och spara konfigurationen.

## <a name="feedback"></a>Feedback

Om du vill ge feedback kan du gå till [sidan Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) och publicera idéer för DNS-analys funktioner att arbeta med. 

## <a name="next-steps"></a>Nästa steg

[Fråga loggar](../log-query/log-query-overview.md) om du vill visa detaljerade DNS-loggfiler.
