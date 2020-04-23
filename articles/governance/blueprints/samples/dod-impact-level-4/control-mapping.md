---
title: Provkontroller för DoD Impact Level 4-skiss
description: Kontrollera mappningen av skissexemplet DoD Impact Level 4. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärdering.
ms.date: 03/06/2020
ms.topic: sample
ms.openlocfilehash: a11a8637618bb42831ccf1a7001e4da40529ba74
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024837"
---
# <a name="control-mapping-of-the-dod-impact-level-4-blueprint-sample"></a>Kontrollkartläggning av ritningsprovet dod impact level 4

I följande artikel beskrivs hur azure blueprints Department of Defense Impact Level 4 (DoD IL4) skiss exempelmappningar till DoD Impact Level 4-kontrollerna. Mer information om kontrollerna finns i [DoD Cloud Computing Security Requirements Guide (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf). Defense Information Systems Agency (DISA) är en byrå för det amerikanska försvarsdepartementet (DoD) som ansvarar för att utveckla och underhålla DoD Cloud Computing Security Requirements Guide (SRG). SRG definierar grundläggande säkerhetskrav för molntjänstleverantörer (CSP: er) som är värd för DoD-information, system och program och för DoD:s användning av molntjänster.  

Följande mappningar är till **Kontrollerna för DoD-påverkansnivå 4.** Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj det inbyggda principinitiativet ** \[Förhandsgranska:\]DoD Impact Level 4.**

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden.
> Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-4/control-mapping.md).

## <a name="ac-2-account-management"></a>AC-2 Kontohantering

Den här skissen hjälper dig att granska konton som kanske inte uppfyller organisationens kontohanteringskrav. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som granskar externa konton med läs-, skriv- och ägarbehörigheter för en prenumeration och föråldrade konton. Genom att granska de räkenskaper som granskas av dessa principer kan du vidta lämpliga åtgärder för att säkerställa att kontohanteringskraven uppfylls.

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Kontohantering | Rollbaserade system

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar också [Azure-principdefinitioner](../../../policy/overview.md) för granskning av Azure Active Directory-autentisering för SQL-servrar och tjänstinfrastruktur. Med Hjälp av Azure Active Directory-autentisering möjliggör förenklad behörighetshantering och centraliserad identitetshantering för databasanvändare och andra Microsoft-tjänster. Dessutom tilldelar den här skissen en Azure-principdefinition för att granska användningen av anpassade RBAC-regler. Att förstå var anpassade RBAC-regler implementeras kan hjälpa dig att verifiera behov och korrekt implementering, eftersom anpassade RBAC-regler är felbenägna.

- En Azure Active Directory-administratör bör etableras för SQL-servrar
- Granskningsanvändning av anpassade RBAC-regler
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Kontohantering | Kontoövervakning / Atypisk användning

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. Alla JIT-begäranden om åtkomst till virtuella datorer loggas i aktivitetsloggen så att du kan övervaka för atypisk användning. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Information Flow Verkställighet

Korsursprungsresursdelning (CORS) kan tillåta att App Services-resurser begärs från en extern domän. Microsoft rekommenderar att du endast tillåter nödvändiga domäner att interagera med dina API-, funktions- och webbprogram. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att övervaka BEGRÄNSNINGAR för ÅTKOMST TILL CORS-resurser i Azure Security Center. Genom att förstå CORS-implementeringar kan du kontrollera att informationsflödeskontroller implementeras.

- CORS bör inte tillåta alla resurser att komma åt ditt webbprogram

## <a name="ac-5-separation-of-duties"></a>AC-5 Tulldelning

Att bara ha en Azure-prenumerationsägare tillåter inte administrativ redundans. Omvänt kan för många Azure-prenumerationsägare öka risken för ett intrång via ett komprometterat ägarkonto. Den här skissen hjälper dig att underhålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar antalet ägare för Azure-prenumerationer. Den här skissen tilldelar också Azure-principdefinitioner som hjälper dig att kontrollera medlemskap i gruppen Administratörer på virtuella Datorer i Windows. Genom att hantera administratörsbehörighet och administratörsbehörighet för virtuella datorer kan du implementera lämplig åtskillnad mellan uppgifter.

- Högst 3 ägare bör utses för din prenumeration
- Granska virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Granska virtuella windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Distribuera krav för granskning av virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för granskning av virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Minst privilegium | Granskning av användarbehörigheter

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) till granskningskonton som ska prioriteras för granskning. Genom att granska dessa kontoindikatorer kan du se till att lägsta behörighetskontroller implementeras.

- Högst 3 ägare bör utses för din prenumeration
- Granska virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Granska virtuella windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Distribuera krav för granskning av virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för granskning av virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Fjärråtkomst | Automatiserad övervakning / kontroll

Den här skissen hjälper dig att övervaka och styra fjärråtkomst genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att övervaka att fjärrfelsökning för Azure App Service-programmet är inaktiverat och principdefinitioner som granskar virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord. Den här skissen tilldelar också en Azure-principdefinition som hjälper dig att övervaka obegränsad åtkomst till lagringskonton. Genom att övervaka dessa indikatorer kan du se till att fjärråtkomstmetoderna följer din säkerhetsprincip.

- \[Preview\]: Granska virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- \[Preview\]: Distribuera krav för att granska virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- Granska obegränsad nätverksåtkomst till lagringskonton
- Fjärrfelsökning bör inaktiveras för API-app
- Fjärrfelsökning bör inaktiveras för Funktionsapp
- Fjärrfelsökning bör inaktiveras för webbprogram

## <a name="ac-23-data-mining"></a>AC-23 Data Mining

Den här skissen innehåller principdefinitioner som hjälper dig att se till att datasäkerhetsmeddelanden är korrekt aktiverade. Dessutom säkerställer den här skissen att granskning och avancerad datasäkerhet konfigureras på SQL-servrar.

- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Avancerad datasäkerhet bör aktiveras på dina SQL-hanterade instanser
- Avancerade typer av skydd mot hot bör ställas in på alla i avancerade datasäkerhetsinställningar för SQL-servern
- Avancerade typer av skydd mot hot bör ställas in på alla i SQL-hanterade instans avancerade datasäkerhetsinställningar
- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server
- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade datasäkerhetsinställningar för SQL-servern
- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade inställningar för SQL-hanterad instans
- Avancerade datasäkerhetsinställningar för SQL-servern bör innehålla en e-postadress för att ta emot säkerhetsaviseringar
- Avancerade datasäkerhetsinställningar för SQL-hanterad instans bör innehålla en e-postadress för att ta emot säkerhetsaviseringar

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Innehållet i revisionshandlingar | Centraliserad hantering av planerat granskningspostinnehåll

Loggdata som samlas in av Azure Monitor lagras på en Log Analytics-arbetsyta som möjliggör centraliserad konfiguration och hantering. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar och framtvingar distribution av Log Analytics-agenten på virtuella Azure-datorer.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Arbetsyta för granskningslogganalys för virtuell dator – rapportfelmatchning
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 Svar på fel i granskningsbehandling

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som övervakar gransknings- och händelseloggningskonfigurationer. Övervakning av dessa konfigurationer kan ge en indikator på ett granskningssystemfel eller felkonfiguration och hjälpa dig att vidta korrigerande åtgärder.

- Granska diagnostikinställning
- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server
- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Granskning, analys och rapportering av revision | Central granskning och analys

Loggdata som samlas in av Azure Monitor lagras på en Log Analytics-arbetsyta som möjliggör centraliserad rapportering och analys. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar och framtvingar distribution av Log Analytics-agenten på virtuella Azure-datorer.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Arbetsyta för granskningslogganalys för virtuell dator – rapportfelmatchning
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) Granskning, analys och rapportering av revision | Funktioner för integration / skanning och övervakning

Den här skissen innehåller principdefinitioner som granskar poster med analys av sårbarhetsbedömning på virtuella datorer, skaluppsättningar för virtuella datorer, SQL-hanterade instanser och SQL-servrar.
Dessa principdefinitioner granskar också konfigurationen av diagnostikloggar för att ge insikt i åtgärder som utförs inom Azure-resurser. Dessa insikter ger information i realtid om säkerhetstillståndet för dina distribuerade resurser och kan hjälpa dig att prioritera reparationsåtgärder.
För detaljerad sårbarhetsskanning och övervakning rekommenderar vi att du även använder Azure Sentinel och Azure Security Center.

- \[Preview\]: Sårbarhetsbedömning bör aktiveras på virtuella datorer
- Sårbarhetsbedömning bör aktiveras på dina SQL-servrar
- Granska diagnostikinställning
- Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser
- Sårbarhetsbedömning bör aktiveras på dina SQL-servrar
- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad

## <a name="au-12-audit-generation"></a>AU-12 Audit Generation

Den här skissen innehåller principdefinitioner som granskar och framtvingar distribution av Log Analytics-agenten på virtuella Azure-datorer och konfiguration av granskningsinställningar för andra Azure-resurstyper.
Dessa principdefinitioner granskar också konfigurationen av diagnostikloggar för att ge insikt i åtgärder som utförs inom Azure-resurser. Dessutom konfigureras granskning och avancerad datasäkerhet på SQL-servrar.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Arbetsyta för granskningslogganalys för virtuell dator – rapportfelmatchning
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer
- Granska diagnostikinställning
- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server
- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera granskning på SQL-servrar
- Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) Revisionsgenerering | Systemomfattande / tidsrelerade verifieringskedja

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar logginställningar på Azure-resurser.
Den här inbyggda principen kräver att du anger en matris med resurstyper för att kontrollera om diagnostikinställningar är aktiverade eller inte.

- Granska diagnostikinställning

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Minst funktionalitet | Förhindra programkörning

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll kan köras i ett tvingande läge som förhindrar att ett icke-godkänt program körs. Den här skissen tilldelar en Azure Policy-definition som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Minst funktionalitet | Auktoriserad programvara / vitlistning

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll hjälper dig att skapa godkända programlistor för dina virtuella datorer. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="cm-11-user-installed-software"></a>CM-11 Programvara för användarinstallerad programvara

Adaptiv programkontroll i Azure Security Center är en intelligent, automatiserad heltäckande lösning för vitlistning av program som kan blockera eller förhindra att viss programvara körs på dina virtuella datorer. Programkontroll kan hjälpa dig att genomdriva och övervaka efterlevnaden av principer för begränsning av programvara. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer där ett program vitlista rekommenderas men ännu inte har konfigurerats.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="cp-7-alternate-processing-site"></a>Alternativ bearbetningsplats för CP-7

Azure Site Recovery replikerar arbetsbelastningar som körs på virtuella datorer från en primär plats till en sekundär plats. Om ett avbrott inträffar på den primära platsen misslyckas arbetsbelastningen över den sekundära platsen. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som granskar virtuella datorer utan haveriberedskap konfigurerad. Övervakning av den här indikatorn kan hjälpa dig att se till att nödvändiga beredskapskontroller finns på plats.

- Granska virtuella datorer utan haveriberedskap konfigurerad

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) Säkerhetskopiering av informationssystem | Överför till alternativ lagringsplats

Den här skissen tilldelar Azure-principdefinitioner som granskar organisationens systemsäkerhetsinformation till den alternativa lagringsplatsen elektroniskt. För fysisk leverans av lagringsmetadata bör du överväga att använda Azure Data Box.

- Geo redundant lagring bör aktiveras för lagringskonton
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database för PostgreSQL
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database for MySQL
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database för MariaDB
- Långsiktig geo-redundant säkerhetskopiering bör aktiveras för Azure SQL-databaser

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identifiering och autentisering (organisatoriska användare) | Nätverksåtkomst till privilegierade konton

Den här skissen hjälper dig att begränsa och kontrollera privilegierad åtkomst genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att granska konton med ägare och/eller skrivbehörighet som inte har multifaktorautentisering aktiverad. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen äventyras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras.

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska aktiveras på konton med skrivbehörighet för din prenumeration

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identifiering och autentisering (organisatoriska användare) | Nätverksåtkomst till konton som inte är privilegierade

Den här skissen hjälper dig att begränsa och kontrollera åtkomst genom att tilldela en [Azure-principdefinition](../../../policy/overview.md) för granskning av konton med läsbehörigheter som inte har multifaktorautentisering aktiverad. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen äventyras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras.

- MFA ska aktiveras på konton med läsbehörighet för din prenumeration

## <a name="ia-5-authenticator-management"></a>IA-5 Autentiseringshantering

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som granskar virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord och/eller har felaktiga behörigheter för passwd-filen. Den här skissen tilldelar också principdefinitioner som granskar konfigurationen av lösenordskrypteringstypen för virtuella Windows-datorer. Genom att övervaka dessa indikatorer kan du se till att systemautentiseringspersonerna följer organisationens identifierings- och autentiseringsprincip.

- \[Preview\]: Audit Linux virtuella datorer som inte har passwd filbehörigheter inställd på 0644
- \[Preview:\]Granska virtuella Linux-datorer som har konton utan lösenord
- \[Preview\]: Granska virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Linux-datorer som inte har passwd-filbehörigheterna inställda på 0644
- \[Preview\]: Distribuera krav för att granska virtuella Linux-datorer som har konton utan lösenord
- \[Preview\]: Distribuera krav för granskning av virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Förvaltning av autentiseringsperson | Lösenordsbaserad autentisering

Den här skissen hjälper dig att framtvinga starka lösenord genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar virtuella Windows-datorer som inte tillämpar minsta styrka och andra lösenordskrav. Medvetenhet om virtuella datorer i strid med principen om lösenordsstyrka hjälper dig att vidta korrigerande åtgärder för att säkerställa att lösenord för alla användarkonton för virtuella datorer följer organisationens lösenordsprincip.

- \[Preview\]: Granska virtuella windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Preview\]: Granska virtuella windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Preview\]: Granska virtuella windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsgranskning\]: Granska virtuella windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Preview\]: Granska virtuella windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Preview\]: Granska virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Förhandsgranskning\]: Distribuera krav för granskning av virtuella Windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Preview\]: Distribuera krav för granskning av virtuella Windows-datorer som inte lagrar lösenord med reversibla kryptering

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) IncidentRapportering | Sårbarheter relaterade till incidenter

Den här skissen innehåller principdefinitioner som granskar poster med analys av sårbarhetsbedömning på virtuella datorer, skaluppsättningar för virtuella datorer och SQL-servrar. Dessa insikter ger information i realtid om säkerhetstillståndet för dina distribuerade resurser och kan hjälpa dig att prioritera reparationsåtgärder.

- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning
- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas
- Säkerhetsproblem i behållarens säkerhetskonfigurationer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas

## <a name="ra-5-vulnerability-scanning"></a>RA-5 Sårbarhetsskanning

Den här skissen hjälper dig att hantera sårbarheter i informationssystem genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser. Den här skissen tilldelar också principdefinitioner som granskar och framtvingar avancerad datasäkerhet på SQL-servrar. Avancerad datasäkerhet inkluderade sårbarhetsbedömning och avancerade hotskyddsfunktioner som hjälper dig att förstå sårbarheter i dina distribuerade resurser.

- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Sårbarheter i säkerhetskonfigurationen på dina virtuella datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Skydd över överbelastning

Azures ddos-standardnivå (Distributed Denial of Service) ger ytterligare funktioner och begränsningsfunktioner över den grundläggande tjänstnivån. Dessa ytterligare funktioner inkluderar Azure Monitor-integrering och möjligheten att granska rapporter efter attacken. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som granskar om DDoS-standardnivån är aktiverad. Genom att förstå kapacitetsskillnaden mellan tjänstnivåerna kan du välja den bästa lösningen för att hantera överbelastningsskydd för din Azure-miljö.

- DDoS Protection Standard bör aktiveras

## <a name="sc-7-boundary-protection"></a>Gränsskydd för SC-7

Den här skissen hjälper dig att hantera och styra systemgränsen genom att tilldela en [Azure Policy-definition](../../../policy/overview.md) som övervakar för rekommendationer för nätverkssäkerhetsgrupper som härdar i Azure Security Center. Azure Security Center analyserar trafikmönster för Internet som är vänt mot virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgrupper för att minska den potentiella angreppsytan.
Dessutom tilldelar den här skissen också principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och lagringskonton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informationssystemet.

- Regler för nätverkssäkerhetsgrupp för internetvända virtuella datorer bör skärpas
- Åtkomst via internetvänd slutpunkt bör begränsas
- NSGs-reglerna för webbapplikationer på IaaS bör härdas
- Granska obegränsad nätverksåtkomst till lagringskonton

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Gränsskydd | Åtkomstpunkter

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. JIT virtuell dator åtkomst hjälper dig att begränsa antalet externa anslutningar till dina resurser i Azure. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Gränsskydd | Externa telekommunikationstjänster

Åtkomst till virtuella datorer (JUST-in-time) låser sig för inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som enkel åtkomst till virtuella datorer vid behov kan anslutas till virtuella datorer. ÅTKOMST till virtuella datorer för JIT hjälper dig att hantera undantag från din trafikflödesprincip genom att underlätta åtkomstbegäran och godkännandeprocesser. Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka virtuella datorer som kan stödja just-in-time-åtkomst men som ännu inte har konfigurerats.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Sekretess och integritet för överföring | Kryptografiskt eller alternativt fysiskt skydd

Den här skissen hjälper dig att skydda den konfidentiella och integriteten hos överförd information genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att övervaka kryptografisk mekanism som implementeras för kommunikationsprotokoll. Om du säkerställer att kommunikationen är korrekt krypterad kan du uppfylla organisationens krav eller skydda information från obehörigt röjande och ändring.

- API-app bör endast vara tillgänglig via HTTPS
- Granska Windows-webbservrar som inte använder säkra kommunikationsprotokoll
- Distribuera krav för granskning av Windows-webbservrar som inte använder säkra kommunikationsprotokoll
- Funktionsappen ska endast vara tillgänglig via HTTPS
- Endast säkra anslutningar till Redis-cachen ska aktiveras
- Säker överföring till lagringskonton ska vara aktiverat
- Webbprogrammet ska endast vara tillgängligt via HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Skydd av information i vila | Kryptografiskt skydd

Den här skissen hjälper dig att tillämpa din princip om användningen av kryptografkontroller för att skydda information i vila genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som tillämpar specifika kryptografkontroller och granskningsanvändning av svaga kryptografiska inställningar. Om du förstår var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta korrigerande åtgärder för att säkerställa att resurser konfigureras i enlighet med din informationssäkerhetsprincip. De principdefinitioner som tilldelas av den här skissen kräver kryptering för lagringskonton för datasjöar. kräver transparent datakryptering i SQL-databaser. och granska kryptering som saknas i SQL-databaser, diskar för virtuella datorer och automatiseringskonto.

- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera SQL DB-transparent datakryptering
- Diskkryptering bör tillämpas på virtuella datorer
- Kräv kryptering på DataSjölagringskonton
- Transparent datakryptering i SQL-databaser bör aktiveras

## <a name="si-2-flaw-remediation"></a>SI-2 Fel sanering

Den här skissen hjälper dig att hantera brister i informationssystemet genom att tilldela [Azure-principdefinitioner](../../../policy/overview.md) som övervakar systemuppdateringar som saknas, sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser. Den här skissen tilldelar också en principdefinition som säkerställer korrigering av operativsystemet för skalningsuppsättningar för virtuella datorer.

- Kräv automatisk OS-bildkorrigering på skalningsuppsättningar för virtuella datorer
- Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras
- Systemuppdateringar bör installeras på dina virtuella datorer
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Sårbarheter i säkerhetskonfigurationen på dina virtuella datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>SI-02 (06) Felsanering | Borttagning av tidigare versioner av programvara / firmware

Den här skissen tilldelar principdefinitioner som hjälper dig att se till att program använder den senaste versionen av .NET Framework, HTTP, Java, PHP, Python och TLS. Den här skissen tilldelar också en principdefinition som säkerställer att Kubernetes Services uppgraderas till den icke-sårbara versionen.

- Se till att ".Net Framework"-versionen är den senaste, om den används som en del av API-appen
- Se till att ".Net Framework"-versionen är den senaste, om den används som en del av funktionsappen
- Se till att ".Net Framework"-versionen är den senaste, om den används som en del av webbappen
- Kontrollera att HTTP-versionen är den senaste, om den används för att köra Api-appen
- Kontrollera att HTTP-versionen är den senaste, om den används för att köra funktionsappen
- Kontrollera att HTTP-versionen är den senaste, om den används för att köra webbappen
- Se till att "Java-versionen" är den senaste, om den används som en del av Api-appen
- Se till att "Java-versionen" är den senaste, om den används som en del av funktionsappen
- Se till att "Java-versionen" är den senaste, om den används som en del av webbappen
- Se till att "PHP-versionen" är den senaste, om den används som en del av Api-appen
- Se till att "PHP-versionen" är den senaste, om den används som en del av funktionsappen
- Se till att "PHP-versionen" är den senaste, om den används som en del av webbappen
- Se till att Python-versionen är den senaste, om den används som en del av Api-appen
- Se till att Python-versionen är den senaste, om den används som en del av funktionsappen
- Se till att Python-versionen är den senaste, om den används som en del av webbappen
- Senaste TLS-versionen ska användas i din API-app
- Senaste TLS-versionen ska användas i din funktionsapp
- Senaste TLS-versionen ska användas i din Webbapp
- \[Preview\]: Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes version

## <a name="si-3-malicious-code-protection"></a>SI-3 Skydd mot skadlig kod

Den här skissen hjälper dig att hantera slutpunktsskydd, inklusive skadligt kodskydd, genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd på virtuella datorer i Azure Security Center och framtvingar Microsofts lösning mot skadlig kod på virtuella Windows-datorer.

- Distribuera standardtillägget Microsoft IaaSAntimalware för Windows Server
- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer
- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Skydd mot skadlig kod | Central ledning

Den här skissen hjälper dig att hantera slutpunktsskydd, inklusive skadligt kodskydd, genom att tilldela [Azure-principdefinitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd på virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller centraliserade hanterings- och rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser.

- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer
- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="si-4-information-system-monitoring"></a>ÖVERVAKNING AV INFORMATIONSSYSTEM I SI-4

Den här skissen hjälper dig att övervaka ditt system genom att granska och upprätthålla loggning och datasäkerhet över Azure-resurser. Närmare bestämt de principer som tilldelats granskning och framtvinga distribution av Log Analytics-agenten och förbättrade säkerhetsinställningar för SQL-databaser, lagringskonton och nätverksresurser. Dessa funktioner kan hjälpa dig att upptäcka avvikande beteende och indikatorer på attacker så att du kan vidta lämpliga åtgärder.

- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Arbetsyta för granskningslogganalys för virtuell dator – rapportfelmatchning
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer
- Avancerad datasäkerhet bör aktiveras på dina hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Distribuera avancerad datasäkerhet på SQL-servrar
- Distribuera avancerat skydd mot hot på lagringskonton
- Distribuera granskning på SQL-servrar
- Distribuera nätverksbevakare när virtuella nätverk skapas
- Distribuera hotidentifiering på SQL-servrar
- Tillåtna platser
- Tillåtna platser för resursgrupper

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) Övervakning av informationssystem | Automatiska varningar

Den här skissen innehåller principdefinitioner som hjälper dig att se till att datasäkerhetsmeddelanden är korrekt aktiverade. Dessutom säkerställer den här skissen att standardprisnivån är aktiverad för Azure Security Center. Observera att standardprisnivån möjliggör hotidentifiering för nätverk och virtuella datorer, vilket ger hotinformation, avvikelseidentifiering och beteendeanalys i Azure Security Center.

- E-postmeddelande till prenumerationsägaren för varningar om hög allvarlighetsgrad bör aktiveras
- En e-postadress för säkerhetskontakter ska anges för din prenumeration 
- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade inställningar för SQL-hanterad instans 
- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade datasäkerhetsinställningar för SQL-servern 
- Ett telefonnummer för säkerhetskontakter ska anges för din prenumeration
- Avancerade datasäkerhetsinställningar för SQL-servern bör innehålla en e-postadress för att ta emot säkerhetsaviseringar
- Standardprisnivå för Security Center-standard bör väljas

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Övervakning av informationssystem | Analysera trafik / Dold Exfiltration

Avancerat skydd mot hot för Azure Storage identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Skyddsaviseringar omfattar avvikande åtkomstmönster, avvikande extraherar/uppladdningar och misstänkt lagringsaktivitet. Dessa indikatorer kan hjälpa dig att upptäcka dold exfiltration av information.

- Distribuera avancerat skydd mot hot på lagringskonton

> [!NOTE]
> Tillgängligheten för specifika Azure-principdefinitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollmappningen av DoD Impact Level 4-skissen kan du besöka följande artiklar om hur du använder skissen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [DoD Impact Level 4-skiss - Översikt](./index.md)
> [DoD Impact Level 4-skiss - Distributionssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).