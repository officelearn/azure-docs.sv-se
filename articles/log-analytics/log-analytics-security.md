---
title: "Logga Analytics datasäkerhet | Microsoft Docs"
description: "Läs mer om hur logganalys skyddar din integritet och skyddar dina data."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.openlocfilehash: 91af774560860b35913e57b49fb7a1dd59f5640f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-security"></a>Logga Analytics datasäkerhet
Microsoft strävar efter att skydda din integritet och skydda dina data, samtidigt som de levererar programvaror och tjänster som hjälper dig att hantera IT-infrastrukturen i din organisation. Vi förstår att när du ge dina data till andra förtroendet kräver omfattande säkerhet. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

Säkra och skydda data är högsta prioritet på Microsoft. Kontakta oss med frågor, förslag och problem om något av följande information, inklusive vår säkerhetsprinciper på [Azure supportalternativ](http://azure.microsoft.com/support/options/).

Den här artikeln förklarar hur data som samlas in, bearbetas och skyddas av logganalys i Operations Management Suite (OMS). Du kan använda agenter för att ansluta till webbtjänsten, använda System Center Operations Manager för att samla in användningsdata eller hämta data från Azure-diagnostik för användning av logganalys. Insamlade data skickas via Internet med hjälp av certifikatbaserad autentisering och SSL-3 till Log Analytics-tjänsten som finns i Microsoft Azure. Data komprimeras av agenten innan den skickas.

Logganalys-tjänsten hanterar molnbaserade data på ett säkert sätt med hjälp av följande metoder:

* Dataavgränsning
* datalagring
* Fysisk säkerhet
* Hantering av incidenter
* Kompatibilitet
* standarder Säkerhetscertifieringar

## <a name="data-segregation"></a>Dataavgränsning
Kundinformation lagras logiskt separat på varje komponent i OMS-tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. Varje kund har en dedikerad Azure blob som innehåller den långsiktiga data

## <a name="data-retention"></a>Datakvarhållning
Indexerade Sök loggdata lagras och behålls enligt prisnivå planen. Mer information finns i [Log Analytics priser](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft tar bort kundinformation 30 dagar efter OMS-arbetsytan har stängts. Microsoft tar också bort Azure Storage-konto där data finns. Inga fysiska enheter förstörs när kundens data tas bort.

I följande tabell visas några av de tillgängliga lösningarna i OMS och exempel typer av data som de samlar in.

| **Lösning** | **Datatyper** |
| --- | --- |
| Konfigurationsbedömning |Konfigurationsdata, metadata och systemtillstånd |
| Kapacitetsplanering |Prestandadata och metadata |
| Programvara mot skadlig kod |Konfigurationsdata och metadata |
| Systemuppdateringsbedömning |Metadata och tillståndsdata |
| Logghantering |Användardefinierade händelseloggar, Windows-händelseloggar och/eller IIS-loggar |
| Spårning av ändringar |Programvaruinventering och metadata för Windows-tjänst |
| SQL- och Active Directory-utvärdering |WMI-data, registerdata, prestandadata och SQL Server dynamiska hanteringsvyn visa resultat |

I följande tabell visas exempel på datatyper:

| **Datatyp** | **Fält** |
| --- | --- |
| Varning |Varna namn, Aviseringsbeskrivningen, BaseManagedEntityId, Problem-ID, IsMonitorAlert, RuleId, ResolutionState, prioritet, allvarlighetsgrad, kategori, ägare, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguration |CustomerID, AgentID, ID för entiteterna, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Händelse |Händelse-ID, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, antalet, kategori, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Obs:** när du skriver händelser med anpassade fält i Windows-händelseloggen OMS samlar in dem. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, nätverksnamn, IP-adress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-adress, NetbiosDomainName, LogicalProcessors, DNS-namn, DisplayName, DomainDnsName, ActiveDirectorySite, huvudkontot, OffsetInMinuteFromGreenwichTime |
| Prestanda |Objektnamn, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, kontext, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysisk säkerhet
Logganalys i OMS-tjänsten är bemannat av Microsoft-Personal och alla aktiviteter loggas och kan granskas. Tjänsten körs helt i Azure och uppfyller Azure vanliga tekniska kriterierna. Du kan visa information om den fysiska säkerheten för Azure tillgångar på sidan 18 i den [översikt över Microsoft Azure-säkerhet](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysiska åtkomsträttigheter för att kunna skydda områden ändras inom en arbetsdag för alla som har ansvar för OMS-tjänsten, inklusive överföring och avslutning inte längre. Du kan läsa om globala fysisk infrastruktur som vi använder på [Microsoft Datacenters](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Hantering av incidenter
OMS har en process för hantering av incidenter som följer alla Microsoft-tjänster. Sammanfattningsvis, vi:

* Använd en delad ansvar modell där en del av ansvar för säkerhet som tillhör Microsoft och som en del tillhör kunden
* Hantera Azure säkerhetsincidenter
  * Starta en undersökning vid identifiering av en incident
  * Utvärdera inverkan och allvarlighetsgrad för en incident av en medlem i gruppen på anrop incidenter. Baserat på bevis bedömningen kan eller inte kan resultera i ytterligare eskalering till security response-gruppen.
  * Felsöka en incident säkerhet svar experter att genomföra tekniska eller kriminaltekniska undersökningar, identifiera strategier för inneslutning, begränsning och lösning. Om säkerhetsteam anser att kundinformation kan ha blir utsätts för en olagligt eller obehörig person, börjar parallell körning av kunden Incident Notification processen parallellt.  
  * Hålla och återställa från incidenten. Teamet incidenter skapas en återställningsplan för att undvika problemet. Kris inneslutning steg, till exempel sätta i karantän berörda system kan uppstå omedelbart och parallellt med diagnos. Längre sikt åtgärder planeras som inträffar efter omedelbar risken har passerat.  
  * Stäng incidenten och utföra en post före. Teamet incidenter skapas en post före som visar information om incidenten med avsikt att ändra principer, procedurer och processer för att förhindra en upprepning av händelsen.
* Meddela kunder för säkerhetsincidenter
  * Fastställa omfattningen av berörda kunder och ange vem som helst som påverkas som detaljerad ett meddelande som möjligt
  * Skapa ett meddelande om att tillhandahålla kunder med detaljerad tillräckligt med information så att de kan utföra en undersökning på deras syfte och uppfyller några åtaganden som har gjorts i slutanvändarna när inte fördröja notification-processen.
  * Bekräfta och deklarera incident, vid behov.
  * Meddela kunder med en incident meddelande utan orimligt fördröjning och i enlighet med ett åtagande lag eller avtal. Meddelanden om säkerhetsincidenter levereras till en eller flera av kundens administratörer på något sätt som Microsoft väljer, inklusive via e-post.
* Genomföra team beredskap och utbildning
  * Microsoft-Personal krävs för att slutföra säkerhets- och utbildning som hjälper dem att identifiera och rapportera misstänkta säkerhetsproblem.  
  * Operatorer som arbetar på Microsoft Azure-tjänsten har ytterligare utbildning skyldigheter omgivande åtkomsten till känsliga system som är värd för kundens data.
  * Microsoft security response-personal få särskilda utbildning för deras roller

Om en kund dataförlust, meddelar vi varje kund inom en dag. Kunden dataförlust har aldrig uppstod med OMS. Dessutom upprätthåller vi kopior av data som har skapats och den distribueras geografiskt.

Läs mer om hur Microsoft ska svara på säkerhetsincidenter [Microsoft Azure Security Response i molnet](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Efterlevnad
OMS programvara utvecklings- och gruppens säkerhets- och styrning programmet information stöder affärskraven och följer lagar och förordningar enligt beskrivningen i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) och [Microsoft förtroende Center kompatibilitet](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Hur OMS upprättar säkerhetskrav, identifierar säkerhetsåtgärder, hanterar och övervakar risker beskrivs också det. Årligen, vi granska principer, standarder och procedurer som riktlinjer.

Varje medlem i gruppen OMS utveckling får formella säkerhetsutbildning. Internt, använder vi ett system för version för programutveckling. Varje projekt program är skyddat av systemet för versionskontroll.

Microsoft har ett team för säkerhet och efterlevnad som övervakar och utvärderar alla tjänster i Microsoft. Information security polis utgör teamet och de är inte kopplade till de tekniska avdelningar som utvecklar OMS. De har sina egna management kedjan och utföra oberoende bedömning av produkter och tjänster för att garantera säkerhet och efterlevnad.

Microsofts styrelse meddelas av en årlig rapport om alla information säkerhetsprogram på Microsoft.

OMS programvara utvecklings- och teamet arbetar aktivt med Microsoft Legal och kompatibiliteten team och andra branschpartners för att få olika certifikat.

## <a name="certifications-and-attestations"></a>Certifikat och intyg
OMS logganalys uppfyller följande krav:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* [Betalningen kort (PCI-kompatibel) Data Security branschstandard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) PCI Security Standards rådet.
* [Tjänsten organisation kontroller (SOC) 1 typen 1 och SOC 2 typ 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) kompatibla
* [HIPAA och HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) för företag som har ett HIPAA Business associera avtal
* Windows vanliga tekniker villkor
* Microsoft Trustworthy Computing
* De komponenter som använder OMS följa Azure efterföljandekrav som en Azure-tjänst. Du kan läsa mer i [Microsoft förtroende Center kompatibilitet](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> I vissa certifieringar/intyg logganalys anges under dess tidigare namnet på *åtgärdsinformation*.
>
>


## <a name="cloud-computing-security-data-flow"></a>Molntjänster dataflöde för säkerhet
Följande diagram visar en moln-säkerhetsarkitekturen som flödet av information från företaget och hur den skyddas som flyttar till Log Analytics-tjänsten slutligen ses av du i OMS-portalen. Mer information om varje steg följer diagrammet.

![Bild av OMS-datainsamling och säkerhet](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Registrera dig för Log Analytics och samla in data
Din organisation att skicka data till logganalys, konfigurerar du Windows-agenter, agenter som körs på virtuella Azure-datorer eller OMS-Agent för Linux. Om du använder Operations Manager-agenter kan använda du en konfigurationsguide i Operations-konsolen för att konfigurera dem. Användare (som kan vara dig, andra enskilda användare eller en grupp människor) skapa en eller flera OMS-konton (OMS arbetsytor) och registrera agenter med någon av följande konton:

* [Organisations-ID](../active-directory/sign-up-organization.md)
* [Microsoft-konto – Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

En OMS-arbetsyta är där data som samlas in, aggregeras, analyseras och visas. En arbetsyta används främst för att partitionera data och varje arbetsyta är unika. Du kanske vill ha produktionsdata hanteras med en OMS-arbetsytan och testa data hanteras med en annan arbetsyta. Arbetsytor också hjälpa en administratören kontrollera åtkomst till data. Varje arbetsyta kan ha flera användarkonton som är kopplade till den, och varje användarkonto som har åtkomst till flera arbetsytor i OMS. Du skapar arbetsytor baserat på datacenter region. Varje arbetsyta replikeras till andra datacenter i region, främst för OMS tjänstetillgänglighet.

För Operations Manager upprättar varje hanteringsgrupp för Operations Manager en anslutning med tjänsten logganalys när guiden är klar. Du kan sedan använda guiden Lägg till datorer för att välja vilka datorer i hanteringsgruppen ska kunna skicka data till tjänsten. För andra typer av agenten ansluter varje på ett säkert sätt till OMS-tjänsten.

All kommunikation mellan anslutna system och Log Analytics-tjänsten är krypterad.  TLS (HTTPS)-protokollet används för kryptering.  Microsoft SDL-processen följs för att säkerställa logganalys är uppdaterad med de senaste utvecklingen av kryptografiska protokoll.

Varje typ av agenten samlar in data för Log Analytics. Vilken typ av data som samlas in är beror på vilka typer av lösningar som används. Du kan se en översikt över datainsamling vid [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). Dessutom finns mer detaljerad samlingsinformation för de flesta lösningar. En lösning är ett paket med fördefinierade vyer, log sökfrågor, regler för insamling av data och standardbearbetningslogiken. Endast administratörer kan använda Log Analytics för att importera en lösning. När lösningen har importerats flyttas det till Operations Manager-hanteringsservrar (om sådan används) och sedan till alla eventuella agenter som du har valt. Därefter kan agenterna samla in data.

## <a name="2-send-data-from-agents"></a>2. Skicka data från agenter
Du kan registrera alla typer av agenten med en nyckel för registrering och upprätta en säker anslutning mellan agenten och tjänsten Log Analytics med hjälp av certifikatbaserad autentisering och SSL med port 443. OMS använder en hemlig Arkiv för att skapa och underhålla nycklar. Privata nycklar roteras efter 90 dagar och lagras i Azure och hanteras av Azure åtgärderna som följer strikta regler och kompatibilitet metoder.

Med Operations Manager kan du registrera en arbetsyta med Log Analytics-tjänsten och en säker HTTPS-anslutning upprättas mellan Operations Manager-hanteringsservern.

Windows-agenter som körs på virtuella Azure-datorer, används en skrivskyddad lagringsnyckel för att läsa diagnostiska händelser i Azure-tabeller.

Om alla agenter har inte att kommunicera med tjänsten av någon anledning, insamlade data lagras lokalt i en tillfällig cache och hanteringsservern försöker skicka data var 8: e minut i två timmar. Agentens cachelagrade data skyddas av operativsystemets Arkiv för autentiseringsuppgifter. Om tjänsten inte kan bearbeta data efter två timmar, köar agenterna data. Om kön är full, startar OMS släppa datatyper, från och med prestandadata. Kögränsen agent är en registernyckel så att du kan ändra den, om det behövs. Insamlade data komprimeras och skickas till tjänsten, vilket kringgår lokala databaser så belastning inte läggs till dem. När insamlade data skickas bort det från cachen.

Enligt beskrivningen ovan, skickas data från dina agenter via SSL till Microsoft Azure-datacenter. Du kan även använda ExpressRoute för att ge extra säkerhet för data. ExpressRoute är ett sätt att ansluta direkt till Azure från ditt befintliga WAN-nätverk, t.ex. flera protokoll etikett växling (MPLS) VPN, som tillhandahålls av en nätverkstjänstleverantören. Mer information finns i [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Logganalys-tjänsten som tar emot och bearbetar data
Logganalys-tjänsten gör att inkommande data från en betrodd källa genom att verifiera certifikat och dataintegritet med Azure-autentisering. Obearbetat rådata lagras som en blob i [Microsoft Azure Storage](../storage/common/storage-introduction.md) och inte är krypterad. Dock har varje Azure storage-blob en unik uppsättning nycklar, som endast är tillgängligt för användaren. Vilken typ av data som lagras beror på vilka typer av lösningar som har importerats och används för att samla in data. Logganalys-tjänsten bearbetar sedan rådata för Azure storage-blob.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Använda Log Analytics för att komma åt data
Du kan logga in till logganalys i OMS-portalen med hjälp av organisationens konto eller ett Microsoft-konto som du skapat tidigare. All trafik mellan OMS-portalen och logganalys i OMS skickas via en säker kanal för HTTPS. När du använder OMS-portalen, genereras ett sessions-ID på klienten (webbläsare) för användaren och data lagras i ett lokalt cacheminne tills sessionen avslutas. När avslutas, bort cacheminnet. Klientsidans cookies, som inte innehåller personligt identifierbar information, tas inte bort automatiskt. Sessionscookies är markerad HTTPOnly och skyddas. Efter en förbestämd inaktiv period avslutas OMS portal sessionen.

Med hjälp av OMS-portalen kan du exportera data till en CSV-fil och du kan komma åt data med hjälp av API: er Search. Exportera CSV-fil är begränsad till 50 000 rader per export och API-data är begränsad till 5 000 rader per sökning.

## <a name="next-steps"></a>Nästa steg
* [Kom igång med logganalys](log-analytics-get-started.md) att lära sig mer om logganalys och komma igång i minuter.
