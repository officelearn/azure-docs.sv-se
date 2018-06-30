---
title: Logga Analytics datasäkerhet | Microsoft Docs
description: Läs mer om hur logganalys skyddar din integritet och skyddar dina data.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 7596428b4ed067bf53f3b295a1682ed372f8d472
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131452"
---
# <a name="log-analytics-data-security"></a>Logga Analytics datasäkerhet
Det här dokumentet är avsett att ge Azure logganalys specifik information för att komplettera informationen på [Azure Säkerhetscenter](../security/security-microsoft-trust-center.md).  

Den här artikeln förklarar hur data som samlas in, bearbetas och skyddas av logganalys. Du kan använda agenter för att ansluta till webbtjänsten, använda System Center Operations Manager för att samla in användningsdata eller hämta data från Azure-diagnostik för användning av logganalys. 

Logganalys-tjänsten hanterar molnbaserade data på ett säkert sätt med hjälp av följande metoder:

* Dataavgränsning
* Datakvarhållning
* Fysisk säkerhet
* Hantering av incidenter
* Efterlevnad
* Standarder Säkerhetscertifieringar

Kontakta oss med frågor, förslag och problem om något av följande information, inklusive vår säkerhetsprinciper på [Azure supportalternativ](http://azure.microsoft.com/support/options/).

## <a name="data-segregation"></a>Dataavgränsning
När dina data är inhämtas av tjänsten logganalys sparas data logiskt separat på varje komponent i hela tjänsten. Alla data är taggade per arbetsytan. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. Dina data lagras i en dedikerad databas i lagringsklustret i den region som du har valt.

## <a name="data-retention"></a>Datakvarhållning
Indexerade Sök loggdata lagras och behålls enligt prisnivå planen. Mer information finns i [Log Analytics priser](https://azure.microsoft.com/pricing/details/log-analytics/).

Som en del av din [prenumerationsavtalet](https://azure.microsoft.com/support/legal/subscription-agreement/), Microsoft behåller dina data enligt villkoren i avtalet.  När data tas bort, vi också ta bort Azure Storage-konto där data finns.  Inga fysiska enheter förstörs när kundens data tas bort.  

I följande tabell visas några av de tillgängliga lösningarna och ger exempel på typ av information som samlas in.

| **Lösning** | **Datatyper** |
| --- | --- |
| Kapacitet och prestanda |Prestandadata och metadata |
| Utvärdering av skadlig kod |Konfigurationsdata och metadata |
| Uppdateringshantering |Metadata och tillståndsdata |
| Logghantering |Användardefinierade händelseloggar, Windows-händelseloggar och/eller IIS-loggar |
| Spårning av ändringar |Programvaruinventering, Windows-tjänst och Linux daemon metadata och Windows-/ Linux filens metadata |
| SQL- och Active Directory-utvärdering |WMI-data, registerdata, prestandadata och SQL Server dynamiska hanteringsvyn visa resultat |

I följande tabell visas exempel på datatyper:

| **Datatyp** | **Fält** |
| --- | --- |
| Varning |Varna namn, Aviseringsbeskrivningen, BaseManagedEntityId, Problem-ID, IsMonitorAlert, RuleId, ResolutionState, prioritet, allvarlighetsgrad, kategori, ägare, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved TimeResolutionStateLastModified TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguration |CustomerID, AgentID, ID för entiteterna, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Händelse |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Obs:** när du skriver händelser med anpassade fält i Windows-händelseloggen OMS samlar in dem. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, nätverksnamn, IP-adress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adress NetbiosDomainName, LogicalProcessors, DNS-namn, DisplayName, DomainDnsName, ActiveDirectorySite, huvudkontot, OffsetInMinuteFromGreenwichTime |
| Prestanda |Objektnamn, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysisk säkerhet
Logganalys-tjänsten hanteras av Microsoft-Personal och alla aktiviteter loggas och kan granskas. Logganalys styrs som en Azure-tjänst och uppfyller alla krav för Azure efterlevnad och säkerhet. Du kan visa information om den fysiska säkerheten för Azure tillgångar på sidan 18 i den [översikt över Microsoft Azure-säkerhet](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysiska åtkomsträttigheter för att kunna skydda områden ändras inom en arbetsdag för alla som har ansvar för OMS-tjänsten, inklusive överföring och avslutning inte längre. Du kan läsa om globala fysisk infrastruktur som vi använder på [Microsoft Datacenters](https://azure.microsoft.com/en-us/global-infrastructure/).

## <a name="incident-management"></a>Hantering av incidenter
OMS har en process för hantering av incidenter som följer alla Microsoft-tjänster. Sammanfattningsvis, vi:

* Använd en delad ansvar modell där en del av ansvar för säkerhet som tillhör Microsoft och som en del tillhör kunden
* Hantera Azure säkerhetsincidenter:
  * Starta en undersökning vid identifiering av en incident
  * Utvärdera inverkan och allvarlighetsgrad för en incident av en medlem i gruppen på anrop incidenter. Baserat på bevis bedömningen kan eller inte kan resultera i ytterligare eskalering till security response-gruppen.
  * Felsöka en incident säkerhet svar experter att genomföra tekniska eller kriminaltekniska undersökningar, identifiera strategier för inneslutning, begränsning och lösning. Om säkerhetsteam anser att kundinformation kan ha blir utsätts för en olagligt eller obehörig person, börjar parallell körning av kunden Incident Notification processen parallellt.  
  * Hålla och återställa från incidenten. Teamet incidenter skapas en återställningsplan för att undvika problemet. Kris inneslutning steg, till exempel sätta i karantän berörda system kan uppstå omedelbart och parallellt med diagnos. Längre sikt åtgärder planeras som inträffar efter omedelbar risken har passerat.  
  * Stäng incidenten och utföra en post före. Teamet incidenter skapas en post före som visar information om incidenten med avsikt att ändra principer, procedurer och processer för att förhindra en upprepning av händelsen.
* Meddela kunder för säkerhetsincidenter:
  * Fastställa omfattningen av berörda kunder och ange vem som helst som påverkas som detaljerad ett meddelande som möjligt
  * Skapa ett meddelande om att tillhandahålla kunder med detaljerad tillräckligt med information så att de kan utföra en undersökning på deras syfte och uppfyller några åtaganden som har gjorts i slutanvändarna när inte fördröja notification-processen.
  * Bekräfta och deklarera incident, vid behov.
  * Meddela kunder med en incident meddelande utan orimligt fördröjning och i enlighet med ett åtagande lag eller avtal. Meddelanden om säkerhetsincidenter levereras till en eller flera av kundens administratörer på något sätt som Microsoft väljer, inklusive via e-post.
* Genomföra team beredskap och utbildning:
  * Microsoft-Personal krävs för att slutföra säkerhets- och utbildning som hjälper dem att identifiera och rapportera misstänkta säkerhetsproblem.  
  * Operatorer som arbetar på Microsoft Azure-tjänsten har ytterligare utbildning skyldigheter omgivande åtkomsten till känsliga system som är värd för kundens data.
  * Microsoft security response-personal få särskilda utbildning för deras roller

Om en kund dataförlust, meddelar vi varje kund inom en dag. Kunden dataförlust har aldrig uppstått med tjänsten. 

Läs mer om hur Microsoft ska svara på säkerhetsincidenter [Microsoft Azure Security Response i molnet](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Efterlevnad
Logganalys programvara utvecklings- och gruppens säkerhets- och styrning programmet information stöder affärskraven och följer lagar och förordningar enligt beskrivningen i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) och [ Microsoft Trust Center kompatibilitet](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hur logganalys upprättar säkerhetskrav, identifierar säkerhetsåtgärder, hanterar och övervakar risker beskrivs också det. Årligen, vi granska principer, standarder och procedurer som riktlinjer.

Varje medlem i gruppen utveckling får formella säkerhetsutbildning. Internt, använder vi ett system för version för programutveckling. Varje projekt program är skyddat av systemet för versionskontroll.

Microsoft har ett team för säkerhet och efterlevnad som övervakar och utvärderar alla tjänster i Microsoft. Information security polis utgör teamet och de är inte kopplade till de tekniska avdelningar som utvecklar logganalys. De har sina egna management kedjan och utföra oberoende bedömning av produkter och tjänster för att garantera säkerhet och efterlevnad.

Microsofts styrelse meddelas av en årlig rapport om alla information säkerhetsprogram på Microsoft.

Logganalys programvara utvecklings- och teamet arbetar aktivt med Microsoft Legal och kompatibiliteten team och andra branschpartners för att få olika certifikat.

## <a name="certifications-and-attestations"></a>Certifikat och intyg
Azure logganalys uppfyller följande krav:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Betalningen kort (PCI-kompatibel) Data Security branschstandard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) PCI Security Standards rådet.
* [Tjänsten organisation kontroller (SOC) 1 typen 1 och SOC 2 typ 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) kompatibla
* [HIPAA och HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) för företag som har ett HIPAA Business associera avtal
* Windows vanliga tekniker villkor
* Microsoft Trustworthy Computing
* De komponenter som Log Analytics använder följa Azure efterföljandekrav som en Azure-tjänst. Du kan läsa mer i [Microsoft förtroende Center kompatibilitet](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> I vissa certifieringar/intyg logganalys anges under dess tidigare namnet på *åtgärdsinformation*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Molntjänster dataflöde för säkerhet
Följande diagram visar en moln-säkerhetsarkitekturen som flödet av information från företaget och hur den skyddas som flyttar till Log Analytics-tjänsten slutligen ses av du i Azure-portalen eller klassiska OMS-portalen. Mer information om varje steg följer diagrammet.

![Bild av logganalys-datainsamling och säkerhet](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Registrera dig för Log Analytics och samla in data
Din organisation att skicka data till logganalys, konfigurerar du en Windows- eller Linux-agenten som körs på virtuella Azure-datorer eller på virtuella eller fysiska datorer i din miljö eller andra molntjänstleverantör.  Om du använder Operations Manager från hanteringsgruppen konfigurera du Operations Manager-agenten. Användare (som kan vara dig, andra enskilda användare eller en grupp människor) skapa en eller flera logganalys arbetsytor och registrera agenter med någon av följande konton:

* [Organisations-ID](../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-konto – Outlook, Office Live, MSN](https://account.microsoft.com/account)

Logganalys-arbetsytan är där data som samlas in, aggregeras, analyseras och visas. En arbetsyta används främst för att partitionera data och varje arbetsyta är unika. Du kanske vill ha produktionsdata hanteras med en arbetsyta och testa data hanteras med en annan arbetsyta. Arbetsytor också hjälpa en administratören kontrollera åtkomst till data. Varje arbetsyta kan ha flera användarkonton som är kopplade till den, och varje användarkonto kan komma åt flera logganalys arbetsytor. Du skapar arbetsytor baserat på datacenter region. Varje arbetsyta replikeras till andra datacenter i region, främst för tillgänglighet för Log Analytics-tjänst.

För Operations Manager upprättar Operations Manager-hanteringsgruppen en anslutning med Log Analytics-tjänsten. Du kan sedan konfigurera agenthanterade datorer i hanteringsgruppen ska kunna samla in och skicka data till tjänsten. Beroende på den lösning som du har aktiverat data från dessa lösningar är antingen skickas direkt från en Operations Manager-hanteringsserver till Log Analytics-tjänsten, eller på grund av mängden data som samlas in av systemet hanteras med agent skickas direkt från agenten till tjänsten. För datorer som inte övervakas av Operations Manager, ansluter varje på ett säkert sätt till logganalys-tjänsten direkt.

All kommunikation mellan anslutna system och Log Analytics-tjänsten är krypterad.  TLS (HTTPS)-protokollet används för kryptering.  Microsoft SDL-processen följs för att säkerställa logganalys är uppdaterad med de senaste utvecklingen av kryptografiska protokoll.

Varje typ av agenten samlar in data för Log Analytics. Vilken typ av data som samlas in är beror på vilka typer av lösningar som används. Du kan se en översikt över datainsamling vid [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). Dessutom finns mer detaljerad samlingsinformation för de flesta lösningar. En lösning är ett paket med fördefinierade vyer, log sökfrågor, regler för insamling av data och standardbearbetningslogiken. Endast administratörer kan använda Log Analytics för att importera en lösning. När lösningen har importerats flyttas det till Operations Manager-hanteringsservrar (om sådan används) och sedan till alla eventuella agenter som du har valt. Därefter kan agenterna samla in data.

## <a name="2-send-data-from-agents"></a>2. Skicka data från agenter
Du kan registrera alla typer av agenten med en nyckel för registrering och upprätta en säker anslutning mellan agenten och tjänsten Log Analytics med hjälp av certifikatbaserad autentisering och SSL med port 443. Log Analytics använder en hemlig Arkiv för att skapa och underhålla nycklar. Privata nycklar roteras efter 90 dagar och lagras i Azure och hanteras av Azure åtgärderna som följer strikta regler och kompatibilitet metoder.

Med Operations Manager upprättar en säker HTTPS-anslutning med en Operations Manager-hanteringsserver i hanteringsgruppen registrerats med en logganalys-arbetsytan.

Windows- eller Linux-agenter körs på virtuella Azure-datorer, används en skrivskyddad lagringsnyckel för att läsa diagnostiska händelser i Azure-tabeller.  

Med alla agenter som rapporterar till en Operations Manager-hanteringsgrupp som är integrerad med logganalys om hanteringsservern inte kan kommunicera med lagras tjänsten av någon anledning insamlade data lokalt i en tillfällig cache på hantering Server.   De försöker skicka data var 8: e minut i två timmar.  Beteendet är konsekvent med Windows-agenten för data som kringgår hanteringsservern och skickas direkt till Log Analytics.  

I Windows eller management server-agenten cachelagrade data skyddas av operativsystemets Arkiv för autentiseringsuppgifter. Om tjänsten inte kan bearbeta data efter två timmar, köar agenterna data. Om kön är full, börjar agenten släppa datatyper, från och med prestandadata. Kögränsen agent är en registernyckel så att du kan ändra den, om det behövs. Insamlade data komprimeras och skickas till tjänsten, vilket kringgår Operations Manager management group-databaserna så belastning inte läggs till dem. När insamlade data skickas bort det från cachen.

Enligt beskrivningen ovan, skickas data från hanteringsservern eller direktanslutna agenter via SSL till Microsoft Azure-datacenter. Du kan även använda ExpressRoute för att ge extra säkerhet för data. ExpressRoute är ett sätt att ansluta direkt till Azure från ditt befintliga WAN-nätverk, t.ex. flera protokoll etikett växling (MPLS) VPN, som tillhandahålls av en nätverkstjänstleverantören. Mer information finns i [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Logganalys-tjänsten som tar emot och bearbetar data
Logganalys-tjänsten gör att inkommande data från en betrodd källa genom att verifiera certifikat och dataintegritet med Azure-autentisering. Obearbetat rådata lagras sedan i en Azure-Händelsehubb i den region så småningom kommer att lagras data i vila. Vilken typ av data som lagras beror på vilka typer av lösningar som har importerats och används för att samla in data. Log Analytics-tjänsten processer rådata och en i databasen.

Kvarhållningsperioden för insamlade data som lagras i databasen är beroende av den valda prisavtal. För den *lediga* nivån insamlade data är tillgängliga för 7 dagar. För den *betald* nivån insamlade data är tillgängliga i 31 dagar som standard, men kan utökas till 720 dagar. Data lagras krypterat i vila i Azure storage så datasekretess. De senaste två veckorna data lagras också SSD-baserad cache och det här cacheminnet för närvarande inte är krypterad.  Vi planerar att stödja dessa kryptering i den senare halvan av 2018.  

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Använda Log Analytics för att komma åt data
Komma åt logganalys-arbetsytan kan du logga in på Azure-portalen med organisationens konto eller Microsoft-konto som du skapat tidigare. All trafik mellan portalen och Log Analytics-tjänsten skickas via en säker kanal för HTTPS. När du använder portalen ett sessions-ID genereras på klienten (webbläsare) för användaren och data lagras i ett lokalt cacheminne tills sessionen avslutas. När avslutas, bort cacheminnet. Klientsidans cookies, som inte innehåller personligt identifierbar information, tas inte bort automatiskt. Sessionscookies är markerad HTTPOnly och skyddas. Efter en förbestämd inaktiv period avslutas Azure portal sessionen.

## <a name="next-steps"></a>Nästa steg
* Lär dig att samla in data med Log Analytics för din virtuella Azure-datorer efter den [Virtuella Azure quickstart](log-analytics-quick-collect-azurevm.md).  

*  Om du vill samla in data från fysiska eller virtuella Windows- eller Linux-datorer i din miljö finns i [Snabbstart för Linux-datorer](log-analytics-quick-collect-linux-computer.md) eller [Snabbstart för Windows-datorer](log-analytics-quick-collect-windows-computer.md)

