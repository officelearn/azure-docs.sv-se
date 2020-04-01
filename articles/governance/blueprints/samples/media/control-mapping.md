---
title: Exempelkontroller för medieritning
description: Kontrollera mappningen av medieritningsexempel. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärdering.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201908"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Styra mappning av medieritningsprovet

I följande artikel beskrivs hur exempelmappningen för Azure Blueprints Media-skissen mappar till mediekontrollerna. Mer information om kontrollerna finns i [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Följande mappningar är **Media** till mediekontrollerna. Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda ** \[på\]** och välj förhandsversionen: Granska media styr det inbyggda principinitiativet.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Åtkomstkontroll

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1- Se till att det inte finns någon rotåtkomstnyckel

- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte innehåller de angivna certifikaten i Betrodd rot

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - Lösenord, PIN-koder och tokens måste skyddas

- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - Åtkomst för delade konton är förbjuden

- Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från servicebussens namnområde

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 -Systemet måste begränsa åtkomsten till behöriga användare.

- Granska obegränsad nätverksåtkomst till lagringskonton

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 -Systemet måste upprätthålla åtkomsträttigheter.

- \[Förhandsversion\]: Distribuera förutsättningar för granskning av Windows-virtuella datorers konfigurationer i "Tilldelning av användarrättigheter"

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 -Förhindra obehörig åtkomst till säkerhetsrelevant information eller säkerhetsfunktioner.

- \[Förhandsgranskning:\]Visa granskningsresultat från Windows virtuella datorer i "Säkerhetsalternativ - Systeminställningar"

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - Åtskillnad av arbetsuppgifter måste verkställas genom lämplig tilldelning av roll.

- [Preview:\]Rollbaserad åtkomstkontroll (RBAC) ska användas på Kubernetes Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40- Se till att systemen inte ansluter betrodda nätverk och ej betrodda nätverk samtidigt.

- \[Förhandsversion:\]Distribuera förutsättningar för granskning av Windows-virtuella datorers konfigurationer i "Säkerhetsalternativ - Nätverksåtkomst"

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC- 1.43 - Fjärråtkomst för icke-anställda måste begränsas så att åtkomst endast tillåts till särskilt godkända informationssystem

- \[Preview\]: Visa granskningsresultat från virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50- Logga säkerhetsrelaterade händelser för alla informationssystemkomponenter.

- Diagnostikloggar i Logic Apps bör aktiveras

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54- Se till att MFA (Multi Factor Authentication) är aktiverat för alla användare av molnkonsoler.

- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- MFA (MultiFaktor Authentication) bör aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra brott mot konton eller resurser.

## <a name="auditing--logging"></a>Granskning & loggning

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1- Lyckade och misslyckade händelser måste loggas.

- Diagnostikloggar i Söktjänster bör aktiveras

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - Nätverksenheter/instanser måste logga alla händelser som klassificeras som en kritisk säkerhetshändelse av den nätverksenheten/instansen (ELBs, brandväggar för webbprogram osv.)

- \[Förhandsgranska\]: Visa granskningsresultat från Windows virtuella datorer i "Säkerhetsalternativ - Konton"

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17- Servrar/instanser måste logga alla händelser som klassificeras som en kritisk säkerhetshändelse av den servern/instansen

- \[Förhandsgranska\]: Visa granskningsresultat från Windows virtuella datorer i "Säkerhetsalternativ - Konton"

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - Domänhändelser måste logga alla händelser som klassificeras som en kritisk eller hög säkerhetshändelse av domänhanteringsprogrammet

- \[Förhandsgranska\]: Visa granskningsresultat från Windows virtuella datorer i "Säkerhetsalternativ - Konton"
- \[Förhandsversion:\]Distribuera förutsättningar för granskning av Windows-virtuella datorers konfigurationer i "Säkerhetsalternativ - Microsoft-nätverksklient"

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20- Domänhändelser måste logga alla händelser som klassificeras som en kritisk säkerhetshändelse med domänsäkerhetskontroller

- \[Förhandsgranska\]: Visa granskningsresultat från Windows virtuella datorer i "Säkerhetsalternativ - Konton"

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21- Domänhändelser måste logga åtkomst eller ändringar i domänloggen

- \[Preview\]: Visa granskningsresultat från Windows virtuella datorer konfigurationer i "Säkerhetsalternativ - Återställningskonsol"

## <a name="cryptographic-controls"></a>Kryptografiska kontroller

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2- Program och system måste använda aktuella kryptografiska lösningar för att skydda data.

- Transparent datakryptering i SQL-databaser bör aktiveras
- Transparent datakryptering bör aktiveras för att skydda data i vila och uppfylla efterlevnadskraven

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5- Digitala certifikat måste undertecknas av en godkänd certifikatutfärdare.

- \[Förhandsgranskning\]: Visa granskningsresultat från virtuella Windows-datorer som innehåller certifikat som löper ut inom det angivna antalet dagar

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6- Digitala certifikat måste tilldelas en användare eller enhet på ett unikt sätt.

- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som innehåller certifikat som löper ut inom det angivna antalet dagar

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7- Kryptografiskt material måste lagras för att möjliggöra dekryptering av posterna under den tid som posterna behålls.

- Diskkryptering bör tillämpas på virtuella datorer
- Virtuella datorer utan aktiverad diskkryptering övervakas av Azure Security Center som rekommendationer

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8- Hemliga och privata nycklar måste förvaras säkert.

- Transparent datakryptering i SQL-databaser bör aktiveras
- Transparent datakryptering bör aktiveras för att skydda data i vila och uppfylla efterlevnadskraven

## <a name="change--config-management"></a>Ändra & Config-hantering

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2- Endast behöriga användare får implementera godkända ändringar i systemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12- Upprätthålla en uppdaterad, fullständig, korrekt och lättillgänglig baslinjekonfiguration av informationssystemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13- Använd automatiserade verktyg för att upprätthålla en baslinjekonfiguration av informationssystemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14- Identifiera och inaktivera onödiga och/eller icke-säkra funktioner, portar, protokoll och tjänster.

- Nätverksgränssnitt bör inaktivera IP-vidarebefordran
- \[Preview\]: IP-vidarebefordran på din virtuella dator bör inaktiveras

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19- Övervaka ändringar i säkerhetskonfigurationsinställningarna.

- Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22- Se till att endast auktoriserad programvara och uppdateringar är installerade på företagets system.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

## <a name="identity--authentication"></a>Identitets- & autentisering

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1- Användarkonton måste tilldelas personer unikt för att få tillgång till information som inte klassificeras som offentlig. Konto-ID:er måste konstrueras med ett standardiserat logiskt format.

- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter bör tas bort från prenumerationen för att förhindra oövervakad åtkomst.

## <a name="network-security"></a>Nätverkssäkerhet

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2- Åtkomst till nätverksenhetshanteringsfunktioner är begränsad till behöriga användare.

- \[Förhandsversion:\]Distribuera förutsättningar för granskning av Windows-virtuella datorers konfigurationer i "Säkerhetsalternativ - Nätverksåtkomst"

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3- Alla nätverksenheter måste konfigureras med hjälp av sina säkraste konfigurationer.

- \[Förhandsversion:\]Distribuera förutsättningar för granskning av Windows-virtuella datorers konfigurationer i "Säkerhetsalternativ - Nätverksåtkomst"

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5- Alla nätverksanslutningar till ett system via en brandvägg måste godkännas och granskas regelbundet.

- \[Förhandsgranskning:\]Visa granskningsresultat från Windows-virtuella datorer i "Egenskaper för Windows-brandväggen"

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7- Lämpliga kontroller måste finnas vid varje gräns mellan ett betrott nätverk och alla ej betrodda eller offentliga nätverk.

- \[Förhandsversion:\]Distribuera förutsättningar för granskning av Windows-virtuella datorers konfigurationer i "Egenskaper för Windows-brandväggen"

## <a name="security-planning"></a>Säkerhetsplanering

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3- Hot måste identifieras som kan påverka sekretessen, integriteten eller tillgängligheten av företagets information och innehåll negativt tillsammans med sannolikheten för att de ska inträffa.

- Avancerade typer av skydd mot hot bör ställas in på alla i SQL-hanterade instans avancerade datasäkerhetsinställningar

### <a name="security-continuity"></a>Kontinuitet i säkerheten

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5- Data i långtidslagring måste vara tillgängliga under hela lagringsperioden och skyddas mot medieförsämring och teknikförändringar.

- SQL-servrar bör konfigureras med granskning kvarhållningsdagar som är större än 90 dagar.
- Granska SQL-servrar som konfigurerats med en granskningskvarhållningsperiod på mindre än 90 dagar.

## <a name="system-integrity"></a>Systemets integritet

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3- Endast behörig personal får övervaka nätverks- och användaraktiviteter.

- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Övervaka sårbarhetsbedömningssökningsresultat och rekommendationer för hur du åtgärdar databassårbarheter.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4- Internet-vända system måste ha intrång upptäckt.

- Distribuera hotidentifiering på SQL-servrar

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13- Standardiserad centralt hanterad anti-malware programvara bör genomföras i hela företaget.

- Distribuera standardtillägget Microsoft IaaSAntimalware för Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14- Anti-malware programvara måste skanna datorer och media varje vecka på ett minimum.

- Distribuera standardtillägget Microsoft IaaSAntimalware för Windows Server

## <a name="vulnerability-management"></a>Sårbarhetshantering

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4- Se till att program genomsöks efter säkerhetsproblem varje månad.

- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Granska operativsystemets sårbarheter på dina skalningsuppsättningar för virtuella datorer för att skydda dem från attacker.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5- Se till att sårbarheter identifieras, paras ihop med hot och utvärderas för risk.

- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Granska operativsystemets sårbarheter på dina skalningsuppsättningar för virtuella datorer för att skydda dem från attacker.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6- Se till att identifierade sårbarheter har åtgärdats inom en ömsesidigt överenskommen tidsplan.

- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Granska operativsystemets sårbarheter på dina skalningsuppsättningar för virtuella datorer för att skydda dem från attacker.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7- Åtkomst till och användning av sårbarhetshanteringssystem måste begränsas till behörig personal.

- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Granska operativsystemets sårbarheter på dina skalningsuppsättningar för virtuella datorer för att skydda dem från attacker.

> [!NOTE]
> Tillgängligheten för specifika Azure-principdefinitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Du har granskat kontrollmappningen av medieritningsprovet. Gå sedan till följande artiklar om du vill veta mer om översikten och hur du distribuerar det här exemplet:

> [!div class="next step action"]
> [Medieritning - Översikt](./control-mapping.md)
> [Media blueprint - Distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
