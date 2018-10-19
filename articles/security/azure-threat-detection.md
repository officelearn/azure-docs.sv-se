---
title: Azure avancerad hotidentifiering | Microsoft Docs
description: Läs mer om Azure AD Identity Protection och dess funktioner.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 33a9cc0a7b3d18004e19d73a0d9b91bf33cdb055
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408837"
---
# <a name="azure-advanced-threat-detection"></a>Azure avancerad hotidentifiering

Azure erbjuder inbyggda funktioner för identifiering av Avancerat via tjänster som Azure Active Directory (AD Azure), Azure Log Analytics och Azure Security Center. Den här samlingen av tjänster och funktioner som ger ett snabbt och enkelt sätt att förstå vad som händer i din Azure-distributioner.

Azure tillhandahåller en mängd olika alternativ för att konfigurera och anpassa säkerheten för att uppfylla kraven för din appdistributioner. Den här artikeln beskriver hur du uppfyller dessa krav.

## <a name="azure-active-directory-identity-protection"></a>Identitetsskydd för Azure Active Directory

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är en [Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) edition-funktion som innehåller en översikt över riskhändelser och potentiella problem som kan påverka din organisations identiteter. Identity Protection använder befintliga Azure AD funktioner för identifiering av avvikelser som är tillgängliga via [Azure AD avvikande aktivitetsrapporter](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports), och introducerar nya typer av riskhändelser som kan identifiera avvikelser i realtid.

![Azure AD Identity Protection-diagram](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Identity Protection använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera avvikelser och riskhändelser som kan tyda på att en identitet har komprometterats. Med dessa data genererar Identity Protection rapporter och aviseringar så att du kan undersöka dessa riskhändelser och vidta lämpliga avhjälpande eller minskning åtgärd.

Azure Active Directory Identity Protection är mer än en övervakning och rapportering verktyget. Baserat på riskhändelser, beräknar identitetsskydd en risknivån för varje användare, så att du kan konfigurera riskbaserade principer för att automatiskt skydda identiteter för din organisation.

Dessa riskbaserade principer, utöver övriga [villkorlig åtkomstkontroller](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) som tillhandahålls av Azure Active Directory och [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), kan automatiskt blockera eller erbjuder anpassningsbara åtgärder som Inkludera lösenordsåterställning och multifaktorautentisering tillämpning.

### <a name="identity-protection-capabilities"></a>Identity Protection-funktioner

Azure Active Directory Identity Protection är mer än en övervakning och rapportering verktyget. För att skydda din organisations identiteter kan konfigurera du riskbaserade principer som automatiskt svarar på identifierade problem när en angiven risknivå har uppnåtts. Dessa principer, utöver andra kontroller för villkorlig åtkomst som tillhandahålls av Azure Active Directory och EMS, kan automatiskt blockera eller initiera anpassningsbar åtgärder inklusive lösenordsåterställning och multifaktorautentisering tvingande.

Exempel på några av de sätt som Azure Identity Protection kan hjälpa att skydda dina konton och identiteter omfattar:

[Identifiering av riskhändelser och riskfyllda konton](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Identifiera sex typer av riskhändelser med maskininlärning och heuristisk regler.
-   Beräkna risknivåer för användaren.
-   Ange anpassade rekommendationer för att förbättra övergripande säkerhetspositionen genom att markera sårbarheter.

[Undersöka riskhändelser](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Skicka meddelanden för riskhändelser.
-   Undersök riskhändelser med relevant och sammanhangsberoende information.
-   Ange grundläggande arbetsflöden för att spåra undersökningar.
-   Ger enkel åtkomst till åtgärder som t.ex. återställning av lösenord.

[Principer för riskbaserad, villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Minimera riskfyllda inloggningar genom att blockera inloggningar eller att kräva multifaktorautentisering utmaningar.
-   Blockera eller skydda riskfyllda användarkonton.
-   Kräv att användare att registrera sig för multifaktorautentisering.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Med [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), du kan hantera, kontrollera och övervaka åtkomst inom din organisation. Den här funktionen ger åtkomst till resurser i Azure AD och andra Microsoft-onlinetjänster, till exempel Office 365 eller Microsoft Intune.

![Azure AD Privileged Identity Management-diagram](./media/azure-threat-detection/azure-threat-detection-fig2.png)

PIM hjälper dig att:

-   Få aviseringar och rapporter om Azure AD-administratörer och just-in-time (JIT) administrativ åtkomst till Microsofts onlinetjänster som Office 365 och Intune.

-   Få rapporter om administratören åtkomsthistorik och ändringar i administratör tilldelningar.

-   Få aviseringar om åtkomst till en privilegierad roll.

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är en Microsoft molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur. Eftersom Log Analytics har implementerats som en molnbaserad tjänst kan kan du ha den igång snabbt med minsta möjliga investering i infrastrukturtjänster. Nya säkerhetsfunktioner levereras automatiskt, sparar löpande underhåll och Uppgraderingskostnader.

Förutom att tillhandahålla värdefulla tjänster på egen hand, Log Analytics kan integreras med System Center-komponenter, till exempel [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), för att utöka dina befintliga säkerhets hanteringsinvestering i molnet. System Center och Log Analytics kan hjälpa dig för att skapa en fullständig hybridhantering upplevelse.

### <a name="holistic-security-and-compliance-posture"></a>Heltäckande säkerhets- och efterlevnadsstatus

Den [Log Analytics säkerhet och granskning instrumentpanelen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) ger en heltäckande översikt över din organisations IT-säkerhetsstatus, med inbyggda sökfrågor för viktiga problem som kräver din uppmärksamhet. Instrumentpanelen för säkerhet och granskning är startsidan för allt som rör säkerhet i Log Analytics. Den innehåller en översikt över säkerhetsstatusen för dina datorer. Du kan också visa alla händelser från de senaste 24 timmarna, 7 dagar eller anpassad tidsram.

Log Analytics kan du snabbt och lätt att förstå den övergripande säkerhetstillståndet för alla miljöer, allt inom ramen för IT-avdelningen, inklusive programvara som uppdatering av utvärdering, utvärdering av program mot skadlig kod och konfigurationsbaslinjer. Loggdata för säkerhet är allmänt tillgänglig för att effektivisera processer för säkerhets- och granskning.

![Instrumentpanelen för Log Analytics säkerhet och granskning](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

Log Analytics säkerhet och granskning instrumentpanelen är uppdelad i fyra huvudkategorier:

-   **Säkerhetsdomäner**: kan du ytterligare utforska säkerhetsposter över tid, åtkomst skadlig kod utvärderingar, av kontroll av uppdateringar, visa nätverkssäkerhet, identitet, och komma åt information; Visa datorer med säkerhetshändelser; och snabbt komma åt Azure Instrumentpanelen för Security Center.

-   **Anmärkningsvärda problem**: gör att du snabbt identifiera antalet aktiva problem och allvarlighetsgraden på problemen.

-   **Identifieringar (förhandsversion)**: kan du identifiera angreppsmönster genom att visa säkerhetsaviseringar när de inträffar mot dina resurser.

-   **Hotinformation**: kan du identifiera angreppsmönster genom att visa det totala antalet servrar med utgående skadlig IP-trafik, typen av hot och en karta över platser för IP-adresser.

-   **Vanliga säkerhetsfrågor**: Visar en lista över de vanligaste säkerhetsfrågorna som du kan använda för att övervaka din miljö. När du väljer en fråga, Sök-rutan öppnas och visar resultatet för frågan.

### <a name="insight-and-analytics"></a>Insikter och analys
I mitten av [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) är lagringsplatsen, som är värd för Azure.

![Diagrammet insikter och analys](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Du samla in data till databasen från anslutna källor genom att konfigurera datakällor och lägga till lösningar i din prenumeration.

![Log Analytics-instrumentpanelen ](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Datakällor och lösningar skapar olika posttyper med sin egen uppsättning egenskaper, men du kan fortfarande analysera dem tillsammans i förfrågningar till databasen. Du kan använda samma verktyg och metoder för att arbeta med en mängd olika data som samlas in av olika källor.


De flesta interaktionen med Log Analytics är via Azure-portalen, som körs i alla webbläsare och ger åtkomst till konfigurationsinställningar och flera olika verktyg för att analysera och agera utifrån insamlade data. Från portalen kan använda du:
* [Loggsökningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) där du skapar förfrågningar för att analysera insamlade data.
* [Instrumentpaneler](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), som du kan anpassa med grafiska vyer över dina mest värdefulla sökningar.
* [Lösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), som ger ytterligare funktioner och analysverktyg.

![Analysverktyg](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Lösningar lägger till funktioner i Log Analytics. De främst körs i molnet och ger analys av data som samlas in i Log Analytics-databasen. Lösningar kan också definiera nya typer av poster som ska samlas in som kan analyseras med loggsökningar eller genom att använda en ytterligare användargränssnitt som lösningen tillhandahåller i Log Analytics-instrumentpanelen.

Instrumentpanelen för säkerhet och granskning är ett exempel på dessa typer av lösningar.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatisering och kontroll: avisering på säkerhetskonfiguration drifts

Azure Automation automatiserar administrativa processer med runbooks som är baserade på PowerShell och körs i molnet. Runbooks kan också köras på en server i ditt lokala datacenter för att hantera lokala resurser. Azure Automation tillhandahåller konfigurationshantering med PowerShell Desired State Configuration (DSC).

![Azure Automation-diagram](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Du kan skapa och hantera DSC-resurser som finns i Azure och koppla dem till molnet och lokala system. Genom att göra så du kan definiera och automatiskt genomdriva deras konfiguration eller få rapporter om drift för att säkerställa säkerheten konfigurationer finns kvar i principen.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper dig att skydda dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer. I tjänsten kan du definiera principer mot både dina Azure-prenumerationer och [resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) större precision.

![Azure Security Center-diagram](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Microsofts säkerhetsforskare söker hela tiden efter hot. De har tillgång till en omfattande uppsättning telemetri från Microsofts globala närvaro i molnet och i lokala infrastrukturer. Med den här omfattande och mångfaldiga samlingen data kan Microsoft identifiera nya angreppsmönster och trender i lokala konsument- och företagsprodukter, och i onlinetjänster.

Security Center kan därför snabbt uppdatera sin eftersom angripare hittar nya och alltmer sofistikerade angrepp. Den här metoden hjälper dig att hålla jämna steg med en snabbrörliga hotmiljö.

![Identifiering av hot i Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Hotidentifieringen i Security Center sker genom automatisk insamling av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Den analyserar den här informationen kan korrelera information från flera källor för att identifiera hot.

Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas.

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Genombrott i stordata och [maskininlärning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tekniker som används för att utvärdera händelser i hela molninfrastrukturen. Avancerad analys kan du upptäcka hot som skulle vara omöjliga att identifiera via manuella metoder och att förutsäga utvecklingen av nya attacker. Dessa säkerhetstyper analytics beskrivs i nästa avsnitt.

### <a name="threat-intelligence"></a>Hotinformation

Microsoft har åtkomst till stora mängder global hotinformation.

Telemetri flödar in från flera källor, till exempel Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) och Microsoft Security Response Center (MSRC).

![Threat intelligence resultat](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Forskare får även hotinformation som delas med större leverantörer av molntjänster och de prenumerera hotinformation från tredje part. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer. Några exempel är:

-   **Utnyttjar kraften i maskininlärning**: Azure Security Center har åtkomst till en stor mängd data om Molnets nätverksaktivitet, som kan användas för att identifiera hot mot din Azure-distributioner.

-   **Nyckelsökningsangrepp**: maskininlärning används för att skapa ett historiska mönster för fjärråtkomst försök, vilket innebär att det kan identifiera brute force-attacker mot SSH (Secure Shell), Remote Desktop Protocol (RDP) och SQL-portar.

-   **Utgående DDoS och botnet-identifiering**: ett vanliga mål för attacker som är riktade till molnresurser är att använda beräkningskraft på dessa resurser för att utföra andra attacker.

-   **Ny beteendeanalys servrar och virtuella datorer**: när en server eller virtuell dator äventyras, angripare använder en mängd olika metoder för att köra skadlig kod på systemet undvika identifiering, säkerställer persistence och lagringsadministratörer säkerhetsåtgärder.

-   **Azure SQL Database Threat Detection**: Hotidentifiering för Azure SQL-databas som identifierar avvikande databasaktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser.

### <a name="behavioral-analytics"></a>Beteendeanalys

Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder,

![Beteendeanalys resultat](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

Mönstren bestäms också genom noggrann analys av skadliga beteenden av expertanalytiker. Azure Security Center kan använda beteendeanalyser för att identifiera resurser som har komprometterats baserat på analyser av virtuella datorer, loggar, virtuella nätverksenheter, infrastrukturloggar, kraschdumpfiler och andra källor.

Dessutom korreleras mönster med andra tecken för att hitta bevis på utbredda. Den här korrelationen gör det möjligt att identifiera händelser som matchar fastställda hotindikatorer.

Några exempel är:
-   **Misstänkt körning av processer**: Angripare använder flera metoder för att köra skadlig programvara utan identifiering. Exempelvis kan kan en angripare ge skadlig kod med samma namn som legitima systemfiler men placera filerna på en annan plats, använda ett namn som liknar den på en oskadlig fil eller maskera filens egentliga filnamnstillägg. Security Center-modellerna bearbetar beteenden och körningar för Övervakare för processen att identifiera avvikelser som dessa.

-   **Dold skadlig kod och försök**: avancerad skadlig kod kan ta sig förbi traditionella antivirusprodukter genom att aldrig skriva till disk eller kryptera programvarukomponenter som lagras på disk. Sådan skadlig kod kan dock identifieras med minnesanalyser eftersom den skadliga koden måste lämna spår i minnet ska fungera. När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen. Genom att analysera minnet i kraschdumpen kan Azure Security Center identifiera tekniker som utnyttjar sårbarheter i programvara, kommer åt känsliga data och gömda på en komprometterad dator utan att påverka prestanda för din datorn.

-   **Lateral förflyttning och intern rekognosering**: Om du vill spara i ett komprometterat nätverk och leta upp och samla värdefull information, försöker angripare ofta lateralt från den komprometterade datorn till andra i samma nätverk. Security Center övervakar process- och inloggningsaktiviteter för att identifiera försök att expandera en angripares fäste i nätverket, till exempel fjärrkörning, nätverksavsökning och kontouppräkning.

-   **Skadliga PowerShell-skript**: PowerShell kan användas av angripare för att köra skadlig kod på virtuella måldatorer för olika ändamål. Security Center inspekterar PowerShell-aktivitet för att hitta tecken på misstänkt aktivitet.

-   **Utgående attacker**: Angripare attackerar ofta resurser i molnet med avsikten att använda dessa resurser för att bygga upp fler attacker. Angripna virtuella datorer kan till exempel användas för att köra råstyrkeattacker mot andra virtuella datorer, skicka skräppost eller söka av öppna portar och andra enheter på internet. Genom att använda maskininlärning för nätverkstrafiken kan Security Center upptäcka avvikande utgående nätverkskommunikation. När skräppost identifieras Security Center även korrelera ovanlig e-posttrafik med information fån Office 365 för att avgöra om den e-posten är skräppost eller resultatet av en legitim e-postkampanj.

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Security Center använder också avvikelseidentifiering för att identifiera hot. Till skillnad mot beteendeanalyser (som är beroende av kända mönster som härleds från stora datamängder) är avvikelseidentifiering mer ”anpassad” och utgår från standarder som är specifika för dina distributioner. Maskininlärning används för att fastställa normala aktiviteten i dina distributioner och sedan genereras regler som definierar avvikande förhållanden som kan representera en säkerhetshändelse. Här är ett exempel:

-   **Inkommande RDP/SSH-råstyrkeattacker**: dina distributioner kan ha upptagna virtuella datorer med många inloggningar varje dag och andra virtuella datorer som har några, om sådana finns, inloggningar. Azure Security Center kan fastställa standardvärden för inloggningsaktiviteten för dessa virtuella datorer och använda machine learning för att definiera runt de normala inloggningsaktiviteter. Om det förekommer någon avvikelse med den baslinje som definierats för inloggning relaterade egenskaper, kan en avisering genereras. Maskininlärningen avgör vad som är viktigt.

### <a name="continuous-threat-intelligence-monitoring"></a>Kontinuerlig övervakning av hotinformation

Azure Security Center fungerar med säkerhet säkerhetsforskning och datavetenskapsteam över hela världen som kontinuerligt övervakar ändringar i hotlandskapet. Bland annat kan följande projekt nämnas:

-   **Övervakning av hotinformation**: Hotinformation inbegriper mekanismer, indikatorer, effekter och rekommendationer relaterade till befintliga eller nya hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar feeds om hotinformation från interna och externa källor.

-   **Informationsdelning**: insikter säkerhetsteamen breda Microsoft portfölj av molnet och lokala tjänster, servrar, och enheter för klientslutpunkter delar och analyseras.

-   **Microsofts säkerhetsexperter**: kontinuerligt arbete i team inom hela Microsoft som arbetar inom specialiserade säkerhetsområden, till exempel datautredning och identifiering av webbattacker.

-   **Identifieringsjustering**: algoritmer körs mot kundens verkliga datauppsättningar och säkerhetsanalytiker arbetar med kunden för att granska resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade ansträngningar resulterar i nya och förbättrade identifieringar, som du kan dra nytta av direkt. Det finns inga åtgärder att vidta.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Funktionerna för avancerad hotidentifiering: andra Azure-tjänster

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuella datorer: Microsoft program mot skadlig kod

[Microsoft program mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware) för Azure är en enskild agent-lösning för program och klient-miljöer, som är avsedd för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på dina behov för din programbelastningar, med antingen grundläggande säker som standard eller avancerade anpassad konfiguration, bland annat övervakning av program mot skadlig kod. Azure mot skadlig kod är ett säkerhetsalternativ för Azure-datorer som installeras automatiskt på alla Azure PaaS-datorer.

#### <a name="microsoft-antimalware-core-features"></a>Kärnfunktioner för Microsoft-program mot skadlig kod

Här följer de funktionerna i Azure som distribuerar och aktiverar Microsoft program mot skadlig kod för dina program:

-   **Realtidsskydd**: övervakar aktiviteten i cloud services och på virtuella datorer för att identifiera och blockera körning av skadlig kod.

-   **Schemalagd genomsökning**: utför regelbundet riktade genomsökning för att identifiera skadlig kod, däribland program som körs aktivt.

-   **Korrigering av skadlig kod**: automatiskt ska hantera identifierad skadlig kod, till exempel ta bort eller placera det i karantän skadliga filer och rensar skadliga registerposter.

-   **Signaturuppdateringar**: installerar automatiskt de senaste signaturerna (virusdefinitioner av) för att säkerställa att skyddet är uppdaterad på en förbestämd frekvens.

-   **Motorn för program mot skadlig kod uppdaterar**: uppdaterar automatiskt Microsoft Antimalware Engine.

-   **Uppdateringar för program mot skadlig kod plattform**: uppdaterar automatiskt Microsoft-plattformen för program mot skadlig kod.

-   **Aktivt skydd**: rapporter telemetri metadata om identifierade hot och misstänkta resurser till Microsoft Azure för att se till att snabbt svar på växande hotbild är att aktivera i realtid synkron signatur analysleverans via den Microsoft active protection system.

-   **Exempel reporting**: ger och rapporterar exempel till tjänsten Microsoft program mot skadlig kod för att förfina tjänsten och aktivera felsökning.

-   **Undantag**: gör att program och administratörer kan konfigurera vissa filer, processer, och enheter ska undantas från skyddet och sökning efter prestanda och andra orsaker.

-   **Händelseinsamling för program mot skadlig kod**: registrerar den tjänstehälsa för program mot skadlig kod, misstänkta aktiviteter och åtgärder som vidtagits i händelseloggen för operativsystemet och samlar in dem till kundens Azure-lagringskonto.

### <a name="azure-sql-database-threat-detection"></a>Hotidentifiering för Azure SQL Database

[Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) är en ny security intelligence funktion som är inbyggd i tjänsten Azure SQL Database. Arbetar dygnet runt för att lära sig, profilera och identifiera avvikande databasaktiviteter, identifierar Azure SQL Database Threat Detection potentiella hot mot databasen.

Security införlivande eller andra avsedda administratörer kan få en omedelbar avisering om misstänkta databasaktiviteter när de inträffar. Varje avisering innehåller information om misstänkt aktivitet och rekommenderar att vidare undersöka och minska risken.

För närvarande, identifierar Azure SQL Database Threat Detection potentiella säkerhetsrisker och SQL-filinmatningsattacker och avvikande mönster i databasåtkomst.

När tas emot ett e-postmeddelande för identifiering av hot, ska användare kunna navigera och visa relevanta granskningsposter via en djuplänk i e-postmeddelandet. Länken öppnar visningsprogram för granskning eller en förkonfigurerade granskning Excel-mall som visar relevanta granskningsposter ungefär samma tidpunkt som den misstänkta händelsen, enligt följande:

-   Granska lagring för databasservern med avvikande databasaktiviteter.

-   Relevanta audit lagringstabell som användes vid tidpunkten för händelsen för att skriva granskningsloggen.

-   Granskningsposter på den timma som direkt efter händelsen.

-   Granskningsposter med en liknande händelse-ID vid tidpunkten för händelsen (valfritt för vissa detektorerna).

SQL Database threat detektorerna använder något av följande metoder för identifiering:

-   **Deterministisk identifiering**: identifierar misstänkta mönster (regler baserade) i SQL-klientfrågor som matchar kända attacker. Den här metoden har identifiering av hög och låg falskt positivt, men begränsad täckning eftersom den faller inom kategorin ”atomiska identifieringar”.

-   **Beteendeanalys identifiering**: identifierar avvikande aktivitet, vilket är onormalt beteende i databasen som inte visades under de senaste 30 dagarna. Exempel på SQL-klienten avvikande aktivitet kan vara en ökning av misslyckade inloggningar eller frågor, en stor mängd data som extraheras, ovanlig canonical frågor eller okänd IP-adresser som används för att få åtkomst till databasen.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway Web Application Firewall

[Web Application Firewall (WAF)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) är en funktion i [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) som ger skydd till webbprogram som använder en Programgateway för standard [programkontroll leverans](https://kemptechnologies.com/in/application-delivery-controllers) funktioner. Brandvägg för webbaserade program gör detta genom att skydda dem mot de flesta av de [OWASP Open Web Application Security Project () topp 10 vanligaste webbsårbarheterna](https://www.owasp.org/index.php/Top_10_2010-Main).

![Application Gateway Web Application Firewall diagram](./media/azure-threat-detection/azure-threat-detection-fig13.png)

Skydd omfattar:

-   Skydd av SQL-inmatning.

-   Cross site scripting skydd.

-   Skydd mot vanliga webbattacker, till exempel kommandot inmatning, HTTP-begäran som kommandoinmatning, HTTP-svar och fjärrfil inkludering attack.

-   Skydd mot protokollbrott för HTTP.

-   Användaragent för värden och accept-skydd mot HTTP-protokollet avvikelser, till exempel som saknas.

-   Skydd mot robotar, crawlers och skannrar.

-   Identifiering av vanliga felkonfigureringar för programmet (det vill säga Apache, IIS och så vidare).

Konfigurering av WAF vid application gateway ger följande fördelar:

-   Skyddar ditt webbprogram från säkerhetsrisker och attacker utan ändringar av backend-koden.

-   Skyddar flera webbprogram samtidigt bakom en Programgateway. En application gateway har stöd för upp till 20 webbplatser.

-   Övervakare webbprogram mot attacker med hjälp av i realtid rapporter som genereras av application gateway WAF-loggar.

-   Hjälper dem att uppfylla efterlevnadskrav. Vissa kompatibilitetskontroller kräver alla internet-riktade slutpunkter som ska skyddas av en WAF-lösning.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Avvikelseidentifiering API: Byggda med Azure Machine Learning

API: T för Avvikelseidentifiering identifiering är en API som är användbart för att upptäcka en mängd olika avvikande mönster i tidsseriedata. API: et tilldelar en avvikelsepoäng till varje datapunkt i tidsserien, som kan användas för att generera aviseringar, övervaka via instrumentpaneler eller ansluta till dina ärendesystem.

Den [API: T för Avvikelseidentifiering](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) kan identifiera följande typer av avvikelser i tidsseriedata:

-   **Toppar och dalar**: när du övervakar antalet misslyckade inloggningar till en tjänst eller antalet utcheckningar på en webbplats för e-handel, ovanliga toppar och dalar kan säkerhetsattacker eller störningar-tjänsten.

-   **Positiva och negativa trender**: när du övervakar minnesanvändningen vid, minska storleken på ledigt minne storleken anger en potentiell minnesläcka. För tjänsten Kölängd övervakning, innebära en beständig uppåtgående trend en underliggande programproblem.

-   **Nivåändringar och ändringar i dynamiska värdeintervall**: nivå ändringar i latensen för en tjänst efter en uppgradering eller lägre nivåer av undantag efter uppgraderingen kan vara intressant att övervaka.

Machine learning-baserade API möjliggör:

-   **Flexibel och stabil**: avvikelseidentifiering identifiering modeller kan du konfigurera inställningar för känslighet och identifiera avvikelser mellan säsongs- och icke säsongsbundna datauppsättningar. Användare kan justera avvikelseidentifiering identifiering av modellen för att göra identifiering API mer eller mindre känsliga utifrån deras behov. Detta skulle innebära att kunna identifiera de mindre eller mer synliga avvikelser i data med och utan säsongens mönster.

-   **Skalbar och snabb identifiering**: ett traditionellt sätt att övervakning med finns vad som anges i experternas domänkunskap är kostsamma och inte skalbar till miljontals dynamiskt ändra datauppsättningar. Modeller på identifiering av avvikelser i den här API: et lärs och modeller är justerade automatiskt från både historiska data och realtidsdata.

-   **Proaktiv och användbara**: långsamma trend och nivåändring identifiering kan användas för tidig avvikelseidentifiering. Tidig onormalt signaler som identifieras kan användas för att dirigera människor att undersöka och agera utifrån problemområden. Dessutom rotorsak analysis-modeller och aviseringar verktyg kan utvecklas på den här avvikelseidentifiering API-tjänsten.

API: T för avvikelseidentifiering är en och effektiva lösning för en mängd olika scenarier, till exempel tjänstehälsa och KPI övervakning, IoT, prestandaövervakning och övervakning av nätverkstrafik. Här följer några populära scenarier där detta API kan vara användbara:

- IT-avdelningar måste verktyg för att spåra händelser, felkod, log användning och prestanda (processor, minne och så vidare) i tid.

-   Online handelswebbplatser vill spåra kundaktiviteter, sidvisningar, klick och så vidare.

-   Utility företag vill spåra användningen av vatten, gas, elektricitet och andra resurser.

-   Funktion eller en byggnad hanteringstjänster vill övervaka temperatur, fuktighet, trafik och så vidare.

-   IoT/tillverkare vill använda sensordata i tidsserier till arbetsflödet för övervakaren, kvaliteten och så vidare.

-   Leverantörer av tjänster, till exempel kundtjänst, behöver övervaka tjänsten begäran trend, volym incidenter, väntekö och så vidare.

-   Analytics affärsgrupper vill övervaka KPI: er (till exempel försäljningsvolymen, kunden sentiment eller priser) onormalt förflyttning i realtid.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) är en fundamental komponent i Microsoft Cloud Security-stacken. Det är en omfattande lösning som kan hjälpa din organisation när du migrerar till dra full nytta av molnapparnas potential. Det behåller du kontrollen med ökad insyn i aktiviteten. Det hjälper också att öka skyddet av viktiga data i molnappar.

Med verktyg som hjälper att upptäcka skugg-IT, utvärdera risker, genomdriva principer, undersöka aktiviteter och stoppa hot kan kan organisationen säkrare flytta till molnet samtidigt som de behåller kontrollen över viktiga data.

| | |
|---|---|
| Utforska | Upptäck shadow IT med Cloud App Security. Få insyn genom att identifiera appar, aktiviteter, användare, data och filer i molnmiljön. Identifiera tredjepartsappar som är anslutna till molnet.|
|Undersök | Undersök dina molnappar med hjälp av datautredningsverktyg djupdykning riskfyllda appar, specifika användare och filer i nätverket. Hitta mönster i data som samlas in från molnet. Generera rapporter för molnövervakning. |
| Kontroll | Minska riskerna genom att ange principer och varningar för att uppnå full kontroll över molntrafiken i nätverket. Använda Cloud App Security för att migrera användarna till säkra sanktionerade molnappar app alternativ. |
| Skydda | Använd Cloud App Security att sanktionera eller förbjuda program, tillämpa skydd mot dataförlust, kontrollera behörigheter och delning och skapa anpassade rapporter och aviseringar. |
| Kontroll | Minska riskerna genom att ange principer och varningar för att uppnå full kontroll över molntrafiken i nätverket. Använda Cloud App Security för att migrera användarna till säkra sanktionerade molnappar app alternativ. |
| | |


![Cloud App Security-diagram](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integrerar synlighet i molnet genom:

-   via Cloud Discovery för att mappa och identifiera molnmiljön och molnapparna som din organisation använder.

-   Sanktionering och förbud mot appar i molnet.

-   med hjälp av enkla att distribuera appanslutningsverktyg som utnyttjar providerns API: er för synlighet och styrning av appar som du ansluter till.

-   stöd för kontinuerlig kontroll genom inställningen och kontinuerligt finjustera, principer.

På insamling av data från dessa källor kör Cloud App Security avancerad analys på den. Den omedelbart aviserar avvikande aktiviteter och ger dig bättre insyn i molnmiljön. Du kan konfigurera en princip i Cloud App Security och använda den för att skydda allt innehåll i molnmiljön.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Tredje parts avancerad Hotidentifiering funktioner via Azure Marketplace

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brandvägg för webbaserade program granskar inkommande webbtrafik och blockerar SQL-inmatningar, cross site scripting, överföring av skadlig kod, program-DDoS-attacker och andra attacker mot dina webbprogram. Den kontrollerar även svar från backend-webbservrar för dataförlustskydd (DLP). Integrerad access control engine gör att administratörer kan skapa detaljerade principer för åtkomstkontroll för autentisering, auktorisering och redovisning (AAA), som ger organisationer stark autentisering och kontroll.

Brandvägg för webbaserade program ger följande fördelar:

-   Identifierar och blockerar SQL-inmatningar, Cross Site Scripting, överföring av skadlig kod, program-DDoS eller andra attacker mot ditt program.

-   Autentisering och åtkomstkontroll.

-   Söker igenom utgående trafik för att identifiera känsliga data och kan maskera eller blockera information läcks.

-   Påskyndar leveransen av web application innehåll, med hjälp av funktioner som cachelagring, komprimering och andra trafik optimeringar.

Exempel på brandväggar för webbprogram som är tillgängliga i Azure Marketplace finns [Barracuda WAF, Brocade virtuella waf (vWAF), Imperva SecureSphere och ThreatSTOP IP-brandvägg](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Nästa steg

- [Identifieringsfunktioner i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): hjälper dig att identifiera aktiva hot som mål dina Azure-resurser och ger insikter som du måste svara snabbt.

- [Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): hjälper dig att fokusera på dina problem om potentiella hot mot dina databaser.
