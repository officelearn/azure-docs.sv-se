---
title: Logga Analytics-datasäkerhet | Microsoft Docs
description: Läs mer om hur Log Analytics skyddar din integritet och skyddar dina data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 0ac169060f7ba0e58aeb3e36e3af1629b6453fc1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667371"
---
# <a name="log-analytics-data-security"></a>Logga Analytics-datasäkerhet
Det här dokumentet är avsett att ge information som är speciell för Log Analytics, som är en funktion i Azure Monitor, för att komplettera informationen på [Azure Säkerhetscenter](../../security/fundamentals/trust-center.md).  

Den här artikeln förklarar hur data samlas in, bearbetas och skyddas av Log Analytics. Du kan använda agenter att ansluta till webbtjänsten bör använda System Center Operations Manager för att samla in användningsdata eller hämta data från Azure-diagnostik för användning av Log Analytics. 

Log Analytics-tjänsten hanterar dina molnbaserade data på ett säkert sätt med hjälp av följande metoder:

* dataavgränsning
* Datakvarhållning
* Fysisk säkerhet
* incidenthantering
* Efterlevnad
* certifieringar av standarder för säkerhet

Kontakta oss med frågor, förslag eller frågor om någon av följande uppgifter, inklusive våra säkerhets principer på [Support alternativ för Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Skicka data på ett säkert sätt med hjälp av TLS 1.2 

Om du vill se till att skydda data under överföringen till Log Analytics, rekommenderar vi starkt att du kan konfigurera att agenten ska du använda minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte**och branschen flyttas snabbt till överge support för dessa äldre protokoll. 

[PCI Security Standards-rådet](https://www.pcisecuritystandards.org/) har ställt in en [tids gräns på den 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) för att inaktivera äldre versioner av TLS/SSL och uppgradera till säkrare protokoll. När Azure sjunker bakåtkompatibelt stöd om agenterna inte kan kommunicera över minst TLS 1.2 inte skulle du kunna skicka data till Log Analytics. 

Vi rekommenderar inte uttryckligen inställningen din agent att bara använda TLS 1.2, såvida inte är absolut nödvändigt, eftersom det kan bryta säkerhet på funktioner som gör det möjligt att automatiskt identifiera och dra nytta av nya säkrare protokoll när de blir tillgängliga, till exempel som TLS 1.3. 

### <a name="platform-specific-guidance"></a>Plattformsspecifika vägledning

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner tenderar att förlita sig på [openssl](https://www.openssl.org) för TLS 1,2-stöd.  | Kontrol lera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows 8.0-10 | Stöds och aktiverat som standard. | För att bekräfta att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Stöds och aktiverat som standard. | Bekräfta att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men inte är aktiverad som standard. | På sidan [Transport Layer Security (TLS) register inställningar](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) finns mer information om hur du aktiverar.  |

## <a name="data-segregation"></a>dataavgränsning
När dina data matas in av Log Analytics-tjänsten, lagras data logiskt separerade på varje komponent i tjänsten. Alla data taggas per arbetsyta. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten. Dina data lagras i en dedikerad databas i lagringsklustret i den region som du har valt.

## <a name="data-retention"></a>Datakvarhållning
Indexerade log search-data lagras och bevaras enligt prisplanen. Mer information finns i [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/log-analytics/).

Som en del av ditt [prenumerations avtal](https://azure.microsoft.com/support/legal/subscription-agreement/)behåller Microsoft dina data enligt villkoren i avtalet.  När kundinformation tas bort, förstörs inga fysiska enheter.  

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
| Händelse |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Obs:** När du skriver händelser med anpassade fält i till händelse loggen i Windows samlar Log Analytics in dem. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, nätverksnamn, IP-adress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adress, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, Principalnamnet, OffsetInMinuteFromGreenwichTime |
| Prestanda |Objektnamn, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysisk säkerhet
Log Analytics-tjänsten hanteras av Microsoft-Personal och alla aktiviteter loggas och kan granskas. Log Analytics körs som en Azure-tjänst och uppfyller alla krav för Azure-efterlevnad och säkerhet. Du kan visa information om den fysiska säkerheten för Azure-tillgångar på sidan 18 i [Microsoft Azure säkerhets översikt](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysiska åtkomsträttigheter för att skydda områden ändras inom en arbetsdag för alla som inte längre har ansvar för Log Analytics-tjänsten, inklusive överföring och avslut. Du kan läsa om den globala fysiska infrastrukturen som vi använder på [Microsoft Data Center](https://azure.microsoft.com/global-infrastructure/).

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

Mer information om hur Microsoft reagerar på säkerhets incidenter finns i [Microsoft Azure säkerhets svar i molnet](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Efterlevnad
Den Log Analytics program utvecklings-och tjänst teamets informations säkerhet och styrnings program stöder sina affärs behov och följer lagar och förordningar enligt beskrivningen i [Microsoft Azure säkerhets Center](https://azure.microsoft.com/support/trust-center/) och [Microsoft Trust Center-efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hur Log Analytics upprättar säkerhetskrav, identifierar säkerhetskontroller, hanterar och övervakar risker beskrivs också det. Årligen, vi granska principer, standarder och procedurer som riktlinjer.

Varje medlem i gruppen utveckling får formella säkerhetsutbildning. Vi använder internt, ett versionskontrollsystem för programutveckling. Varje programvaruprojekt skyddas av systemet för versionskontroll.

Microsoft har ett team för säkerhet och efterlevnad som övervakar och utvärderar alla tjänster i Microsoft. Information security införlivande utgör teamet och de är inte kopplade teknikteam som utvecklar Log Analytics. De har sina egna management-kedjan och utföra oberoende utvärderingar av produkter och tjänster för att säkerställa säkerhet och efterlevnad.

Microsofts styrelse meddelas via en årlig rapport om alla informationssäkerhetsprogram på Microsoft.

Log Analytics-programutveckling och service-teamet arbetar aktivt med Microsoft Legal och efterlevnad teams och andra branschpartners att förvärva olika certifieringar.

## <a name="certifications-and-attestations"></a>Certifieringar och attesteringar
Azure Log Analytics uppfyller följande krav:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Payment Card Industry (PCI-kompatibel) data säkerhets standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) av PCI Security Standards-rådet.
* [Service Organization Controls (SOC) 1 typ 1 och SOC 2 typ 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) är kompatibel
* [HIPAA och HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) för företag som har ett HIPAA affärs associerat avtal
* Villkor för vanliga tekniker i Windows
* Microsoft Trustworthy Computing
* Som en Azure-tjänsten följa de komponenter som Log Analytics använder Azure efterlevnadskrav. Du kan läsa mer på [Microsoft Trust Center-kompatibilitet](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> I vissa certifieringar/attesteringar visas Log Analytics under det tidigare namnet på *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Molnbaserad databehandling security dataflöde
Följande diagram visar en cloud security-arkitektur som flödet av information från ditt företag och hur den är skyddad enligt är flyttar till tjänsten Log Analytics slutligen visas av dig i Azure-portalen. Mer information om varje steg följer diagrammet.

![Bild av Log Analytics-datainsamling och säkerhet](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. registrera dig för Log Analytics och samla in data
Din organisation att skicka data till Log Analytics, konfigurerar du en Windows- eller Linux-agent som körs på Azure virtual machines, eller på virtuella eller fysiska datorer i din miljö eller annan molnleverantör.  Om du använder Operations Manager från hanteringsgruppen konfigurera du Operations Manager-agenten. Användare (som kan vara dig, andra enskilda användare eller en grupp människor) skapa en eller flera Log Analytics-arbetsytor och registrera agenter med någon av följande konton:

* [Organisations-ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-konto – Outlook, Office Live, MSN](https://account.microsoft.com/account)

En Log Analytics-arbetsyta är där data samlas in, aggregeras, analyseras och presenteras. En arbetsyta är främst används som ett sätt att partitionera data och varje arbetsyta är unik. Du kanske vill ha din produktionsdata som hanteras med en arbetsyta och din testdata som hanteras med en annan arbetsyta. Arbetsytor hjälper också att en administratör kontrollera åtkomst till data. Varje arbetsyta kan ha flera användarkonton kopplade till den och varje användarkonto kan komma åt flera Log Analytics-arbetsytor. Du kan skapa arbetsytor baserat på datacenterregion.

För Operations Manager upprättar Operations Manager-hanteringsgrupp en anslutning med Log Analytics-tjänsten. Du kan sedan konfigurera vilka system som hanteras med agent i hanteringsgruppen ska kunna samla in och skicka data till tjänsten. Beroende på lösningen som du har aktiverat och data från dessa lösningar är antingen skickas direkt från en hanteringsserver för Operations Manager till Log Analytics-tjänsten eller på grund av mängden data som samlas in av systemet hanteras med agent, skickas direkt från agenten till tjänsten. För system som inte övervakas av Operations Manager, ansluter var och en på ett säkert sätt till Log Analytics-tjänsten direkt.

All kommunikation mellan anslutna system och Log Analytics-tjänsten är krypterad. TLS (HTTPS)-protokollet används för kryptering.  Microsoft SDL-processen följs för att kontrollera Log Analytics är uppdaterade med de senaste utvecklingen av kryptografiska protokoll.

Varje typ av agenten samlar in data för Log Analytics. Vilken typ av data som samlas in beror på vilka typer av lösningar som används. Du kan se en sammanfattning av data insamling i [Lägg till Log Analytics lösningar från Lösningsgalleriet](../../azure-monitor/insights/solutions.md). Dessutom finns mer detaljerad samlingsinformation för de flesta lösningar. En lösning är ett paket med fördefinierade vyer, loggsökningsfrågor, regler för insamling av data och bearbetning av dataströmmar. Endast administratörer kan använda Log Analytics för att importera en lösning. När lösningen har importerats, flyttas den till Operations Manager-hanteringsservrar (om det används) och sedan till alla eventuella agenter som du har valt. Därefter agenterna samla in data.

## <a name="2-send-data-from-agents"></a>2. skicka data från agenter
Du registrerar alla typer av agenten med en nyckel som registrering och en säker anslutning har upprättats mellan agenten och tjänsten Log Analytics med hjälp av certifikatbaserad autentisering och SSL med port 443. Log Analytics använder en lagringsplats för hemligheter för att skapa och underhålla nycklar. Privata nycklar roteras var 90: e dag och lagras i Azure och som hanteras av Azure-åtgärder som följer strikta regelverks- och efterlevnadskrav metoder.

Med Operations Manager upprättar en säker HTTPS-anslutning med en hanteringsserver för Operations Manager i hanteringsgruppen registrerats hos en Log Analytics-arbetsyta.

För Windows eller Linux-agenter som körs på virtuella Azure-datorer är används en skrivskyddad lagringsnyckeln för att läsa diagnostikhändelser i Azure-tabeller.  

Med alla agenter som rapporterar till en Operations Manager-hanteringsgrupp som är integrerat med Log Analytics om hanteringsservern inte kan kommunicera med lagras tjänsten av någon anledning insamlade data lokalt i en tillfällig cache på hantering Server.   De försöker skicka om data var åttonde minut i två timmar.  Beteendet är konsekvent med Windows-agenten för data som kringgår management-servern och skickas direkt till Log Analytics.  

Windows eller management server-agenten cachelagrade data skyddas av operativsystemets arkivet. Om tjänsten inte kan bearbeta data efter två timmar, köar agenterna data. Om kön är full, börjar agenten släppa datatyper, från och med prestandadata. Kögränsen agent är en registernyckel så att du kan ändra den, om det behövs. Insamlade data komprimeras och skickas till tjänsten, vilket kringgår Operations Manager management group-databaserna så att all belastning inte läggs till dem. När insamlade data skickas, tas den bort från cachen.

Enligt beskrivningen ovan, skickas data från den hanteringsserver eller direktanslutna agenter via SSL till Microsoft Azure-datacenter. Du kan även använda ExpressRoute för att ge ytterligare säkerhet för data. ExpressRoute är ett sätt att ansluta direkt till Azure från ditt befintliga WAN-nätverk, t.ex. Multi-Protocol label switching (MPLS) VPN, som tillhandahålls av en nätverkstjänstleverantör. Mer information finns i [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Log Analytics tjänsten tar emot och bearbetar data
Log Analytics-tjänsten garanterar att inkommande data är från en betrodd källa genom att verifiera certifikat och dataintegritet med Azure-autentisering. Obearbetade rådata lagras sedan i en Azure-Händelsehubb i regionen kommer så småningom att lagras data i vila. Vilken typ av data som lagras beror på vilka typer av lösningar som har importerats och används för att samla in data. Log Analytics service processer rådata och matar in den i databasen.

Kvarhållningsperioden för insamlade data som lagras i databasen är beroende av den valda prisplanen. För den *kostnads fria* nivån finns insamlade data tillgängliga i sju dagar. För den *betalda* nivån är insamlade data tillgängliga i 31 dagar som standard, men kan utökas till 730 dagar. Data lagras krypterat i vila i Azure storage, för att säkerställa datasekretess, och data replikeras inom lokala region med hjälp av lokalt redundant lagring (LRS). De senaste två veckorna med data lagras också i SSD-baserad cache och denna cache är krypterad.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Använd Log Analytics för att komma åt data
För att få åtkomst till Log Analytics-arbetsytan måste du logga in på Azure-portalen med organisationens konto eller Microsoft-konto som du tidigare har konfigurerat. All trafik mellan portalen och Log Analytics-tjänsten skickas via en säker HTTPS-kanal. När du använder portalen, genereras ett sessions-ID på klienten för användare (webbläsare) och data lagras i ett lokalt cacheminne tills sessionen avslutas. När avslutas, tas cachen bort. Klientsidan cookies, som inte innehåller personligt identifierbar information tas inte bort automatiskt. Sessionscookies är markerad HTTPOnly och skyddas. Efter en förutbestämd inaktiv tid, har Azure portal sessionen avslutats.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du samlar in data med Log Analytics för dina virtuella Azure-datorer enligt snabb starten för [Azure VM](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Om du vill samla in data från fysiska eller virtuella Windows-eller Linux-datorer i din miljö kan du läsa [snabb starten för Linux-datorer](../../azure-monitor/learn/quick-collect-linux-computer.md) eller [snabb start för Windows-datorer](../../azure-monitor/learn/quick-collect-windows-computer.md)

