---
title: CIS Microsoft Azure Foundations Benchmark-exempelkontroller
description: Rekommendationsmappning av CIS Microsoft Azure Foundations Benchmark-skissexempel till Azure Policy.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: ea61ae4ea05b34c785485cbb5fd39c8a772565e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656970"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Rekommendationsmappning av cis Microsoft Azure Foundations benchmark-skissprov

I följande artikel beskrivs hur azure blueprints CIS Microsoft Azure Foundations Benchmark-skissen mappas till CIS Microsoft Azure Foundations Benchmark-rekommendationer. Mer information om rekommendationerna finns i [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Följande mappningar är till **CIS Microsoft Azure Foundations Benchmark v1.1.0** rekommendationer. Använd navigeringen till höger för att hoppa direkt till en specifik rekommendationsmappning.
Många av de mappade rekommendationerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj ** \[rekommendationerna för Förhandsgranskning\] av CiS Microsoft Azure Foundations Benchmark v1.1.0 och distribuera specifika VM-tillägg för att stödja inbyggda** principinitiativ för granskningskrav.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Se till att multifaktorautentisering är aktiverat för alla privilegierade användare

Den här skissen tilldelar [Azure-principdefinitioner](../../../policy/overview.md) som hjälper dig att övervaka när multifaktorautentisering inte är aktiverad på privilegierade Azure Active Directory-konton.

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska aktiveras på konton med skrivbehörighet för din prenumeration

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Se till att multifaktorautentisering är aktiverat för alla användare som inte har förmånen

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att övervaka när multifaktorautentisering inte är aktiverad på icke-privilegierade Azure Active Directory-konton.

- MFA ska aktiveras på konton med läsbehörighet för din prenumeration

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Se till att det inte finns några gästanvändare

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att övervaka för gästkonton som kan behöva tas bort.

- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Se till att inga anpassade prenumerationsägare skapas

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att övervaka anpassade prenumerationsägare roller som kan behöva tas bort.

- Anpassade prenumerationsägare roller bör inte finnas

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Se till att standardprisnivån väljs

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka nätverk och virtuella datorer där Security Center-standardnivån inte är aktiverad.

 - Standardprisnivå för Security Center-standard bör väljas

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Se till att "automatisk tillhandahållande av övervakningsagent" är inställt på "På"

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att säkerställa automatisk etablering av Log Analytics-agenten är aktiverad.

- Automatisk etablering av log analytics-övervakningsagenten bör aktiveras på din prenumeration

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Se till att ASC:s standardprincipinställning "Övervaka systemuppdateringar" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att systemuppdateringar installeras på virtuella datorer.

- Systemuppdateringar ska ha installerats på dina datorer

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Se till att ASC:s standardprincipinställning "Övervaka os-sårbarheter" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka för omediatmediated virtuella dator sårbarheter.

- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Se till att ASC:s standardprincipinställning "Övervaka slutpunktsskydd" inte är "inaktiverad"

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att slutpunktsskydd är aktiverat på virtuella datorer.

- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Se till att ASC:s standardprincipinställning "Bildskärmsdiskkryptering" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att se till att virtuella datordiskar är krypterade.

- Diskkryptering bör tillämpas på virtuella datorer

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Se till att ASC:s standardprincipinställning "Övervaka nätverkssäkerhetsgrupper" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att skydda internetvända virtuella datorer.

- Regler för nätverkssäkerhetsgrupp för internetvända virtuella datorer bör skärpas

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Se till att ASC:s standardprincipinställning "Monitor Web Application Firewall" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att skydda virtuella datorer som kör webbprogram.

- NSGs-reglerna för webbapplikationer på IaaS bör härdas

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Se till att ASC:s standardprincipinställning "Aktivera övervakning av nästa generations brandvägg(NGFW) inte är "inaktiverad"

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper till att skydda undernät och virtuella datorer från hot genom att begränsa åtkomsten. Security Center-principen som refereras av den här CIS Microsoft Azure Foundations Benchmark-rekommendationen har ersatts av två nya rekommendationer. De riktlinjer som nämns nedan tar upp de nya rekommendationerna.

- Undernät ska associeras med en nätverkssäkerhetsgrupp
- Virtuella datorer bör associeras med en nätverkssäkerhetsgrupp

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Se till att ASC:s standardprincipinställning "Övervaka sårbarhetsbedömning" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att säkerhetsproblem upptäcks och åtgärdas.

- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Se till att ASC:s standardprincipinställning "Monitor Storage Blob Encryption" inte är "inaktiverad"

Azure Storage-kryptering är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras. (Detta är en standard Azure-funktion, det finns ingen principtilldelning.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Se till att ASC:s standardprincipinställning "Övervaka JIT-nätverksåtkomst" inte är "inaktiverad"

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att kontrollera åtkomsten till virtuella datorer.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Se till att ASC:s standardprincipinställning "Monitor Adaptive Application Whitelisting" inte är "inaktiverad"

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att anpassade programkontroller är aktiverade på virtuella datorer.

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Se till att ASC:s standardprincipinställning "Monitor SQL Auditing" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som säkerställer att SQL-servergranskning är aktiverad.

- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Se till att ASC-standardprincipinställningen "Övervaka SQL-kryptering" inte är "inaktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att säkerställa transparent datakryptering är aktiverad på SQL-databaser.

- Transparent datakryptering i SQL-databaser bör aktiveras

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Se till att e-postmeddelanden med säkerhetskontakter är inställda

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att säkerhetsmeddelanden är korrekt aktiverade

- En e-postadress för säkerhetskontakter ska anges för din prenumeration

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Se till att säkerhetskontakten "Telefonnummer" är inställt

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att säkerhetsmeddelanden är korrekt aktiverade

- Ett telefonnummer för säkerhetskontakter ska anges för din prenumeration

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Se till att "Skicka e-postmeddelande för varningar om hög allvarlighetsgrad" är inställt på "På"

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att säkerhetsmeddelanden är korrekt aktiverade

- E-postmeddelande för varningar om hög allvarlighetsgrad bör aktiveras

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Se till att "Skicka e-post även till prenumerationsägare" är inställt på "På"

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att säkerhetsmeddelanden är korrekt aktiverade

- E-postmeddelande till prenumerationsägaren för varningar om hög allvarlighetsgrad bör aktiveras

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Se till att "säker överföring krävs" är inställt på "Aktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka lagringskonton som tillåter osäkra anslutningar.

- Säker överföring till lagringskonton ska vara aktiverat

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Kontrollera att standardregeln för nätverksåtkomst för lagringskonton är inställd på att neka

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att övervaka lagringskonton som tillåter obegränsad åtkomst.

- Granska obegränsad nätverksåtkomst till lagringskonton

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Se till att betrodda Microsoft-tjänster är aktiverat för åtkomst till lagringskonto

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att övervaka lagringskonton som inte tillåter åtkomst från betrodda Microsoft-tjänster.

- Lagringskonton bör tillåta åtkomst från betrodda Microsoft-tjänster

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Se till att "revision" är inställt på "På"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som säkerställer att SQL-servergranskning är aktiverad. 

- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Se till att "AuditActionGroups" i granskningsprincipen för en SQL-server är korrekt inställd

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att SQL-servergranskning är korrekt konfigurerad.

- SQL-granskningsinställningar bör ha åtgärdsgrupper konfigurerade för att samla in kritiska aktiviteter

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Se till att "auditing"-lagring är "större än 90 dagar"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att SQL-serverloggar behålls i minst 90 dagar.

- SQL-servrar bör konfigureras med granskning kvarhållningsdagar som är större än 90 dagar.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Se till att "Avancerad datasäkerhet" på en SQL-server är inställt på "På"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att säkerställa avancerad datasäkerhet är aktiverad på SQL-servrar och SQL-hanterade instanser.

- Avancerad datasäkerhet bör aktiveras på dina SQL-hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Se till att "typer av hotdetektering" är inställda på "Alla"

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att säkerställa att avancerat skydd mot hot är korrekt konfigurerat på SQL-servrar och SQL-hanterade instanser.

- Avancerade typer av skydd mot hot bör ställas in på alla i avancerade datasäkerhetsinställningar för SQL-servern
- Avancerade typer av skydd mot hot bör ställas in på alla i SQL-hanterade instans avancerade datasäkerhetsinställningar

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Se till att "Skicka varningar till" är inställd

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att avancerade datasäkerhetsmeddelanden är korrekt aktiverade.

- Avancerade datasäkerhetsinställningar för SQL-hanterad instans bör innehålla en e-postadress för att ta emot säkerhetsaviseringar
- Avancerade datasäkerhetsinställningar för SQL-servern bör innehålla en e-postadress för att ta emot säkerhetsaviseringar

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Se till att "E-posttjänst och medadministratörer" är aktiverade

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att avancerade datasäkerhetsmeddelanden är korrekt aktiverade.

- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade inställningar för SQL-hanterad instans
- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade datasäkerhetsinställningar för SQL-servern

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Kontrollera att Azure Active Directory Admin är konfigurerad

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att en Azure Active Directory-administratör har etablerats för SQL-servrar.

- En Azure Active Directory-administratör bör etableras för SQL-servrar

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Se till att "Datakryptering" är inställt på "På" i en SQL-databas

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att säkerställa transparent datakryptering är aktiverad på SQL-databaser.

- Transparent datakryptering i SQL-databaser bör aktiveras

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Se till att SQL-serverns TDE-skydd krypteras med BYOK (Använd din egen nyckel)

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att det transparenta datakrypterade skydd för SQL-servrar och SQL-hanterade instanser krypteras med din egen nyckel.

- SQL-hanterad instans TDE-skydd ska krypteras med din egen nyckel
- SQL-server TDE-skydd ska krypteras med din egen nyckel

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Se till att "Framtvinga SSL-anslutning" är inställt på "AKTIVERAD" för MySQL Database Server

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att MySQL-databasservrar framtvingar TLS/SSL-anslutningar.

- Framtvinga SSL-anslutning bör aktiveras för MySQL-databasservrar

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Se till att serverparametern "log_checkpoints" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att säkerställa PostgreSQL databasservrar logga kontrollpunkter.

- Loggkontrollpunkter bör aktiveras för PostgreSQL-databasservrar

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Se till att "Framtvinga SSL-anslutning" är inställt på "AKTIVERAD" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att PostgreSQL-databasservrar framtvingar TLS/SSL-anslutningar.

- Framtvinga SSL-anslutning bör aktiveras för PostgreSQL-databasservrar

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Se till att serverparametern "log_connections" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att säkerställa PostgreSQL-databasservrar logga anslutningar.

- Logganslutningar bör aktiveras för PostgreSQL-databasservrar

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Se till att serverparametern "log_disconnections" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att säkerställa PostgreSQL databasservrar logga frånkopplingar.

- Frånkopplingar bör loggas för PostgreSQL databasservrar.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Se till att serverparametern "log_duration" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att säkerställa att PostgreSQL-databasservrar loggar varaktigheten för slutförda utdrag.

- Loggens varaktighet bör aktiveras för PostgreSQL-databasservrar

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Se till att serverparametern "connection_throttling" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att minska brute force-attacker på PostgreSQL-databasservrar.

- Anslutningsbegränsning bör aktiveras för PostgreSQL-databasservrar

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Se till att Azure Active Directory Admin är konfigurerad

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att en Azure Active Directory-administratör har etablerats för SQL-servrar. CIS Microsoft Azure Foundations Benchmark innehåller den här rekommendationen. Det är dock en kopia av [rekommendation 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- En Azure Active Directory-administratör bör etableras för SQL-servrar

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Se till att det finns en loggprofil

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att det finns en loggprofil för alla Azure-prenumerationer. 

- Azure-prenumerationer bör ha en loggprofil för aktivitetslogg

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Se till att bevarandet av aktivitetsloggen är 365 dagar eller mer

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att aktivitetsloggar behålls i minst ett år.

- Aktivitetsloggen ska behållas i minst ett år

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Se till att revisionsprofilen samlar in all verksamhet

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att loggprofilen är korrekt konfigurerad.

- Azure Monitor-loggprofilen ska samla in loggar för kategorierna "write", "delete" och 'action'

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Se till att loggprofilen samlar in aktivitetsloggar för alla regioner, inklusive globala

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att loggprofilen är korrekt konfigurerad.

- Azure Monitor bör samla in aktivitetsloggar från alla regioner

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Se till att loggning för Azure KeyVault är aktiverat

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att säkerställa att diagnostikloggar är aktiverade för nyckelvalv.

- Diagnostikloggar i Key Vault ska aktiveras

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Se till att network watcher är "aktiverad"

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att Network Watcher är aktiverat för alla regioner där resurser distribueras. Den här principen kräver en parametermatris som anger alla tillämpliga regioner. Standardvärdet i denna definition av politiska initiativ är "eastus".

- Network Watcher ska vara aktiverad

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Se till att "OS-disken" är krypterad

Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att säkerställa diskkryptering är aktiverad på virtuella datorer.

- Diskkryptering bör tillämpas på virtuella datorer

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Se till att "Datadiskar" är krypterade

Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definition som hjälper dig att säkerställa diskkryptering är aktiverad på virtuella datorer.

- Diskkryptering bör tillämpas på virtuella datorer

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Se till att "Obundna diskar" är krypterade

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att obundna diskar är krypterade.

- Obundna diskar ska krypteras

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Se till att endast godkända tillägg är installerade

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att endast godkända tillägg för virtuella datorer installeras. Den här principen kräver en parametermatris som anger alla godkända tillägg för virtuella datorer. Den här principinitiativdefinitionen innehåller föreslagna standardvärden som kunder bör validera. 

 - Endast godkända VM-tillägg ska installeras

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Se till att de senaste OS-korrigeringarna för alla virtuella datorer tillämpas

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att systemuppdateringar installeras på virtuella datorer.

- Systemuppdateringar ska ha installerats på dina datorer

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Se till att slutpunktsskyddet för alla virtuella datorer är installerat

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att slutpunktsskydd är aktiverat på virtuella datorer.

- Övervaka saknade slutpunktsskydd i Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Se till att nyckelvalvet kan återställas

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att se till att nyckelvalvsobjekt kan återställas vid oavsiktlig borttagning.

- Key Vault-objekt ska kunna återställas

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Aktivera rollbaserad åtkomstkontroll (RBAC) i Azure Kubernetes Services

Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) som hjälper dig att se till att rollbaserad åtkomstkontroll används för hanterade behörigheter i Kubernetes tjänstkluster

- \[Preview\]: Rollbaserad åtkomstkontroll (RBAC) ska användas på Kubernetes Services

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Se till att webbappen omdirigerar all HTTP-trafik till HTTPS i Azure App Service

Den här skissen tilldelar en [Azure Policy-definition](../../../policy/overview.md) som hjälper dig att säkerställa att webbprogram endast är tillgängliga via säkra anslutningar.

- Webbprogrammet ska endast vara tillgängligt via HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Se till att webbappen använder den senaste versionen av TLS-kryptering

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att webbappar använder den senaste TLS-versionen.

- Senaste TLS-versionen ska användas i din API-app
- Senaste TLS-versionen ska användas i din funktionsapp
- Senaste TLS-versionen ska användas i din Webbapp

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Se till att webbappen har "Klientcertifikat (inkommande klientcertifikat)" inställt på "På"

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att endast klienter med giltiga certifikat kan nå en webbapp.

- Kontrollera api-app har "Klientcertifikat (inkommande klientcertifikat)" inställt på "På"
- Kontrollera att funktionsappen har "Klientcertifikat (inkommande klientcertifikat)" inställt på 'På'
- Kontrollera att WEB-appen har "Klientcertifikat (inkommande klientcertifikat)" inställt på 'På'

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Se till att registrera dig med Azure Active Directory är aktiverat på App Service

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att webbappar använder en hanterad identitet.

- Kontrollera att Registrera dig med Azure Active Directory är aktiverat i API-appen
- Kontrollera att Registrera dig med Azure Active Directory är aktiverat på Function App
- Kontrollera att Registrera dig med Azure Active Directory är aktiverat på WEB App

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Se till att ".Net Framework"-versionen är den senaste, om den används som en del av webbappen

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att webbappar använder den senaste versionen av .Net Framework.

- Se till att ".Net Framework"-versionen är den senaste, om den används som en del av API-appen
- Se till att ".Net Framework"-versionen är den senaste, om den används som en del av funktionsappen
- Se till att ".Net Framework"-versionen är den senaste, om den används som en del av webbappen

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Se till att "PHP-versionen" är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att webbappar använder den senaste versionen av PHP.

- Se till att "PHP-versionen" är den senaste, om den används som en del av Api-appen
- Se till att "PHP-versionen" är den senaste, om den används som en del av funktionsappen
- Se till att "PHP-versionen" är den senaste, om den används som en del av webbappen

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Se till att Python-versionen är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att webbappar använder den senaste versionen av Python.

- Se till att Python-versionen är den senaste, om den används som en del av Api-appen
- Se till att Python-versionen är den senaste, om den används som en del av funktionsappen
- Se till att Python-versionen är den senaste, om den används som en del av webbappen

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Se till att "Java-versionen" är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att webbappar använder den senaste versionen av Java.

- Se till att "Java-versionen" är den senaste, om den används som en del av Api-appen
- Se till att "Java-versionen" är den senaste, om den används som en del av Funtion-appen
- Se till att "Java-versionen" är den senaste, om den används som en del av webbappen

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Se till att "HTTP-versionen" är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) som hjälper dig att se till att webbappar använder den senaste versionen av HTTP.

- Kontrollera att HTTP-versionen är den senaste, om den används för att köra Api-appen
- Kontrollera att HTTP-versionen är den senaste, om den används för att köra funktionsappen
- Kontrollera att HTTP-versionen är den senaste, om den används för att köra webbappen


## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollmappningen av CIS Microsoft Azure Foundations Benchmark-skiss kan du besöka följande artiklar om skissen eller besöka Azure Policy i Azure-portalen för att tilldela initiativet:

> [!div class="nextstepaction"]
> [Cis Microsoft Azure Foundations benchmark-ritning – Översikt CIS](./index.md)
> [Microsoft Azure Foundations benchmark-skiss – distributionssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).