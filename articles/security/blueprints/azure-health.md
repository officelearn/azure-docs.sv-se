---
title: Azure Health Analytics Blueprint
description: Vägledning för distribution av en HIPAA/HITRUST hälsotillstånd Analytics skiss
services: security
documentationcenter: na
author: RajeevRangappa
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.topic: article
ms.date: 07/23/2018
ms.author: rarangap
ms.openlocfilehash: 5f23435a43d139ecb9f20e5036124f175b1830c9
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225373"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Azure-säkerhet och efterlevnad skissen - HIPAA/hitrust – hälsodata och AI

## <a name="overview"></a>Översikt

**Den Azure Blueprint för säkerhet och efterlevnad – HIPAA/hitrust – hälsodata och AI erbjuder en nyckelfärdig distribution av en Azure PaaS och IaaS-lösning som visar hur du kan mata in, lagra, analysera, interagera, identitet och distribuera lösningar med hälsodata samtidigt på ett säkert sätt att kunna uppfylla efterlevnadskrav i branschen. Skissen hjälper dig att påskynda molnanvändning och belastningen för kunder med data som har reglerats.**

Den Azure Blueprint för säkerhet och efterlevnad – HIPAA/hitrust – hälsodata och AI skiss tillhandahåller verktyg och vägledning för att distribuera ett säkert, Health Insurance Portability och Accountability Act (HIPAA) och Health Information förtroende Alliance (HITRUST) redo Platform as a service (PaaS)-miljö för att mata in, lagra, analysera och interagera med personlig och icke-personlig medicinska journaler i en säker och flera nivåer molnmiljö som har distribuerats som en lösning för slutpunkt till slutpunkt. 

IaaS-lösningen visar hur du migrerar en lokal SQL-baserad lösning till Azure och för att implementera en privilegierad åtkomst arbetsstation (PAW) för att på ett säkert sätt hantera molnbaserade tjänster och lösningar. IaaS SQL Server-databasen läggs potentiella experimentering data importeras till en SQL IaaS-VM och att virtuell dator använder MSI-autentiserad åtkomst för att interagera med en SQL Azure PaaS-tjänst. Båda dessa presenterar en gemensam Referensarkitektur och har utformats för att förenkla användningen av Microsoft Azure. Den här tillhandahållna arkitekturen illustrerar en lösning för att uppfylla behoven hos organisationer som vill ha en molnbaserad metod för att minska belastningen och kostnaden för distributionen.

![](images/components.png)

Lösningen har utformats för att använda en exempeldatauppsättning som formaterats med snabb Healthcare samverkan resurser (FHIR), en globalt standard för informationsutbyte hälsovård elektroniskt, och lagra den på ett säkert sätt. Kunder kan sedan använda Azure Machine Learning Studio för att dra nytta av kraftfulla business intelligence-verktyg och analyser för att granska förutsägelser som görs på exempeldata. Som ett exempel på vilken typ av experimentet som Azure Machine Learning Studio kan underlätta innehåller skissen en exempeldatauppsättning, skript och verktyg för att förutsäga längden på en patients stanna i en sjukhusanläggning. 

Den här skissen är avsedd att fungera som en modulbaserad grund för kunder enligt deras specifika krav, utveckla nya Azure Machine learning-experiment för att lösa båda klinisk och driftsmässig användningsfall. Det är avsett att vara skyddade och kompatibla när de distribueras; dock ansvarar kunderna för att konfigurera roller på rätt sätt och implementera eventuella ändringar. Observera följande:

-   Denna skiss tillhandahåller en baslinje för att hjälpa kunder som använder Microsoft Azure i en HITRUST och HIPAA-miljö.

-   Även om skissen har utformats för att ligga i linje med HIPAA och HITRUST (via Common Security Framework--CSF), ska den inte betraktas kompatibel tills certifierats av en extern granskare per HIPAA och HITRUST certifieringskrav.

-   Kunderna ansvarar för att utföra lämpliga säkerhets- och granskningar av alla lösningar som skapats med den här grundläggande arkitektur.

## <a name="deploying-the-automation"></a>Distribuera automation

- För att distribuera lösningen, följer du instruktionerna i den [distributionsvägledning](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md). 

- Titta på den här för en snabb överblick över hur den här lösningen fungerar [video](https://aka.ms/healthblueprintvideo) hon förklarar och visar dess distribution.

- Vanliga frågor och svar finns i den [vanliga frågor och svar](https://aka.ms/healthblueprintfaq) vägledning.

-   **Arkitekturdiagram för.** Diagrammet visar referensarkitekturen används för skissen och exemplet användningsfallsscenario.

-   [IaaS-tillägget](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/README%20IaaS.md) den här lösningen visar hur du migrerar en lokal SQL-baserad lösning till Azure och för att implementera en privilegierad åtkomst arbetsstation för att på ett säkert sätt hantera molnbaserade tjänster och lösningar. 

## <a name="solution-components"></a>Lösningskomponenter


Grundläggande arkitekturen består av följande komponenter:

-   **[Hotmodell](https://aka.ms/healththreatmodel)**  en omfattande hotmodell har tillhandahållits i tm7 format för användning med den [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168), som visar komponenterna i lösningen, data som flödar mellan dem och förtroende gränser. Modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du utvecklar Machine Learning Studio komponenter eller andra ändringar.

-   **[Matris för implementering av kunden](https://aka.ms/healthcrmblueprint)**  A Microsoft Excel-arbetsbok innehåller relevanta HITRUST kraven och förklarar hur Microsoft och kunden ansvarar för att uppfylla var och en.

-   **[Granska hälsa.](https://aka.ms/healthreviewpaper)** Lösningen har granskat av Coalfire systems, Inc. Hälsotillstånd efterlevnad (HIPAA och HITRUST) och vägledning för implementering ger en granskare\'s genomgång av lösningen och överväganden för omvandling av modell som en produktionsklar-distribution.

## <a name="architectural-diagram"></a>Arkitekturdiagram


![](images/ra2.png)

## <a name="roles"></a>Roller


Skissen definierar två roller för administrativa användare (operatörer) och tre roller för användare i sjukhuset hanterings- och patientvård. En sjätte roll definieras för en granskare att utvärdera efterlevnad av HIPAA och andra bestämmelser. Azure rollbaserad åtkomstkontroll (RBAC) möjliggör exakt fokuserad åtkomsthantering för varje användare av lösningen via inbyggda och anpassade roller. Se [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](https://docs.microsoft.com/azure/role-based-access-control/overview) och [inbyggda roller för rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) detaljerad information om RBAC, roller och behörigheter.

### <a name="site-administrator"></a>Webbplatsadministratör


Webbplatsadministratören ansvarar för kundens Azure-prenumeration. De styr den övergripande distributionen, men ha inte åtkomst till patientjournaler.

-   Standard-rolltilldelningar: [Ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Anpassade rolltilldelningar: Gäller inte

-   Omfång: Prenumeration

### <a name="database-analyst"></a>Databasen analytiker

Databas-analytiker administrerar SQL Server-instans och databas.
De har ingen åtkomst till patientjournaler.

-   Inbyggda rolltilldelningar: [SQL DB-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [SQL Server-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Anpassade rolltilldelningar: Gäller inte

-   Omfång: ResourceGroup

### <a name="data-scientist"></a>Dataexpert


Data Science fungerar Azure Machine Learning Studio. De kan importera, exportera, och hantera data och köra rapporter. Data Science har tillgång till patientens data, men har inte administratörsbehörighet.

-   Inbyggda rolltilldelningar: [Lagringskontodeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Anpassade rolltilldelningar: Gäller inte

-   Omfång: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>Chief Medical Information Officer (CMIO)


Cmio: N är mellan informatics/teknik och sjukvårdspersonalen inom organisationen. Ansvarsområdet ingår normalt med analytics för att avgöra om resurser allokeras på rätt sätt i organisationen.

-   Inbyggda rolltilldelningar: Ingen

### <a name="care-line-manager"></a>Care Line Manager


Care line manager är direkt inblandad i patientvården.
I den här rollen ingår att övervaka statusen för enskilda patienter och se till att det finns personal för patienternas olika vårdbehov. Care line manager ansvarar för att lägga till och uppdatera patientjournaler.

-   Inbyggda rolltilldelningar: Ingen

-   Anpassade rolltilldelningar: Har behörighet att köra HealthcareDemo.ps1 för att göra båda Patient-åtkomst och utskrivning.

-   Omfång: ResourceGroup

### <a name="auditor"></a>Granskare


Granskaren utvärderar lösningen för kompatibilitet. De har ingen direkt åtkomst till nätverket.

-   Inbyggda rolltilldelningar: [Läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Anpassade rolltilldelningar: Gäller inte

-   Omfång: Prenumeration

## <a name="example-use-case"></a>Exempel användningsfall


Exempel-användningsfall som ingår i den här skissen illustrerar hur skissen kan användas för att aktivera maskininlärning och analys på hälsodata i molnet. Contosoclinic är ett litet sjukhus finns i USA. Sjukhus-nätverket som administratörer ska kunna använda Azure Machine Learning Studio för att bättre förutse längden på en patients stanna vid tidpunkten för tillträde, för att öka effektiviteten och förbättra vårdkvaliteten det kan ge.

### <a name="predicting-length-of-stay"></a>Förutsägelser kring längd på


Användningsfall för exemplet använder Azure Machine Learning Studio för att förutsäga en nyligen släppas in patient längd genom att jämföra medicinska information tas i patientens intag till sammanställda historiska data från föregående patienter.
Skissen innehåller ett stort antal maskerade medicinska journaler att demonstrera funktionerna för utbildning och förutsägande i lösningen. I en Produktionsdistribution använder kunder sina egna poster för att träna lösningen mer exakta förutsägelser som återger unika information om deras miljö, anläggningar och patienter.

### <a name="users-and-roles"></a>Användare och roller


**Platsadministratören--Alex**

*E-postadress: Alex\_SiteAdmin*

Alexs jobb är att utvärdera tekniker som kan minska bördan med att hantera ett lokalt nätverk och minska kostnaderna för hantering. Alex har utvärdera Azure under en viss tid, men har fastnade att konfigurera de tjänster som han behöver för att uppfylla krav på efterlevnad HiTrust att lagra tålamod Data i molnet. Alex har valt Azure hälsotillstånd AI du distribuerar en lösning för efterlevnad färdiga för hälsotillstånd som har åtgärdas kraven för att uppfylla kundens krav för HiTrust.

**Datavetare – Adina**

*E-postadress: Debra\_DataScientist*

Adina ansvarar för att använda och skapa modeller som analyserar medicinska journaler för att ge insikter om patientvård. Adina använder SQL och det statistiska programmeringsspråket R för att skapa sin modeller.

**Databasen analytiker--Danny**

*E-postadress: Danny\_DBAnalyst*

Danny är den viktigaste kontakten för allt om Microsoft SQL Server som lagrar alla Patientdata för Contosoclinic. Danny är en erfaren SQL Server-administratör som har nyligen bekanta dig med Azure SQL Database.

**Chief Medical Information Officer--Caroline**

Caroline samarbetar med Chris Care Line Manager och Adina den dataexpert för att fastställa vilka faktorer som påverkar patientens längd.
Caroline använder förutsägelser från längden av stanna (LOS)-lösning för att avgöra om resurser allokeras på rätt sätt i sjukhuset-nätverk. Till exempel med hjälp av instrumentpanelen i den här lösningen.

**Care Line Manager – Chris**

*E-postadress: Chris\_CareLineManager*

Som personen som direkt ansvarar för att hantera inskrivning och utsläpp på Contosoclinic, Chris använder förutsägelser som genereras av lösningen LOS så att det finns tillräcklig personal att förse patienter försiktighet när de stanna kvar på den anläggning.

**Granskare – Han**

*E-postadress: Han\_granskare*

Han är en certifierade granskare som har erfarenhet granskning för ISO, SOC och HiTrust. Han har anlitat för att granska Contosoclincs nätverk. Han kan granska kundens ansvar matrisen medföljer lösningen för att säkerställa att skissen och LOS lösningen kan användas för lagrar, bearbetar och visa känslig personlig information.


## <a name="design-configuration"></a>Design-konfiguration


Det här avsnittet beskrivs de standardinställningar och säkerhetsåtgärder som är inbyggda i skissen som beskrivs för att:

- **INMATNING** raw datakällor inklusive FHIR-datakälla
- **STORE** känslig information
- **ANALYSERA** och förutsäga resultat
- **INTERAGERA** med resultat och förutsägelser
- **IDENTITET** hantering av lösning
- **SECURITY** aktiverat funktioner


## <a name="identity"></a>IDENTITET 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory och rollbaserad åtkomstkontroll (RBAC)


**Autentisering:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) är Microsofts\'s flera innehavare molnbaserad katalog- och identity management-tjänsten. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare med åtkomst till SQL-databasen.



-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till din organisations identiteter, och undersöker misstänkta incidenter och vidtar lämpliga åtgärder du kan åtgärda detta.

-   [Azure rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/role-assignments-portal) aktiverar exakt fokuserade åtkomsthantering för Azure. Prenumerationsåtkomst är begränsad till administratör för prenumerationen och Azure Key Vault-åtkomst är begränsad till platsadministratören. Starka lösenord (12 tecken minsta med minst en övre/lägre bokstav, siffra och specialtecken) krävs.

-   Multifaktorautentisering stöds när växeln - enableMFA aktiveras under distributionen.

-   Lösenord upphör att gälla efter 60 dagar när växeln - enableADDomainPasswordPolicy har aktiverats under distributionen.

**Roller:**

-   Lösningen använder [inbyggda roller](/azure/role-based-access-control/built-in-roles) att hantera åtkomst till resurser.

-   Alla användare tilldelas specifika inbyggda roller som standard.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Data som lagras i Key Vault omfattar:

    -   Nyckel för Application insight
    -   Patientens dataåtkomst för Storage-nyckel
    -   Patientens anslutningssträng
    -   Patientdata tabellnamn
    -   Azure ML Web Service-slutpunkt
    -   Azure ML Service API-nyckel

-   Avancerade åtkomstprinciper har konfigurerats på basis av behöver
-   Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter
-   Alla nycklar och hemligheter i Key Vault ha ett förfallodatum
-   Alla nycklar i Key Vault är skyddade av HSM \[nyckeltypen = HSM-skyddade 2048-bitars RSA-nyckel\]
-   Alla användare/identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll (RBAC)
-   Program delar inte ett Key Vault om de litar på varandra och de behöver åtkomst till samma hemligheterna vid körning
-   Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
-   Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs

## <a name="ingest"></a>MATA IN 

### <a name="azure-functions"></a>Azure Functions
Lösningen har utformats för att använda [Azure Functions](/azure/azure-functions/) att bearbeta exempel längden på Håll data som används i analytics-demo. Tre funktioner i funktionerna har skapats.

**1. Massimport av kundinformation data phi**

När du använder demo-skriptet. . \\HealthcareDemo.ps1 med den **BulkPatientAdmission** växla som beskrivs i **distribuera och köra demon** följande process-pipelinen körs:
1. **Azure Blob Storage** -Patientdata CSV-filen exempel laddas upp till storage
2. **Event Grid** -händelse publicerar data till Azure-funktion (massimport - blob-händelse)
3. **Azure-funktion** - utför bearbetningen och lagrar data i SQL-lagring med hjälp av funktionen säker - händelse (Skriv; blob url)
4. **SQL DB** – databasarkiv för patienternas Data med hjälp av taggar för klassificering och ML-processen har startats för att göra träningsexperimentet.

![](images/dataflow.png)

Azure-funktionen har dessutom utformats för att läsa och skydda avsedda känsliga data i datauppsättningen exemplet med hjälp av följande taggar:
- dataProfile = > ”ePHI”
- ägare = > \<plats Admin UPN\>
- miljö = > ”Pilot”
- avdelning = > ”globala ekosystemet” de taggning har tillämpats på provdatauppsättning där patient ”namn” identifierades i klartext.

**2. Åtkomst av nya patienter**

När du använder demo-skriptet. . \\HealthcareDemo.ps1 med den **BulkPatientadmission** växla som beskrivs i **distribuera och köra demon** följande process-pipelinen körs: ![](images/securetransact.png)
**1. Azure-funktion** utlöses och funktionen begäranden för en [ägartoken](/rest/api/) från Azure Active directory.

**2. Key Vault** begärda för en hemlighet som är kopplad till begärd token.

**3. Azure-roller** verifiera begäran och auktorisera åtkomstbegäran till Key Vault.

**4. Key Vault** returnerar hemlighet, i det här fallet SQL DB-anslutningssträngen.

**5. Azure-funktion** använder anslutningssträngen för att säkert ansluta till SQL-databas och fortsätter ytterligare bearbetning för att lagra ePHI data.

För att uppnå lagring av data, implementerades ett gemensamt API-schema följa snabbt Healthcare samverkan-resurser (FHIR, uttalas fire). Funktionen angavs följande FHIR exchange element:

-   [Patientens schemat](https://www.hl7.org/fhir/patient.html) innehåller ”som” information om hur en patient.

-   [Fjärrvisning schemat](https://www.hl7.org/fhir/observation.html) omfattar centrala elementet inom hälsovården, används för att stödja diagnostik, övervaka förloppet, fastställa baslinjer och mönster och även fånga demografiska egenskaper. 

-   [Om du stöter på schemat](https://www.hl7.org/fhir/encounter.html) täcker typerna av möten, till exempel ambulatory, nödfall, hem hälsa och vård virtuella möten.

-   [Villkoret schemat](https://www.hl7.org/fhir/condition.html) innehåller detaljerad information om ett villkor, problem, diagnos, eller andra händelsen, situation, problemet eller kliniska begrepp som har ökat till en nivå av problem.  



### <a name="event-grid"></a>Event Grid


Lösningen har stöd för Azure Event Grid, en enskild tjänst för att hantera routning av alla händelser från valfri källa till valfritt mål tillhandahåller:

-   [Säkerhet och autentisering](/azure/event-grid/security-authentication)

-   [Rollbaserad åtkomstkontroll](/azure/event-grid/security-authentication#management-access-control) för olika hanteringsåtgärder, till exempel visa en lista över prenumerationer på händelser, skapa nya och nyckelgenerering

-   Granskning

## <a name="store"></a>STORE 

### <a name="sql-database-and-server"></a>SQL Database och Server 


-   [Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) ger i realtid kryptering och dekryptering av data som lagras i Azure SQL Database med hjälp av en nyckel som lagras i Azure Key Vault.

-   [Sårbarhetsbedömning för SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) är ett enkelt sätt att konfigurera verktyg som kan upptäcka, spåra och åtgärda säkerhetsrisker i databasen.

-   [SQL Database Threat Detection](/azure/sql-database/sql-database-threat-detection) aktiverat.

-   [SQL Database Auditing](/azure/sql-database/sql-database-auditing) aktiverat.

-   [SQL Database-mått och diagnostikloggning](/azure/sql-database/sql-database-metrics-diag-logging) aktiverat.

-   [Brandväggsregler för Server - och databasnivå](/azure/sql-database/sql-database-firewall-configure) har blivit strängare än tidigare.

-   [Alltid krypterad kolumner](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) används för att skydda patientens första mellersta och sista namnen.
    Dessutom använder kolumnen databaskryptering också Azure Active Directory (AD) för att autentisera program till Azure SQL Database.

-   Åtkomst till SQL Database och SQL Server har konfigurerats enligt principen om lägsta behörighet.

-   Endast nödvändiga IP-adresser tillåts åtkomst via brandväggen för SQL.

### <a name="storage-accounts"></a>Lagringskonton


-   [Data i rörelse överförs med hjälp av TLS/SSL endast](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Anonym åtkomst tillåts inte för behållare.

-   Varningsregler är konfigurerade för att spåra anonym aktivitet.

-   HTTPS krävs för att komma åt resurser för storage-kontot.

-   Autentiseringsdata för begäran loggas och övervakas.

-   Data i Blob storage krypteras i vila.

## <a name="analyze"></a>ANALYSERA

### <a name="machine-learning"></a>Machine Learning


- [Loggning är aktiverat](/azure/machine-learning/studio/web-services-logging) för Machine Learning Studio-webbtjänster.
- Med hjälp av [Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio) kräver utvecklingen av experiment som ger möjlighet att förutsäga till en uppsättning av lösningen.

## <a name="security"></a>SÄKERHET

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) ger en central vy över säkerhetsläget hos alla dina Azure-resurser. På ett ögonblick, kan du kontrollera att rätt säkerhetskontroller är på plats och korrekt konfigurerad och att du snabbt kan identifiera eventuella resurser som kräver uppmärksamhet. 

- [Azure Advisor](/azure/advisor/advisor-overview) är en personligt anpassad molnrådgivare som hjälper dig att följa bästa praxis för att optimera dina Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) är en utökningsbar tjänst Application Performance Management (APM) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Den identifierar prestandaavvikelser. Den inkluderar kraftfulla analysverktyg för att hjälpa dig diagnosticera problem och förstå vad användare faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

### <a name="azure-alerts"></a>Azure-aviseringar
- [Aviseringar](/azure/azure-monitor/platform/alerts-metric) erbjuder en metod för att övervaka Azure-tjänster så att du kan konfigurera villkor för över data. Aviseringar tillhandahåller även meddelanden när en varningsvillkor matchar övervakningsdata.

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
[Azure Monitor-loggar](/azure/operations-management-suite/operations-management-suite-overview) är en samling hanteringstjänster.

-   Arbetsytan har aktiverats för Security Center

-   Arbetsytan är aktiverat för övervakning av arbetsbelastning

-   Arbetsbelastningen övervakning är aktiverad för:

    -   Identitet och åtkomst

    -   Säkerhet och granskning

    -   Azure SQL DB Analytics

    -   [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics) lösning

    -   Key Vault-analys

    -   Spårning av ändringar

-   [Application Insights-Anslutningsapp (förhandsversion)](/azure/log-analytics/log-analytics-app-insights-connector) är aktiverat

-   [Aktivitetslogganalys](/azure/log-analytics/log-analytics-activity) är aktiverat
