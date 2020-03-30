---
title: DNS Analytics-lösning i Azure Monitor | Microsoft-dokument
description: Konfigurera och använd DNS Analytics-lösningen i Azure Monitor för att samla in insikter om DNS-infrastruktur om säkerhet, prestanda och åtgärder.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657341"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Samla in insikter om din DNS-infrastruktur med förhandsversionen av DNS Analytics

![DNS Analytics-symbol](./media/dns-analytics/dns-analytics-symbol.png)

I den här artikeln beskrivs hur du konfigurerar och använder Azure DNS Analytics-lösningen i Azure Monitor för att samla in insikter om DNS-infrastruktur om säkerhet, prestanda och åtgärder.

DNS Analytics hjälper dig att:

- Identifiera klienter som försöker matcha skadliga domännamn.
- Identifiera inaktuella resursposter.
- Identifiera ofta efterfrågade domännamn och pratsamma DNS-klienter.
- Visa belastning för begäran på DNS-servrar.
- Visa dynamiska DNS-registreringsfel.

Lösningen samlar in, analyserar och korrelerar Windows DNS-analys- och granskningsloggar och andra relaterade data från DINA DNS-servrar.

## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| **Ansluten källa** | **Support** | **Beskrivning** |
| --- | --- | --- |
| [Windows-agenter](../platform/agent-windows.md) | Ja | Lösningen samlar in DNS-information från Windows-agenter. |
| [Linux-agenter](../learn/quick-collect-linux-computer.md) | Inga | Lösningen samlar inte in DNS-information från direkta Linux-agenter. |
| [System Center Operations Manager-hanteringsgrupp](../platform/om-agents.md) | Ja | Lösningen samlar in DNS-information från agenter i en ansluten Hanteringsgrupp för Operations Manager. En direkt anslutning från Operations Manager-agenten till Azure Monitor krävs inte. Data vidarebefordras från hanteringsgruppen till log analytics-arbetsytan. |
| [Azure-lagringskonto](../platform/collect-azure-metrics-logs.md) | Inga | Azure-lagring används inte av lösningen. |

### <a name="data-collection-details"></a>Information om datainsamling

Lösningen samlar in DNS-inventering och DNS-händelserelaterade data från DNS-servrarna där en Log Analytics-agent är installerad. Dessa data överförs sedan till Azure Monitor och visas i lösningsinstrumentpanelen. Lagerrelaterade data, till exempel antalet DNS-servrar, zoner och resursposter, samlas in genom att köra DNS PowerShell-cmdletar. Uppgifterna uppdateras en gång varannan dag. Händelserelaterade data samlas in i nära realtid från de [analytiska och granskningsloggar](https://technet.microsoft.com/library/dn800669.aspx#enhanc) som tillhandahålls av förbättrad DNS-loggning och diagnostik i Windows Server 2012 R2.

## <a name="configuration"></a>Konfiguration

Använd följande information för att konfigurera lösningen:

- Du måste ha en [Windows-](../platform/agent-windows.md) eller [Operations Manager-agent](../platform/om-agents.md) på varje DNS-server som du vill övervaka.
- Du kan lägga till DNS Analytics-lösningen i din Log Analytics-arbetsyta från [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Du kan också använda processen som beskrivs i [Lägg till Azure Monitor-lösningar från lösningsgalleriet](solutions.md).

Lösningen börjar samla in data utan att behöva konfigureras ytterligare. Du kan dock använda följande konfiguration för att anpassa datainsamlingen.

### <a name="configure-the-solution"></a>Konfigurera lösningen

Öppna sidan DNS Analytics-konfiguration **på** lösningsinstrumentpanelen. Det finns två typer av konfigurationsändringar som du kan göra:

- **Vitlistade domännamn**. Lösningen bearbetar inte alla uppslagsfrågor. Den har en vitlista över domännamnssuffix. Uppslagsfrågorna som matchar domännamnen som matchar domännamnssuffix i den här vitlistan bearbetas inte av lösningen. Bearbetning av vitlistade domännamn hjälper till att optimera data som skickas till Azure Monitor. Standardvitlistan innehåller populära offentliga domännamn, till exempel www.google.com och www.facebook.com. Du kan visa hela standardlistan genom att rulla.

  Du kan ändra listan för att lägga till alla suffix för domännamn som du vill visa uppslagsstatistik för. Du kan också ta bort alla suffix för domännamn som du inte vill visa uppslagsstatistik för.

- **Talkativ klienttröskel**. DNS-klienter som överskrider tröskelvärdet för antalet uppslagsbegäranden markeras i bladet **DNS-klienter.** Standardtröskeln är 1 000. Du kan redigera tröskelvärdet.

    ![Vitlistade domännamn](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Hanteringspaket

Om du använder Microsoft Monitoring Agent för att ansluta till din Log Analytics-arbetsyta installeras följande hanteringspaket:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)

Om hanteringsgruppen för Operations Manager är ansluten till logganalysarbetsytan installeras följande hanteringspaket i Operations Manager när du lägger till den här lösningen. Det finns ingen konfiguration eller underhåll av dessa hanteringspaket:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)
- DNS Analytics-konfiguration för Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Använda DNS Analytics-lösningen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


DNS-panelen innehåller antalet DNS-servrar där data samlas in. Den innehåller också antalet begäranden som gjorts av klienter för att lösa skadliga domäner under de senaste 24 timmarna. När du klickar på panelen öppnas instrumentpanelen för lösningen.

![Panelen DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Instrumentpanel för lösningen

Lösningsinstrumentpanelen visar sammanfattad information för lösningens olika funktioner. Den innehåller också länkar till den detaljerade vyn för kriminalteknisk analys och diagnos. Som standard visas data för de senaste sju dagarna. Du kan ändra datum- och tidsintervallet med hjälp av **datum-tidsvalskontrollen,** vilket visas i följande bild:

![Kontroll för tidsval](./media/dns-analytics/dns-time.png)

Lösningsinstrumentpanelen visar följande blad:

**DNS-säkerhet**. Rapporterar de DNS-klienter som försöker kommunicera med skadliga domäner. Genom att använda Microsofts hotinformationsflöden kan DNS Analytics identifiera klient-IPs som försöker komma åt skadliga domäner. I många fall, malware-infekterade enheter "ringa ut" till "kommando och kontroll" centrum för den skadliga domänen genom att lösa malware domännamnet.

![DNS-säkerhetsblad](./media/dns-analytics/dns-security-blade.png)

När du klickar på en klient-IP i listan öppnas loggsökning och visar uppslagsinformationen för respektive fråga. I följande exempel upptäckte DNS Analytics att kommunikationen gjordes med en [IRCbot:](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)

![Logga sökresultat som visar ircbot](./media/dns-analytics/ircbot.png)

Informationen hjälper dig att identifiera:

- Klient-IP som initierade kommunikationen.
- Domännamn som matchas till den skadliga IP-adressen.
- IP-adresser som domännamnet matchas till.
- Skadlig IP-adress.
- Allvarlighetsgrad för problemet.
- Orsak till svartlistning av skadlig IP.
- Detektionstid.

**Domäner frågade**. Innehåller de vanligaste domännamnen som efterfrågas av DNS-klienterna i din miljö. Du kan visa listan över alla domännamn som efterfrågas. Du kan också öka detaljnivån i information om uppslagsbegäran för ett visst domännamn i Loggsökning.

![Bladet Efterfrågade domäner](./media/dns-analytics/domains-queried-blade.png)

**DNS-klienter**. Rapporterar att klienter *bryter mot tröskelvärdet* för antal frågor under den valda tidsperioden. Du kan visa listan över alla DNS-klienter och information om de frågor som de har ställt i Loggsökning.

![Blad för DNS-klienter](./media/dns-analytics/dns-clients-blade.png)

**Dynamiska DNS-registreringar**. Rapporterar fel i namnregistreringen. Alla registreringsfel för [adressresursposter](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A och AAAA) markeras tillsammans med klient-IPs som gjorde registreringsbegärandena. Du kan sedan använda den här informationen för att hitta orsaken till registreringsfelet genom att följa dessa steg:

1. Leta reda på den zon som är auktoritär för namnet som klienten försöker uppdatera.

1. Använd lösningen för att kontrollera lagerinformationen för den zonen.

1. Kontrollera att den dynamiska uppdateringen för zonen är aktiverad.

1. Kontrollera om zonen är konfigurerad för säker dynamisk uppdatering eller inte.

    ![Bladet dynamiska DNS-registreringar](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Namnregistreringsbegäranden**. Den övre panelen visar en trendlinje för lyckade och misslyckade DNS-dynamiska uppdateringsbegäranden. I den nedre panelen visas de tio bästa klienterna som skickar misslyckade DNS-uppdateringsbegäranden till DNS-servrarna, sorterade efter antalet fel.

![Blad för namnregistreringsbegärande](./media/dns-analytics/name-reg-req-blade.png)

**Exempel på DDI Analytics-frågor**. Innehåller en lista över de vanligaste sökfrågorna som hämtar rådata direkt.


![Exempelfrågor](./media/dns-analytics/queries.png)

Du kan använda dessa frågor som utgångspunkt för att skapa egna frågor för anpassad rapportering. Frågorna länkar till söksidan för DNS Analytics-loggen där resultaten visas:

- **Lista över DNS-servrar**. Visar en lista över alla DNS-servrar med tillhörande FQDN, domännamn, skogsnamn och server-IPs.
- **Lista över DNS-zoner**. Visar en lista över alla DNS-zoner med associerat zonnamn, dynamisk uppdateringsstatus, namnservrar och DNSSEC-signeringsstatus.
- **Oanvända resursposter**. Visar en lista över alla oanvända/inaktuella resursposter. Den här listan innehåller resurspostnamn, resursposttyp, associerad DNS-server, postgenereringstid och zonnamn. Du kan använda den här listan för att identifiera de DNS-resursposter som inte längre används. Baserat på den här informationen kan du sedan ta bort dessa poster från DNS-servrarna.
- **INläsning av DNS-servrar.** Visar information så att du kan få ett perspektiv på DNS-belastningen på DINA DNS-servrar. Den här informationen kan hjälpa dig att planera kapaciteten för servrarna. Du kan gå till fliken **Mått** om du vill ändra vyn till en grafisk visualisering. Den här vyn hjälper dig att förstå hur DNS-belastningen fördelas över DINA DNS-servrar. Den visar DNS-frågefrekvenstrender för varje server.

    ![Sökresultat för DNS-servrar frågar logg](./media/dns-analytics/dns-servers-query-load.png)

- **DNS-zonernas frågeinläsning**. Visar DNS-zon-query-per-second-statistiken för alla zoner på DNS-servrarna som hanteras av lösningen. Klicka på fliken **Mått** om du vill ändra vyn från detaljerade poster till en grafisk visualisering av resultaten.
- **Konfigurationshändelser**. Visar alla DNS-konfigurationsändringshändelser och tillhörande meddelanden. Du kan sedan filtrera dessa händelser baserat på tid för händelsen, händelse-ID, DNS-server eller uppgiftskategori. Data kan hjälpa dig att granska ändringar som gjorts på specifika DNS-servrar vid specifika tidpunkter.
- **DNS-analyslogg**. Visar alla analytiska händelser på alla DNS-servrar som hanteras av lösningen. Du kan sedan filtrera dessa händelser baserat på tid för händelsen, händelse-ID, DNS-server, klient-IP som gjorde uppslagsfrågan och frågetypsuppgiftskategori. DNS-server analytiska händelser möjliggör aktivitetsspårning på DNS-servern. En analytisk händelse loggas varje gång servern skickar eller tar emot DNS-information.

### <a name="search-by-using-dns-analytics-log-search"></a>Sök med hjälp av DNS Analytics Log Search

På sidan Loggsökning kan du skapa en fråga. Du kan filtrera sökresultaten med hjälp av faktkontroller. Du kan också skapa avancerade frågor för att omvandla, filtrera och rapportera om dina resultat. Börja med att använda följande frågor:

1. Skriv om du `DnsEvents` vill visa alla DNS-händelser som genereras av DNS-servrarna som hanteras av lösningen i **sökfrågerutan.** Resultaten listar loggdata för alla händelser som är relaterade till uppslagsfrågor, dynamiska registreringar och konfigurationsändringar.

    ![Loggsökning för DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Om du vill visa loggdata för uppslagsfrågor väljer du **Letauppkry** som **undertypsfilter** från aspektkontrollen till vänster. En tabell som visar alla uppslagsfrågehändelser för den valda tidsperioden visas.

    b. Om du vill visa loggdata för dynamiska registreringar väljer du **Dynamiskregistrering** som **undertypsfilter** från aspektkontrollen till vänster. En tabell som visar alla dynamiska registreringshändelser för den valda tidsperioden visas.

    c. Om du vill visa loggdata för konfigurationsändringar väljer du **ConfigurationChange** som **undertypsfilter** från fasettkontrollen till vänster. En tabell som visar alla konfigurationsändringshändelser för den valda tidsperioden visas.

1. Skriv om `DnsInventory` du vill visa alla DNS-lagerrelaterade data för DE DNS-servrar som hanteras av lösningen i **sökfrågerutan.** Resultaten listar loggdata för DNS-servrar, DNS-zoner och resursposter.

    ![Loggsökning i DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Felsökning

Vanliga felsökningssteg:

1. DNS-sökningsdata saknas – Om du vill felsöka problemet kan du försöka återställa konfigurationssidan eller bara läsa in konfigurationssidan en gång i portalen. För återställning ändrar du bara en inställning till ett annat värde och ändrar den sedan tillbaka till det ursprungliga värdet och sparar konfigurationen.

## <a name="feedback"></a>Feedback

Om du vill ge feedback besöker du [sidan Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) för att publicera idéer för DNS Analytics-funktioner att arbeta med. 

## <a name="next-steps"></a>Nästa steg

[Frågeloggar](../log-query/log-query-overview.md) för att visa detaljerade DNS-loggposter.
