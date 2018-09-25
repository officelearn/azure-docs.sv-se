---
title: Logga Analytics-datasäkerhet | Microsoft Docs
description: Läs mer om hur Log Analytics skyddar din integritet och skyddar dina data.
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
ms.date: 09/19/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: b91b906db1307343a50ffc3be07d562091f2e335
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978814"
---
# <a name="log-analytics-data-security"></a>Logga Analytics-datasäkerhet
Det här dokumentet är avsedd att ge specifik information till Log Analytics, som är en funktion i Azure Monitor för att komplettera informationen på [Azure Trust Center](../security/security-microsoft-trust-center.md).  

Den här artikeln förklarar hur data som samlas in, bearbetas och skyddas av Log Analytics. Du kan använda agenter att ansluta till webbtjänsten bör använda System Center Operations Manager för att samla in användningsdata eller hämta data från Azure-diagnostik för användning av Log Analytics. 

Log Analytics-tjänsten hanterar dina molnbaserade data på ett säkert sätt med hjälp av följande metoder:

* dataavgränsning
* Datakvarhållning
* Fysisk säkerhet
* incidenthantering
* Efterlevnad
* certifieringar av standarder för säkerhet

Kontakta oss med frågor, förslag eller problem om något av följande information, inklusive våra säkerhetsprinciper på [supportalternativ för Azure](http://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Skicka data på ett säkert sätt med hjälp av TLS 1.2 

Om du vill se till att skydda data under överföringen till Log Analytics, rekommenderar vi starkt att du kan konfigurera att agenten ska du använda minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har påträffats sårbara och de fungerar fortfarande för närvarande för att tillåta bakåtkompatibilitet kompatibilitet, de arbetar **rekommenderas inte**, och branschen snabbt flytta att lämna support äldre protokoll. 

Den [PCI Security Standards Council](https://www.pcisecuritystandards.org/) har ställt in en [deadline på den 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) att inaktivera äldre versioner av TLS/SSL och uppgraderingen säkrare protokoll. När Azure sjunker bakåtkompatibelt stöd om agenterna inte kan kommunicera över minst TLS 1.2 inte skulle du kunna skicka data till Log Analytics. 

Vi rekommenderar inte uttryckligen inställningen din agent att bara använda TLS 1.2, såvida inte är absolut nödvändigt, eftersom det kan bryta säkerhet på funktioner som gör det möjligt att automatiskt identifiera och dra nytta av nya säkrare protokoll när de blir tillgängliga, till exempel som TLS 1.3. 

### <a name="platform-specific-guidance"></a>Plattformsspecifika vägledning

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner tenderar att förlita dig på [OpenSSL](https://www.openssl.org) för stöd för TSL 1.2.  | Kontrollera den [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) att bekräfta din version av OpenSSL stöds.|
| Windows 8.0-10 | Stöds och aktiverat som standard. | Bekräfta att du fortfarande använder den [standardinställningar](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Stöds och aktiverat som standard. | Bekräfta att du fortfarande använder den [standardinställningar](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men inte är aktiverad som standard. | Se den [registerinställningar för Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) för information om hur du aktiverar.  |
| Windows Server 2008 SP2 | Stöd för TLS 1.2 kräver en uppdatering. | Se [Update för att lägga till stöd för TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) i Windows Server 2008 SP2. |

## <a name="data-segregation"></a>dataavgränsning
När dina data matas in av Log Analytics-tjänsten, lagras data logiskt separerade på varje komponent i tjänsten. Alla data taggas per arbetsyta. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. Dina data lagras i en dedikerad databas i lagringsklustret i den region som du har valt.

## <a name="data-retention"></a>Datakvarhållning
Indexerade log search-data lagras och bevaras enligt prisplanen. Mer information finns i [priser för Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Som en del av din [prenumerationsavtalet](https://azure.microsoft.com/support/legal/subscription-agreement/), Microsoft behåller dina data enligt villkoren i avtalet.  När data tas bort, vi också ta bort Azure Storage-konto där data finns.  När kundinformation tas bort, förstörs inga fysiska enheter.  

I följande tabell visas några av de tillgängliga lösningarna och innehåller exempel på vilken sorts information som samlas in.

| **Lösning** | **Datatyper** |
| --- | --- |
| Kapacitet och prestanda |Prestandadata och metadata |
| Uppdateringshantering |Metadata och tillstånd |
| Logghantering |Användardefinierade händelseloggar, Windows-händelseloggar och/eller IIS-loggar |
| Spårning av ändringar |Programvaruinventering, Windows-tjänsten och metadata för Linux-daemon och Windows-/ Linux filens metadata |
| SQL- och Active Directory-utvärdering |Data från WMI, registret, prestandadata och SQL Server dynamic management visa resultat |

I följande tabell visas exempel på datatyper:

| **Datatyp** | **Fält** |
| --- | --- |
| Varning |Avisera namn, Aviseringsbeskrivningen, BaseManagedEntityId, Problem-ID, IsMonitorAlert, RuleId, ResolutionState, prioritet, allvarlighetsgrad, kategori, ägare, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguration |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Händelse |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Obs:** när du skriver händelser med anpassade fält i Windows-händelseloggen, Log Analytics samlar in dem. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, nätverksnamn, IP-adress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adress, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, Principalnamnet, OffsetInMinuteFromGreenwichTime |
| Prestanda |Objektnamn, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysisk säkerhet
Log Analytics-tjänsten hanteras av Microsoft-Personal och alla aktiviteter loggas och kan granskas. Log Analytics körs som en Azure-tjänst och uppfyller alla krav för Azure-efterlevnad och säkerhet. Du kan visa information om den fysiska säkerheten för Azure-tillgångar på sidan 18 i den [översikt över Microsoft Azure Security](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysiska åtkomsträttigheter för att skydda områden ändras inom en arbetsdag för alla som inte längre har ansvar för Log Analytics-tjänsten, inklusive överföring och avslut. Du kan läsa om den globala fysiska infrastrukturen som vi använder på [Microsofts Datacenters](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>incidenthantering
Log Analytics har en incidenthanteringsprocess som följer alla Microsoft-tjänster. Sammanfattningsvis, vi:

* Använda ett delat ansvar modell där en del av security ansvar som tillhör Microsoft och en del tillhör kunden
* Hantera Azure-säkerhet:
  * Starta en undersökning vid identifiering av en incident
  * Utvärdera effekten och för ett ärende genom en gruppmedlem kan göra på anrop incidenter. Utifrån bevis utvärderingen kan eller inte kan resultera i ytterligare eskalering till säkerhetsteamet för svaret.
  * Diagnostisera en incident av svaret säkerhetsexperter att utföra tekniska eller kriminaltekniska undersökning, identifiera strategier för inneslutning, begränsning och lösning. Om säkerhetsteamet anser att kundernas data kan ha bli exponerat för en obehörig person, börjar parallell körning av kunden Incident meddelandeprocessen parallellt.  
  * Stabilisera och återställa från incidenten. Incidenthanteringsteam skapar en återställningsplan för att åtgärda problemet. Kriser inneslutning steg, till exempel placera det i karantän berörda system kan uppstå omedelbart och parallellt med diagnos. Längre sikt åtgärder planeras som inträffar efter omedelbar risken har passerat.  
  * Stänger incidenten och utföra en utvärdering. Incidenthanteringsteam skapar en utvärdering som visar information om incidenten med avsikt att ändra principer, procedurer och processer för att förhindra att en upprepning av händelsen.
* Meddela kunder om säkerhetsincidenter:
  * Fastställa omfattningen av berörda kunder och för att ange vem som helst som påverkas så detaljerat ett meddelande som möjligt
  * Skapa ett meddelande för att ge kunder med detaljerad tillräckligt med information så att de kan utföra en undersökning på sina slutet och uppfyller alla som har gjorts i slutanvändarna när inte vilket försenar meddelandeprocessen.
  * Bekräfta och deklarera incident, vid behov.
  * Meddela kunder med ett meddelande om incident utan orimliga fördröjning och i enlighet med alla juridiska eller avtalsenliga åtagande. Meddelanden om incidenter levereras till en eller flera av en kunds administratörer på något sätt som Microsoft väljer, inklusive via e-post.
* Beredskap för uppförande team och utbildning:
  * Microsoft-Personal krävs för att slutföra säkerhets- och utbildning som hjälper dem att identifiera och rapportera säkerhetsincidenter.  
  * Operatörer som arbetar med Microsoft Azure-tjänst har ytterligare utbildning skyldigheter omgivande åtkomsten till känsliga system som är värd för kunddata.
  * Microsoft security response personal får specialiserade utbildning för deras roller

Om all kundinformation går förlorade, meddelar vi varje kund på en dag. Kunden dataförlust har aldrig uppstått med tjänsten. 

Läs mer om hur Microsoft besvarar säkerhetsincidenter [Microsoft Azure Security Response i molnet](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Efterlevnad
Log Analytics programvara utvecklings- och gruppens information säkerhet och styrning programmet stöder affärskraven och följer lagar och föreskrifter enligt beskrivningen i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) och [ Microsoft Trust Center efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hur Log Analytics upprättar säkerhetskrav, identifierar säkerhetskontroller, hanterar och övervakar risker beskrivs också det. Årligen, vi granska principer, standarder och procedurer som riktlinjer.

Varje medlem i gruppen utveckling får formella säkerhetsutbildning. Vi använder internt, ett versionskontrollsystem för programutveckling. Varje programvaruprojekt skyddas av systemet för versionskontroll.

Microsoft har ett team för säkerhet och efterlevnad som övervakar och utvärderar alla tjänster i Microsoft. Information security införlivande utgör teamet och de är inte kopplade teknikteam som utvecklar Log Analytics. De har sina egna management-kedjan och utföra oberoende utvärderingar av produkter och tjänster för att säkerställa säkerhet och efterlevnad.

Microsofts styrelse meddelas via en årlig rapport om alla informationssäkerhetsprogram på Microsoft.

Log Analytics-programutveckling och service-teamet arbetar aktivt med Microsoft Legal och efterlevnad teams och andra branschpartners att förvärva olika certifieringar.

## <a name="certifications-and-attestations"></a>Certifieringar och attesteringar
Azure Log Analytics uppfyller följande krav:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [STANDARDEN ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Säkerhetsstandard för Payment Card Industry (PCI-kompatibel) Data (PCI-DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) av PCI Security Standards Council.
* [Tjänsten organisation Controls (SOC) 1 typ 1 och SOC 2 typ 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) kompatibla
* [HIPAA och HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) för företag som har ett HIPAA företag associera avtal
* Villkor för vanliga tekniker i Windows
* Microsoft Trustworthy Computing
* Som en Azure-tjänsten följa de komponenter som Log Analytics använder Azure efterlevnadskrav. Du kan läsa mer på [Microsoft förtroende Center-efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> I vissa certifieringar/attesteringar, Log Analytics finns under dess tidigare namnet på *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Molnbaserad databehandling security dataflöde
Följande diagram visar en cloud security-arkitektur som flödet av information från ditt företag och hur den är skyddad enligt är flyttar till tjänsten Log Analytics slutligen visas av dig i Azure-portalen. Mer information om varje steg följer diagrammet.

![Bild av Log Analytics-datainsamling och säkerhet](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Registrera dig för Log Analytics och samla in data
Din organisation att skicka data till Log Analytics, konfigurerar du en Windows- eller Linux-agent som körs på Azure virtual machines, eller på virtuella eller fysiska datorer i din miljö eller annan molnleverantör.  Om du använder Operations Manager från hanteringsgruppen konfigurera du Operations Manager-agenten. Användare (som kan vara dig, andra enskilda användare eller en grupp människor) skapa en eller flera Log Analytics-arbetsytor och registrera agenter med någon av följande konton:

* [Organisations-ID](../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-konto – Outlook, Office Live, MSN](https://account.microsoft.com/account)

En Log Analytics-arbetsyta är där data samlas in, aggregeras, analyseras och presenteras. En arbetsyta är främst används som ett sätt att partitionera data och varje arbetsyta är unik. Du kanske vill ha din produktionsdata som hanteras med en arbetsyta och din testdata som hanteras med en annan arbetsyta. Arbetsytor hjälper också att en administratör kontrollera åtkomst till data. Varje arbetsyta kan ha flera användarkonton kopplade till den och varje användarkonto kan komma åt flera Log Analytics-arbetsytor. Du kan skapa arbetsytor baserat på datacenterregion. Varje arbetsyta replikeras till andra datacenter i regionen, främst för Log Analytics tjänstens tillgänglighet.

För Operations Manager upprättar Operations Manager-hanteringsgrupp en anslutning med Log Analytics-tjänsten. Du kan sedan konfigurera vilka system som hanteras med agent i hanteringsgruppen ska kunna samla in och skicka data till tjänsten. Beroende på lösningen som du har aktiverat och data från dessa lösningar är antingen skickas direkt från en hanteringsserver för Operations Manager till Log Analytics-tjänsten eller på grund av mängden data som samlas in av systemet hanteras med agent, skickas direkt från agenten till tjänsten. För system som inte övervakas av Operations Manager, ansluter var och en på ett säkert sätt till Log Analytics-tjänsten direkt.

All kommunikation mellan anslutna system och Log Analytics-tjänsten är krypterad. TLS (HTTPS)-protokollet används för kryptering.  Microsoft SDL-processen följs för att kontrollera Log Analytics är uppdaterade med de senaste utvecklingen av kryptografiska protokoll.

Varje typ av agenten samlar in data för Log Analytics. Vilken typ av data som samlas in beror på vilka typer av lösningar som används. Du kan se en sammanfattning av insamling av data på [lägga till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md). Dessutom finns mer detaljerad samlingsinformation för de flesta lösningar. En lösning är ett paket med fördefinierade vyer, loggsökningsfrågor, regler för insamling av data och bearbetning av dataströmmar. Endast administratörer kan använda Log Analytics för att importera en lösning. När lösningen har importerats, flyttas den till Operations Manager-hanteringsservrar (om det används) och sedan till alla eventuella agenter som du har valt. Därefter agenterna samla in data.

## <a name="2-send-data-from-agents"></a>2. Skicka data från agenter
Du registrerar alla typer av agenten med en nyckel som registrering och en säker anslutning har upprättats mellan agenten och tjänsten Log Analytics med hjälp av certifikatbaserad autentisering och SSL med port 443. Log Analytics använder en lagringsplats för hemligheter för att skapa och underhålla nycklar. Privata nycklar roteras var 90: e dag och lagras i Azure och som hanteras av Azure-åtgärder som följer strikta regelverks- och efterlevnadskrav metoder.

Med Operations Manager upprättar en säker HTTPS-anslutning med en hanteringsserver för Operations Manager i hanteringsgruppen registrerats hos en Log Analytics-arbetsyta.

För Windows eller Linux-agenter som körs på virtuella Azure-datorer är används en skrivskyddad lagringsnyckeln för att läsa diagnostikhändelser i Azure-tabeller.  

Med alla agenter som rapporterar till en Operations Manager-hanteringsgrupp som är integrerat med Log Analytics om hanteringsservern inte kan kommunicera med lagras tjänsten av någon anledning insamlade data lokalt i en tillfällig cache på hantering Server.   De försöker skicka om data var åttonde minut i två timmar.  Beteendet är konsekvent med Windows-agenten för data som kringgår management-servern och skickas direkt till Log Analytics.  

Windows eller management server-agenten cachelagrade data skyddas av operativsystemets arkivet. Om tjänsten inte kan bearbeta data efter två timmar, köar agenterna data. Om kön är full, börjar agenten släppa datatyper, från och med prestandadata. Kögränsen agent är en registernyckel så att du kan ändra den, om det behövs. Insamlade data komprimeras och skickas till tjänsten, vilket kringgår Operations Manager management group-databaserna så att all belastning inte läggs till dem. När insamlade data skickas, tas den bort från cachen.

Enligt beskrivningen ovan, skickas data från den hanteringsserver eller direktanslutna agenter via SSL till Microsoft Azure-datacenter. Du kan även använda ExpressRoute för att ge ytterligare säkerhet för data. ExpressRoute är ett sätt att ansluta direkt till Azure från ditt befintliga WAN-nätverk, t.ex. Multi-Protocol label switching (MPLS) VPN, som tillhandahålls av en nätverkstjänstleverantör. Mer information finns i [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Log Analytics-tjänsten tar emot och bearbetar data
Log Analytics-tjänsten garanterar att inkommande data är från en betrodd källa genom att verifiera certifikat och dataintegritet med Azure-autentisering. Obearbetade rådata lagras sedan i en Azure-Händelsehubb i regionen kommer så småningom att lagras data i vila. Vilken typ av data som lagras beror på vilka typer av lösningar som har importerats och används för att samla in data. Log Analytics service processer rådata och matar in den i databasen.

Kvarhållningsperioden för insamlade data som lagras i databasen är beroende av den valda prisplanen. För den *kostnadsfri* nivå, insamlade data är tillgängliga i sju dagar. För den *betald* nivå, insamlade data är tillgängliga i 31 dagar som standard, men kan utökas till 730 dagar. Data lagras krypterat i vila i Azure storage, för att säkerställa datasekretess, och data replikeras inom lokala region med hjälp av lokalt redundant lagring (LRS). De senaste två veckorna data lagras också i SSD-baserad cacheminnet och det här cacheminnet är för närvarande inte krypterat.  Vi arbetar för att stödja kryptering av SSD-baserad cache.      

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Använd Log Analytics för att få åtkomst till data
För att få åtkomst till Log Analytics-arbetsytan måste du logga in på Azure-portalen med organisationens konto eller Microsoft-konto som du tidigare har konfigurerat. All trafik mellan portalen och Log Analytics-tjänsten skickas via en säker HTTPS-kanal. När du använder portalen, genereras ett sessions-ID på klienten för användare (webbläsare) och data lagras i ett lokalt cacheminne tills sessionen avslutas. När avslutas, tas cachen bort. Klientsidan cookies, som inte innehåller personligt identifierbar information tas inte bort automatiskt. Sessionscookies är markerad HTTPOnly och skyddas. Efter en förutbestämd inaktiv tid, har Azure portal sessionen avslutats.

## <a name="next-steps"></a>Nästa steg
* Lär dig att samla in data med Log Analytics för dina virtuella Azure-datorer efter den [snabbstarten för Azure virtuella datorer](log-analytics-quick-collect-azurevm.md).  

*  Om du vill samla in data från fysiska eller virtuella Windows- eller Linux-datorer i din miljö finns i den [Snabbstart för Linux-datorer](log-analytics-quick-collect-linux-computer.md) eller [Snabbstart för Windows-datorer](log-analytics-quick-collect-windows-computer.md)

