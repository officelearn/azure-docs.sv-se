---
title: Avancerad hotidentifiering av Azure | Microsoft-dokument
description: Lär dig mer om Azure AD Identity Protection och dess funktioner.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3c1c385a87fc302d180729ec2e4bcd1c4a315f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981471"
---
# <a name="azure-advanced-threat-detection"></a>Avancerad hotidentifiering i Azure

Azure erbjuder inbyggda avancerade funktioner för identifiering av hot via tjänster som Azure Active Directory (Azure AD), Azure Monitor-loggar och Azure Security Center. Den här samlingen av säkerhetstjänster och funktioner är ett enkelt och snabbt sätt att förstå vad som händer i dina Azure-distributioner.

Azure innehåller ett brett utbud av alternativ för att konfigurera och anpassa säkerheten för att uppfylla kraven i dina appdistributioner. I den här artikeln beskrivs hur du uppfyller dessa krav.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) är en [Azure Active Directory Premium P2-utgåva](../../active-directory/active-directory-whatis.md) som ger en översikt över riskidentifieringar och potentiella sårbarheter som kan påverka organisationens identiteter. Identity Protection använder befintliga Azure AD-avvikelseidentifieringsfunktioner som är tillgängliga via [Azure AD Anomalous Activity Reports](../../active-directory/active-directory-reporting-azure-portal.md)och introducerar nya riskidentifieringstyper som kan identifiera avvikelser i realtid.

![Azure AD-identitetsskyddsdiagram](./media/threat-detection/azure-threat-detection-fig1.png)

Identity Protection använder adaptiva maskininlärningsalgoritmer och heuristik för att identifiera avvikelser och riskidentifieringar som kan tyda på att en identitet har komprometterats. Med hjälp av dessa data genererar Identity Protection rapporter och aviseringar så att du kan undersöka dessa riskidentifieringar och vidta lämpliga åtgärder för reparation eller begränsning.

Azure Active Directory Identity Protection är mer än ett övervaknings- och rapporteringsverktyg. Baserat på riskidentifieringar beräknar Identity Protection en användarrisknivå för varje användare, så att du kan konfigurera riskbaserade principer för att automatiskt skydda identiteterna för din organisation.

Dessa riskbaserade principer, förutom andra [villkorliga åtkomstkontroller](../../active-directory/active-directory-conditional-access-azure-portal.md) som tillhandahålls av Azure Active Directory och [EMS,](../../active-directory/active-directory-conditional-access-azure-portal.md)kan automatiskt blockera eller erbjuda anpassningsbara åtgärder för reparation som inkluderar lösenordsåterställning och tvingande multifaktorautentisering.

### <a name="identity-protection-capabilities"></a>Funktioner för identitetsskydd

Azure Active Directory Identity Protection är mer än ett övervaknings- och rapporteringsverktyg. För att skydda organisationens identiteter kan du konfigurera riskbaserade principer som automatiskt svarar på identifierade problem när en angiven risknivå har uppnåtts. Dessa principer, förutom andra villkorliga åtkomstkontroller som tillhandahålls av Azure Active Directory och EMS, kan antingen automatiskt blockera eller initiera adaptiva reparationsåtgärder, inklusive återställning av lösenord och tvingande multifaktorautentisering.

Exempel på några av de sätt som Azure Identity Protection kan hjälpa till att skydda dina konton och identiteter är:

[Identifiera riskidentifieringar och riskfyllda konton](../../active-directory/identity-protection/overview.md)
-   Identifiera sex riskidentifieringstyper med hjälp av maskininlärnings- och heuristiska regler.
-   Beräkna användarrisknivåer.
-   Ge anpassade rekommendationer för att förbättra den övergripande säkerhetshållningen genom att belysa sårbarheter.

[Undersöka riskidentifiering](../../active-directory/identity-protection/overview.md)
-   Skicka meddelanden för riskidentifiering.
-   Undersök riskidentifieringar med hjälp av relevant och kontextuell information.
-   Tillhandahålla grundläggande arbetsflöden för att spåra undersökningar.
-   Ge enkel åtkomst till reparationsåtgärder som återställning av lösenord.

[Riskbaserade principer för villkorlig åtkomst](../../active-directory/identity-protection/overview.md)
-   Minska riskfyllda inloggningar genom att blockera inloggningar eller kräva multifaktorautentiseringsutmaningar.
-   Blockera eller säkra riskfyllda användarkonton.
-   Kräv att användare registrerar sig för multifaktorautentisering.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Med [Azure Active Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)kan du hantera, kontrollera och övervaka åtkomsten inom organisationen. Den här funktionen omfattar åtkomst till resurser i Azure AD och andra Microsoft-onlinetjänster, till exempel Office 365 eller Microsoft Intune.

![Azure AD-behörighetsidentitetshanteringsdiagram](./media/threat-detection/azure-threat-detection-fig2.png)

PIM hjälper dig:

-   Få aviseringar och rapporter om Azure AD-administratörer och just-in-time (JIT) administrativ åtkomst till Microsofts onlinetjänster, till exempel Office 365 och Intune.

-   Hämta rapporter om administratörsåtkomsthistorik och ändringar i administratörstilldelningar.

-   Få aviseringar om åtkomst till en privilegierad roll.

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](../../azure-monitor/index.yml) är en Microsoft-molnbaserad IT-hanteringslösning som hjälper dig att hantera och skydda din lokala infrastruktur och molninfrastruktur. Eftersom Azure Monitor-loggar implementeras som en molnbaserad tjänst kan du få den igång snabbt med minimala investeringar i infrastrukturtjänster. Nya säkerhetsfunktioner levereras automatiskt, vilket sparar löpande underhålls- och uppgraderingskostnader.

Förutom att tillhandahålla värdefulla tjänster på egen hand kan Azure Monitor-loggar integreras med System Center-komponenter, till exempel [System Center Operations Manager,](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/)för att utöka dina befintliga investeringar i säkerhetshantering i molnet. System Center- och Azure Monitor-loggar kan arbeta tillsammans för att ge en fullständig hybridhanteringsupplevelse.

### <a name="holistic-security-and-compliance-posture"></a>Holistisk säkerhet och efterlevnad hållning

[Instrumentpanelen Log Analytics Security and Audit](../../security-center/security-center-intro.md) ger en omfattande vy över organisationens IT-säkerhetsposition, med inbyggda sökfrågor för anmärkningsvärda problem som kräver din uppmärksamhet. Instrumentpanelen Säkerhet och granskning är startskärmen för allt som rör säkerhet i Azure Monitor-loggar. Den innehåller en översikt över säkerhetsstatusen för dina datorer. Du kan också visa alla händelser från de senaste 24 timmarna, 7 dagar eller någon annan anpassad tidsram.

Azure Monitor-loggar hjälper dig att snabbt och enkelt förstå den övergripande säkerhetspositionen för alla miljöer, allt inom ramen för IT-åtgärder, inklusive bedömning av programuppdatering, utvärdering av skadlig kod och konfigurationsbaslinjer. Säkerhetsloggdata är lättillgängliga för att effektivisera säkerhets- och efterlevnadsgranskningsprocesserna.

![Instrumentpanelen För säkerhet och granskning i Logganalys](./media/threat-detection/azure-threat-detection-fig3.jpg)

Instrumentpanelen Log Analytics Security and Audit är uppdelad i fyra huvudkategorier:

-   **Säkerhetsdomäner:** Låter dig utforska säkerhetsposter ytterligare över tid; få tillgång till bedömningar av skadlig kod. Uppdatera bedömningar. Visa nätverkssäkerhet, identitet och åtkomstinformation. visa datorer med säkerhetshändelser. och snabbt komma åt Azure Security Center-instrumentpanelen.

-   **Anmärkningsvärda problem:** Gör att du snabbt kan identifiera antalet aktiva problem och hur allvarliga problemen är.

-   **Identifieringar (förhandsversion)**: Gör att du kan identifiera attackmönster genom att visa säkerhetsaviseringar när de inträffar mot dina resurser.

-   **Threat Intelligence**: Låter dig identifiera attackmönster genom att visa det totala antalet servrar med utgående skadlig IP-trafik, den skadliga hottypen och en karta över IP-platser.

-   **Vanliga säkerhetsfrågor**: Visar de vanligaste säkerhetsfrågorna som du kan använda för att övervaka din miljö. När du väljer en fråga öppnas sökfönstret och resultatet för frågan visas.

### <a name="insight-and-analytics"></a>Insikt och analys
I mitten av [Azure Monitor loggar](../../log-analytics/log-analytics-queries.md) är databasen, som är värd Azure.

![Insikts- och analysdiagram](./media/threat-detection/azure-threat-detection-fig4.png)

Du samlar in data i databasen från anslutna källor genom att konfigurera datakällor och lägga till lösningar i din prenumeration.

![Instrumentpanelen för Azure Monitor loggar](./media/threat-detection/azure-threat-detection-fig5.png)

Datakällor och lösningar skapar var och en separata posttyper med sin egen uppsättning egenskaper, men du kan fortfarande analysera dem tillsammans i frågor till databasen. Du kan använda samma verktyg och metoder för att arbeta med en mängd olika data som samlas in av olika källor.


Det mesta av din interaktion med Azure Monitor-loggar är via Azure-portalen, som körs i alla webbläsare och ger dig tillgång till konfigurationsinställningar och flera verktyg för att analysera och agera på insamlade data. Från portalen kan du använda:
* [Loggsökningar](../../log-analytics/log-analytics-queries.md) där du konstruerar frågor för att analysera insamlade data.
* [Instrumentpaneler](../../azure-monitor/learn/tutorial-logs-dashboards.md), som du kan anpassa med grafiska vyer av dina mest värdefulla sökningar.
* [Lösningar](../../monitoring/monitoring-solutions.md), som ger ytterligare funktioner och analysverktyg.

![Analysverktyg](./media/threat-detection/azure-threat-detection-fig6.png)

Lösningar lägger till funktioner i Azure Monitor-loggar. De körs främst i molnet och tillhandahåller analys av data som samlas in i logganalysarkivet. Lösningar kan också definiera nya posttyper som ska samlas in som kan analyseras med loggsökningar eller med hjälp av ytterligare ett användargränssnitt som lösningen tillhandahåller i instrumentpanelen för logganalys.

Instrumentpanelen Säkerhet och granskning är ett exempel på dessa typer av lösningar.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatisering och kontroll: Varning om säkerhetskonfiguration driver

Azure Automation automatiserar administrativa processer med runbooks som baseras på PowerShell och körs i molnet. Runbooks kan också köras på en server i ditt lokala datacenter för att hantera lokala resurser. Azure Automation tillhandahåller konfigurationshantering med PowerShell Desired State Configuration (DSC).

![Azure Automation-diagram](./media/threat-detection/azure-threat-detection-fig7.png)

Du kan skapa och hantera DSC-resurser som finns i Azure och tillämpa dem på moln- och lokala system. På så sätt kan du definiera och automatiskt genomdriva deras konfiguration eller få rapporter på drift för att säkerställa att säkerhetskonfigurationer förblir inom principen.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper till att skydda dina Azure-resurser. Det ger integrerad säkerhetsövervakning och principhantering över dina Azure-prenumerationer. Inom tjänsten kan du definiera principer mot både dina Azure-prenumerationer och [resursgrupper](../../azure-resource-manager/management/manage-resources-portal.md) för större granularitet.

![Azure Security Center-diagram](./media/threat-detection/azure-threat-detection-fig8.png)

Microsofts säkerhetsforskare söker hela tiden efter hot. De har tillgång till en omfattande uppsättning telemetri från Microsofts globala närvaro i molnet och i lokala infrastrukturer. Med den här omfattande och mångfaldiga samlingen data kan Microsoft identifiera nya angreppsmönster och trender i lokala konsument- och företagsprodukter, och i onlinetjänster.

Security Center kan därför snabbt uppdatera sina identifieringsalgoritmer när angripare släpper nya och alltmer sofistikerade bedrifter. Den här metoden hjälper dig att hålla jämna steg med en snabbrörlig hotmiljö.

![Identifiering av hot i Säkerhetscenter](./media/threat-detection/azure-threat-detection-fig9.jpg)

Hotidentifieringen i Security Center sker genom automatisk insamling av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Den analyserar denna information, korrelera information från flera källor, för att identifiera hot.

Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas.

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Genombrott inom stordata- och [maskininlärningsteknik](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) används för att utvärdera händelser i hela molnstrukturen. Avancerad analys kan upptäcka hot som skulle vara omöjliga att identifiera genom manuella metoder och förutsäga utvecklingen av attacker. Dessa typer av säkerhetsanalyser beskrivs i nästa avsnitt.

### <a name="threat-intelligence"></a>Hotinformation

Microsoft har tillgång till en enorm mängd global hotinformation.

Telemetri flödar in från flera källor, till exempel Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) och Microsoft Security Response Center (MSRC).

![Hot intelligens resultat](./media/threat-detection/azure-threat-detection-fig10.jpg)

Forskare får också information om hotinformation som delas mellan stora molntjänstleverantörer, och de prenumererar på hotinformationsflöden från tredje part. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer. Några exempel är:

-   **Utnyttja kraften i maskininlärning:** Azure Security Center har åtkomst till en stor mängd data om molnnätverksaktivitet, som kan användas för att identifiera hot som är inriktade på dina Azure-distributioner.

-   **Brute force detection**: Machine learning används för att skapa ett historiskt mönster av fjärråtkomstförsök, vilket gör det möjligt att upptäcka brute force-attacker mot Secure Shell (SSH), Remote Desktop Protocol (RDP) och SQL-portar.

-   **Utgående DDoS och botnet-identifiering:** Ett vanligt mål för attacker som riktar sig till molnresurser är att använda beräkningskraften för dessa resurser för att utföra andra attacker.

-   **Nya beteendeanalysservrar och virtuella datorer:** När en server eller virtuell dator har komprometterats använder angripare en mängd olika tekniker för att köra skadlig kod på det systemet samtidigt som de undviker identifiering, säkerställer beständighet och undanröjer säkerhetskontroller.

-   **Hotidentifiering av Azure SQL-databas:** Hotidentifiering för Azure SQL Database, som identifierar avvikande databasaktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

### <a name="behavioral-analytics"></a>Beteendeanalys

Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder,

![Undersökningsresultat för beteendeanalys](./media/threat-detection/azure-threat-detection-fig11.jpg)

Mönstren bestäms också genom noggrann analys av skadliga beteenden av expertanalytiker. Azure Security Center kan använda beteendeanalys för att identifiera komprometterade resurser baserat på analys av loggar för virtuella datorer, loggar för virtuella nätverksenheter, infrastrukturloggar, kraschdumpar och andra källor.

Dessutom är mönster korrelerade med andra signaler för att kontrollera om det finns stödbevis för en omfattande kampanj. Den här korrelationen gör det möjligt att identifiera händelser som matchar fastställda hotindikatorer.

Några exempel är:
-   **Misstänkt körning av processer**: Angripare använder flera metoder för att köra skadlig programvara utan identifiering. En angripare kan till exempel ge skadlig kod samma namn som legitima systemfiler, men placera dessa filer på en annan plats, använda ett namn som liknar ett godartade fil eller maskera filens sanna tillägg. Security Center-modeller bearbetar beteenden och övervakar processkörningar för att identifiera extremvärden som dessa.

-   **Dolda malware och försök utnyttjande:** Sofistikerad skadlig kod kan kringgå traditionella antimalware produkter genom att antingen aldrig skriva till disk eller kryptera programvarukomponenter som lagras på disk. Sådan skadlig kod kan dock identifieras med hjälp av minnesanalys, eftersom skadlig kod måste lämna spår i minnet för att fungera. När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen. Genom att analysera minnet i kraschdumpen kan Azure Security Center identifiera tekniker som används för att utnyttja sårbarheter i programvara, komma åt konfidentiella data och i smyg finnas kvar i en komprometterade dator utan att påverka prestanda för din Maskin.

-   **Lateral rörelse och intern spaning:** För att framhärda i ett komprometterat nätverk och lokalisera och skörda värdefulla data, angripare försöker ofta att flytta i sidled från den komprometterade maskinen till andra inom samma nätverk. Security Center övervakar process- och inloggningsaktiviteter för att identifiera försök att expandera en angripares fotfäste i nätverket, till exempel fjärrkörning av kommandon, nätverksavplaning och kontouppräkning.

-   **Skadliga PowerShell-skript:** PowerShell kan användas av angripare för att köra skadlig kod på virtuella måldatorer för olika ändamål. Security Center inspekterar PowerShell-aktivitet för att hitta tecken på misstänkt aktivitet.

-   **Utgående attacker**: Angripare attackerar ofta resurser i molnet med avsikten att använda dessa resurser för att bygga upp fler attacker. Komprometterade virtuella datorer, till exempel, kan användas för att starta brute force-attacker mot andra virtuella datorer, skicka skräppost eller skanna öppna portar och andra enheter på Internet. Genom att använda maskininlärning för nätverkstrafiken kan Security Center upptäcka avvikande utgående nätverkskommunikation. När skräppost upptäcks korrelerar Security Center också ovanlig e-posttrafik med intelligens från Office 365 för att avgöra om posten sannolikt är skändlig eller resultatet av en legitim e-postkampanj.

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Security Center använder också avvikelseidentifiering för att identifiera hot. Till skillnad mot beteendeanalyser (som är beroende av kända mönster som härleds från stora datamängder) är avvikelseidentifiering mer ”anpassad” och utgår från standarder som är specifika för dina distributioner. Maskininlärning används för att bestämma normal aktivitet för dina distributioner och sedan genereras regler för att definiera avvikande villkor som kan representera en säkerhetshändelse. Här är ett exempel:

-   **Inkommande RDP / SSH brute force attacker:** Dina distributioner kan ha upptagen virtuella datorer med många inloggningar varje dag och andra virtuella datorer som har få, om någon, inloggningar. Azure Security Center kan bestämma grundläggande inloggningsaktivitet för dessa virtuella datorer och använda maskininlärning för att definiera runt de normala inloggningsaktiviteterna. Om det finns någon avvikelse med baslinjen som definierats för inloggningsrelaterade egenskaper kan en avisering genereras. Maskininlärningen avgör vad som är viktigt.

### <a name="continuous-threat-intelligence-monitoring"></a>Kontinuerlig övervakning av hotinformation

Azure Security Center arbetar med säkerhetsforskning och datavetenskap team över hela världen som kontinuerligt övervakar förändringar i hotlandskapet. Bland annat kan följande projekt nämnas:

-   **Övervakning av hotinformation:** Hotinformation omfattar mekanismer, indikatorer, konsekvenser och användbara råd om befintliga eller framväxande hot. Den här informationen delas i säkerhetsgruppen och Microsoft övervakar kontinuerligt hotinformationsflöden från interna och externa källor.

-   **Signaldelning**: Insikter från säkerhetsteam i den breda Microsoft-portföljen med molntjänster och lokala tjänster, servrar och klientslutpunktsenheter delas och analyseras.

-   **Microsofts säkerhetsspecialister**: Kontinuerligt samarbete med team över hela Microsoft som arbetar inom specialiserade säkerhetsområden, till exempel kriminalteknik och identifiering av webbattacker.

-   **Identifieringsjustering:** Algoritmer körs mot verkliga kunddatauppsättningar och säkerhetsforskare arbetar med kunder för att validera resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade insatser kulminerar i nya och förbättrade upptäckter, som du kan dra nytta av direkt. Det finns ingen åtgärd för dig att vidta.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Avancerade hotidentifieringsfunktioner: Andra Azure-tjänster

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuella datorer: Microsoft antimalware

[Microsoft antimalware](antimalware.md) för Azure är en lösning med en agent för program och klientmiljöer som utformats för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på behoven hos dina programarbetsbelastningar, med antingen grundläggande säker standard eller avancerad anpassad konfiguration, inklusive övervakning mot skadlig kod. Azure antimalware är ett säkerhetsalternativ för virtuella Azure-datorer som installeras automatiskt på alla virtuella Azure PaaS-datorer.

#### <a name="microsoft-antimalware-core-features"></a>Microsofts kärnfunktioner mot skadlig kod

Här är funktionerna i Azure som distribuerar och aktiverar Microsofts skadlig kod för dina program:

-   **Realtidsskydd**: Övervakar aktivitet i molntjänster och virtuella datorer för att upptäcka och blockera körning av skadlig kod.

-   **Schemalagd skanning**: Utför regelbundet riktad skanning för att upptäcka skadlig kod, inklusive aktivt program som körs.

-   **Malware reparation:** Fungerar automatiskt på upptäckt skadlig kod, till exempel ta bort eller karantän skadliga filer och rensa upp skadliga registerposter.

-   **Signaturuppdateringar:** Installerar automatiskt de senaste skyddssignaturerna (virusdefinitioner) för att säkerställa att skyddet är uppdaterat på en förutbestämd frekvens.

-   **Uppdateringar av antimalware-motorn**: Uppdaterar automatiskt Microsoft Antimalware Engine.

-   **Uppdateringar av plattformen mot skadlig kod**: Uppdaterar automatiskt Microsofts plattform mot skadlig kod.

-   **Aktivt skydd**: Rapporterar telemetrimetadata om identifierade hot och misstänkta resurser till Microsoft Azure för att säkerställa snabba svar på det växande hotlandskapet, vilket möjliggör synkroniseringsleverans i realtid via Microsofts aktiva skyddssystem.

-   **Exempelrapportering**: Innehåller och rapporterar exempel till Microsofts antimalware-tjänst för att förfina tjänsten och aktivera felsökning.

-   **Undantag**: Tillåter program- och tjänstadministratörer att konfigurera vissa filer, processer och enheter för uteslutning från skydd och skanning av prestanda och andra orsaker.

-   **Händelsesamling mot skadlig kod**: Registrerar hälsotillståndet mot skadlig programvara, misstänkta aktiviteter och åtgärder för reparation som vidtas i operativsystemets händelselogg och samlar in dem i kundens Azure-lagringskonto.

### <a name="azure-sql-database-threat-detection"></a>Hotidentifiering av Azure SQL-databas

[Hotidentifiering av Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) är en ny säkerhetsinformationsfunktion som är inbyggd i Azure SQL Database-tjänsten. Azure SQL Database Threat Detection arbetar dygnet runt för att lära sig, profilera och identifiera avvikande databasaktiviteter och identifierar potentiella hot mot databasen.

Säkerhetstjänstemän eller andra utsedda administratörer kan få ett omedelbart meddelande om misstänkta databasaktiviteter när de inträffar. Varje meddelande innehåller information om den misstänkta aktiviteten och rekommenderar hur du ytterligare undersöker och minskar hotet.

Azure SQL Database Threat Detection identifierar potentiella sårbarheter och SQL-injektionsattacker och avvikande databasåtkomstmönster.

När användarna har fått ett e-postmeddelande om hotidentifiering kan de navigera och visa relevanta granskningsposter via en djuplänk i e-postmeddelandet. Länken öppnar en granskningsvisare eller en förkonfigurerad granskning av Excel-mall som visar relevanta granskningsposter runt tidpunkten för den misstänkta händelsen, enligt följande:

-   Granska lagring för databasen/servern med avvikande databasaktiviteter.

-   Relevant granskningslagringstabell som användes vid tidpunkten för händelsen för att skriva granskningsloggen.

-   Granskningsposter för timmen omedelbart efter händelsens förekomst.

-   Granska poster med ett liknande händelse-ID vid tidpunkten för händelsen (valfritt för vissa detektorer).

SQL Database-hotdetektorer använder någon av följande detektionsmetoder:

-   **Deterministisk identifiering**: Identifierar misstänkta mönster (regler baserade) i SQL-klientfrågor som matchar kända attacker. Denna metod har hög upptäckt och låg falskt positiva, men begränsad täckning eftersom det faller inom kategorin "atomskydd."

-   **Beteendeidentifiering**: Identifierar avvikande aktivitet, vilket är onormalt beteende i databasen som inte har setts under de senaste 30 dagarna. Exempel på SQL-klientens avvikande aktivitet kan vara en topp av misslyckade inloggningar eller frågor, en hög mängd data som extraheras, ovanliga kanoniska frågor eller okända IP-adresser som används för att komma åt databasen.

### <a name="application-gateway-web-application-firewall"></a>Brandvägg för webbprogram för programgateway

[Waf (Web Application Firewall)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) är en funktion i [Azure Application Gateway](../../application-gateway/application-gateway-web-application-firewall-overview.md) som skyddar webbprogram som använder en programgateway för [standardfunktioner för programleveranskontroll.](https://kemptechnologies.com/in/application-delivery-controllers) Brandvägg för webbprogram gör detta genom att skydda dem mot de flesta av [OWASP-topp 10-vanliga webbsäkerhetsproblem (Open Web Application Security Project).](https://www.owasp.org/index.php/Top_10_2010-Main)

![Brandväggsdiagram för programgateway-webbprogram](./media/threat-detection/azure-threat-detection-fig13.png)

Skydd inkluderar:

-   SQL-injektionsskydd.

-   Skydd för skript över flera webbplatser.

-   Gemensamt skydd för webbattacker, till exempel kommandoinjektion, HTTP-begäransmuggling, HTTP-svarsdelning och attack mot fjärrfilinkludering.

-   Skydd mot HTTP-protokollöverträdelser.

-   Skydd mot HTTP-protokollavvikelser, till exempel saknad värdanvändaragent och acceptera rubriker.

-   Förebyggande mot robotar, sökrobotar och skannrar.

-   Identifiering av vanliga programmisskonfigurationer (det vill säga Apache, IIS och så vidare).

Konfigurera WAF på programgatewayen ger följande fördelar:

-   Skyddar webbprogrammet från webbsår och attacker utan att backend-koden ändras.

-   Skyddar flera webbprogram samtidigt bakom en programgateway. En programgateway stöder värd för upp till 20 webbplatser.

-   Övervakar webbprogram mot attacker med hjälp av realtidsrapporter som genereras av WAF-loggar för programgateway.

-   Hjälper till att uppfylla efterlevnadskraven. Vissa efterlevnadskontroller kräver att alla internetvända slutpunkter skyddas av en WAF-lösning.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API för avvikelseidentifiering: Byggt med Azure Machine Learning

Api:et för avvikelseidentifiering är ett API som är användbart för att identifiera en mängd avvikande mönster i tidsseriedata. API:et tilldelar varje datapunkt i tidsserien en avvikelsepoäng, som kan användas för att generera aviseringar, övervaka via instrumentpaneler eller ansluta till dina biljettsystem.

[Api:et för avvikelseidentifiering](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) kan identifiera följande typer av avvikelser i tidsseriedata:

-   **Spikar och dips**: När du övervakar antalet inloggningsfel till en tjänst eller antal kassor på en e-handelsplats kan ovanliga toppar eller dips indikera säkerhetsattacker eller avbrott i tjänsten.

-   **Positiva och negativa trender:** När du övervakar minnesanvändningen i datorer indikerar krympande ledigt minne en potentiell minnesläcka. För övervakning av tjänstens kölängd kan en ihållande uppåtgående trend indikera ett underliggande programvaruproblem.

-   **Nivåändringar och ändringar i dynamiskt värdeintervall:** Nivåändringar i svarstider för en tjänst efter en tjänstuppgradering eller lägre nivåer av undantag efter uppgradering kan vara intressant att övervaka.

Det maskininlärningsbaserade API:et gör det möjligt att:

-   **Flexibel och robust identifiering:** Modellerna för avvikelseidentifiering gör det möjligt för användare att konfigurera känslighetsinställningar och identifiera avvikelser bland säsongs- och icke-säsongsdatauppsättningar. Användare kan justera avvikelseidentifieringsmodellen för att göra identifierings-API:et mindre eller mer känsligt enligt deras behov. Detta skulle innebära att upptäcka de mindre eller mer synliga avvikelserna i data med och utan säsongsmönster.

-   **Skalbar och snabb identifiering:** Det traditionella sättet att övervaka med nuvarande tröskelvärden som fastställts av experternas domänkunskap är kostsamma och inte skalbara för miljontals dynamiskt föränderliga datauppsättningar. Avvikelseidentifieringsmodellerna i det här API:et lärs in och modeller justeras automatiskt från både historiska data och realtidsdata.

-   **Proaktiv och användbar identifiering:** Långsam trend- och nivåändringsidentifiering kan användas för tidig avvikelseidentifiering. De tidiga onormala signaler som detekteras kan användas för att styra människor att undersöka och agera på problemområdena. Dessutom kan rotorsaksanalysmodeller och varningsverktyg utvecklas ovanpå den här API-tjänsten för avvikelseidentifiering.

API:et för avvikelseidentifiering är en effektiv och effektiv lösning för ett brett spektrum av scenarier, till exempel tjänsthälsokontroll och KPI-övervakning, IoT, prestandaövervakning och övervakning av nätverkstrafik. Här är några populära scenarier där det här API:et kan vara användbart:

- IT-avdelningar behöver verktyg för att spåra händelser, felkod, användningslogg och prestanda (CPU, minne och så vidare) i tid.

-   Online handelswebbplatser vill spåra kundaktiviteter, sidvisningar, klick och så vidare.

-   Allmännyttiga företag vill spåra förbrukningen av vatten, gas, el och andra resurser.

-   Anläggning eller fastighetsförvaltning tjänster vill övervaka temperatur, fukt, trafik, och så vidare.

-   IoT/manufacturers vill använda sensordata i tidsserier för att övervaka arbetsflöde, kvalitet och så vidare.

-   Tjänsteleverantörer, till exempel callcenter, måste övervaka trend för efterfrågan på tjänster, incidentvolym, väntkölängd och så vidare.

-   Affärsanalysgrupper vill övervaka affärs-KPI:er (till exempel försäljningsvolym, kundsentiment eller prissättning) onormala rörelser i realtid.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) är en viktig komponent i Microsoft Cloud Security stacken. Det är en omfattande lösning som kan hjälpa din organisation när du flyttar för att dra full nytta av löftet om molnprogram. Det håller dig i kontroll, genom förbättrad insyn i aktivitet. Det hjälper också till att bättre skydda viktiga data i molnappar.

Med verktyg för att upptäcka Shadow IT, utvärdera risker, genomdriva principer, undersöka aktiviteter och stoppa hot kan organisationen migrera till molnet på ett säkrare sätt samtidigt som ni behåller kontrollen över viktiga data.

| | |
|---|---|
| Utforska | Upptäck Shadow IT med Cloud App Security. Få insyn genom att identifiera appar, aktiviteter, användare, data och filer i molnmiljön. Identifiera tredjepartsappar som är anslutna till molnet.|
|Undersök
 | Undersök molnapparna med hjälp av datautredningsverktyg för att analysera riskfyllda appar, specifika användare och filer i nätverket. Hitta mönster i de data som samlas in från molnet. Generera rapporter för att övervaka molnet. |
| Kontroll | Minska riskerna genom att skapa principer och varningar som ger dig fullständig kontroll över molntrafiken i nätverket. Använd Cloud App Security för att migrera användarna till säkra sanktionerade molnappar. |
| Skydda | Använd Cloud App Security för att sanktionera eller förbjuda program, genomdriva dataförlustskydd, kontrollera behörigheter och delning och generera anpassade rapporter och aviseringar. |
| Kontroll | Minska riskerna genom att skapa principer och varningar som ger dig fullständig kontroll över molntrafiken i nätverket. Använd Cloud App Security för att migrera användarna till säkra sanktionerade molnappar. |
| | |


![Säkerhetsdiagram för molnappar](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integrerar synligheten med molnet genom att:

-   Använda Cloud Discovery för att kartlägga och identifiera din molnmiljö och de molnappar som din organisation använder.

-   Sanktionera och förbjuda appar i molnet.

-   Använda lätt distribuera appkopplingar som utnyttjar provider-API:er, för synlighet och styrning av appar som du ansluter till.

-   Hjälper dig att ha kontinuerlig kontroll genom att ställa in och sedan kontinuerligt finjustera, principer.

När du samlar in data från dessa källor kör Cloud App Security sofistikerad analys på den. Du varnas omedelbart om avvikande aktiviteter och får djupgående insyn i molnmiljön. Du kan konfigurera en princip i Cloud App Security och använda den för att skydda allt innehåll i molnmiljön.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Avancerade funktioner för avancerad hotidentifiering från tredje part via Azure Marketplace

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brandvägg för webbprogram kontrollerar inkommande webbtrafik och blockerar SQL-injektioner, skript över flera webbplatser, uppladdningar av skadlig programvara, DDoS-programattacker och andra attacker som är riktade mot dina webbprogram. Den granskar också svaren från backend-webbservrar för dataförlust förebyggande (DLP). Den integrerade åtkomstkontrollmotorn gör det möjligt för administratörer att skapa detaljerade åtkomstkontrollprinciper för autentisering, auktorisering och redovisning (AAA), vilket ger organisationer stark autentisering och användarkontroll.

Brandvägg för webbprogram ger följande fördelar:

-   Identifierar och blockerar SQL-injektioner, skript på flera webbplatser, uppladdningar av skadlig programvara, DDoS-program eller andra attacker mot ditt program.

-   Autentisering och åtkomstkontroll.

-   Söker igenom utgående trafik för att identifiera känsliga data och kan maskera eller blockera informationen från att läcka ut.

-   Påskyndar leveransen av webbprograminnehåll med hjälp av funktioner som cachelagring, komprimering och andra trafikoptimeringar.

Exempel på brandväggar för webbprogram som är tillgängliga på Azure Marketplace finns i [Barracuda WAF, Brocade virtual web application firewall (vWAF), Imperva SecureSphere och ThreatSTOP IP firewall](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Nästa steg

- [Svara på dagens hot](../../security-center/security-center-alerts-overview.md#respond-threats): Hjälper till att identifiera aktiva hot som riktar sig till dina Azure-resurser och ger de insikter du behöver för att svara snabbt.

- [Hotidentifiering av Azure SQL Database:](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)Hjälper dig att lösa dina problem med potentiella hot mot dina databaser.
