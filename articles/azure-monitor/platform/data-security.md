---
title: Logganalysdatasäkerhet | Microsoft-dokument
description: Läs mer om hur Log Analytics skyddar din integritet och skyddar dina data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 63d8d8d3701a9adca4bd01e6e061877f5d0bd245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333348"
---
# <a name="log-analytics-data-security"></a>Logganalysdatasäkerhet
Det här dokumentet är avsett att ge information som är specifik för Log Analytics, som är en funktion i Azure Monitor, för att komplettera informationen på [Azure Trust Center](../../security/fundamentals/trust-center.md).  

Den här artikeln förklarar hur data samlas in, bearbetas och skyddas av Log Analytics. Du kan använda agenter för att ansluta till webbtjänsten, använda System Center Operations Manager för att samla in driftdata eller hämta data från Azure-diagnostik för användning av Log Analytics. 

Log Analytics-tjänsten hanterar dina molnbaserade data på ett säkert sätt med hjälp av följande metoder:

* Dataavgränsning
* Datakvarhållning
* Fysisk säkerhet
* Incidenthantering
* Efterlevnad
* Certifiering av säkerhetsstandarder

Kontakta oss med frågor, förslag eller problem om någon av följande information, inklusive våra säkerhetsprinciper på [Azure-supportalternativ](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Skicka data säkert med TLS 1.2 

För att försäkra säkerheten för data under överföring till Log Analytics rekommenderar vi starkt att du konfigurerar agenten så att den använder minst TLS (Transport Layer Security) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har visat sig vara sårbara och medan de fortfarande arbetar för att tillåta bakåtkompatibilitet rekommenderas de **inte**och branschen flyttar snabbt för att överge stödet för dessa äldre protokoll. 

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) har satt [en tidsfrist den 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) för att inaktivera äldre versioner av TLS/SSL och uppgradera till säkrare protokoll. När Azure släpper äldre support, om dina agenter inte kan kommunicera över minst TLS 1.2 kan du inte skicka data till Log Analytics. 

Vi rekommenderar inte att du uttryckligen ställer in din agent så att den bara använder TLS 1.2 om det inte är absolut nödvändigt, eftersom det kan bryta säkerhetsfunktioner på plattformsnivå som gör att du automatiskt kan upptäcka och dra nytta av nyare säkrare protokoll när de blir tillgängliga, till exempel som TLS 1.3. 

### <a name="platform-specific-guidance"></a>Plattformsspecifik vägledning

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner tenderar att förlita sig på [OpenSSL](https://www.openssl.org) för TLS 1.2-stöd.  | Kontrollera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows 8.0 - 10 | Stöds och aktiveras som standard. | Så här bekräftar du att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Stöds och aktiveras som standard. | Så här bekräftar du att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men inte aktiverat som standard. | Mer information om hur du aktiverar finns på [registerinställningssidan för Transport Layer Security (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |

## <a name="data-segregation"></a>Dataavgränsning
När dina data har förtärs av Log Analytics-tjänsten hålls data logiskt åtskilda på varje komponent i hela tjänsten. Alla data taggas per arbetsyta. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. Dina data lagras i en dedikerad databas i lagringsklustret i den region som du har valt.

## <a name="data-retention"></a>Datakvarhållning
Indexerade loggsökdata lagras och behålls enligt din prisplan. Mer information finns i [Logga analytics-priser](https://azure.microsoft.com/pricing/details/log-analytics/).

Som en del av ditt [prenumerationsavtal](https://azure.microsoft.com/support/legal/subscription-agreement/)behåller Microsoft dina uppgifter enligt villkoren i avtalet.  När kunddata tas bort förstörs inga fysiska enheter.  

I följande tabell visas några av de tillgängliga lösningarna och exempel på vilken typ av data de samlar in.

| **Lösning** | **Datatyper** |
| --- | --- |
| Kapacitet och prestanda |Prestandadata och metadata |
| Uppdateringshantering |Metadata och tillståndsdata |
| Logghantering |Användardefinierade händelseloggar, Windows-händelseloggar och/eller IIS-loggar |
| Spårning av ändringar |Metadata för programvaruinventering, Windows-tjänst och Linux-daemon och metadata för Windows/Linux-filer |
| SQL- och Active Directory-utvärdering |WMI-data, registerdata, prestandadata och sql server dynamiska hanteringsvyresultat |

I följande tabell visas exempel på datatyper:

| **Datatyp** | **Fält** |
| --- | --- |
| Varning |Varningsnamn, aviseringsbeskrivning, BaseManagedEntityId, Problem-ID, IsMonitorAlert, RuleId, ResolutionState, Prioritet, Allvarlighetsgrad, Kategori, Ägare, ResolvedBy, TimeRaised, TimeAd, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguration |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Händelse |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Anm.:** När du skriver händelser med anpassade fält i Windows-händelseloggen samlar Log Analytics in dem. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostNameIsVirtualMachine, IP Adress, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Prestanda |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, Sammanhang, TimeGenerated, TimeAd, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysisk säkerhet
Log Analytics-tjänsten hanteras av Microsofts personal och alla aktiviteter loggas och kan granskas. Log Analytics drivs som en Azure-tjänst och uppfyller alla Azure Compliance och säkerhetskrav. Du kan visa information om den fysiska säkerheten för Azure-resurser på sidan 18 i [Microsoft Azure Security Overview](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysiska åtkomsträttigheter till säkra områden ändras inom en arbetsdag för alla som inte längre har ansvar för Log Analytics-tjänsten, inklusive överföring och uppsägning. Du kan läsa om den globala fysiska infrastruktur som vi använder på [Microsoft Datacenters](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Incidenthantering
Log Analytics har en incidenthanteringsprocess som alla Microsoft-tjänster följer. Sammanfattningsvis vi:

* Använd en modell för delat ansvar där en del av säkerhetsansvaret tillhör Microsoft och en del tillhör kunden
* Hantera Azure-säkerhetsincidenter:
  * Starta en utredning efter upptäckt av en incident
  * Utvärdera effekten och svårighetsgraden av en incident av en jourhavande incidentgruppmedlem. På grundval av bevis kan bedömningen leda till ytterligare eskalering till säkerhetsinsatsgruppen.
  * Diagnostisera en incident av säkerhetsinsatsexperter för att genomföra den tekniska eller kriminaltekniska undersökningen, identifiera inneslutnings-, begränsnings- och lösningsstrategier. Om säkerhetsteamet tror att kunddata kan ha blivit utsatta för en olaglig eller obehörig person, börjar parallell körning av customer incident notification-processen parallellt.  
  * Stabilisera och återhämta sig från incidenten. Incidentsvarsteamet skapar en återställningsplan för att minska problemet. Krisinneslutningssteg såsom karantän påverkade system kan inträffa omedelbart och parallellt med diagnos. Långsiktiga mildrande åtgärder kan planeras som inträffar efter det att den omedelbara risken har passerat.  
  * Stäng incidenten och gör en obduktion. Incidentsvarsteamet skapar en post-mortem som beskriver detaljerna i incidenten, med avsikt att revidera principer, procedurer och processer för att förhindra en upprepning av händelsen.
* Meddela kunderna om säkerhetsincidenter:
  * Fastställa omfattningen av påverkade kunder och för att ge alla som påverkas så detaljerat ett meddelande som möjligt
  * Skapa ett meddelande för att ge kunderna tillräckligt detaljerad information så att de kan utföra en undersökning på slutet och uppfylla alla åtaganden som de har gjort till sina slutanvändare utan att otillbörligt fördröja meddelandeprocessen.
  * Bekräfta och förklara händelsen vid behov.
  * Meddela kunderna om en incidentanmälan utan oskäligt dröjsmål och i enlighet med eventuella juridiska eller avtalsenliga åtaganden. Meddelanden om säkerhetsincidenter levereras till en eller flera av en kunds administratörer på något sätt som Microsoft väljer, inklusive via e-post.
* Genomföra teamberedskap och utbildning:
  * Microsofts personal måste slutföra säkerhets- och medvetenhetsutbildning, vilket hjälper dem att identifiera och rapportera misstänkta säkerhetsproblem.  
  * Operatörer som arbetar med Microsoft Azure-tjänsten har tilläggsutbildningsskyldigheter som omger deras åtkomst till känsliga system som är värdar för kunddata.
  * Microsofts säkerhetssvarspersonal får specialiserad utbildning för sina roller

Om förlust av kunddata inträffar meddelar vi varje kund inom en dag. Förlust av kunddata har dock aldrig skett med tjänsten. 

Mer information om hur Microsoft svarar på säkerhetsincidenter finns [i Microsoft Azure Security Response i molnet](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Efterlevnad
Log Analytics programutveckling och serviceteams informationssäkerhet och styrningsprogram stöder företagets affärskrav och följer lagar och förordningar enligt beskrivningen på [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) och Microsoft Trust Center [Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hur Log Analytics fastställer säkerhetskrav, identifierar säkerhetskontroller, hanterar och övervakar risker beskrivs också där. Varje år granskar vi policyer, standarder, rutiner och riktlinjer.

Varje medlem i utvecklingsteamet får formell programsäkerhetsutbildning. Internt använder vi ett versionskontrollsystem för mjukvaruutveckling. Varje programvaruprojekt skyddas av versionskontrollsystemet.

Microsoft har ett säkerhets- och efterlevnadsteam som övervakar och utvärderar alla tjänster i Microsoft. Informationssäkerhetsansvariga utgör teamet och de är inte associerade med de ingenjörsteam som utvecklar Log Analytics. Ordningsvakterna har en egen ledningskedja och gör oberoende bedömningar av produkter och tjänster för att säkerställa säkerhet och efterlevnad.

Microsofts styrelse meddelas av en årlig rapport om alla informationssäkerhetsprogram på Microsoft.

Log Analytics mjukvaruutveckling och serviceteam arbetar aktivt med Microsoft Legal and Compliance-team och andra branschpartners för att förvärva olika certifieringar.

## <a name="certifications-and-attestations"></a>Certifieringar och intyg
Azure Log Analytics uppfyller följande krav:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [PCI Security Standard (PCI-säkerhetsstandard) (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) av PCI Security Standards Council(PCI Compliant) Data Security Standard (PCI DSS).
* [Service Organisation Controls (SOC) 1 Typ 1 och SOC 2 Typ 1-kompatibel](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA och HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) för företag som har ett HIPAA Business Associate Agreement
* Windows gemensamma tekniska kriterier
* Microsofts pålitliga datoranvändning
* Som en Azure-tjänst följer de komponenter som Log Analytics använder Azure-efterlevnadskraven. Du kan läsa mer på [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> I vissa certifieringar/intyg listas Log Analytics under sitt tidigare namn *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Säkerhetsdataflöde för molnbaserad data
Följande diagram visar en molnsäkerhetsarkitektur som informationsflödet från ditt företag och hur den skyddas när den flyttas till Log Analytics-tjänsten, som du slutligen ser i Azure-portalen. Mer information om varje steg följer diagrammet.

![Bild av Logganalysdatainsamling och säkerhet](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Registrera dig för Log Analytics och samla in data
För att din organisation ska kunna skicka data till Log Analytics konfigurerar du en Windows- eller Linux-agent som körs på virtuella Azure-datorer eller på virtuella eller fysiska datorer i din miljö eller annan molnleverantör.  Om du använder Operations Manager konfigurerar du Operations Manager-agenten från hanteringsgruppen. Användare (som kan vara du, andra enskilda användare eller en grupp personer) skapar en eller flera Log Analytics-arbetsytor och registrerar agenter med något av följande konton:

* [Organisations-ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-konto – Outlook, Office Live, MSN](https://account.microsoft.com/account)

En Log Analytics-arbetsyta är där data samlas in, aggregeras, analyseras och presenteras. En arbetsyta används främst som ett sätt att partitionera data och varje arbetsyta är unik. Du kanske till exempel vill att dina produktionsdata ska hanteras med en arbetsyta och dina testdata hanteras med en annan arbetsyta. Arbetsytor hjälper också en administratör att kontrollera användarnas åtkomst till data. Varje arbetsyta kan ha flera användarkonton kopplade till sig och varje användarkonto kan komma åt flera Log Analytics-arbetsytor. Du skapar arbetsytor baserat på datacenterregionen.

För Operations Manager upprättar Ledningsgruppen för Operations Manager en anslutning till log analytics-tjänsten. Du kan sedan konfigurera vilka agenthanterade system i hanteringsgruppen som får samla in och skicka data till tjänsten. Beroende på vilken lösning du har aktiverat skickas data från dessa lösningar antingen direkt från en Hanteringsserver för Operations Manager till Log Analytics-tjänsten, eller på grund av mängden data som samlas in av det agenthanterade systemet, skickas direkt från en Hanteringsserver för Operations Manager till Log Analytics-tjänsten, eller på grund av mängden data som samlas in av det agenthanterade systemet, skickas direkt från en hanteringsserver för Operations Manager till Log Analytics-tjänsten, eller på grund av mängden data som samlas in av det agenthanterade systemet, skickas direkt från agenten till tjänsten. För system som inte övervakas av Operations Manager ansluter var och en säkert till Log Analytics-tjänsten direkt.

All kommunikation mellan anslutna system och Log Analytics-tjänsten är krypterad. HTTPS-protokollet (TLS) används för kryptering.  Microsoft SDL-processen följs för att säkerställa att Log Analytics är uppdaterad med de senaste framstegen inom kryptografiska protokoll.

Varje typ av agent samlar in data för Log Analytics. Vilken typ av data som samlas in beror på vilka typer av lösningar som används. Du kan se en sammanfattning av datainsamlingen på [Lägg till logganalyslösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md). Dessutom finns mer detaljerad insamlingsinformation tillgänglig för de flesta lösningar. En lösning är en bunt fördefinierade vyer, loggsökningsfrågor, datainsamlingsregler och bearbetningslogik. Endast administratörer kan använda Log Analytics för att importera en lösning. När lösningen har importerats flyttas den till Hanteringsservrarna i Operations Manager (om den används) och sedan till alla agenter som du har valt. Efteråt samlar agenterna in data.

## <a name="2-send-data-from-agents"></a>2. Skicka data från agenter
Du registrerar alla agenttyper med en registreringsnyckel och en säker anslutning upprättas mellan agenten och Log Analytics-tjänsten med hjälp av certifikatbaserad autentisering och TLS med port 443. Log Analytics använder en hemlig butik för att generera och underhålla nycklar. Privata nycklar roteras var 90:e dag och lagras i Azure och hanteras av Azure-åtgärder som följer strikta regel- och efterlevnadspraxis.

Med Operations Manager upprättar hanteringsgruppen som är registrerad med en Log Analytics-arbetsyta en säker HTTPS-anslutning till en Hanteringsserver för Operations Manager.

För Windows- eller Linux-agenter som körs på virtuella Azure-datorer används en skrivskyddad lagringsnyckel för att läsa diagnostikhändelser i Azure-tabeller.  

Med någon agent som rapporterar till en Hanteringsgrupp i Operations Manager som är integrerad med Log Analytics, om hanteringsservern av någon anledning inte kan kommunicera med tjänsten, lagras de insamlade data lokalt i en tillfällig cache på hanteringen Server.   De försöker skicka data var åttonde minut i två timmar.  För data som kringgår hanteringsservern och skickas direkt till Log Analytics överensstämmer beteendet med Windows-agenten.  

Windows- eller hanteringsserveragenten som cachelagrats av skyddas av operativsystemets autentiseringsuppgifter. Om tjänsten inte kan bearbeta data efter två timmar kommer agenterna att köa data. Om kön blir full börjar agenten släppa datatyper, med början i prestandadata. Gränsen för agentkön är en registernyckel så att du kan ändra den om det behövs. Insamlade data komprimeras och skickas till tjänsten och kringgår Hanteringsgruppdatabaserna i Operations Manager, så att de inte läggs till någon belastning. När de insamlade data skickas tas de bort från cachen.

Som beskrivits ovan skickas data från hanteringsservern eller direktanslutna agenter via TLS till Microsoft Azure-datacenter. Du kan också använda ExpressRoute för att ge ytterligare säkerhet för data. ExpressRoute är ett sätt att direkt ansluta till Azure från ditt befintliga WAN-nätverk, till exempel en VPN för växling av flera protokoll , som tillhandahålls av en nätverkstjänstleverantör. Mer information finns i [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Log Analytics-tjänsten tar emot och bearbetar data
Log Analytics-tjänsten säkerställer att inkommande data kommer från en betrodd källa genom att validera certifikat och dataintegriteten med Azure-autentisering. Obearbetade rådata lagras sedan i en Azure Event Hub i regionen som data så småningom kommer att lagras i vila. Vilken typ av data som lagras beror på vilka typer av lösningar som har importerats och använts för att samla in data. Sedan bearbetar log analytics-tjänsten rådata och intar dem i databasen.

Lagringsperioden för insamlade data som lagras i databasen beror på den valda prisplanen. För den *kostnadsfria* nivån är insamlade data tillgängliga i sju dagar. För nivån *Betald* är insamlade data tillgängliga i 31 dagar som standard, men kan förlängas till 730 dagar. Data lagras krypterade i vila i Azure-lagring, för att säkerställa datasekretess och data replikeras inom den lokala regionen med lokalt redundant lagring (LRS). De senaste två veckornas data lagras också i SSD-baserad cache och den här cachen är krypterad.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Använd Log Analytics för att komma åt data
Om du vill komma åt din Log Analytics-arbetsyta loggar du in på Azure-portalen med det organisationskonto eller Microsoft-konto som du har konfigurerat tidigare. All trafik mellan portalen och Log Analytics-tjänsten skickas via en säker HTTPS-kanal. När du använder portalen genereras ett sessions-ID på användarklienten (webbläsaren) och data lagras i en lokal cache tills sessionen avslutas. När cacheminnet avslutas tas cachen bort. Cookies på klientsidan, som inte innehåller personligt identifierbar information, tas inte bort automatiskt. Sessionscookies är markerade HTTPOnly och är säkrade. Efter en förutbestämd inaktiv period avslutas Azure-portalsessionen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du samlar in data med Log Analytics för dina virtuella Azure-datorer efter [snabbstarten för Azure VM](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Om du vill samla in data från fysiska eller virtuella Windows- eller Linux-datorer i din miljö läser du [Snabbstarten för Linux-datorer](../../azure-monitor/learn/quick-collect-linux-computer.md) eller [Snabbstart för Windows-datorer](../../azure-monitor/learn/quick-collect-windows-computer.md)

