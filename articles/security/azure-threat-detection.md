---
title: Azure avancerade Hotidentifiering | Microsoft Docs
description: Läs mer om identitetsskydd och dess funktioner.
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
ms.openlocfilehash: 0813e0f6b51c747d033ca2c44aed21cf94c32000
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113035"
---
# <a name="azure-advanced-threat-detection"></a>Azure avancerade Hotidentifiering
## <a name="introduction"></a>Introduktion

### <a name="overview"></a>Översikt

Microsoft har utvecklat en serie faktablad, säkerhet översikter, bästa praxis och checklistor som en hjälp om de olika säkerhetsrelaterade funktionerna tillgängliga i Azure-kunder och omgivande Azure-plattformen. Avsnitten intervallet vad gäller bredd och djup och uppdateras regelbundet. Det här dokumentet är en del av serien som sammanfattas i följande avsnitt i abstrakt.

### <a name="azure-platform"></a>Azure-plattformen

Azure är en offentlig molntjänstplattform som stöder bredaste valet av operativsystem, programmeringsspråk, ramverk, verktyg, databaser, och enheter.
Den stöder följande programmeringsspråk:
-   Kör Linux behållare med Docker-integration.
-   Skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js
-   Build-servrar för iOS, Android och Windows enheter.

Offentliga Azure-molnet och support att samma tekniker miljontals utvecklare och IT-proffs redan förlitar sig på och litar på.

När du migrerar till ett offentligt moln med en organisation att organisation ansvarar för att skydda dina data och ger säkerhet och styrning runt systemet.

Allt i Azures infrastruktur, från anläggning till tillämpningar, är utformat för att fungera som värd för miljoner kunder samtidigt, och den tillhandahåller en säker grund som företaget kan använda sig av för att möta de interna säkerhetsbehoven. Azure tillhandahåller en mängd olika alternativ för att konfigurera och anpassa säkerhet för att uppfylla kraven för din appdistribution. Det här dokumentet hjälper dig att möta dessa krav.

### <a name="abstract"></a>Abstrakt

Microsoft Azure erbjudanden som har skapats i avancerade threat detection funktioner med hjälp av tjänster som Azure Active Directory, Azure Operations Management Suite (OMS) och Azure Security Center. Den här samlingen av tjänster och funktioner innehåller ett snabbt och enkelt sätt att förstå vad som händer i din Azure-distributioner.

I det här dokumentet hjälper dig i ”Microsoft Azure närmar sig” mot hot säkerhetsproblem diagnostik och analysera risken som är associerade med skadliga aktiviteter som riktar sig mot servrar och andra Azure-resurser. Det hjälper dig att identifiera metoder för hantering av identifiering och säkerhetsproblem med optimerade lösningar genom Azure-plattformen och kunden vända säkerhetstjänster och tekniker.

I det här dokumentet fokuserar på teknik för Azure-plattformen och kunden vända kontroller och försöker inte adressen SLA: er priser modeller och DevOps överväganden.

## <a name="azure-active-directory-identity-protection"></a>Identitetsskydd för Azure Active Directory

![Identitetsskydd för Azure Active Directory](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory-identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är en funktion i den [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) edition som ger dig en översikt över riskhändelser och potentiella säkerhetsproblem som påverkar din organisations identiteter. Microsoft har skydda molnbaserade identiteter för över tio år och med Azure AD Identity Protection Microsoft att skydda datorerna tillgängliga för enterprise-kunder. Identity Protection använder befintliga Azure ADS avvikelseidentifiering identifiering funktioner tillgängliga via [Azure AD avvikande aktivitetsrapporter](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports), och ger nya risk händelsetyper som kan identifiera avvikelser i realtid.

Identity Protection använder anpassningsbara maskininlärningsalgoritmer och heurestik för att identifiera avvikelser och riskhändelser som kan tyda på att en identitet är utsatt för risk. Med dessa data kan genererar identitetsskydd rapporter och aviseringar som gör att du kan undersöka dessa riskhändelser och vidta lämpliga reparation eller lösning åtgärd.

Men Azure Active Directory Identity Protection är mer än ett verktyg för övervakning och rapportering. Baserat på riskhändelser beräknar identitetsskydd en användare risknivå för varje användare, så att du kan konfigurera risk-baserade principer för att automatiskt skydda identiteter i din organisation.

Dessa risk-principer, utöver andra [villkorlig åtkomstkontroll](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) tillhandahålls av Azure Active Directory och [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), kan automatiskt blockera eller erbjuda anpassade åtgärder som omfattar lösenordsåterställning och multifaktorautentisering tvingande.

### <a name="identity-protections-capabilities"></a>Identity Protection funktioner

Azure Active Directory Identity Protection är mer än en övervakning och rapportering av verktyget. Du kan konfigurera risk-baserade principer som svarar på identifierade problem automatiskt när en angiven risknivå har uppnåtts för att skydda din organisations identiteter. Dessa principer, utöver andra kontroller för villkorlig åtkomst som tillhandahålls av Azure Active Directory och EMS, kan automatiskt blockera eller initiera anpassningsbar reparationsåtgärder inklusive lösenordsåterställning och multifaktorautentisering tvingande.

Exempel på några av de sätt som Azure Identity Protection hjälper dig skydda dina konton och identiteter inkluderar:

[Identifiering av riskhändelser och riskfyllda konton:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Identifiera händelsetyper sex risken med machine learning och heuristisk regler
-   Beräkning av risknivåer för användaren
-   Att tillhandahålla anpassade rekommendationer för att förbättra övergripande säkerhetstillståndet genom att markera säkerhetsrisker

[Undersöka riskhändelser:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Skicka meddelanden för riskhändelser
-   Undersöka riskhändelser med hjälp av relevanta och sammanhangsberoende information
-   Tillhandahåller grundläggande arbetsflöden för att spåra undersökningar
-   Enkel åtkomst till åtgärder, till exempel återställning av lösenord

[Principer för risk-baserad villkorlig åtkomst:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Princip för att minska riskfyllda inloggningar genom att blockera inloggningar eller att kräva multifaktorautentisering utmaningar.
-   Princip för att blockera eller säker riskfyllda användarkonton
-   Princip för att kräva att användarna registrera sig för multifaktorautentisering

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

Med [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

Du kan hantera, styr och övervaka åtkomst inom din organisation. Detta inkluderar åtkomst till resurser i Azure AD och andra Microsoft online-tjänster som Office 365 eller Microsoft Intune.

Azure AD Privileged Identity Management kan du:

-   Få en avisering och rapportera om Azure AD-administratörer och just-in-time ”administrativ åtkomst till Microsofts onlinetjänster som Office 365 och Intune

-   Hämta rapporter om administratören åtkomsthistorik och ändringar i administratörstilldelningar

-   Få meddelanden om åtkomst till en privilegierad roll

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är Microsofts molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur. Eftersom OMS implementeras som en molnbaserad tjänst kan du snabbt komma igång med minimal investering i infrastrukturtjänster. Nya säkerhetsfunktioner levereras automatiskt, sparar din löpande underhållet och uppgradera kostnader.

Förutom att tillhandahålla tjänster på sin egen OMS kan integreras med System Center-komponenter som [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) att utöka din befintliga security management investeringar i molnet. System Center och OMS kan tillsammans tillhandahålla en komplett hybridhanteringsmiljö.

### <a name="holistic-security-and-compliance-posture"></a>Heltäckande säkerhet och efterlevnad Hållningsdata

Den [OMS säkerhet och granska instrumentpanelen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) tillhandahåller en heltäckande översikt över din organisations IT säkerhetstillståndet med inbyggda sökfrågor för anmärkningsvärda problem som kräver din uppmärksamhet. Säkerhet och granska instrumentpanelen är startsidan för allt relaterade till säkerhet i OMS. Den innehåller en översikt över säkerhetsstatusen för dina datorer. Här kan du också visa alla händelser från de senaste 24 timmarna, sju dagarna eller ett annat anpassat tidsintervall.

OMS instrumentpaneler som hjälper dig att snabbt och enkelt förstå övergripande säkerhetstillståndet för alla miljöer alla inom ramen för IT-avdelningen, inklusive: utvärdering av uppdateringar för programvara, program mot skadlig kod bedömning och konfigurationsbaslinjer. Loggdata för säkerhet är dessutom lättillgängliga att effektivisera processer för säkerhet och efterlevnad granskning.

Instrumentpanelen för Säkerhet och granskning i OMS är ordnad i fyra huvudkategorier:

![Instrumentpanelen för Säkerhet och granskning i OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Säkerhetsdomäner:** i detta område, kommer du att kunna ytterligare utforska säkerhetsposter över tid, gå till kontroll av skadlig kod, uppdatera assessment, nätverkssäkerhet, identitet och åtkomst, datorer med säkerhetshändelser och snabbt har åtkomst till instrumentpanelen Azure Security Center.

-   **Viktiga problem:** det här alternativet kan du snabbt identifiera antalet aktiva problem och allvarlighetsgrad för de här problemen.

-   **Identifieringar (förhandsversion):** gör att du kan identifiera angreppsmönster genom att visualisera säkerhetsaviseringar som de ske mot dina resurser.

-   **Hotinformation:** kan du identifiera angreppsmönster av visualisering av det totala antalet servrar med utgående skadlig IP-trafik, skadliga hot typ och en karta som visar var dessa IP-adresser kommer från.

-   **Vanliga säkerhetsfrågor:** det här alternativet visar en lista över de vanligaste säkerhetsfrågor som du kan använda för att övervaka din miljö. När du klickar på någon av dessa frågor öppnas bladet sökning med resultaten för frågan.

### <a name="insight-and-analytics"></a>Insikter och analys
I mitten av [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) är OMS-databasen, som finns i Azure-molnet.

![Insikter och analys](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Data samlas i databasen från anslutna källor genom att konfigurera datakällor och lägga till lösningar i din prenumeration.

![prenumeration](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Alla datakällor och lösningar skapar olika typer av poster som har en egen uppsättning egenskaper, men de kan fortfarande analyseras tillsammans i förfrågningar till databasen. Det gör att du kan använda samma verktyg och metoder för att arbeta med olika typer av data som samlats från olika källor.


De flesta interaktionen med logganalys är OMS-portalen som körs i en webbläsare och ger du åtkomst till konfigurationsinställningar och flera olika verktyg för att analysera och agera på insamlade data. Från portalen, kan du använda [logga sökningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) där du skapar frågor för att analysera insamlade data [instrumentpaneler](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), som du kan anpassa med grafiska vyer av dina mest värdefulla sökningar och [lösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), som ger ytterligare funktioner och analys verktyg.

![Verktyg för analys](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Lösningar lägger till funktioner i Log Analytics. De körs huvudsakligen i molnet och ger analys av data som samlats i OMS-databasen. De kan också definiera nya typer av poster som ska samlas in som kan analyseras med loggsökningar eller genom ytterligare användargränssnitt som tillhandahålls i lösningen i OMS-instrumentpanelen.
Säkerhet och granskning är ett exempel på dessa typer av lösningar.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automation- och kontrollservern: avisering på säkerhetskonfiguration drifts

Azure Automation automatiserar administrativa processer med runbooks som är baserade på PowerShell och kör i Azure-molnet. Runbooks kan också köras på en server i ditt lokala datacenter för att hantera lokala resurser. Azure Automation ger konfigurationshantering med PowerShell DSC (Desired State Configuration).

![Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Du kan skapa och hantera DSC-resurser som finns i Azure och tillämpa dem till molnet och lokalt system för att definiera och automatiskt tillämpa konfigurationen eller få rapporter om drift för att försäkra dig om att säkerhetskonfigurationer finns kvar i principen.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center skyddar resurserna i Azure. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer. Inom tjänsten, du kan definiera principer inte bara mot dina Azure-prenumerationer, utan även mot [resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), så du kan vara mer detaljerade.

![Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Microsofts säkerhetsforskare söker hela tiden efter hot. De har tillgång till en omfattande uppsättning telemetri från Microsofts globala närvaro i molnet och i lokala infrastrukturer. Med den här omfattande och mångfaldiga samlingen data kan Microsoft identifiera nya angreppsmönster och trender i lokala konsument- och företagsprodukter, och i onlinetjänster.

Därför kan Security Center snabbt uppdateras som angripare släpper nya och allt mer avancerade kryphål. Den här metoden kan du hålla jämna steg med en fast flytta hot-miljö.

![Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Hotidentifieringen i Security Center sker genom automatisk insamling av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar.  Den här informationen korrelerar information från flera källor, identifiera hot analyseras.
Säkerhetsaviseringar prioriteras i Security Center tillsammans med rekommendationer om hur hotet kan åtgärdas.

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Genombrott i stordata och [maskininlärning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tekniker som används för att utvärdera händelser över hela molninfrastrukturresurs – upptäcka hot som skulle vara omöjligt att identifiera med manuella metoder och förutsäga utvecklingen av attacker. Dessa säkerhet analytics innehåller följande.

### <a name="threat-intelligence"></a>Hotinformation

Microsoft har tillgång till en enorm mängd global hotinformation.
Telemetri flödar i från flera källor, till exempel Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) och Microsoft Security Response Center (MSRC).

![Hotinformation](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Forskare får även hotinformation som delas med viktiga providers och prenumeranter av molntjänster och i feeds om hotinformation från tredje parter. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer. Några exempel är:

-   **Utnyttjar den Power av Machine Learning -** Azure Security Center har åtkomst till en mängd data om molnet nätverksaktivitet, som kan användas för att identifiera hot målobjekt för din Azure-distributioner. Exempel:

-   **Brute Force identifieringar -** maskininlärning används för att skapa ett historiska remote anslutningsförsök, vilket gör att de kan identifiera brute force-attacker mot SSH RDP och SQL-portar.

-   **Utgående DDoS och identifiering av Botnät** -ett gemensamt mål för attacker molnresurser som mål är att använda datorkraft resurserna för att köra andra attacker.

-   **Nya Beteendebaserade Analytics-servrar och virtuella datorer -** när en server eller virtuell dator äventyras, angripare använda en mängd olika tekniker för att köra skadlig kod på systemet och undvika identifiering, säkerställer beständiga och undanröja säkerhetsåtgärder.

-   **Azure SQL Database-Hotidentifiering -** Hotidentifiering för Azure SQL-databas som identifierar avvikande databasaktiviteter som indikerar ovanliga och potentiellt skadliga försöker komma åt eller utnyttja databaser.

### <a name="behavioral-analytics"></a>Beteendeanalys

Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder,

![Beteendeanalys](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


och av expertanalytiker genom noggrann analys av skadliga beteenden. Azure Security Center kan använda beteendeanalys för att identifiera angripna resurser baserat på analys av loggar för virtuell dator, enhetsloggar för virtuellt nätverk, fabric loggar, krascher Dumpar och andra källor.

Dessutom korreleras data med andra tecken för att hitta bevis på utbredda attacker. Den här korrelationen gör det möjligt att identifiera händelser som matchar fastställda hotindikatorer.

Några exempel är:
-   **Körningen av misstänkt processen:** angripare använda flera tekniker för att köra skadlig programvara utan identifiering. Till exempel kan en angripare ge skadlig kod med samma namn som legitima systemfiler men placera dessa filer på en annan plats, Använd ett namn som är mycket som en ofarlig fil eller maskera filtillägg som true. Security Center-modellerna bearbetar beteenden och övervakar körningen av processer för att identifiera avvikelser som dessa.

-   **Dold skadlig kod och utnyttjande försök:** avancerad skadlig kod kan undandra sig traditionella antivirusprodukter av aldrig skrivning till disk eller kryptera programvarukomponenter som lagras på disk. Dock kan sådana skadlig kod identifieras med hjälp av minnesanalys som den skadliga koden måste lämna spårningar i minnet ska fungera. När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen. Genom att analysera minnet i krasch-dump Azure Security Center kan identifiera tekniker som används för att utnyttja sårbarheter i program, komma åt känsliga data och spara smyg inom en komprometterad dator utan att påverka prestandan för din datorn.

-   **Lateral förflyttning och interna rekognosering:** för att sparas i en komprometterad nätverks- och leta upp/skörd värdefulla data angripare försöka ofta flytta sidled från den avslöjade datorn till andra i samma nätverk. Security Center övervakar processen och logga in aktiviteter för att identifiera om du försöker expandera en angripare fot i nätverket, till exempel fjärrkörning nätverksåtkomst och kontouppräkning.

-   **PowerShell-skript:** PowerShell kan användas av angripare för att köra skadlig kod på virtuella datorer som mål för olika ändamål. Security Center inspekterar PowerShell-aktivitet för att hitta tecken på misstänkt aktivitet.

-   **Utgående attacker:** angripare ofta mål molnresurser med målet att använda dessa resurser för att montera ytterligare attacker. Angripna virtuella datorer kan till exempel användas för att starta brute force-attacker mot andra virtuella datorer, skicka skräppost eller skanna öppna portar och andra enheter på Internet. Genom att använda maskininlärning för nätverkstrafiken kan Security Center upptäcka avvikande utgående nätverkskommunikation. När skräppost, Security Center också korrelerar ovanliga e-trafik med information från Office 365 för att avgöra om e-post är sannolikt nefarious eller resultatet av en giltig e-kampanj.

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Security Center använder också avvikelseidentifiering för att identifiera hot. Till skillnad mot beteendeanalyser (som är beroende av kända mönster som härleds från stora datamängder) är avvikelseidentifiering mer ”anpassad” och utgår från standarder som är specifika för dina distributioner. Maskininlärning tillämpas för att fastställa om aktiviteten i dina distributioner är normal. Sedan genereras regler som definierar avvikande förhållanden som kan representera en säkerhetshändelse. Här är ett exempel:

-   **Inkommande RDP/SSH brute force-attacker:** dina distributioner kan ha upptagen virtuella datorer med många inloggningar varje dag och andra virtuella datorer har några eller alla inloggningar. Azure Security Center kan fastställa baslinjen inloggningsaktivitet för dessa virtuella datorer och använder maskininlärning att definiera runt normal inloggningen aktiviteter. Om det inte finns någon avvikelse med den baslinje som definierats för relaterade inloggning egenskaper, och sedan en avisering kan genereras. Maskininlärningen avgör vad som är viktigt.

### <a name="continuous-threat-intelligence-monitoring"></a>Kontinuerlig Hotinformation övervakning

Azure Security Center fungerar med säkerhet forskning vetenskaplig team över hela världen som övervakar kontinuerligt efter ändringar i hotbilden i grunden. Bland annat kan följande projekt nämnas:

-   **Övervakning av hot intelligence:** hot intelligence inkluderar mekanismer, indikatorer, effekter och tillämplig råd om befintliga eller nya hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar feeds om hotinformation från interna och externa källor.

-   **Signal delning:** insikter från säkerhet arbetsgrupper i Microsofts stor portfölj av molnet och lokala tjänster, servrar och klienten endpoint enheter delade och analyseras.

-   **Microsoft security specialister:** pågående interaktion med team på Microsoft som fungerar i särskild säkerhet fält, exempelvis dataforensik och web angreppsidentifiering.

-   **Identifiering av inställning:** algoritmer körs mot verkliga kunden datauppsättningar och säkerhet forskare arbeta med kunder för att kontrollera resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade ansträngningar resulterar i nya och förbättrade identifieringar, som du kan dra nytta av direkt – ingen åtgärd krävs av dig.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Funktioner för identifiering av Advanced Threat - andra Azure-tjänster

### <a name="virtual-machine-microsoft-antimalware"></a>Virtuell dator: Microsoft Antimalware

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för Azure är en enskild agent lösning för program och klient-miljöer, som är avsedd för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd utifrån behoven i din programbelastningar, med antingen grundläggande secure-som-standard eller avancerad anpassad konfiguration, inklusive övervakning av program mot skadlig kod. Azure program mot skadlig kod är ett säkerhetsalternativ för virtuella datorer i Azure och installeras automatiskt på alla Azure PaaS virtuella datorer.

**Funktioner i Azure för att distribuera och aktivera Microsoft Antimalware för dina program**

#### <a name="microsoft-antimalware-core-features"></a>Microsoft-program mot skadlig kod kärnfunktioner

-   **Realtidsskydd -** övervakar aktiviteten i Cloud Services och på virtuella datorer för att identifiera och blockera skadlig kod körning.

-   **Schemalagd genomsökning -** regelbundet utför riktade genomsökning för att upptäcka skadlig kod, inklusive program som körs aktivt.

-   **Korrigering av skadlig kod -** automatiskt vidtar åtgärder på upptäckt skadlig kod, till exempel ta bort eller sätta i karantän skadliga filer och rensa skadliga registerposter.

-   **Signaturuppdateringar -** installeras automatiskt de senaste signaturerna (virusdefinitioner av) för att skyddet uppdaterad på en förbestämd frekvens.

-   **Motorn för program mot skadlig kod uppdateringar -** uppdateras automatiskt Microsoft Antimalware-motorn.

-   **Uppdateringar för program mot skadlig kod plattform –** uppdateras automatiskt Microsoft Antimalware-plattformen.

-   **Aktivt skydd -** rapporterar telemetri metadata om identifierade hot och misstänkta resurser till Microsoft Azure för att säkerställa snabba svar till utvecklingen av hotbild och aktivera realtidsskydd synkron signatur leverans via den Microsoft Active Protection System (MAPS).

-   **Exempel för reporting -** innehåller och rapporter prover till tjänsten Microsoft Antimalware för att förbättra tjänsten och aktivera felsökning.

-   **Undantag –** gör att program och administratörer kan konfigurera vissa filer, processer, och enheter för att utesluta dem från skyddet och skanning för prestanda och/eller andra orsaker.

-   **Program mot skadlig kod händelseinsamling -** registrerar tjänstens hälsa för program mot skadlig kod, misstänkta aktiviteter och åtgärder som vidtagits i händelseloggen för operativsystemet och samlar in dem i kundens Azure Storage-konto.

### <a name="azure-sql-database-threat-detection"></a>Hotidentifiering för Azure SQL-databas

[Azure SQL Database-Hotidentifiering](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) är en ny intelligence säkerhetsfunktion inbyggda i Azure SQL Database-tjänsten. Arbetar dygnet runt för att lära dig, profil och identifiera avvikande databasaktiviteter, identifierar Azure SQL Database Hotidentifiering potentiella hot mot databasen.

Säkerhet polis eller andra avsedda administratörer kan få en omedelbar avisering om misstänkt databasaktiviteter när de inträffar. Varje meddelande innehåller information om misstänkt aktivitet och rekommenderas så att ytterligare undersöka och minska risken.

För närvarande identifierar Azure SQL Database Hotidentifiering potentiella säkerhetsproblem och SQL injection attacker och avvikande databasen åtkomstmönster.

Vid mottagning threat detection e-postavisering är användare kan navigera och visa relevanta granskningsposter med hjälp av djup länken i e-postmeddelandet som öppnar ett visningsprogram för granskning och/eller förkonfigurerade granskning Excel-mallen som visar relevanta granskningsposter runt tid för händelsen misstänkta enligt följande:
-   Granska lagring för databasservern med avvikande databasaktiviteter

-   Relevanta audit lagringstabellen som användes vid tidpunkten för händelsen för att skriva granskningsloggen

-   Granska poster för följande timmen eftersom händelsen inträffar.

-   Granskningsposter med liknande händelse-ID vid tidpunkten för händelsen (valfritt för vissa detektorerna)

SQL-databas hot detektorerna använder något av följande metoder för identifiering:

-   **Deterministisk identifiering –** identifierar misstänkta mönster (regler baserade) i SQL-klientfrågor som matchar kända attacker. Den här metoden har identifiering av hög och låg falsklarm dock begränsat täckning eftersom det hamnar inom kategorin ”atomiska identifieringar”.

-   **Beteendemässiga identifiering –** fel avvikande aktivitet, onormalt beteende för databasen som inte påträffades under de senaste 30 dagarna.  Ett exempel på SQL avvikande aktivitet kan vara en insamling av misslyckade inloggningar-frågor, stor mängd data som extraheras, onormal kanoniska frågor och okänd IP-adresser som används för att få åtkomst till databasen

### <a name="application-gateway-web-application-firewall"></a>Programmet Gateway Brandvägg för webbaserade program

[Web Application Firewall](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) är en funktion i [Azure Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) som ger skydd till webbprogram som använder Programgateway för standard [programkontrollen leverans](https://kemptechnologies.com/in/application-delivery-controllers)funktioner. Brandvägg för webbaserade program gör detta genom att skydda dem mot de flesta av de [OWASP topp 10 vanliga säkerhetsproblem för webbprogram](https://www.owasp.org/index.php/Top_10_2010-Main)

![Application Gateway Web Application Firewally](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   Skydd mot SQL-inmatning

-   Skydd mot skriptkörning över flera webbplatser

-   Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

-   Skydd mot åtgärder som inte följer HTTP-protokollet

-   Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

-   Skydd mot robotar, crawlers och skannrar

-   Identifiering av program vanliga felinställningar (det vill säga Apache, IIS osv.)

Konfigurera Brandvägg på Programgateway erbjuder följande fördelen att du:

-   Skyddar ditt webbprogram från säkerhetsrisker och attacker utan att du behöver ändra koden i serverdelen.

-   Skyddar flera webbprogram samtidigt bakom en programgateway. Application Gateway har stöd för att vara värd för upp till 20 webbsidor bakom en enda gateway som skyddar dem alla från webbattacker.

-   Övervaka ditt webbprogram för att förhindra attacker, med hjälp av realtidsrapporter som genereras av gatewayens WAF-loggar.

-   Vissa kompatibilitetskontroller kräver att alla slutpunkter som är internetanslutna ska skyddas av en WAF-lösning. Genom att använda programgatewayen med WAF aktiverat kan du uppfylla dessa kompatibilitetskrav.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Avvikelseidentifiering – en API som byggts med Azure Machine Learning

Avvikelseidentifiering är en API som byggts med Azure Machine Learning som är användbar för att identifiera olika typer av avvikande mönster i tid series-data. API: et tilldelar en avvikelseidentifiering poäng till varje datapunkt i tidsserien som kan användas för att generera aviseringar, övervakning genom instrumentpaneler eller ansluta med loggnings-system.

Den [Avvikelseidentifiering identifiering API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) kan identifiera följande typer av avvikelser på tid series-data:

-   **Ger spikar i diagrammet och sjunker:** exempelvis vid övervakning av antal misslyckade inloggningar till en tjänst eller antal utcheckningar i en e-handelsplats ovanliga toppar korta kan ange säkerhetsattacker eller avbrott i tjänsten.

-   **Positiva och negativa trender:** när du övervakar minnesanvändning i computing exempelvis krympa ledigt minne är indikation på en minnesläcka; när du övervakar service Kölängd beständiga tendens kan tyda på en underliggande problem med programvara.

-   **Nivå ändringar och ändringar i dynamiskt värdeintervallet:** exempelvis nivå ändringar i svarstiderna för en tjänst när en tjänst har uppgraderat eller lägre nivåer av undantag efter uppgraderingen kan vara av intresse för att övervaka.

Machine learning baserat API som ger:

-   **Flexibel och kraftfull identifiering:** avvikelseidentifiering identifiering modeller Tillåt användare att konfigurera inställningar för känslighet och identifiera avvikelser mellan säsongsbaserade och icke-säsongsbaserade datauppsättningar. Användare kan justera avvikelseidentifiering identifiering av modellen för att göra identifiering API mindre eller mer känslig utifrån deras behov. Detta betyder att upptäcka mindre eller mer synliga avvikelser i data med och utan säsongsbaserade mönster.

-   **Skalbar och rimlig identifiering:** traditionella sätt att övervaka med finns tröskelvärden som angetts av experter kunskap är kostsamma och inte skalbara till miljontals dynamiskt ändra datauppsättningar. Avvikelseidentifiering identifiering modeller i detta API lärs och modeller justerade automatiskt från både historiska och realtid data.

-   **Proaktiv och tillämplig:** långsam trend och förändring av identifiering kan användas för tidig avvikelseidentifiering. Tidig onormal signaler identifieras kan användas för att dirigera människor att undersöka och agera på problemområden.  Dessutom rotorsak analys modeller och aviseringar verktyg kan utvecklas ovanpå detta avvikelseidentifiering API-tjänsten.

Avvikelseidentifiering API är en effektiv lösning för en mängd olika scenarier som tjänstens hälsa & KPI övervakning, IoT, övervakning av programprestanda och övervakning av nätverkstrafik. Här följer några vanliga scenarier där detta API kan vara användbart:
- IT-avdelningar måste verktyg för att spåra händelser, felkoden, användning loggen och prestanda (CPU, minne och så vidare) inom rimlig tid.

-   Online commerce platser vill spåra aktiviteterna för kunden, sidvisningar, klick och så vidare.

-   Verktyget företag vill spåra användningen av vattenstämplar, gas, elektricitet och andra resurser.

-   Lokal/byggnad hanteringstjänster vill övervaka temperatur, fukt, trafik och så vidare.

-   IoT/tillverkare vill använda sensordata i tidsserien flödet för övervakarens arbete, kvalitet och så vidare.

-   Leverantörer, till exempel call Center måste kunna övervaka tjänsten begäran trend, volym incidenter, vänta kölängd och så vidare.

-   Analytics affärsgrupper vill övervaka affärsverksamhet (till exempel försäljning volym, våra kunder, priser) onormal flytt i realtid.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) är en kritisk komponent i Microsoft Cloud Security-stacken. Det är en heltäckande lösning som kan hjälpa din organisation när du flyttar till dra full nytta av molnapparnas potential, men tänk kontrollen med ökad insyn i aktiviteten. Det hjälper även öka skydda viktiga data i molnappar.

Med verktyg som hjälper Upptäck skugg-IT, utvärdera risker, genomdriva principer, undersöka aktiviteter och stoppa hot kan kan din organisation säkrare flytta till molnet samtidigt som kontrollen av kritiska data.

<table style="width:100%">
 <tr>
   <td>Utforska</td>
   <td>Upptäck skugg-IT med Cloud App Security. Få insyn genom att identifiera appar, aktiviteter, användare, data och filer i din molnmiljö. Identifiera tredjepartsprogram som är anslutna till molnet.</td>
 </tr>
 <tr>
   <td>Undersök</td>
   <td>Undersök dina molnappar med hjälp av datautredningsverktyg för att djupdykning i riskfyllda appar, specifika användare och filer i nätverket. Hitta mönster i data som samlas in från molnet. Generera rapporter för molnövervakning.</td>

 </tr>
 <tr>
   <td>Kontroll</td>
   <td>Minska riskerna genom att skapa principer och varningar för att uppnå full kontroll över molntrafiken i nätverket. Använda Cloud App Security för att migrera användarna till säkra, sanktionerade molnappar.</td>

 </tr>
 <tr>
   <td>Skydda</td>
   <td>Använd Cloud App Security att sanktionera eller förbjuda program, tillämpa skydd mot dataförlust, behörighet och dela och skapa anpassade rapporter och aviseringar.</td>

 </tr>
 <tr>
   <td>Kontroll</td>
   <td>Minska riskerna genom att skapa principer och varningar för att uppnå full kontroll över molntrafiken i nätverket. Använda Cloud App Security för att migrera användarna till säkra, sanktionerade molnappar.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integrerar synlighet i molnet genom att

-   Via Cloud Discovery för att mappa och identifiera molnmiljön och molnapparna använder organisationen.


-   Sanktionering och förbjuder appar i molnet.



-   Med hjälp av enkelt att distribuera anslutningsverktyg som utnyttjar providerns API: er, för synlighet och styrning av appar som du ansluter till.

-   Hjälper att du har kontinuerlig kontroll av inställningen och kontinuerligt finjustera principer.

För att samla in data från dessa källor en Cloud App Security avancerad analys av data. Den omedelbart aviserar om avvikande aktiviteter och du får djup insyn i din molnmiljö. Du kan konfigurera en princip i Cloud App Security och använda den för att skydda allt innehåll i molnmiljön.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Tredjeparts-ATD funktioner via Azure Marketplace

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brandvägg för webbaserade program kontrollerar om inkommande web trafik och block SQL injektionerna globala webbplatsskript, skadlig kod överföringar & DDoS-program och andra attacker riktade mot webbaserade program. Den kontrollerar också svar från backend-webbservrar för Data går förlorade förebyggande (DLP). Integrerad access control motorn kan administratörer skapa detaljerade principer för åtkomstkontroll för autentisering, auktorisering och redovisning, som ger organisationer stark autentisering och användarkontroll.

**Visar:**
-   Identifierar och blockerar SQL injektioner globala webbplatsskript, skadlig kod överföringar, DDoS-program eller andra attacker mot ditt program.

-   Autentisering och åtkomstkontroll.

-   Skannar utgående trafik om du vill identifiera känsliga data och kan maskera eller blockera information läcks.

-   Påskyndar leveransen av web application innehåll, med hjälp av funktioner, till exempel cachelagring, komprimering och andra trafik optimeringar.

Följande är exempel på webbprogrammet brandväggar som är tillgängliga i Azure Marketplace:

[Barracuda Brandvägg för webbaserade program, Brocade virtuella Brandvägg för webbaserade program (Brocade vWAF), Imperva SecureSphere och det ThreatSTOP IP-brandväggen.](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf)

## <a name="next-steps"></a>Nästa steg

- [Identifieringsfunktioner i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Funktioner för avancerad identifiering av Azure Security Center hjälper dig för att identifiera active hot målobjekt för Microsoft Azure-resurser och ger dig de insikter som behövs för att svara snabbt.

- [Hotidentifiering för Azure SQL-databas](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Azure SQL Database-Hotidentifiering hjälpt adressen synpunkter om potentiella hot mot sin databas.
