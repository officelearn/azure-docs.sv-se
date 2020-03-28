---
title: SWIFT CSP-CSCF v2020 skiss prov kontroller
description: Kontrollkartläggning av swift csp-cscf-exemplet mot 2020. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärdering.
ms.date: 09/24/2019
ms.topic: sample
ms.openlocfilehash: 1826e4820f06fc3a858ce6cd5f8906baaa9808cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546515"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Kontrollkartläggning av swift-csp-cscf-avtalet om v2020-strategin

I följande artikel beskrivs hur azure blueprints SWIFT CSP-CSCF v2020-skissen mappas till SWIFT CSP-CSCF v2020-kontrollerna. Mer information om kontrollerna finns i [SWIFT CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

Följande mappningar är till **KONTROLLERNA SWIFT CSP-CSCF v2020.** Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj ** \[förhandsversionen:\]Granska SWIFT CSP-CSCF v2020-kontroller och distribuera specifika vm-tillägg för att stödja inbyggda** principinitiativ för granskningskrav.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1.2 och 5.1 Kontohantering

Den här skissen hjälper dig att granska konton som kanske inte uppfyller organisationens kontohanteringskrav. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som granskar externa konton med läs-, skriv- och ägarbehörigheter för en prenumeration och föråldrade konton. Genom att granska de räkenskaper som granskas av dessa principer kan du vidta lämpliga åtgärder för att säkerställa att kontohanteringskraven uppfylls.

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2.6, 5.1, 6.4 och 6.5A Account Management | Rollbaserade system

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar också [Azure-principdefinitioner](../../../policy/overview.md) för granskning av Azure Active Directory-autentisering för SQL-servrar och tjänstinfrastruktur. Med Hjälp av Azure Active Directory-autentisering möjliggör förenklad behörighetshantering och centraliserad identitetshantering för databasanvändare och andra Microsoft-tjänster. Dessutom tilldelar den här skissen en Azure-principdefinition för att granska användningen av anpassade RBAC-regler. Att förstå var anpassade RBAC-regler implementeras kan hjälpa dig att verifiera behov och korrekt implementering, eftersom anpassade RBAC-regler är felbenägna.

- En Azure Active Directory-administratör bör etableras för SQL-servrar
- Granskningsanvändning av anpassade RBAC-regler
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9A Kontohantering | Kontoövervakning / Atypisk användning

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. Alla JIT-begäranden om åtkomst till virtuella datorer loggas i aktivitetsloggen så att du kan övervaka för atypisk användning. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="13-51-and-64-separation-of-duties"></a>1.3, 5.1 och 6.4 Åtskillnad av arbetsuppgifter

Att bara ha en Azure-prenumerationsägare tillåter inte administrativ redundans. Omvänt kan för många Azure-prenumerationsägare öka risken för ett intrång via ett komprometterat ägarkonto. Den här skissen hjälper dig att underhålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar antalet ägare för Azure-prenumerationer. Den här skissen tilldelar också Azure-principdefinitioner som hjälper dig att kontrollera medlemskap i gruppen Administratörer på virtuella Datorer i Windows. Genom att hantera administratörsbehörighet och administratörsbehörighet för virtuella datorer kan du implementera lämplig åtskillnad mellan uppgifter.

- Högst 3 ägare bör utses för din prenumeration
- Visa granskningsresultat från virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1.3, 5.1 och 6.4 Minst privilegium | Granskning av användarbehörigheter

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) till granskningskonton som ska prioriteras för granskning. Genom att granska dessa kontoindikatorer kan du se till att lägsta behörighetskontroller implementeras.

- Högst 3 ägare bör utses för din prenumeration
- Visa granskningsresultat från virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="22-and-27-security-attributes"></a>2.2 och 2.7 Säkerhetsattribut

Dataidentifierings- och klassificeringsfunktionerna för avancerad datasäkerhet för Azure SQL Database ger funktioner för att upptäcka, klassificera, etikettera och skydda känsliga data i dina databaser. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen. Avancerad datasäkerhet kan hjälpa dig att säkerställa information som är associerad med lämpliga säkerhetsattribut för din organisation. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) för att övervaka och framtvinga användning av avancerad datasäkerhet på SQL-servern. 

- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2.2, 2.7, 4.1 och 6.1 Fjärråtkomst | Automatiserad övervakning / kontroll

Den här skissen hjälper dig att övervaka och styra fjärråtkomst genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att övervaka att fjärrfelsökning för Azure App Service-programmet är inaktiverat och principdefinitioner som granskar virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord. Den här skissen tilldelar också en Azure-principdefinition som hjälper dig att övervaka obegränsad åtkomst till lagringskonton. Genom att övervaka dessa indikatorer kan du se till att fjärråtkomstmetoderna följer din säkerhetsprincip.

- \[Preview\]: Visa granskningsresultat från virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- \[Förhandsversion:\]Distribuera förutsättningar för granskning av virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- Granska obegränsad nätverksåtkomst till lagringskonton
- Fjärrfelsökning bör inaktiveras för API-app
- Fjärrfelsökning bör inaktiveras för Funktionsapp
- Fjärrfelsökning bör inaktiveras för webbprogram

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1.3 och 6.4 Innehållet i revisionshandlingar | Centraliserad hantering av planerat granskningspostinnehåll

Loggdata som samlas in av Azure Monitor lagras på en Log Analytics-arbetsyta som möjliggör centraliserad konfiguration och hantering. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar och framtvingar distribution av Log Analytics-agenten på virtuella Azure-datorer.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2.2, 2.7 och 6.4 Svar på fel i granskningshanteringen

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som övervakar gransknings- och händelseloggningskonfigurationer. Övervakning av dessa konfigurationer kan ge en indikator på ett granskningssystemfel eller felkonfiguration och hjälpa dig att vidta korrigerande åtgärder.

- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Granska diagnostikinställning
- Distribuera granskning på SQL-server

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1.3 och 6.4 Granskning, analys och rapportering av revision | Central granskning och analys

Loggdata som samlas in av Azure Monitor lagras på en Log Analytics-arbetsyta som möjliggör centraliserad rapportering och analys. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar och framtvingar distribution av Log Analytics-agenten på virtuella Azure-datorer.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer

## <a name="13-22-27-64-and-65a-audit-generation"></a>1.3, 2.2, 2.7, 6.4 och 6.5A Audit Generation

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar logginställningar på Azure-resurser. Dessa principdefinitioner granskar och tillämpar distribution av Log Analytics-agenten på virtuella Azure-datorer och konfiguration av granskningsinställningar för andra Azure-resurstyper. Dessa principdefinitioner granskar också konfigurationen av diagnostikloggar för att ge insikt i åtgärder som utförs inom Azure-resurser. Dessutom konfigureras granskning och avancerad datasäkerhet på SQL-servrar.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer
- Granska diagnostikinställning
- Granska granskningsinställningar på SQL-servernivå
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera granskning på SQL-servrar
- Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper

## <a name="11-least-functionality--prevent-program-execution"></a>1.1 Minst funktionalitet | Förhindra programkörning

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll kan köras i ett tvingande läge som förhindrar att ett icke-godkänt program körs. Den här skissen tilldelar en Azure Policy-definition som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1.1 Minst funktionalitet | Auktoriserad programvara / vitlistning

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll hjälper dig att skapa godkända programlistor för dina virtuella datorer. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="11-user-installed-software"></a>1.1 Programvara för användarinstallerad

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll kan hjälpa dig att genomdriva och övervaka efterlevnaden av principer för begränsning av programvara. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4.2 Identifiering och autentisering (organisatoriska användare) | Nätverksåtkomst till privilegierade konton

Den här skissen hjälper dig att begränsa och kontrollera privilegierad åtkomst genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att granska konton med ägare och/eller skrivbehörighet som inte har multifaktorautentisering aktiverad. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen äventyras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras.

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska aktiveras på konton med skrivbehörighet för din prenumeration

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4.2 Identifiering och autentisering (organisatoriska användare) | Nätverksåtkomst till konton som inte är privilegierade

Den här skissen hjälper dig att begränsa och kontrollera åtkomst genom att tilldela en [Azure-principdefinition](../../../policy/overview.md) för granskning av konton med läsbehörigheter som inte har multifaktorautentisering aktiverad. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen äventyras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras.

- MFA ska aktiveras på konton med läsbehörighet för din prenumeration

## <a name="23-and-41-authenticator-management"></a>2.3 och 4.1 Autentiseringshantering

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som granskar virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord och/eller har felaktiga behörigheter för passwd-filen. Den här skissen tilldelar också principdefinitioner som granskar konfigurationen av lösenordskrypteringstypen för virtuella Windows-datorer. Genom att övervaka dessa indikatorer kan du se till att systemautentiseringspersonerna följer organisationens identifierings- och autentiseringsprincip.

- \[Preview\]: Visa granskningsresultat från virtuella Linux-datorer som inte har passwd-filbehörigheterna inställda på 0644
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Linux-datorer som inte har passwd-filbehörigheterna inställda på 0644
- \[Preview\]: Visa granskningsresultat från virtuella Linux-datorer som har konton utan lösenord
- \[Preview\]: Distribuera krav för att granska virtuella Linux-datorer som har konton utan lösenord
- \[Förhandsgranskning\]: Visa granskningsresultat från virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering
- \[Preview\]: Distribuera krav för granskning av virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2.3 och 4.1 Förvaltning av autentiseringspersoner | Lösenordsbaserad autentisering

Den här skissen hjälper dig att framtvinga starka lösenord genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar virtuella Windows-datorer som inte tillämpar minsta styrka och andra lösenordskrav. Medvetenhet om virtuella datorer i strid med principen om lösenordsstyrka hjälper dig att vidta korrigerande åtgärder för att säkerställa att lösenord för alla användarkonton för virtuella datorer följer organisationens lösenordsprincip.

- \[Preview\]: Visa granskningsresultat från virtuella Windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Preview\]: Visa granskningsresultat från virtuella Windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Preview\]: Visa granskningsresultat från virtuella Windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsgranskning\]: Visa granskningsresultat från virtuella windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Förhandsgranskning\]: Visa granskningsresultat från virtuella windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Förhandsgranskning\]: Visa granskningsresultat från virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering

## <a name="22-and-27-vulnerability-scanning"></a>2.2 och 2.7 Sårbarhetsskanning

Den här skissen hjälper dig att hantera sårbarheter i informationssystem genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser. Den här skissen tilldelar också principdefinitioner som granskar och framtvingar avancerad datasäkerhet på SQL-servrar. Avancerad datasäkerhet inkluderade sårbarhetsbedömning och avancerade hotskyddsfunktioner som hjälper dig att förstå sårbarheter i dina distribuerade resurser.

- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas 
- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas

## <a name="13-denial-of-service-protection"></a>1.3 Skydd för överbelastning

Azures ddos-standardnivå (Distributed Denial of Service) ger ytterligare funktioner och begränsningsfunktioner över den grundläggande tjänstnivån. Dessa ytterligare funktioner inkluderar Azure Monitor-integrering och möjligheten att granska rapporter efter attacken. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som granskar om DDoS-standardnivån är aktiverad. Genom att förstå kapacitetsskillnaden mellan tjänstnivåerna kan du välja den bästa lösningen för att hantera överbelastningsskydd för din Azure-miljö.

- DDoS Protection Standard bör aktiveras

## <a name="11-and-61-boundary-protection"></a>1.1 och 6.1 Gränsskydd

Den här skissen hjälper dig att hantera och styra systemgränsen genom att tilldela en [Azure Policy-definition](../../../policy/overview.md) som övervakar för rekommendationer för nätverkssäkerhetsgrupper som härdar i Azure Security Center. Azure Security Center analyserar trafikmönster för Internet som är vänt mot virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgrupper för att minska den potentiella angreppsytan.
Dessutom tilldelar den här skissen också principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och lagringskonton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informationssystemet.

- Regler för nätverkssäkerhetsgrupp för internetvända virtuella datorer bör skärpas
- Åtkomst via internetvänd slutpunkt bör begränsas
- Granska obegränsad nätverksåtkomst till lagringskonton

## <a name="29a-boundary-protection--access-points"></a>2.9A Gränsskydd | Åtkomstpunkter

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. JIT virtuell dator åtkomst hjälper dig att begränsa antalet externa anslutningar till dina resurser i Azure. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9A Gränsskydd | Externa telekommunikationstjänster

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. ÅTKOMST till virtuella datorer för JIT hjälper dig att hantera undantag från din trafikflödesprincip genom att underlätta åtkomstbegäran och godkännandeprocesser. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2.1, 2.4, 2.4A, 2.5A och 2.6 Konfidentialitet och integritet | Kryptografiskt eller alternativt fysiskt skydd

Den här skissen hjälper dig att skydda den konfidentiella och integriteten hos överförd information genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att övervaka kryptografisk mekanism som implementeras för kommunikationsprotokoll. Om du säkerställer att kommunikationen är korrekt krypterad kan du uppfylla organisationens krav eller skydda information från obehörigt röjande och ändring.

- API-app bör endast vara tillgänglig via HTTPS
- Visa granskningsresultat från Windows webbservrar som inte använder säkra kommunikationsprotokoll
- Distribuera förutsättningar för granskning av Windows-webbservrar som inte använder säkra kommunikationsprotokoll
- Funktionsappen ska endast vara tillgänglig via HTTPS
- Endast säkra anslutningar till Redis-cachen ska aktiveras
- Säker överföring till lagringskonton ska vara aktiverat
- Webbprogrammet ska endast vara tillgängligt via HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2.2, 2.3, 2.5, 4.1 och 2.7 Skydd av information i vila | Kryptografiskt skydd

Den här skissen hjälper dig att tillämpa din princip om användningen av kryptografkontroller för att skydda information i vila genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som tillämpar specifika kryptografkontroller och granskningsanvändning av svaga kryptografiska inställningar. Om du förstår var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta korrigerande åtgärder för att säkerställa att resurser konfigureras i enlighet med din informationssäkerhetsprincip. De principdefinitioner som tilldelas av den här skissen kräver kryptering för lagringskonton för datasjöar. kräver transparent datakryptering i SQL-databaser. och granska kryptering som saknas i SQL-databaser, diskar för virtuella datorer och automatiseringskonto.

- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera SQL DB-transparent datakryptering
- Transparent datakryptering i SQL-databaser bör aktiveras

## <a name="13-22-and-27-flaw-remediation"></a>1.3, 2.2 och 2.7 Felsanering

Den här skissen hjälper dig att hantera brister i informationssystemet genom att tilldela [Azure-principdefinitioner](../../../policy/overview.md) som övervakar systemuppdateringar som saknas, sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser. Den här skissen tilldelar också en principdefinition som säkerställer korrigering av operativsystemet för skalningsuppsättningar för virtuella datorer.

- Kräv automatisk OS-bildkorrigering på skalningsuppsättningar för virtuella datorer
- Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras
- Systemuppdateringar bör installeras på dina virtuella datorer
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Sårbarheter i säkerhetskonfigurationen på dina virtuella datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas

## <a name="61-malicious-code-protection"></a>6.1 Skydd mot skadlig kod

Den här skissen hjälper dig att hantera slutpunktsskydd, inklusive skadligt kodskydd, genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd på virtuella datorer i Azure Security Center och framtvingar Microsofts lösning mot skadlig kod på virtuella Windows-datorer.

- Distribuera standardtillägget Microsoft IaaSAntimalware för Windows Server
- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer
- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="61-malicious-code-protection--central-management"></a>6.1 Skydd mot skadlig kod | Central ledning

Den här skissen hjälper dig att hantera slutpunktsskydd, inklusive skadligt kodskydd, genom att tilldela [Azure-principdefinitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd på virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller centraliserade hanterings- och rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser.

- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer
- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1.1, 1.3, 2.2, 2.7, 2.8 och 6.4 Övervakning av informationssystem

Den här skissen hjälper dig att övervaka ditt system genom att granska och upprätthålla loggning och datasäkerhet över Azure-resurser. Närmare bestämt de principer som tilldelats granskning och framtvinga distribution av Log Analytics-agenten och förbättrade säkerhetsinställningar för SQL-databaser, lagringskonton och nätverksresurser. Dessa funktioner kan hjälpa dig att upptäcka avvikande beteende och indikatorer på attacker så att du kan vidta lämpliga åtgärder.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Avancerade datasäkerhetsinställningar för SQL-servern bör innehålla en e-postadress för att ta emot säkerhetsaviseringar
- Diagnostikloggar i Azure Stream Analytics ska aktiveras
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera granskning på SQL-servrar
- Distribuera nätverksbevakare när virtuella nätverk skapas
- Distribuera hotidentifiering på SQL-servrar

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2.2 och 2.8 Övervakning av informationssystem | Analysera trafik / Dold Exfiltration

Avancerat skydd mot hot för Azure Storage identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Skyddsaviseringar omfattar avvikande åtkomstmönster, avvikande extraherar/uppladdningar och misstänkt lagringsaktivitet. Dessa indikatorer kan hjälpa dig att upptäcka dold exfiltration av information.

- Distribuera hotidentifiering på SQL-servrar

> [!NOTE]
> Tillgängligheten för specifika Azure-principdefinitioner kan variera i Azure Government och andra nationella moln.

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollmappningen av SWIFT CSP-CSCF v2020-skissen kan du besöka följande artiklar om skissen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [SWIFT CSP-CSCF v2020-plan - Översikt](./index.md)
> [SWIFT CSP-CSCF v2020-plan - Distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
