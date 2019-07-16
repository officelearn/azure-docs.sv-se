---
title: Exempel - SP NIST 800-53 R4 skissen - kontroll mappning
description: Styra mappning av SP NIST 800-53 R4 skissen exempel att använda Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 251dbc396aea5694c4bdec45c194439c9476238b
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226046"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Styra mappning av SP NIST 800-53 R4 skissen exemplet

I följande artikel beskriver hur Azure skisser NIST SP 800-53 R4 skissen exemplet mappas till SP NIST 800-53 R4 kontroller. Läs mer om kontrollerna, [SP NIST 800-53](https://nvd.nist.gov/800-53).

Följande mappningar avser den **SP NIST 800-53 (Rev. 4)** kontroller. Använd navigeringen till höger för att gå direkt till en viss kontroll-mappning. Många av de mappade kontrollerna implementeras med en [Azure Policy](../../../policy/overview.md) initiativ. Om du vill granska fullständig initiativet öppna **princip** i Azure-portalen och välj den **definitioner** sidan. Hitta och välj sedan den  **\[förhandsversion\]: Granska SP NIST 800-53 R4 kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav** inbyggd princip initiativ.

## <a name="ac-2-account-management"></a>AC-2-kontohantering

Den här skissen kan du granska konton som inte kanske uppfyller organisationens krav för kontot. Den här skissen tilldelar fem Azure principdefinitioner som gransknings-och externa konton med behörigheter för Läs-, Skriv- och ägare för en prenumeration och inaktuella konton. Genom att granska de konton som granskas av dessa principer kan vidta du lämpliga åtgärder så kontokrav uppfylls.

- \[Förhandsversion av\]: Granska inaktuella konton på en prenumeration
- \[Förhandsversion av\]: Granska inaktuella konton med ägarbehörigheter för en prenumeration
- \[Förhandsversion av\]: Granska externa konton med ägarbehörigheter för en prenumeration
- \[Förhandsversion av\]: Granska externa konton med läsbehörigheter för en prenumeration
- \[Förhandsversion av\]: Granska externa konton med skrivbehörigheter för en prenumeration

## <a name="ac-2-7-account-management--role-based-schemes"></a>Hantering av AC-2 (7) | Rollbaserad scheman

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med Azure-portalen kan granska du vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar också två [Azure Policy](../../../policy/overview.md) definitioner för att granska användning av Azure Active Directory-autentisering för SQL-servrar och Service Fabric. Autentisering möjliggör med Azure Active Directory förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.

- Granska etablering av en Azure Active Directory-administratör för SQLServer
- Granska användning av Azure Active Directory för klientautentisering i Service Fabric

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>Hantering av AC-2 (12) | Kontot övervakning / ovanliga användning

Just-in-time (JIT) VM låsta inkommande trafik till Azure-datorer, minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Alla JIT-begäranden för att få åtkomst till virtuella datorer loggas i aktivitetsloggen så att du kan övervaka för ovanliga användning. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-time-åtkomst, men ännu inte har konfigurerats.

- \[Förhandsversion av\]: Övervaka möjliga precis i tid JIT-nätverksåtkomst i Azure Security Center

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Information Flow tvingande

Mellan origin resource kan sharing (CORS) resurser för Apptjänster att begäras från en extern domän. Microsoft rekommenderar att du tillåter endast nödvändiga domäner att interagera med dina API: et, funktion och webbprogram. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition för att övervaka åtkomstbegränsningar för CORS-resurser i Azure Security Center.
Förstå CORS-implementeringar kan hjälpa dig att kontrollera att information Flödeskontroller implementeras.

- \[Förhandsversion av\]: Granska åtkomstbegränsningar för CORS-resursen för ett webbprogram

## <a name="ac-5-separation-of-duties"></a>AC-5 uppdelning av uppgifter

Med bara en Azure-prenumerationsägare kan inte för administrativa redundans. Däremot kan har för många Azure-prenumerationsägare öka risken för ett intrång via en komprometterad ägare-konto. Den här skissen hjälper dig att bibehålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela två [Azure Policy](../../../policy/overview.md) definitioner som granska antalet ägare för Azure-prenumerationer. Den här skissen tilldelar också fyra Azure principdefinitioner som hjälper dig att styra medlemskap i gruppen Administratörer på Windows-datorer. Hantera Prenumerationens ägare och behörigheter för VM-administratör kan hjälpa dig att implementera lämpliga uppdelning av uppgifter.

- \[Förhandsversion av\]: Granska högsta antalet ägare för en prenumeration
- \[Förhandsversion av\]: Granska minsta antalet ägare för prenumeration
- Granska att gruppen Administratörer i Windows virtuella datorer omfattar inte de angivna medlemmarna
- Granska att gruppen Administratörer i Windows virtuella datorer innehåller de angivna medlemmarna
- Distribuera VM-tillägget för att granska att gruppen Administratörer i Windows virtuella datorer omfattar inte de angivna medlemmarna
- Distribuera VM-tillägget för att granska att gruppen Administratörer i Windows virtuella datorer innehåller de angivna medlemmarna

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) lägsta behörighet | Granskning av användarbehörigheter

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med Azure-portalen kan granska du vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar sex [Azure Policy](../../../policy/overview.md) definitioner för att granska konton som ska prioriteras för granskning. Granska dessa konto-indikatorer kan hjälpa dig att kontrollera minsta privilegium kontroller har implementerats.

- \[Förhandsversion av\]: Granska högsta antalet ägare för en prenumeration
- \[Förhandsversion av\]: Granska minsta antalet ägare för prenumeration
- Granska att gruppen Administratörer i Windows virtuella datorer omfattar inte de angivna medlemmarna
- Granska att gruppen Administratörer i Windows virtuella datorer innehåller de angivna medlemmarna
- Distribuera VM-tillägget för att granska att gruppen Administratörer i Windows virtuella datorer omfattar inte de angivna medlemmarna
- Distribuera VM-tillägget för att granska att gruppen Administratörer i Windows virtuella datorer innehåller de angivna medlemmarna

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>Fjärråtkomst för AC-17 (1) | Automatiserad övervakning / styra

Den här skissen hjälper dig att övervaka och kontrollera fjärråtkomst genom att tilldela tre [Azure Policy](../../../policy/overview.md) definitioner till bildskärmar som fjärrfelsökning för Azure App Service-programmet är avstängd och två principdefinitioner som gransknings-och Linux virtuella datorer som tillåter fjärranslutningar från konton utan lösenord. Den här skissen tilldelar också en Azure Policy-definition som hjälper dig att övervaka obegränsad åtkomst till lagringskonton. Dessa indikatorer för övervakningen kan hjälpa dig att säkerställa fjärråtkomstmetoder uppfyller säkerhetsprinciperna.

- \[Förhandsversion av\]: Granska fjärrfelsökning tillstånd för en Funktionsapp
- \[Förhandsversion av\]: Granska fjärrfelsökning tillstånd för ett webbprogram
- \[Förhandsversion av\]: Granska fjärrfelsökning tillstånd för en API-App
- \[Förhandsversion av\]: Granska att virtuella Linux-datorer inte tillåta fjärranslutningar från konton utan lösenord
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att virtuella Linux-datorer inte tillåta fjärranslutningar från konton utan lösenord
- Granska obegränsad nätverksåtkomst till storage-konton

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) innehåll av granskningsposter | Centraliserad hantering av planerad Audit spela in innehåll

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics-arbetsyta aktiverar centraliserad konfiguration och hantering. Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela sju [Azure Policy](../../../policy/overview.md) definitioner som granska och genomdriva distribution av Log Analytics-agenten på Azure virtual machines.

- \[Förhandsversion av\]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-arbetsyta för VM - matchningsfel för rapport
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux-datorer
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows-datorer

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5-svar på Granska bearbetningsfel

Den här skissen tilldelar fem [Azure Policy](../../../policy/overview.md) definitioner som övervakar gransknings- och konfigurationer för loggning av händelser. Övervaka de här konfigurationerna kan ge ett tecken på ett fel vid granskning system eller en felaktig konfiguration och hur du vidta åtgärder.

- \[Förhandsversion av\]: Övervaka Ej granskad SQL-servrar i Azure Security Center
- Granska diagnostikinställning
- Granska SQL-hanterade instanser utan avancerad säkerhet för Data
- Granska nivå granskningsinställningarna för SQL server
- Granska SQL-servrar utan avancerad säkerhet för Data

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU – 6 (4) Audit granska, analys och rapportering | Central granskning och analys

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics-arbetsyta aktiverar centraliserad rapportering och analys. Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela sju [Azure Policy](../../../policy/overview.md) definitioner som granska och genomdriva distribution av Log Analytics-agenten på Azure virtual machines.

- \[Förhandsversion av\]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-arbetsyta för VM - matchningsfel för rapport
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux-datorer
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows-datorer

## <a name="au-12-audit-generation"></a>AU – 12 Audit Generation

Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela 15 [Azure Policy](../../../policy/overview.md) definitioner som gransknings-och inställningar på Azure-resurser. Dessa principdefinitioner gransknings- och tillämpa distribution av Log Analytics-agenten på Azure virtual machines och konfiguration av granskningsinställningar för andra Azure-resurstyper. Dessa principdefinitioner också kan granska konfigurationen av diagnostikloggar till ger information om åtgärder som utförs inom Azure-resurser. Dessutom granskning och avancerad säkerhet för Data konfigureras på SQL-servrar.

- \[Förhandsversion av\]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-arbetsyta för VM - matchningsfel för rapport
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux-datorer
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows-datorer
- \[Förhandsversion av\]: Övervaka Ej granskad SQL-servrar i Azure Security Center
- Använd diagnostikinställningar för Nätverkssäkerhetsgrupper
- Granska diagnostikinställning
- Granska SQL-hanterade instanser utan avancerad säkerhet för Data
- Granska nivå granskningsinställningarna för SQL server
- Granska SQL-servrar utan avancerad säkerhet för Data
- Distribuera avancerad säkerhet för Data i SQL-servrar
- Distribuera granskning på SQL-servrar

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>Lägsta CM-7.2-funktioner | Förhindra att köra enhetsprogram

Anpassningsbar programkontroll i Azure Security Center är en intelligent, automatiserad slutpunkt till slutpunkt vitlistning lösning som kan blockera eller förhindra att vissa program körs på dina virtuella datorer. Programkontroll kan köra i en tvingande läge som tillåter inte icke-godkänd program från att köras. Den här skissen tilldelar en Azure Policy definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men ännu inte har konfigurerats.

- \[Förhandsversion av\]: Övervaka möjlig lista över tillåtna i Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>Lägsta CM-7.5-funktioner | Auktoriserade program / lista över tillåtna

Anpassningsbar programkontroll i Azure Security Center är en intelligent, automatiserad slutpunkt till slutpunkt vitlistning lösning som kan blockera eller förhindra att vissa program körs på dina virtuella datorer. Programkontroll kan du skapa godkända programmets listor för dina virtuella datorer. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men ännu inte har konfigurerats.

- \[Förhandsversion av\]: Övervaka möjlig lista över tillåtna i Azure Security Center

## <a name="cm-11-user-installed-software"></a>CM-11 användarinstallerade program

Anpassningsbar programkontroll i Azure Security Center är en intelligent, automatiserad slutpunkt till slutpunkt vitlistning lösning som kan blockera eller förhindra att vissa program körs på dina virtuella datorer. Programkontroll kan hjälpa dig att tillämpa och övervaka att fastställda principer för begränsning av programvara. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men ännu inte har konfigurerats.

- \[Förhandsversion av\]: Övervaka möjlig lista över tillåtna i Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternativ bearbetar webbplatsen

Azure Site Recovery replikerar arbetsbelastningar som körs på virtuella datorer från en primär plats till en sekundär plats. Om ett eventuellt strömavbrott på den primära platsen växlar arbetsbelastningen över den sekundära platsen. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som granskar virtuella datorer utan haveriberedskap som har konfigurerats. Övervakning av indikatorn kan hjälpa dig att kontrollera att nödvändiga oförutsedda händelser kontroller är uppfyllda.

- Granska virtuella datorer utan haveriberedskap som har konfigurerats

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1)-identifiering och autentisering (Organisationsanvändare) | Åtkomst till Privilegierade konton

Den här skissen hjälper dig att begränsa och styr behörig åtkomst genom att tilldela två [Azure Policy](../../../policy/overview.md) definitioner för att granska konton med ägare och/eller skrivbehörighet som inte har aktiverat multifaktorautentisering. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen komprometteras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan identifiera du konton som kan vara mer troligt systemproblem.

- \[Förhandsversion av\]: Granska konton med ägarbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Granska konton med skrivbehörigheter som inte är MFA är aktiverat på en prenumeration

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2)-identifiering och autentisering (Organisationsanvändare) | Nätverksåtkomst till icke-privilegierade konton

Den här skissen hjälper dig att begränsa och kontrollera åtkomsten genom att tilldela en [Azure Policy](../../../policy/overview.md) definitionen för att granska konton med läsbehörigheter som inte har aktiverat multifaktorautentisering. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen komprometteras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan identifiera du konton som kan vara mer troligt systemproblem.

- \[Förhandsversion av\]: Granska konton med läsbehörigheter som inte är MFA är aktiverat på en prenumeration

## <a name="ia-5-authenticator-management"></a>IA-5 Authenticator Management

Den här skissen tilldelar fem [Azure Policy](../../../policy/overview.md) definitioner som gransknings-och Linux-datorer som tillåta fjärranslutningar från konton utan lösenord och/eller har felaktiga behörigheter nastavit passwd-filen. Den här skissen tilldelar också en principdefinition som granskar konjugation av lösenord krypteringstyp för Windows-datorer. Övervakning av dessa indikatorer kan du se till att systemet autentiserare uppfyller organisationens princip för identifiering och autentisering.

- \[Förhandsversion av\]: Granska att virtuella Linux-datorer inte har konton utan lösenord
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att virtuella Linux-datorer inte har konton utan lösenord
- \[Förhandsversion av\]: Granska att passwd filbehörigheter inställd 0644 på virtuella Linux-datorer
- \[Förhandsversion av\]: Granska att Windows-datorer lagra lösenord med omvändbar kryptering
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att passwd filbehörigheter inställd 0644 på virtuella Linux-datorer

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Authenticator Management | Lösenordsbaserad autentisering

Den här skissen hjälper dig att genomdriva starka lösenord genom att tilldela 12 [Azure Policy](../../../policy/overview.md) definitioner som gransknings-och Windows-datorer som inte framtvingar minsta styrka och andra lösenordskrav. Medvetenhet om virtuella datorer i överträdelse av styrka lösenordsprincip kan du vidta åtgärder för att se till att lösenord för användarkonton för alla virtuella datorer följer organisationens lösenordsprincip.

- \[Förhandsversion av\]: Granska att Windows-datorer inte kan återanvända tidigare 24 lösenord
- \[Förhandsversion av\]: Granska att Windows-datorer har en högsta ålder för lösenord 70 dagar
- \[Förhandsversion av\]: Granska att Windows-datorer har en lägsta ålder för lösenord på 1 dag
- \[Förhandsversion av\]: Granska att Windows virtuella datorer har lösenordet komplexiteten inställningen aktiverad
- \[Förhandsversion av\]: Granska att Windows virtuella datorer att begränsa minsta längd på lösenord och 14 tecken
- \[Förhandsversion av\]: Granska att Windows-datorer lagra lösenord med omvändbar kryptering
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att Windows-datorer inte kan återanvända tidigare 24 lösenord
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att Windows-datorer har en högsta ålder för lösenord 70 dagar
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att Windows-datorer har en lägsta ålder för lösenord på 1 dag
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att Windows virtuella datorer har lösenordet komplexiteten inställningen aktiverad
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att Windows virtuella datorer att begränsa minsta längd på lösenord och 14 tecken
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska att Windows-datorer lagra lösenord med omvändbar kryptering

## <a name="ra-5-vulnerability-scanning"></a>RA-5 granskar säkerhetsrisker

Den här skissen hjälper dig att hantera information system sårbarheter genom att tilldela fyra [Azure Policy](../../../policy/overview.md) definitioner som övervaka säkerhetsproblem med operativsystem, SQL-säkerhetsrisker och säkerhetsrisker i virtuell dator i Azure Security Center. Azure Security Center rapporteringsfunktioner som hjälper dig att ha i realtid insyn i säkerhetsläget för distribuerade Azure-resurser. Den här skissen tilldelar också tre principdefinitioner som granska och genomdriva avancerad säkerhet för Data på SQL-servrar. Avancerade datasäkerhet med utvärdering av säkerhetsrisker och Avancerat skydd-funktioner som hjälper dig förstå säkerhetsrisker i dina distribuerade resurser.

- Granska SQL-hanterade instanser utan avancerad säkerhet för Data
- Granska SQL-servrar utan avancerad säkerhet för Data
- Distribuera avancerad säkerhet för Data i SQL-servrar
- \[Förhandsversion av\]: Granska OS-säkerhetsproblem på VM-skalningsuppsättning som anger i Azure Security Center
- \[Förhandsversion av\]: Övervaka OS-säkerhetsproblem i Azure Security Center
- \[Förhandsversion av\]: Övervaka SQL sårbarhetsbedömning resulterar i Azure Security Center
- \[Förhandsversion av\]: Övervaka säkerhetsrisker i virtuell dator i Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>SC-5 med DOS-skydd

Azure-datorn för standardnivån för tjänsten (DDoS) ger ytterligare funktioner och skyddsfunktioner över grundläggande tjänstnivån. Dessa ytterligare funktioner innefattar integrering med Azure Monitor och möjligheten att granska efter nätverksattacker rapporter. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som granskar om DDoS standard-nivån är aktiverad. Förstå skillnaden mellan tjänstnivåerna funktionen kan hjälpa dig att välja den bästa lösningen för adress denial of service-skydd för Azure-miljön.

- \[Förhandsversion av\]: Gransknings-och standard-nivån för DDoS protection har aktiverats för ett virtuellt nätverk

## <a name="sc-7-boundary-protection"></a>SC-7 gräns skydd

Den här skissen hjälper dig att hantera och styra systemgränsen genom att tilldela en [Azure Policy](../../../policy/overview.md) definition som övervakar nätverkssäkerhetsgrupper med Tillåtande regler. Regler som är alltför Tillåtande kan tillåta oönskade nätverksåtkomst och bör granskas. Den här skissen tilldelar också en principdefinition som övervakar network security group härdning rekommendationer i Azure Security Center. Azure Security Center analyserar trafikmönster för virtuella datorer som riktas mot Internet och ger nätverkssäkerhet rekommendationer för regel för att minska den potentiella risken för angrepp.
Dessutom kan tilldelar den här skissen också tre principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och storage-konton med obegränsad åtkomst kan oavsiktlig tillgång till informationen i systemet.

- \[Förhandsversion av\]: Övervaka mot Internet-datorer för Nätverkssäkerhetsgruppen trafik Härdning av rekommendationer
- \[Förhandsversion av\]: Övervaka tillåten åtkomst till nätverk i Azure Security Center
- \[Förhandsversion av\]: Övervaka oskyddade nätverksslutpunkter i Azure Security Center
- \[Förhandsversion av\]: Övervaka oskyddat webbprogram i Azure Security Center
- Granska obegränsad nätverksåtkomst till storage-konton

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7.3 gräns skydd | Åtkomstpunkter

Just-in-time (JIT) VM låsta inkommande trafik till Azure-datorer, minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. JIT-åtkomst till virtuell dator kan du begränsa antalet externa anslutningar till dina resurser i Azure. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-time-åtkomst, men ännu inte har konfigurerats.

- \[Förhandsversion av\]: Övervaka möjliga precis i tid JIT-nätverksåtkomst i Azure Security Center

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7.4 gräns skydd | Externa telekommunikationstjänster

Just-in-time (JIT) VM låsta inkommande trafik till Azure-datorer, minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. JIT-åtkomst till virtuell dator kan du hantera undantag till principen för din traffic flow genom att underlätta åtkomst-begäran och godkännande processer. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-time-åtkomst, men ännu inte har konfigurerats.

- \[Förhandsversion av\]: Övervaka möjliga precis i tid JIT-nätverksåtkomst i Azure Security Center

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) skydd av Information i vila | Kryptering

Den här skissen kan du tillämpa principen på användningen av cryptograph kontroller för att skydda information i vila genom att tilldela 9 [Azure Policy](../../../policy/overview.md) definitioner som tillämpa specifika cryptograph kontroller och granska användning av svaga kryptografiska inställningar. Förstå där dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta åtgärder för att se till att resurser som är konfigurerade i enlighet med din säkerhetsprincip för information. Mer specifikt Kräv principdefinitioner som tilldelats av den här skissen kryptering för data lake storage-konton; Kräv transparent datakryptering på SQL-databaser och granska saknas kryptering på den SQL-databaser, virtuella diskar och variabler för automation-konto.

- \[Förhandsversion av\]: Övervaka okrypterade SQL-databaser i Azure Security Center
- \[Förhandsversion av\]: Övervaka okrypterade Virtuella Datordiskar i Azure Security Center
- Granska säker överföring till storage-konton
- Granska SQL-hanterade instanser utan avancerad säkerhet för Data
- Granska SQL-servrar utan avancerad säkerhet för Data
- Granska status för transparent datakryptering
- Distribuera avancerad säkerhet för Data i SQL-servrar
- Distribuera transparent datakryptering för SQL DB
- Framtvinga kryptering på Data Lake Store-konton

## <a name="si-2-flaw-remediation"></a>SI-2-fel reparation

Den här skissen hjälper dig att hantera information system fel genom att tilldela sex [Azure Policy](../../../policy/overview.md) definitioner som övervakar saknade systemuppdateringar, säkerhetsproblem med operativsystem, SQL-säkerhetsrisker och virtuell dator Säkerhetsproblem i Azure Security Center. Azure Security Center rapporteringsfunktioner som hjälper dig att ha i realtid insyn i säkerhetsläget för distribuerade Azure-resurser. Den här skissen tilldelar också en principdefinition som säkerställer att automatisk uppgradering av operativsystemet för VM-skalningsuppsättningar.

- \[Förhandsversion av\]: Granska alla saknade systemuppdateringar på VM-skalningsuppsättningar i Azure Security Center
- \[Förhandsversion av\]: Granska OS-säkerhetsproblem på VM-skalningsuppsättning som anger i Azure Security Center
- \[Förhandsversion av\]: Övervaka systemuppdateringar som saknas i Azure Security Center
- \[Förhandsversion av\]: Övervaka OS-säkerhetsproblem i Azure Security Center
- \[Förhandsversion av\]: Övervaka SQL sårbarhetsbedömning resulterar i Azure Security Center
- \[Förhandsversion av\]: Övervaka säkerhetsrisker i virtuell dator i Azure Security Center
- Framtvinga automatisk uppgradering av Operativsystemet med apphälsokontroller i VMSS

## <a name="si-3-malicious-code-protection"></a>SI – 3 skadlig kod Protection

Den här skissen hjälper dig att hantera endpoint protection, inklusive skadlig kod protection genom att tilldela tre [Azure Policy](../../../policy/overview.md) definitioner som Övervakare som saknar endpoint protection på virtuella datorer i Azure Security Center och tillämpa Microsoft-program mot skadlig kod på Windows-datorer.

- \[Förhandsversion av\]: Granska endpoint protection-lösning på VM-skalningsuppsättningar i Azure Security Center
- \[Förhandsversion av\]: Övervaka saknad Endpoint Protection i Azure Security Center
- Distribuera Microsofts IaaSAntimalware-Standardtillägg för Windows Server

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI – 3 (1) skadlig kod Protection | Central hantering

Den här skissen hjälper dig att hantera endpoint protection, inklusive skadlig kod protection genom att tilldela två [Azure Policy](../../../policy/overview.md) definitioner som Övervakare som saknar endpoint protection på virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller centraliserad hantering och rapporteringsfunktioner som hjälper dig att ha i realtid insyn i säkerhetsläget för distribuerade Azure-resurser.

- \[Förhandsversion av\]: Granska endpoint protection-lösning på VM-skalningsuppsättningar i Azure Security Center
- \[Förhandsversion av\]: Övervaka saknad Endpoint Protection i Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI – 4 informationssystem övervakning

Den här skissen hjälper dig att övervaka systemet genom att granska och genomdriva loggning och datasäkerhet i Azure-resurser. Mer specifikt tilldelade principer gransknings- och tillämpa distribution av Log Analytics-agenten och utökade säkerhetsinställningar för SQL-databaser, lagringskonton och nätverksresurser. De här funktionerna kan hjälpa dig att identifiera avvikande beteende och indikatorer för attacker så att du kan vidta lämpliga åtgärder.

- \[Förhandsversion av\]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-arbetsyta för VM - matchningsfel för rapport
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux-datorer
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS)
- \[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows-datorer
- Granska SQL-hanterade instanser utan avancerad säkerhet för Data
- Granska SQL-servrar utan avancerad säkerhet för Data
- Distribuera avancerad säkerhet för Data i SQL-servrar
- Distribuera Avancerat skydd på Storage-konton
- Distribuera granskning på SQL-servrar
- Distribuera nätverksbevakare när virtuella nätverk skapas
- Distribuera Hotidentifiering på SQL-servrar

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI – 4 (18) informationssystem övervakning | Analysera trafik / hemlig Exfiltrering

Avancerat skydd för Azure Storage identifierar onormala och potentiellt skadliga försök att komma åt eller utnyttja storage-konton. Skydd aviseringar innehåller avvikande åtkomstmönster, avvikande extraherar/ladda upp och misstänkta storage-aktivitet. Dessa indikatorer kan hjälpa dig att identifiera hemlig exfiltrering av information.

- Distribuera Avancerat skydd på Storage-konton

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av SP NIST 800-53 R4 skissen, finns i följande artiklar för att lära dig om skissen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [SP NIST 800-53 R4 skissen - översikt](./index.md)
> [SP NIST 800-53 R4 skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
