---
title: Exempel kontroller för Azure Security benchmark-skiss
description: Kontroll av mappningen av utkastet till Azure Policy i Azure Security benchmark.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81538735"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Kontroll mappning för exempel på Azure Security benchmark-skiss

I följande artikel beskrivs hur Azure-skisser exempel på Azures säkerhets modell för säkerhet mappas till Azures säkerhets benchmark-kontroller. Mer information om kontrollerna finns i [Azure Security benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview).

Följande mappningar är till **Azures säkerhets benchmark** -kontroller. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. De mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på ** \[och välj förhands\]granskningen: granska Azures säkerhets benchmark-rekommendationer och distribuera särskilt stöd** för inbyggda princip tillägg för virtuella datorer.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Dessutom kan efterlevnaden av standarden omfatta kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1 skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandväggen på din Virtual Network

- Undernät ska associeras med en nätverks säkerhets grupp
- Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet
- Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk
- Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper
- Service Bus bör använda en tjänst slut punkt för virtuellt nätverk
- App Service bör använda en tjänst slut punkt för virtuellt nätverk
- SQL Server bör använda en tjänst slut punkt för virtuellt nätverk
- Event Hub bör använda en tjänst slut punkt för virtuellt nätverk
- Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk
- Key Vault bör använda en tjänst slut punkt för virtuellt nätverk
- Granska obegränsad nätverks åtkomst till lagrings konton
- Lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk
- Container Registry bör använda en tjänst slut punkt för virtuellt nätverk
- Virtuella nätverk ska använda angiven virtuell nätverksgateway
- Auktoriserade IP-intervall ska definieras på Kubernetes Services
- \[För\]hands version: IP-vidarebefordran på den virtuella datorn bör inaktive ras
- Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper
- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Hanterings portar bör stängas på dina virtuella datorer

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2 övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

- Network Watcher ska vara aktiverat

## <a name="13-protect-critical-web-applications"></a>1,3 skydda viktiga webb program

- Se till att WEBBAPP har "klient certifikat (inkommande klient certifikat)" inställd på "på"
- CORS bör inte tillåta alla resurser åtkomst till dina webb program
- CORS bör inte tillåta alla resurser att komma åt dina funktions program
- CORS bör inte tillåta alla resurser åtkomst till din API-app
- Fjärrfelsökning bör inaktive ras för webb program
- Fjärrfelsökning bör inaktive ras för Function-appar
- Fjärrfelsökning bör inaktive ras för API Apps

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4 neka kommunikation med kända skadliga IP-adresser

- DDoS Protection standard ska vara aktive rad
- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet

## <a name="15-record-network-packets-and-flow-logs"></a>1,5 registrera nätverks paket och flödes loggar

- Network Watcher ska vara aktiverat

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11 Använd automatiserade verktyg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar

- Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ – nätverks åtkomst
- Visa gransknings resultat från virtuella Windows-konfigurationer i säkerhets alternativ – Microsoft Network Client
- Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ – nätverks säkerhet
- Visa gransknings resultat från virtuella Windows-konfigurationer i säkerhets alternativ – nätverks säkerhet
- Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ – Microsoft-nätverks server
- Visa gransknings resultat från virtuella Windows-konfigurationer i säkerhets alternativ-Microsoft-nätverks server
- Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i Administrativa mallar-Network
- Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i Administrativa mallar-Network

## <a name="22-configure-central-security-log-management"></a>2,2 Konfigurera hantering av central säkerhets logg

- Log Analytics agenten ska installeras på virtuella datorer
- Log Analytics agenten ska installeras på Virtual Machine Scale Sets
- Distribuera krav för att granska virtuella Windows-datorer där Log Analytics agent inte är ansluten som förväntat
- Visa gransknings resultat från virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat
- Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "
- Azure Monitor ska samla in aktivitets loggar från alla regioner
- Automatisk etablering av Log Analytics övervaknings agenten ska vara aktive rad för din prenumeration

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3 Aktivera gransknings loggning för Azure-resurser

- Diagnostikloggar i Azure Data Lake Store ska vara aktive rad
- Diagnostikloggar i Logic Apps ska vara aktive rad
- Diagnostikloggar i IoT Hub ska vara aktive rad
- Diagnostikloggar i batch-konton måste vara aktiverade
- Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad
- Diagnostikloggar i Händelsehubben måste vara aktive rad
- Diagnostikloggar i search Services ska vara aktive rad
- Diagnostikloggar i App Services ska vara aktive rad
- Diagnostikloggar i Data Lake Analytics ska vara aktive rad
- Diagnostikloggar i Key Vault ska vara aktive rad
- Diagnostikloggar i Service Bus ska vara aktive rad
- Diagnostikloggar i Azure Stream Analytics ska vara aktive rad
- Granskning på SQL Server måste vara aktiverat
- Granska diagnostikinställning

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4 samla in säkerhets loggar från operativ system

- Automatisk etablering av Log Analytics övervaknings agenten ska vara aktive rad för din prenumeration
- Log Analytics agenten ska installeras på virtuella datorer
- Log Analytics agenten ska installeras på Virtual Machine Scale Sets
- Distribuera krav för att granska virtuella Windows-datorer där Log Analytics agent inte är ansluten som förväntat
- Visa gransknings resultat från virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7 aktivera aviseringar för avvikande aktivitet

- Security Center standard pris nivå ska väljas
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Avancerad data säkerhet ska vara aktiverat på SQL-hanterade instanser
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans

## <a name="28-centralize-anti-malware-logging"></a>2,8 centralisera loggning mot skadlig kod

- Microsoft Antimalware för Azure bör konfigureras att automatiskt uppdatera skydds-signaturer
- Övervaka saknade Endpoint Protection i Azure Security Center
- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1 underhåll av en inventering av administrativa konton

- Högst 3 ägare bör anges för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration

## <a name="33-use-dedicated-administrative-accounts"></a>3,3 Använd dedikerade administrativa konton

- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer inte bara innehåller de angivna medlemmarna
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer inte bara innehåller de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Högst 3 ägare bör anges för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5 Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7 logg och avisering om misstänkt aktivitet från administrativa konton

- Security Center standard pris nivå ska väljas

## <a name="39-use-azure-active-directory"></a>3,9 Använd Azure Active Directory

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering
- Kontrol lera att register med Azure Active Directory är aktiverat i API-appen
- Kontrol lera att register med Azure Active Directory är aktiverat i webbappen
- Kontrol lera att register med Azure Active Directory är aktiverat på Funktionsapp

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10 regelbundet granska och stämma av användar åtkomst

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1 underhålla en inventering av känslig information

- Känsliga data i dina SQL-databaser bör klassificeras

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4 kryptera all känslig information under överföring

- Säker överföring till lagringskonton ska vara aktiverat
- Den senaste TLS-versionen ska användas i din API-app
- Den senaste TLS-versionen ska användas i din webbapp
- Den senaste TLS-versionen ska användas i Funktionsapp
- Funktionsapp bör endast vara tillgängligt via HTTPS
- Webb program bör endast vara tillgängliga via HTTPS
- API-appen bör bara vara tillgänglig via HTTPS
- Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar
- Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5 Använd ett aktivt identifierings verktyg för att identifiera känsliga data

- Känsliga data i dina SQL-databaser bör klassificeras
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Avancerad data säkerhet ska vara aktiverat på SQL-hanterade instanser

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6 Använd Azure RBAC för att styra åtkomsten till resurser

- Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services
- Granska användningen av anpassade RBAC-regler

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8 kryptera känslig information i vila

- transparent datakryptering på SQL-databaser ska aktive ras
- Disk kryptering bör tillämpas på virtuella datorer
- Ej anslutna diskar ska vara krypterade
- SQL Server TDE-skyddskomponenten bör vara krypterat med din egen nyckel
- SQL-hanterad instans TDE-skydd ska vara krypterat med din egen nyckel
- Variabler för Automation-konton ska vara krypterade
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9 logg och varning vid ändringar av kritiska Azure-resurser

- Azure Monitor ska samla in aktivitets loggar från alla regioner

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1 köra automatiserade sårbarhets skannings verktyg

- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- \[För\] hands version av sårbarhets bedömning ska aktive ras på Virtual Machines
- Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2 distribuera automatiserad hanterings lösning för operativ system

- Systemuppdateringar ska ha installerats på dina datorer
- System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras
- Se till att ".NET Framework"-versionen är den senaste, om den används som en del av Funktionsapp
- Se till att ".NET Framework"-versionen är den senaste, om den används som en del av webbappen
- Se till att ".NET Framework"-versionen är den senaste, om den används som en del av API-appen

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3 distribuera automatisk hanterings lösning för program uppdatering från tredje part

- Se till att PHP-version är den senaste, om den används som en del av API-appen
- Se till att PHP-version är den senaste, om den används som en del av webbappen
- Se till att PHP-version är den senaste, om den används som en del av Function-appen
- Se till att Java-version är den senaste, om den används som en del av webbappen
- Se till att Java-version är den senaste, om den används som en del av Function-appen
- Se till att Java-version är den senaste, om den används som en del av API-appen
- Se till att "python-version" är den senaste, om den används som en del av webbappen
- Se till att "python-version" är den senaste, om den används som en del av Function-appen
- Se till att "python-version" är den senaste, om den används som en del av API-appen
- Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5 Använd en risk klassificerings process för att prioritera korrigering av identifierade säkerhets risker

- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas

## <a name="68-use-only-approved-applications"></a>6,8 Använd endast godkända program

- Security Center standard pris nivå ska väljas
- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="69-use-only-approved-azure-services"></a>6,9 Använd endast godkända Azure-tjänster

- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Lagrings konton ska migreras till nya Azure Resource Manager resurser

## <a name="610-implement-approved-application-list"></a>6,10 implementera lista över godkända program

- Security Center standard pris nivå ska väljas
- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3 upprätthålla säker Azure-resurs konfiguration

- \[För\]hands version: Pod-säkerhetsprinciper bör definieras för Kubernetes-tjänster

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4 underhåll av säkra operativ system konfigurationer

- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9 implementera automatisk konfigurations övervakning för Azure-tjänster

- \[För\]hands version: Pod-säkerhetsprinciper bör definieras för Kubernetes-tjänster

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10 implementera automatisk konfigurations övervakning för operativ system

- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas

## <a name="711-manage-azure-secrets-securely"></a>7,11 Hantera Azure-hemligheter på ett säkert sätt

- Key Vault objekt ska vara återställnings bara

## <a name="712-manage-identities-securely-and-automatically"></a>7,12 hantera identiteter säkert och automatiskt

- Hanterad identitet ska användas i Funktionsapp
- Hanterad identitet ska användas i din webbapp
- Hanterad identitet ska användas i din API-app

## <a name="81-use-centrally-managed-anti-malware-software"></a>8,1 Använd centralt hanterat program mot skadlig kod

- Övervaka saknade Endpoint Protection i Azure Security Center
- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2 förgenomsöka filer som ska överföras till Azure-resurser som inte är Compute

- Security Center standard pris nivå ska väljas

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3 se till att program vara och signaturer för program mot skadlig kod uppdateras

- Microsoft Antimalware för Azure bör konfigureras att automatiskt uppdatera skydds-signaturer

## <a name="91-ensure-regular-automated-back-ups"></a>9,1 Säkerställ regelbunden automatisk säkerhets kopiering

- Långsiktig Geo-redundant säkerhets kopiering ska aktive ras för Azure SQL-databaser
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB
- Azure Backup ska vara aktiverat för Virtual Machines

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2 utför fullständiga säkerhets kopieringar och säkerhets kopiering av alla Kundhanterade nycklar

- Långsiktig Geo-redundant säkerhets kopiering ska aktive ras för Azure SQL-databaser
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB
- Azure Backup ska vara aktiverat för Virtual Machines

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4 se till att skydda säkerhets kopior och kund hanterade nycklar

- Key Vault objekt ska vara återställnings bara

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2 Skapa en incident bedömnings-och prioriterings procedur

- Security Center standard pris nivå ska väljas

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4 ge säkerhets incidenter kontakt information och konfigurera aviserings meddelanden för säkerhets incidenter

- Du måste tillhandahålla en e-postadress till en säkerhets kontakt för din prenumeration
- Du måste tillhandahålla ett telefonnummer till en säkerhets kontakt för din prenumeration
- Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- Avancerade data säkerhets inställningar för SQL-hanterad instans ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar i SQL Server
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar för SQL-hanterad instans

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av Azure Security benchmark-skissen kan du gå till Azure Policy i Azure Portal för att tilldela initiativet:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).