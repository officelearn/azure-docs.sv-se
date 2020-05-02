---
title: Exempel kontroller för benchmark-Microsoft Azure
description: Rekommendations mappning av CIS-exemplet Microsoft Azure grunderna för att Azure Policy.
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: 735ab26e1f9432a87d5a0d927c64d44e680b0694
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688109"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Rekommendations mappning av CIS-exemplet för benchmark-Microsoft Azure grunderna

I följande artikel beskrivs hur Azure-ritningar CIS-Microsoft Azure Base-exempel kartor mappar till CIS-Microsoft Azure grunderna för benchmark-rekommendationer. Mer information om rekommendationerna finns i [CIS Microsoft Azure stiftelser benchmark](https://www.cisecurity.org/benchmark/azure/).

Följande mappningar är till **CIS Microsoft Azure grunderna 1.1.0** -rekommendationer för benchmark. Använd navigeringen till höger om du vill gå direkt till en bestämd rekommendations mappning.
Många av de mappade rekommendationerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj gransknings principen för för ** \[hands versions\] CIS Microsoft Azure grunderna 1.1.0-rekommendationer och distribuera särskilda VM-tillägg för att ge stöd för gransknings krav** inbyggda princip initiativ.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 se till att Multi-Factor Authentication har Aktiver ATS för alla privilegierade användare

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att övervaka när Multi-Factor Authentication inte har Aktiver ATS för privilegierade Azure Active Directory-konton.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 se till att Multi-Factor Authentication har Aktiver ATS för alla icke-privilegierade användare

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper dig att övervaka när multifaktorautentisering inte har Aktiver ATS för icke-privilegierade Azure Active Directory-konton.

- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 se till att det inte finns några gäst användare

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att övervaka för gäst konton som kan behöva tas bort.

- Externa konton med Läs behörighet bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1,23 Se till att inga anpassade prenumerations ägar roller skapas

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att övervaka för anpassade prenumerations ägar roller som kan behöva tas bort.

- Roller för anpassade prenumerationer får inte finnas

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 se till att standard pris nivån är vald

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka nätverk och virtuella datorer där Security Center standard nivån inte är aktive rad.

- Security Center standard pris nivå ska väljas

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 kontrol lera att "automatisk etablering av övervaknings agent" är inställt på "on"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att den automatiska etableringen av log Analyticss agent är aktive rad.

- Automatisk etablering av Log Analytics övervaknings agenten ska vara aktive rad för din prenumeration

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 se till att ASC standard princip inställningen "övervaka system uppdateringar" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att system uppdateringar installeras på virtuella datorer.

- Systemuppdateringar ska ha installerats på dina datorer

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 kontrol lera att ASC-standardprincip inställningen "övervaka OS-sårbarheter" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka för unremediated virtuella dator sårbarheter.

- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Se till att ASC standard princip inställningen "övervaka Endpoint Protection" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Endpoint Protection har Aktiver ATS på virtuella datorer.

- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Se till att ASC standard princip inställningen "övervaka disk kryptering" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att virtuella dator diskar är krypterade.

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 Se till att ASC standard princip inställningen "övervaka nätverks säkerhets grupper" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att skydda virtuella datorer som är riktade mot Internet.

- Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 Se till att ASC-standardprincip inställningen "Aktivera nästa generations brand vägg (NGFW) övervakning" inte är inaktive rad "

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som skyddar undernät och virtuella datorer mot hot genom att begränsa åtkomsten. Den Security Center-princip som den här CIS-Microsoft Azure grunderna om benchmark-rekommendation har ersatts av två nya rekommendationer. De principer som refereras nedan åtgärdar de nya rekommendationerna.

- Undernät ska associeras med en nätverks säkerhets grupp
- Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 Se till att ASC standard princip inställningen "övervaka sårbarhets bedömning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att sårbarheter identifieras och åtgärdas.

- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 kontrol lera att ASC-standardprincip inställningen "övervaka Storage BLOB Encryption" inte är inaktive rad

Azure Storage-kryptering är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras. (Det här är en standard funktion i Azure, det finns ingen princip tilldelning.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Se till att ASC standard princip inställningen "övervaka JIT-nätverksanslutning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att kontrol lera åtkomsten till virtuella datorer.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 kontrol lera att ASC-standardprincip inställningen "övervaka adaptiv program vit listning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att adaptiva program kontroller är aktiverat på virtuella datorer.

- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 kontrol lera att ASC-standardprincip inställningen övervaka SQL-granskning är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper till att säkerställa att SQL Server Auditing är aktiverat.

- Granskning på SQL Server måste vara aktiverat

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 kontrol lera att ASC-standardprincip inställningen "övervaka SQL-kryptering" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Transparent datakryptering är aktiverat på SQL-databaser.

- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 kontrol lera att e-postmeddelandet för säkerhets kontakt har angetts

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- Du måste tillhandahålla en e-postadress till en säkerhets kontakt för din prenumeration

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 Se till att säkerhets kontaktens telefonnummer har angetts

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- Du måste tillhandahålla ett telefonnummer till en säkerhets kontakt för din prenumeration

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 Se till att "skicka e-postmeddelande om aviseringar med hög allvarlighets grad" är inställt på "on"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- E-postavisering om aviseringar med hög allvarlighets grad ska aktive ras

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 Se till att "skicka e-post även till prenumerations ägare" är inställt på "på"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att säkerhetsaviseringar är korrekt aktiverade

- E-postmeddelande till Prenumerationens ägare för aviseringar med hög allvarlighets grad måste vara aktiverat

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 kontrol lera att "säker överföring krävs" är inställt på ' Enabled '

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka lagrings konton som tillåter oskyddade anslutningar.

- Säker överföring till lagringskonton ska vara aktiverat

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 Se till att standard regeln för nätverks åtkomst för lagrings konton är inställd på neka

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka lagrings konton som tillåter obegränsad åtkomst.

- Granska obegränsad nätverks åtkomst till lagrings konton

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 Se till att betrodda Microsoft-tjänster är aktiverat för åtkomst till lagrings kontot

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka lagrings konton som inte tillåter åtkomst från betrodda Microsoft-tjänster.

- Lagrings konton ska tillåta åtkomst från betrodda Microsoft-tjänster

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 se till att "granskning" är inställt på "on"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper till att säkerställa att SQL Server Auditing är aktiverat. 

- Granskning på SQL Server måste vara aktiverat

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 kontrol lera att AuditActionGroups i gransknings principen för en SQL Server är korrekt inställd

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att SQL Server-granskningen är korrekt konfigurerad.

- Inställningarna för SQL-granskning bör ha åtgärds grupper konfigurerade för att fånga kritiska aktiviteter

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 kontrol lera att kvarhållning av granskning är större än 90 dagar

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att SQL Server-loggar bevaras i minst 90 dagar.

- SQL-servrar bör konfigureras med granskning av antalet dagar som är större än 90 dagar.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 se till att "avancerad data säkerhet" på en SQL-Server är inställt på "på"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att avancerad data säkerhet är aktiverat på SQL-servrar och SQL-hanterade instanser.

- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Avancerad data säkerhet ska vara aktiverat på SQL-hanterade instanser

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 Se till att hot identifierings typerna är inställt på all

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att säkerställa att Avancerat skydd är korrekt KONFIGURERAT på SQL-servrar och SQL-hanterade instanser.

- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 kontrol lera att skicka aviseringar till har angetts

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att avancerade data säkerhets meddelanden är korrekt aktiverade.

- Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- Avancerade data säkerhets inställningar för SQL-hanterad instans ska innehålla en e-postadress för att ta emot säkerhets aviseringar

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 kontrol lera att "e-posttjänst och medadministratörer" är aktiverat

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att avancerade data säkerhets meddelanden är korrekt aktiverade.

- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar i SQL Server
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar för SQL-hanterad instans

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 kontrol lera att Azure Active Directory admin har kon figurer ATS

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att en Azure Active Directory-administratör tillhandahålls för SQL-servrar.

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Se till att "data kryptering" är inställt på "on" på en SQL Database

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Transparent datakryptering är aktiverat på SQL-databaser.

- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 kontrol lera att SQL Servers TDE-skydd krypteras med BYOK (Använd din egen nyckel)

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att se till att transparenta data krypterade skydd för SQL-servrar och SQL-hanterade instanser krypteras med din egen nyckel.

- SQL Server TDE-skyddskomponenten bör vara krypterat med din egen nyckel
- SQL-hanterad instans TDE-skydd ska vara krypterat med din egen nyckel

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 Se till att tvinga SSL-anslutning är inställt på ENABLEd för MySQL-databasserver

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att MySQL-databas servrar tvingar TLS/SSL-anslutningar.

- Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4,12 Se till att Server parametern "log_checkpoints" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att postgresql loggar kontroll punkter för databas servrar.

- Logg kontroll punkter ska vara aktiverade för PostgreSQL-databas servrar

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 Se till att tvinga SSL-anslutning är inställt på ENABLEd för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att PostgreSQL-databas servrar tvingar TLS/SSL-anslutningar.

- Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4,14 Se till att Server parametern "log_connections" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att databas servrarnas logg anslutningar postgresql.

- Logg anslutningar ska vara aktiverade för PostgreSQL-databas servrar

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4,15 Se till att Server parametern "log_disconnections" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som hjälper dig att säkerställa att PostgreSQL databas servrar loggar från kopplingar.

- Från kopplingar ska loggas för PostgreSQL-databas servrar.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4,16 Se till att Server parametern "log_duration" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att PostgreSQL-databas servrar loggar varaktigheten för slutförda instruktioner.

- Loggens varaktighet ska vara aktive rad för PostgreSQL-databas servrar

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 Se till att Server parametern "connection_throttling" är inställd på "ON" för PostgreSQL Database Server

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att begränsa brute force-attacker på PostgreSQL-databas servrar.

- Anslutnings begränsning ska vara aktiverat för PostgreSQL-databas servrar

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 kontrol lera att Azure Active Directory admin har kon figurer ATS

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att en Azure Active Directory-administratör tillhandahålls för SQL-servrar. CIS-Microsoft Azure grunderna omfattar den här rekommendationen. Det är dock en dubblett av [rekommendation 4,8](#48-ensure-that-azure-active-directory-admin-is-configured).

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 se till att det finns en logg profil

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att det finns en logg profil för alla Azure-prenumerationer. 

- Azure-prenumerationer ska ha en logg profil för aktivitets loggen

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 se till att aktivitets loggens kvarhållning är inställt på 365 dagar eller mer

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att aktivitets loggarna bevaras i minst ett år.

- Aktivitets loggen ska behållas i minst ett år

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 se till att gransknings profilen fångar alla aktiviteter

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att logg profilen är korrekt konfigurerad.

- Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 se till att logg profilen fångar in aktivitets loggar för alla regioner, inklusive global

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att logg profilen är korrekt konfigurerad.

- Azure Monitor ska samla in aktivitets loggar från alla regioner

## <a name="516-ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>5.1.6 Se till att lagrings kontot som innehåller behållaren med aktivitets loggar är krypterat med BYOK (Använd din egen nyckel)

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att lagrings konton som innehåller aktivitets loggar krypteras med BYOK.

- Lagrings kontot som innehåller behållaren med aktivitets loggar måste vara krypterat med BYOK

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 kontrol lera att loggning för Azure-valv är aktiverat

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att diagnostikloggar är aktiverade för nyckel valv.

- Diagnostikloggar i Key Vault ska vara aktive rad

## <a name="521-ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>5.2.1 se till att aktivitets logg aviseringen finns för att skapa princip tilldelning

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa princip åtgärder

## <a name="522-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>5.2.2 se till att aktivitets logg aviseringen finns för skapa eller uppdatera nätverks säkerhets grupp

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder

## <a name="523-ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>5.2.3 se till att aktivitets logg aviseringen finns för ta bort nätverks säkerhets grupp

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder

## <a name="524-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>5.2.4 kontrol lera att det finns en aktivitets logg avisering för regeln skapa eller uppdatera nätverks säkerhets grupp

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder

## <a name="525-ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>5.2.5 kontrol lera att det finns en aktivitets logg avisering för regeln ta bort nätverks säkerhets grupp

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder

## <a name="526-ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>5.2.6 Se till att aktivitets logg aviseringen finns för säkerhets lösningen skapa eller uppdatera

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder

## <a name="527-ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>5.2.7 Se till att aktivitets logg aviseringen finns för borttagning av säkerhetslösningen

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder

## <a name="528-ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>5.2.8 kontrol lera att det finns en aktivitets logg avisering för att skapa eller uppdatera eller ta bort SQL Server brand Väggs regel

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder

## <a name="529-ensure-that-activity-log-alert-exists-for-update-security-policy"></a>5.2.9 Se till att aktivitets logg aviseringen finns för säkerhets princip för uppdatering

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att vissa aktivitets logg aviseringar finns.

- Det bör finnas en aktivitets logg avisering för vissa säkerhets åtgärder

## <a name="61-ensure-that-rdp-access-is-restricted-from-the-internet"></a>6,1 se till att RDP-åtkomsten är begränsad från Internet

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att RDP-åtkomsten är begränsad.

- RDP-åtkomst från Internet ska blockeras

## <a name="62-ensure-that-ssh-access-is-restricted-from-the-internet"></a>6,2 se till att SSH-åtkomsten är begränsad från Internet

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att SSH-åtkomsten är begränsad.

- SSH-åtkomst från Internet ska blockeras

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 kontrol lera att Network Watcher är aktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Network Watcher har Aktiver ATS för alla regioner där resurser distribueras. Den här principen kräver en parameter mat ris som anger alla tillämpliga regioner. Standardvärdet i denna princip initiativs definition är "öst".

- Network Watcher ska vara aktiverat

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 kontrol lera att OS-disken är krypterad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att disk kryptering är aktiverat på virtuella datorer.

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 kontrol lera att data diskarna är krypterade

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att disk kryptering är aktiverat på virtuella datorer.

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 se till att "icke anslutna diskar" är krypterade

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att frånkopplade diskar är krypterade.

- Ej anslutna diskar ska vara krypterade

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 se till att endast godkända tillägg är installerade

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att endast godkända tillägg för virtuella datorer är installerade. Den här principen kräver en parameter mat ris som anger alla godkända tillägg för virtuella datorer. Den här policy initiativs definitionen innehåller föreslagna standardvärden som kunderna bör validera. 

- Endast godkända VM-tillägg ska installeras

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 Se till att de senaste OS-korrigeringarna för alla Virtual Machines tillämpas

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att system uppdateringar installeras på virtuella datorer.

- Systemuppdateringar ska ha installerats på dina datorer

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Se till att Endpoint Protection för alla Virtual Machines har installerats

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att Endpoint Protection har Aktiver ATS på virtuella datorer.

- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 se till att nyckel valvet är återställnings Bart

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att nyckel valvs objekt kan återskapas vid oavsiktlig borttagning.

- Key Vault objekt ska vara återställnings bara

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 aktivera rollbaserad åtkomst kontroll (RBAC) i Azure Kubernetes Services

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att rollbaserad åtkomst kontroll används för hanterade behörigheter i Kubernetes-tjänstekluster

- Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services

## <a name="91-ensure-app-service-authentication-is-set-on-azure-app-service"></a>9,1 se till att App Service autentisering är inställt på Azure App Service

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att se till att förfrågningar till App Service appar autentiseras.

- Autentisering ska vara aktiverat i API-appen
- Autentisering ska aktive ras i din Function-app
- Autentisering ska vara aktiverat på din webbapp

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 se till att webbapp omdirigerar all HTTP-trafik till HTTPS i Azure App Service

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att säkerställa att webb program bara är tillgängliga via säkra anslutningar.

- Webb program bör endast vara tillgängliga via HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9,3 kontrol lera att webb programmet använder den senaste versionen av TLS-kryptering

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att säkerställa att webbappar använder den senaste TLS-versionen.

- Den senaste TLS-versionen ska användas i din API-app
- Den senaste TLS-versionen ska användas i Funktionsapp
- Den senaste TLS-versionen ska användas i din webbapp

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9,4 se till att webbappen har "klient certifikat (inkommande klient certifikat)" inställd på "på"

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att se till att endast klienter med giltiga certifikat kan komma åt en webbapp.

- Se till att API-appen har klient certifikat (inkommande klient certifikat) inställd på
- Se till att Function-appen har klient certifikat (inkommande klient certifikat) inställd på
- Se till att WEBBAPP har "klient certifikat (inkommande klient certifikat)" inställd på "på"

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9,5 kontrol lera att register med Azure Active Directory är aktiverat på App Service

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) -definitioner som hjälper dig att se till att webbappar använder en hanterad identitet.

- Kontrol lera att register med Azure Active Directory är aktiverat i API-appen
- Kontrol lera att register med Azure Active Directory är aktiverat på Funktionsapp
- Kontrol lera att register med Azure Active Directory är aktiverat i webbappen

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9,6 Se till att .NET Framework-versionen är den senaste, om den används som en del av webbappen

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) -definitioner som hjälper dig att se till att webbappar använder den senaste versionen av .NET Framework.

- Se till att .NET Framework-versionen är den senaste, om den används som en del av API-appen
- Se till att .NET Framework-versionen är den senaste, om den används som en del av Funktionsapp
- Se till att .NET Framework-versionen är den senaste, om den används som en del av webbappen

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9,7 Se till att PHP-versionen är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) -definitioner som hjälper dig att se till att webbappar använder den senaste versionen av php.

- Se till att PHP-version är den senaste, om den används som en del av API-appen
- Se till att PHP-version är den senaste, om den används som en del av Function-appen
- Se till att PHP-version är den senaste, om den används som en del av webbappen

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9,8 Se till att "python-version" är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) -definitioner som hjälper dig att se till att webbappar använder den senaste versionen av python.

- Se till att "python-version" är den senaste, om den används som en del av API-appen
- Se till att "python-version" är den senaste, om den används som en del av Function-appen
- Se till att "python-version" är den senaste, om den används som en del av webbappen

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9,9 Se till att Java-version är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) -definitioner som hjälper dig att se till att webbappar använder den senaste versionen av Java.

- Se till att Java-version är den senaste, om den används som en del av API-appen
- Se till att Java-version är den senaste, om den används som en del av Function-appen
- Se till att Java-version är den senaste, om den används som en del av webbappen

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9,10 Se till att "HTTP-version" är den senaste, om den används för att köra webbappen

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) -definitioner som hjälper dig att se till att webbappar använder den senaste versionen av http.

- Se till att "HTTP-version" är den senaste, om den används för att köra API-appen
- Se till att "HTTP-version" är den senaste, om den används för att köra Function-appen
- Se till att "HTTP-version" är den senaste, om den används för att köra webbappen

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av CIS Microsoft Azure grunderna benchmark-skiss kan du gå till följande artiklar om du vill lära dig mer om skissen eller besöka Azure Policy i Azure Portal för att tilldela initiativet:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure grunderna benchmark-översikt – översikt](./index.md)
> [CIS Microsoft Azure grunder benchmark-skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).