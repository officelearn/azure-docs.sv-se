---
title: Exempel på CIS Microsoft Azure grunderna – rekommendations mappning
description: Rekommendations mappning av CIS-exemplet Microsoft Azure grunderna för att Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/01/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 9368ad220f1ded1a11360dbdf0af86c27277a207
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001273"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Rekommendations mappning av CIS-exemplet för benchmark-Microsoft Azure grunderna

I följande artikel beskrivs hur Azure-ritningar CIS-Microsoft Azure Base-exempel kartor mappar till CIS-Microsoft Azure grunderna för benchmark-rekommendationer. Mer information om rekommendationerna finns i [CIS Microsoft Azure stiftelser benchmark](https://www.cisecurity.org/benchmark/azure/).

Följande mappningar är till **CIS Microsoft Azure grunderna 1.1.0** -rekommendationer för benchmark. Använd navigeringen till höger om du vill gå direkt till en bestämd rekommendations mappning.
Många av de mappade rekommendationerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj **\[Preview @ no__t-2 audit CIS Microsoft Azure grunderna för benchmark v-1.1.0 och distribuera särskilda VM-tillägg för att stödja gransknings krav** inbyggda princip initiativ.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 se till att Multi-Factor Authentication har Aktiver ATS för alla privilegierade användare

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att övervaka när Multi-Factor Authentication inte har Aktiver ATS för privilegierade Azure Active Directory-konton.

- Multifaktorautentisering bör aktiveras på konton med ägarbehörighet för prenumerationen
- MFA ska vara aktiverat på konton med Skriv behörighet för din prenumeration

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 se till att Multi-Factor Authentication har Aktiver ATS för alla icke-privilegierade användare

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper dig att övervaka när multifaktorautentisering inte har Aktiver ATS för icke-privilegierade Azure Active Directory-konton.

- Multifaktorautentisering bör aktiveras på konton med läsbehörighet för prenumerationen

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 se till att det inte finns några gäst användare

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att övervaka för gäst konton som kan behöva tas bort.

- Externa konton med ägarbehörighet bör tas bort från prenumerationen
- Externa konton med läsbehörighet bör tas bort från prenumerationen
- Externa konton med skrivbehörighet bör tas bort från prenumerationen

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 se till att standard pris nivån är vald

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka nätverk och virtuella datorer där Security Center standard nivån inte är aktive rad.

 - Standardprisnivån för Security Center ska väljas

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 kontrol lera att "automatisk etablering av övervaknings agent" är inställt på "on"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att den automatiska etableringen av log Analyticss agent är aktive rad.

- Automatisk etablering av Log Analytics Monitoring Agent ska vara aktiverat i din prenumeration

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 se till att ASC standard princip inställningen "övervaka system uppdateringar" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att system uppdateringar installeras på virtuella datorer.

- Systemuppdateringar bör installeras på datorerna

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 kontrol lera att ASC-standardprincip inställningen "övervaka OS-sårbarheter" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka för unremediated virtuella dator sårbarheter.

- Säkerhetsluckor i datorernas säkerhetskonfiguration bör åtgärdas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Se till att ASC standard princip inställningen "övervaka Endpoint Protection" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Endpoint Protection har Aktiver ATS på virtuella datorer.

- Övervaka avsaknad av slutpunktsskydd i Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Se till att ASC standard princip inställningen "övervaka disk kryptering" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att virtuella dator diskar är krypterade.

- Diskkryptering bör tillämpas på virtuella datorer

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 Se till att ASC standard princip inställningen "övervaka nätverks säkerhets grupper" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att skydda virtuella datorer som är riktade mot Internet.

- Reglerna för nätverkssäkerhetsgrupp bör skärpas för Internetbaserade virtuella datorer

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Se till att ASC-standardprincip inställningen "övervaka brand vägg för webbaserade program" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att skydda virtuella datorer som kör webb program.

- Reglerna för nätverkssäkerhetsgrupp bör skärpas för webbprogram på IaaS

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 Se till att ASC-standardprincip inställningen "Aktivera nästa generations brand vägg (NGFW) övervakning" inte är inaktive rad "

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som skyddar undernät och virtuella datorer mot hot genom att begränsa åtkomsten. Den Security Center-princip som den här CIS-Microsoft Azure grunderna om benchmark-rekommendation har ersatts av två nya rekommendationer. De principer som refereras nedan åtgärdar de nya rekommendationerna.

- Undernät bör vara kopplade till en nätverkssäkerhetsgrupp
- Virtuella datorer bör vara kopplade till en nätverkssäkerhetsgrupp

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 Se till att ASC standard princip inställningen "övervaka sårbarhets bedömning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att sårbarheter identifieras och åtgärdas.

- Säkerhetsluckor bör åtgärdas via en sårbarhetsbedömningslösning

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 kontrol lera att ASC-standardprincip inställningen "övervaka Storage BLOB Encryption" inte är inaktive rad

Azure Storage kryptering har Aktiver ATS för alla nya och befintliga lagrings konton och kan inte inaktive ras. (Det här är en standard funktion i Azure, det finns ingen princip tilldelning.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Se till att ASC standard princip inställningen "övervaka JIT-nätverksanslutning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att kontrol lera åtkomsten till virtuella datorer.

- Just-in-Time-kontroll för nätverks åtkomst ska tillämpas på virtuella datorer

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 kontrol lera att ASC-standardprincip inställningen "övervaka adaptiv program vit listning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att adaptiva program kontroller är aktiverat på virtuella datorer.

- Anpassningsbara programkontroller bör aktiveras på virtuella datorer

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 kontrol lera att ASC-standardprincip inställningen övervaka SQL-granskning är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper till att säkerställa att SQL Server Auditing är aktiverat.

- Granskning bör vara aktiverat i Advanced Data Security-inställningarna på SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 kontrol lera att ASC-standardprincip inställningen "övervaka SQL-kryptering" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Transparent datakryptering är aktiverat på SQL-databaser.

- Transparent datakryptering bör aktiveras på SQL-databaser

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 kontrol lera att e-postmeddelandet för säkerhets kontakt har angetts

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- Du måste tillhandahålla en e-postadress för säkerhetskontakt för din prenumeration

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 Se till att säkerhets kontaktens telefonnummer har angetts

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- Du måste ange ett telefonnummer för säkerhetskontakt för din prenumeration

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 Se till att "skicka e-postmeddelande om aviseringar med hög allvarlighets grad" är inställt på "on"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- Avisering via e-post vid hög allvarlighetsgrad bör aktiveras

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 Se till att "skicka e-post även till prenumerations ägare" är inställt på "på"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- Avisering via e-postmeddelande till prenumerationens ägare vid hög allvarlighetsgrad ska aktiveras

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 kontrol lera att "säker överföring krävs" är inställt på ' Enabled '

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka lagrings konton som tillåter oskyddade anslutningar.

- Säker överföring till lagringskonton bör aktiveras

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 Se till att standard regeln för nätverks åtkomst för lagrings konton är inställd på neka

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka lagrings konton som tillåter obegränsad åtkomst.

- Granska obegränsad nätverksåtkomst till lagringskonton

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 Se till att betrodda Microsoft-tjänster är aktiverat för åtkomst till lagrings kontot

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka lagrings konton som inte tillåter åtkomst från betrodda Microsoft-tjänster.

- Lagringskonton måste tillåta åtkomst från betrodda Microsoft-tjänster

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 se till att "granskning" är inställt på "on"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper till att säkerställa att SQL Server Auditing är aktiverat. 

- Granskning bör vara aktiverat i Advanced Data Security-inställningarna på SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 kontrol lera att AuditActionGroups i gransknings principen för en SQL Server är korrekt inställd

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att SQL Server-granskningen är korrekt konfigurerad.

- Inställningarna för SQL-granskning bör ha åtgärds grupper konfigurerade för att fånga kritiska aktiviteter

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 kontrol lera att kvarhållning av granskning är större än 90 dagar

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att SQL Server-loggar bevaras i minst 90 dagar.

- SQL-servrar bör konfigureras med granskning av antalet dagar som är större än 90 dagar.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 se till att "avancerad data säkerhet" på en SQL-Server är inställt på "på"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att avancerad data säkerhet är aktiverat på SQL-servrar och SQL-hanterade instanser.

- Du bör aktivera avancerad datasäkerhet på dina SQL-hanterade instanser
- Avancerad datasäkerhet bör aktiveras på SQL-servrarna

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 Se till att hot identifierings typerna är inställt på all

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att säkerställa att Avancerat skydd är korrekt KONFIGURERAT på SQL-servrar och SQL-hanterade instanser.

- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 kontrol lera att skicka aviseringar till har angetts

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att avancerade data säkerhets meddelanden är korrekt aktiverade.

- Advanced Data Security-inställningar för hanterad SQL-instans ska innehålla en e-postadress för att ta emot säkerhetsaviseringar
- Advanced Data Security-inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhetsaviseringar

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 kontrol lera att "e-posttjänst och medadministratörer" är aktiverat

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att avancerade data säkerhets meddelanden är korrekt aktiverade.

- E-postaviseringar till administratörer och prenumerationsägare ska vara aktiverat i Advanced Threat Protection-inställningarna för hanterad SQL-instans
- E-postaviseringar till administratörer och prenumerationsägare ska vara aktiverat i Advanced Threat Protection-inställningarna för SQL Server

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 kontrol lera att Azure Active Directory admin har kon figurer ATS

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att en Azure Active Directory-administratör tillhandahålls för SQL-servrar.

- En Azure Active Directory-administratör bör registreras för SQL-servrar

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Se till att "data kryptering" är inställt på "on" på en SQL Database

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Transparent datakryptering är aktiverat på SQL-databaser.

- Transparent datakryptering bör aktiveras på SQL-databaser

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 kontrol lera att SQL Servers TDE-skydd krypteras med BYOK (Använd din egen nyckel)

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att se till att transparenta data krypterade skydd för SQL-servrar och SQL-hanterade instanser krypteras med din egen nyckel.

- TDE-skydd för SQL-hanterad instans bör krypteras med egen nyckel
- TDE-skydd för SQL-server bör krypteras med egen nyckel

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 Se till att tvinga SSL-anslutning är inställt på ENABLEd för MySQL-databasserver

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att MySQL-databas servrar tvingar SSL-anslutningar.

- Framtvinga SSL-anslutning måste aktiveras för MySQL-databasservrar

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 Se till att tvinga SSL-anslutning är inställt på ENABLEd för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper dig att säkerställa att PostgreSQL-databas servrar tvingar SSL-anslutningar.

- Framtvinga SSL-anslutning måste aktiveras för PostgreSQL-databasservrar

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 Se till att Server parametern "connection_throttling" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att begränsa brute force-attacker på PostgreSQL-databas servrar.

- Anslutningsbegränsning måste aktiveras för PostgreSQL-databasservrar

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 kontrol lera att Azure Active Directory admin har kon figurer ATS

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att en Azure Active Directory-administratör tillhandahålls för SQL-servrar. CIS-Microsoft Azure grunderna omfattar den här rekommendationen. Det är dock en dubblett av [rekommendation 4,8](#48-ensure-that-azure-active-directory-admin-is-configured).

- En Azure Active Directory-administratör bör registreras för SQL-servrar

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 se till att det finns en logg profil

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att det finns en logg profil för alla Azure-prenumerationer. 

- Azure-prenumerationer måste ha en loggprofil för aktivitetsloggen

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 se till att aktivitets loggens kvarhållning är inställt på 365 dagar eller mer

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att aktivitets loggarna bevaras i minst ett år.

- Aktivitetsloggen ska bevaras i minst ett år

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 se till att gransknings profilen fångar alla aktiviteter

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att logg profilen är korrekt konfigurerad.

- Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 se till att logg profilen fångar in aktivitets loggar för alla regioner, inklusive global

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att logg profilen är korrekt konfigurerad.

- Azure Monitor ska samla in aktivitetsloggar från alla regioner

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 kontrol lera att loggning för Azure-valv är aktiverat

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att diagnostikloggar är aktiverade för nyckel valv.

- Diagnostikloggar i Key Vault bör aktiveras

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 kontrol lera att Network Watcher är aktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Network Watcher har Aktiver ATS för alla regioner där resurser distribueras. Den här principen kräver en parameter mat ris som anger alla tillämpliga regioner. Standardvärdet i denna princip initiativs definition är "öst".

- Network Watcher ska aktiveras

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 kontrol lera att OS-disken är krypterad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att disk kryptering är aktiverat på virtuella datorer.

- Diskkryptering bör tillämpas på virtuella datorer

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 kontrol lera att data diskarna är krypterade

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att disk kryptering är aktiverat på virtuella datorer.

- Diskkryptering bör tillämpas på virtuella datorer

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 se till att "icke anslutna diskar" är krypterade

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att frånkopplade diskar är krypterade.

- Frånkopplade diskar måste vara krypterade

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 se till att endast godkända tillägg är installerade

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att endast godkända tillägg för virtuella datorer är installerade. Den här principen kräver en parameter mat ris som anger alla godkända tillägg för virtuella datorer. Den här policy initiativs definitionen innehåller föreslagna standardvärden som kunderna bör validera. 

 - Endast godkända virtuella datortillägg ska installeras

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 Se till att de senaste OS-korrigeringarna för alla Virtual Machines tillämpas

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att system uppdateringar installeras på virtuella datorer.

- Systemuppdateringar bör installeras på datorerna

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Se till att Endpoint Protection för alla Virtual Machines har installerats

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Endpoint Protection har Aktiver ATS på virtuella datorer.

- Övervaka avsaknad av slutpunktsskydd i Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 se till att nyckel valvet är återställnings Bart

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att nyckel valvs objekt kan återskapas vid oavsiktlig borttagning.

- Nyckelvalvsobjekt måste gå att återställa

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 aktivera rollbaserad åtkomst kontroll (RBAC) i Azure Kubernetes Services

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att rollbaserad åtkomst kontroll används för hanterade behörigheter i Kubernetes-tjänstekluster

- \[Preview @ no__t-1: Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 se till att webbapp omdirigerar all HTTP-trafik till HTTPS i Azure App Service

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att webb program bara är tillgängliga via säkra anslutningar.

- Webbprogram bör enbart vara åtkomliga via HTTPS

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av CIS Microsoft Azure grunderna benchmark-skiss kan du gå till följande artikel om du vill lära dig mer om skissen eller besöka Azure Policy i Azure Portal för att tilldela initiativet:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure grunderna benchmark-skiss – översikt](./index.md)
> [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).