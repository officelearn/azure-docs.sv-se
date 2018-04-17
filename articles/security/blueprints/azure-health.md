---
title: Azure Health Analytics plan
description: Vägledning för distribution av en HIPAA/HITRUST hälsa Analytics plan
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: simorjay
ms.openlocfilehash: 6bc2c63e75e9c750da852b77e4486666c3fc8561
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Azure-säkerhet och efterlevnad modell - HIPAA/HITRUST hälsa Data och AI

## <a name="overview"></a>Översikt

**Azure-säkerhet och efterlevnad modell - HIPAA/HITRUST hälsa Data och AI erbjuder en nyckelfärdig distribution av en Azure PaaS-lösning för att demonstrera hur du på ett säkert sätt mata in, lagra, analysera och interagera med hälsa data när kunna uppfylla branschen kompatibilitet krav. Modell kan påskynda molnet införande och användning för kunder med data som regleras.**

Azure-säkerhet och efterlevnad modell - HIPAA/HITRUST hälsa Data och AI utkast tillhandahåller verktyg och vägledning för att distribuera en säker, Health Insurance Portability och Accountability Act (HIPAA) och hälsotillstånd Information förtroende Alliance (HITRUST) redo plattform som en tjänst (PaaS) miljö för vill föra in, lagra, analysera och interagerar med personlig och icke-personlig medicinska poster i en säker och flera nivåer molnmiljö bör distribueras som en lösning för slutpunkt till slutpunkt. Den innehåller en gemensam Referensarkitektur och förenklar införandet av Microsoft Azure. Den här angivna arkitekturen illustrerar en lösning som passar för organisationer som vill ha en molnbaserad metod för att minska belastningen och kostnaden för distribution.

![](images/components.png)

Lösningen är utformad för att använda data exempeldata formaterats med snabb hälsovård samverkan resurser (FHIR), en global standard för utbyte sjukvården information elektroniskt och lagra den på ett säkert sätt. Användare kan sedan använda Azure Machine Learning dra nytta av kraftfulla business intelligence-verktyg och analyser för att granska förutsägelser som görs på exempeldata. Som ett exempel på vilken typ av experimentet Azure Machine Learning kan underlätta innehåller du vilken en exempeldatamängd, skript och verktyg för att förutsäga tid ett tålamod i en sjukhus anläggning. 

Det här utkastet är avsedd att fungera som en modulär grund för kunder att justera sina specifika krav, utveckla nya Azure Machine learning experiment för att lösa båda kliniska och operativa exempel på scenarier. Den är avsedd att vara skyddade och kompatibla när distribuerade; dock ansvarar kunder för att konfigurera roller på rätt sätt och genomföra alla ändringar. Observera följande:

-   Det här utkastet ger en baslinje för att hjälpa kunderna att använda Microsoft Azure i en HITRUST och HIPAA-miljö.

-   Även om modell har utformats så att de justeras med HIPAA och HITRUST (via det vanliga säkerhetsramverk--CSF), ska den inte betraktas kompatibel tills certifierad av en extern granskare per HIPAA och HITRUST certifieringskraven.

-   Kunder ansvarar för att utföra lämpliga säkerhets- och granskning av någon lösning som skapats med denna grundläggande arkitektur.

## <a name="deploying-the-automation"></a>Distribuera automatisering

- Om du vill distribuera lösningen, följer du anvisningarna i vägledning för distribution. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

För en snabb överblick över hur den här lösningen fungerar, titta på den här [video](https://aka.ms/healthblueprintvideo) förklarar och visar dess distribution.

- Vanliga frågor och svar hittar du i den [vanliga frågor och svar](https://aka.ms/healthblueprintfaq) vägledning.

-   **Arkitekturdiagram för.** Diagrammet visar den referens för arkitekturen för modell och exemplet använder Fallstudie.

-   **Distributionsmallar**. I den här distributionen [Azure Resource Manager-mallar](/azure/azure-resource-manager/resource-group-overview#template-deployment) används för att automatiskt distribuera komponenterna i arkitekturen i Microsoft Azure genom att ange konfigurationsparametrar under installationen.

-   **[Automatisk distribution skript](https://aka.ms/healthblueprintdeploy)**. Dessa skript att distribuera lösningen. Skripten består av:


-   En installation av modulen och [global administratör](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) installationsskriptet används för att installera och kontrollera att PowerShell-moduler som krävs och global administratörsroller är korrekt konfigurerade. 
-   En installation av PowerShell-skript används för att distribuera lösningen som tillhandahålls via en .zip-fil som innehåller en förskapad demo-funktioner.

## <a name="solution-components"></a>Lösningskomponenter


Grundläggande arkitektur består av följande komponenter:

-   **[Hotmodell](https://aka.ms/healththreatmodel)**  en omfattande hotmodell har tillhandahållits i tm7 format för användning med den [hot Modeling verktyget](https://www.microsoft.com/en-us/download/details.aspx?id=49168), visar komponenter i lösningen data som flödar mellan dem och förtroende gränser. Modellen hjälper kunder att förstå punkterna i risken i system-infrastruktur när du utvecklar machine learning komponenter eller andra ändringar.

-   **[Kunden implementering matrisen](https://aka.ms/healthcrmblueprint)**  en Microsoft Excel-arbetsbok innehåller relevant HITRUST kraven och förklarar hur Microsoft och kunden ansvarar för att uppfylla var och en.

-   **[Granska hälsa.](https://aka.ms/healthreviewpaper)** Lösningen granskades av Coalfire systems, Inc. Hälsotillstånd kompatibiliteten (HIPAA och HITRUST) och vägledning för implementering ger en granskare\'s granskning av lösningen och överväganden för att överföra modell som en produktionsklara-distribution.

# <a name="architectural-diagram"></a>Arkitekturdiagram


![](images/refarch.png)

## <a name="roles"></a>Roller


Modell definierar två roller för administrativa användare (operatörer) och tre användarroller i sjukhus hantering och vård. En sjätte roll har definierats för en granskare att utvärdera kompatibiliteten med HIPAA och andra föreskrifter. Azure rollbaserad åtkomstkontroll (RBAC) aktiverar exakt fokuserad åtkomsthantering för varje användare av lösningen via inbyggda och anpassade roller. Se [Kom igång med rollbaserad åtkomstkontroll i Azure portal](https://docs.microsoft.com/azure/role-based-access-control/overview) och [inbyggda roller för rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) detaljerad information om RBAC, roller och behörigheter.

### <a name="site-administrator"></a>Webbplatsens administratör


Webbplatsadministratören ansvarar för kundens Azure-prenumeration. De styr den övergripande distributionen, men har inte behörighet att patientjournaler.

-   Standard rolltilldelningar: [ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Anpassade rolltilldelningar: ej tillämpligt

-   Omfång: prenumeration

### <a name="database-analyst"></a>Databasen analytiker

Databasen analysen administrerar SQL Server-instans och databas.
De har inte behörighet att patientjournaler.

-   Inbyggda rolltilldelningar: [SQL DB-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [SQL Server-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Anpassade rolltilldelningar: ej tillämpligt

-   Omfång: ResourceGroup

 ### <a name="data-scientist"></a>Forskare data


Forskare data fungerar Azure Machine Learning-tjänsten. De kan importera, exportera, hantera data och köra rapporter. Forskare data har åtkomst till Patientdata, men har inte administratörsbehörighet.

-   Inbyggda rolltilldelningar: [Storage-konto deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Anpassade rolltilldelningar: ej tillämpligt

-   Omfång: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>Chefen medicinska Information som (CMIO)


CMIO överbryggar gapet mellan informatics-teknik och vårdpersonal i en sjukvårdsorganisation. Sina uppgifter inkluderar vanligtvis med analytics för att avgöra om resurser som tilldelas på rätt sätt i organisationen.

-   Inbyggda rolltilldelningar: ingen

### <a name="care-line-manager"></a>Hand Linjehanteraren


Linjehanteraren försiktighet är direkt inblandade med försiktighet patienter.
I den här rollen ingår att övervaka statusen för enskilda patienter och se till att det finns personal för patienternas olika vårdbehov. Försiktighet linjehanteraren ansvarar för att lägga till och uppdatera patientjournaler.

-   Inbyggda rolltilldelningar: ingen

-   Anpassade rolltilldelningar: har privilegium för att köra HealthcareDemo.ps1 gör både tålamod åtkomst och tömning.

-   Omfång: ResourceGroup

### <a name="auditor"></a>Granskare


Granskaren utvärderar lösning för kompatibilitet. De har ingen direkt åtkomst till nätverket.

-   Inbyggda rolltilldelningar: [läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Anpassade rolltilldelningar: ej tillämpligt

-   Omfång: prenumeration

## <a name="example-use-case"></a>Exempel användningsfall


Exempel användningsfall som ingår i det här utkastet illustrerar hur du vilken kan användas för att aktivera machine learning och analyser på hälsa data i molnet. Contosoclinic är en liten sjukhus finns i USA. Nätverksadministratörer sjukhus vill använda Azure Machine Learning bättre förutsäga tid ett tålamod vid tidpunkten för tillträde för att öka effektiviteten operativa arbetsbelastning och förbättra kvaliteten på försiktighet som den kan ge.

### <a name="predicting-length-of-stay"></a>Förutsäga tid


Exempel Användarscenario använder Azure Machine Learning för att förutsäga ett nyligen släppas in tålamod tid genom att jämföra informationen medicinska vidtas på patient intag att aggregerade historiska data från föregående patienter.
Modell innehåller en stor mängd anonymiserade medicinska poster för att demonstrera utbildning och förutsägbara funktionerna i lösningen. I en Produktionsdistribution använder kunder sina egna poster för att träna lösning för mer korrekta förutsägelser reflektion unik information till deras miljö och verksamhet patienter.

### <a name="users-and-roles"></a>Användare och roller


**Webbplatsens administratör--Alex**

*E-post: Alex\_SiteAdmin*

Alexs jobb är att utvärdera tekniker som kan minska belastningen för att hantera ett lokalt nätverk och minska kostnaderna för hantering. Alex har utvärdera Azure under en viss tid, men har fastnade att konfigurera de tjänster som han måste uppfylla krav på efterlevnad HiTrust att lagra tålamod Data i molnet. Alex har valt Azure hälsa AI du distribuerar en lösning för kompatibilitet redo hälsa som har beskrivs kraven för att uppfylla kraven för HiTrust kunden.

**Data forskare--Adina**

*E-post: Adina\_DataScientist*

Adina är ansvarig för och skapa modeller som analyserar medicinska poster för att ge insikter om vård. Adina använder SQL och R statistiska programmeringsspråk för att skapa sitt modeller.

**Analytiker--Danny-databas**

*E-post: Danny\_DBAnalyst*

Danny är den huvudsakliga kontakten för något om Microsoft SQL Server som lagrar alla Patientdata för Contosoclinic. Danny är en erfaren SQL Server-administratör som har nyligen bekanta dig med Azure SQL Database.

**Chefen medicinska Information som--Caroline**

Caroline arbetar med Chris Linjehanteraren hand och Adina forskare Data för att fastställa vilka faktorer som påverkar patient tid.
Caroline använder förutsägelser från längden av stanna (LOS) lösning för att avgöra om resurser som tilldelas på rätt sätt i sjukhus-nätverk. Till exempel med hjälp av instrumentpanelen finns i den här lösningen.

**Hand Linjehanteraren--Chris**

*E-post: Chris\_CareLineManager*

Som enskilda direkt ansvarar för att hantera patient åtkomst och utsläpp på Contosoclinic, Chris använder förutsägelser som genererats av LOS lösningen så att det finns tillräcklig personal att förse patienter försiktighet medan de kvar i den anläggning.

**Granskare--Han**

*E-post: Han\_granskare*

Han är en certifierade granskare som har erfarenhet av ISO, SOC och HiTrust granskning. Han har anlitat för att granska Contosoclincs nätverk. Han kan granska kunden ansvar matrisen med lösningen för att säkerställa att utkast och LOS lösningen kan användas att lagra processen och visa känslig personlig information.


# <a name="design-configuration"></a>Design-konfiguration


Det här avsnittet beskrivs standardkonfigurationer och säkerhetsåtgärder som är inbyggda i den modell som anges till:

- **INFOGNINGS** raw datakällor inklusive FHIR-datakälla
- **STORE** känslig information
- **ANALYSERA** och förutsäga resultat
- **INTERAGERA** med resultat och förutsägelser
- **IDENTITY** hantering av lösning
- **SÄKERHET** aktiverat funktioner


## <a name="identity"></a>IDENTITET 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory och rollbaserad åtkomstkontroll (RBAC)


**Autentisering:**

-   [Azure Active Directory (AD Azure)](https://azure.microsoft.com/services/active-directory/) är Microsoft\'s flera innehavare molnbaserad katalog och identity management-tjänsten. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare med åtkomst till SQL-databasen.



-   Tillämpningen utförs autentiseringen med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory-identitetsskydd](/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar din organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör din organisations identiteter och undersöker misstänkta incidenter och vidtar lämpliga åtgärder som kan lösas.

-   [Azure rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/role-assignments-portal) aktiverar exakt fokuserad åtkomsthantering för Azure. Prenumerationen åtkomst begränsas till administratör för prenumeration och Azure Key Vault åtkomst är begränsad till webbplatsens administratör. Starka lösenord (12 tecken minsta med minst en övre/nedre bokstav, siffra och specialtecken) krävs.

-   Multifaktorautentisering stöds när växeln - enableMFA aktiveras under distributionen.

-   Lösenord upphör att gälla efter 60 dagar när växeln - enableADDomainPasswordPolicy aktiveras under distributionen.

**Roller:**

-   Lösningen gör att användning av [inbyggda roller](/azure/role-based-access-control/built-in-roles) att hantera åtkomst till resurser.

-   Alla användare tilldelas specifika inbyggda roller som standard.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Data som lagras i Nyckelvalvet innehåller:

    -   Insight tangent
    -   Patient Data lagringsåtkomst nyckel
    -   Patient anslutningssträngen
    -   Patientdata tabellnamn
    -   Azure ML-webbtjänstens slutpunkt
    -   Azure ML Service API-nyckel

-   Avancerade åtkomstprinciper konfigureras på grundval av behov
-   Key Vault åtkomstprinciper har definierats med minsta behörighet till nycklar och hemligheter
-   Alla nycklar och hemligheter i Nyckelvalvet ha ett förfallodatum
-   Alla nycklar i Key Vault är skyddade av HSM \[nyckeltypen = HSM-skyddad 2048-bitars RSA-nyckel\]
-   Alla användare/identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll (RBAC)
-   Program har inte ett Nyckelvalv om de litar på varandra och de behöver åtkomst till samma hemligheter vid körning
-   Diagnostik-loggarna för Key Vault aktiveras med en kvarhållningsperiod på minst 365 dagar.
-   Tillåtna kryptografiska åtgärder för nycklarna är begränsade till de som krävs

## <a name="ingest"></a>MATA IN 

### <a name="azure-functions"></a>Azure Functions
Lösningen har utformats för att använda [Azure Functions](/azure/azure-functions/) bearbeta stanna data som används i demonstrationen analytics exempel längd. Tre funktionerna i funktionerna som har skapats.

**1. Massimport av kundinformation data fi**

När du använder skriptet demo. . \\HealthcareDemo.ps1 med den **BulkPatientAdmission** växla som beskrivs i **distribuera och köra demonstrationen** utförs följande process-pipelinen:
1. **Azure Blob Storage** -Patient CSV-filen datasampel har överförts till lagring
2. **Händelsen rutnätet** -händelse publicerar data till Azure-funktion (massimport - blob-händelse)
3. **Azure funktionen** - utför bearbetning och lagrar data i SQL-lagring med hjälp av funktionen säker - händelse (Skriv; blob-url)
4. **SQL DB** - databasarkiv för tålamod Data med hjälp av taggar för klassificering och ML-processen har inletts sköta utbildning experimentet.

![](images/dataflow.png)

Dessutom har funktionen azure utformats för att läsa och skydda avsedda känsliga data i datauppsättningen exemplet med hjälp av följande taggar:
- dataProfile = > ”ePHI”
- ägare = > \<plats Admin UPN\>
- miljö = > ”Pilot”
- avdelning = > ”globala ekosystemet” taggarna har tillämpats på datauppsättningen exempel där tålamod ”namn” identifierades i klartext.

**2. Åtkomstkontroll nya patienter**

När du använder skriptet demo. . \\HealthcareDemo.ps1 med den **BulkPatientadmission** växla som beskrivs i **distribuera och köra demonstrationen** utförs följande process-pipelinen: ![](images/securetransact.png) 
 **1. Azure funktionen** utlöses och funktionen begäranden för en [ägartoken](/rest/api/) från Azure Active directory.

**2. Key Vault** begärda för en hemlighet som är kopplad till den begärda token.

**3. Azure roller Validera begäran och godkänna begäran om åtkomst till Nyckelvalvet.

**4. Key Vault** returnerar hemlighet, i det här fallet SQL DB anslutningssträng.

**5. Azure funktionen** använder anslutningssträngen för att ansluta säkert till SQL-databas och fortsätter ytterligare bearbetning för att lagra ePHI data.

För att uppnå lagring av data, implementerades ett gemensamt schema API efter snabb hälsovård samverkan resurser (FHIR, uttalas brand). Funktionen angavs följande FHIR exchange element:

-   [Patient schemat](https://www.hl7.org/fhir/patient.html) beskrivs ”som” hur en öppen.

-   [Anmärkning schemat](https://www.hl7.org/fhir/observation.html) omfattar centrala elementet i hälsovård, används för att stödja diagnostik, övervaka förloppet, fastställa baslinjer och mönster och även avbilda demografisk egenskaper. 

-   [Påträffar schemat](https://www.hl7.org/fhir/encounter.html) omfattar typerna av påträffas, till exempel ambulatory, i nödfall, hem hälsa och vård virtuella möten.

-   [Villkoret schemat](https://www.hl7.org/fhir/condition.html) innehåller detaljerad information om ett villkor, problem, diagnostik, eller andra händelse, situationen, problemet eller kliniska begrepp som har ökat till en nivå av problem.  



### <a name="event-grid"></a>Event Grid


Lösningen stöder Azure händelse rutnät, en enda tjänst för att hantera routning av alla händelser från andra källor till alla mål, förutsatt att:

-   [Säkerhet och autentisering](/azure/event-grid/security-authentication)

-   [Rollbaserad åtkomstkontroll](/azure/event-grid/security-authentication#management-access-control) för olika hanteringsåtgärder som visar en lista över händelseprenumerationer, skapa nya och generera nycklar

-   Granskning

## <a name="store"></a>STORE 

### <a name="sql-database-and-server"></a>SQL Database och Server 


-   [Transparent Data kryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) ger realtid kryptering och dekryptering av data som lagras i Azure SQL Database med en nyckel som lagras i Azure Key Vault.

-   [SQL Vulnerability Assessment](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) är ett enkelt att konfigurera verktyg som kan identifiera, spåra och åtgärda eventuella säkerhetsrisker i databasen.

-   [SQL-databasen Hotidentifiering](/azure/sql-database/sql-database-threat-detection) aktiverat.

-   [SQL Database Auditing](/azure/sql-database/sql-database-auditing) aktiverat.

-   [SQL-databas mått och diagnostikloggning](/azure/sql-database/sql-database-metrics-diag-logging) aktiverat.

-   [Server - och databasnivå brandväggen regler](/azure/sql-database/sql-database-firewall-configure) har varit strängare än tidigare.

-   [Always Encrypted kolumner](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) används för att skydda patient första mellersta och senaste namn.
    Dessutom använder kolumnen databaskryptering också Azure Active Directory (AD) för att autentisera programmet till Azure SQL Database.

-   Åtkomst till SQL Database och SQL Server har konfigurerats enligt principen om minsta behörighet.

-   Endast begärda IP-adresser tillåts via brandväggen för SQL.

### <a name="storage-accounts"></a>Lagringskonton


-   [Data i rörelse överförs med hjälp av TLS/SSL endast](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Anonym åtkomst är inte tillåtet för behållare.

-   Varningsregler är konfigurerade för att spåra anonym aktivitet.

-   HTTPS krävs för att komma åt kontot lagringsresurser.

-   Autentiseringsdata begäran loggas och övervakas.

-   Vilande krypteras data i Blob storage.

## <a name="analyze"></a>ANALYSERA

### <a name="machine-learning"></a>Machine Learning


-   [Loggning är aktiverat](/azure/machine-learning/studio/web-services-logging) för Machine Learning-webbtjänster.
- med hjälp av [Maskininlärning](/azure/machine-learning/preview/experimentation-service-configuration) arbetsstationen kräver experiment, som ger dig möjlighet att förutsäga till en lösning. [Integrera arbetsstationen](/azure/machine-learning/preview/using-git-ml-project) kan hjälpa till att förenkla hanteringen av experiment.

## <a name="security"></a>SÄKERHET

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) innehåller en centraliserad för säkerhetsläget för alla dina Azure-resurser. En överblick över kan du kontrollera att lämpliga säkerhetsåtgärder som är installerade och korrekt konfigurerad och att du snabbt kan identifiera alla resurser som kräver uppmärksamhet. 

- [Azure Advisor](/azure/advisor/advisor-overview) är personliga molnet konsult som hjälper dig att följa bästa praxis för att optimera din Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) är en utökningsbar Management-program (APM)-tjänst för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Den identifierar prestandaavvikelser. Den inkluderar kraftfulla analysverktyg för att hjälpa dig diagnosticera problem och förstå vad användare faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

### <a name="azure-alerts"></a>Azure-aviseringar
- [Aviseringar ger en metod för att övervaka Azure-tjänster och kan du konfigurera villkor över data. Aviseringar kan du även ange meddelanden när ett varningsvillkor matchar övervakningsdata.

### <a name="operations-management-suite-oms"></a>Operations Management Suite (OMS)
[Operations Management Suite (även kallat OMS)](/azure/operations-management-suite/operations-management-suite-overview) är en samling med hanteringstjänster.

-   Arbetsytan har aktiverats för Security Center

-   Arbetsytan är aktiverad för övervakning av arbetsbelastning

-   Arbetsbelastningen övervakning är aktiverad för:

    -   Identitet och åtkomst

    -   Säkerhet och granskning

    -   Azure SQL DB Analytics

    -   [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics) lösning

    -   Key Vault-analys

    -   Spårning av ändringar

-   [Application Insights Connector (förhandsgranskning)](/azure/log-analytics/log-analytics-app-insights-connector) är aktiverad

-   [Aktiviteten logganalys](/azure/log-analytics/log-analytics-activity) är aktiverad
