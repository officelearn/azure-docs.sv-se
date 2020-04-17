---
title: Exempelkontroller för Azure Security Benchmark-skiss
description: Kontrollera mappningen av azure security benchmark-skissens exempel på Azure-principen.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538735"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Styra mappningen av azure security benchmark-skissen

I följande artikel beskrivs hur exempelmappningen azure blueprint-skiss för Azure Blueprints Azure Security Benchmark mappas till Azure Security Benchmark-kontrollerna. Mer information om kontrollerna finns i [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview).

Följande mappningar är till **Azure Security Benchmark-kontrollerna.** Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. De mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj ** \[förhandsversionen:\]Granska Azure Security Benchmark-rekommendationer och distribuera specifika stöd för** inbyggda principinitiativ för vm-tillägg.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom kan efterlevnadsstandarden innehålla kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

- Undernät ska associeras med en nätverkssäkerhetsgrupp
- Adaptiva nätverkshärdningsrekommendationer bör tillämpas på internetvända virtuella datorer
- Virtuella datorer bör anslutas till ett godkänt virtuellt nätverk
- Internetvända virtuella datorer bör skyddas med nätverkssäkerhetsgrupper
- Service Bus ska använda en slutpunkt för virtuella nätverkstjänster
- App-tjänsten bör använda en slutpunkt för virtuella nätverkstjänster
- SQL Server bör använda en slutpunkt för tjänsten för virtuellt nätverk
- Event Hub bör använda en slutpunkt för virtuella nätverkstjänster
- Cosmos DB bör använda en slutpunkt för virtuella nätverkstjänster
- Key Vault bör använda en slutpunkt för virtuella nätverkstjänster
- Granska obegränsad nätverksåtkomst till lagringskonton
- Lagringskonton bör använda en slutpunkt för virtuella nätverkstjänster
- Container-registret bör använda en slutpunkt för en virtuell nätverkstjänst
- Virtuella nätverk bör använda angiven virtuell nätverksgateway
- Auktoriserade IP-intervall bör definieras på Kubernetes Services
- \[Preview\]: IP-vidarebefordran på din virtuella dator bör inaktiveras
- Internetvända virtuella datorer bör skyddas med nätverkssäkerhetsgrupper
- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Hanteringsportar bör stängas på dina virtuella datorer

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

- Network Watcher ska vara aktiverad

## <a name="13-protect-critical-web-applications"></a>1.3 Skydda kritiska webbprogram

- Kontrollera att WEB-appen har "Klientcertifikat (inkommande klientcertifikat)" inställt på 'På'
- CORS bör inte tillåta alla resurser att komma åt dina webbprogram
- CORS bör inte tillåta alla resurser att komma åt dina funktionsappar
- CORS bör inte tillåta alla resurser att komma åt din API-app
- Fjärrfelsökning bör inaktiveras för webbprogram
- Fjärrfelsökning bör inaktiveras för funktionsappar
- Fjärrfelsökning bör inaktiveras för API-appar

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Neka kommunikation med kända skadliga IP-adresser

- DDoS Protection Standard bör aktiveras
- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Adaptiva nätverkshärdningsrekommendationer bör tillämpas på internetvända virtuella datorer

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Spela in nätverkspaket och flödesloggar

- Network Watcher ska vara aktiverad

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

- Distribuera förutsättningar för granskning av Windows-virtuella datorer i "Säkerhetsalternativ - Nätverksåtkomst"
- Visa granskningsresultat från Windows-virtuella datorer i "Säkerhetsalternativ - Microsoft Network Client"
- Distribuera förutsättningar för granskning av Windows-virtuella datorer i "Säkerhetsalternativ - Nätverkssäkerhet"
- Visa granskningsresultat från Windows-virtuella datorer i "Säkerhetsalternativ - Nätverkssäkerhet"
- Distribuera förutsättningar för granskning av Windows-virtuella datorer i "Säkerhetsalternativ – Microsoft Network Server"
- Visa granskningsresultat från Windows-virtuella datorer i "Säkerhetsalternativ - Microsoft Network Server"
- Distribuera förutsättningar för granskning av Windows-virtuella datorer i Administrativa mallar - Nätverk
- Visa granskningsresultat från Windows virtuella datorer i "Administrativa mallar - Nätverk"

## <a name="22-configure-central-security-log-management"></a>2.2 Konfigurera hantering av centrala säkerhetsloggar

- Log Analytics-agenten ska installeras på virtuella datorer
- Log Analytics-agenten ska installeras på skalningsuppsättningar för virtuella datorer
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat
- Visa granskningsresultat från virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat
- Azure Monitor-loggprofilen ska samla in loggar för kategorierna "write", "delete" och 'action'
- Azure Monitor bör samla in aktivitetsloggar från alla regioner
- Automatisk etablering av log analytics-övervakningsagenten bör aktiveras på din prenumeration

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Aktivera granskningsloggning för Azure-resurser

- Diagnostikloggar i Azure Data Lake Store ska aktiveras
- Diagnostikloggar i Logic Apps bör aktiveras
- Diagnostikloggar i IoT Hub ska aktiveras
- Diagnostikloggar i batchkonton ska aktiveras
- Diagnostikloggar i skalningsuppsättningar för virtuella datorer bör aktiveras
- Diagnostikloggar i Event Hub ska aktiveras
- Diagnostikloggar i Söktjänster bör aktiveras
- Diagnostikloggar i App Services bör aktiveras
- Diagnostikloggar i Data Lake Analytics bör aktiveras
- Diagnostikloggar i Key Vault ska aktiveras
- Diagnostikloggar i Service Bus ska aktiveras
- Diagnostikloggar i Azure Stream Analytics ska aktiveras
- Granskning på SQL-server bör aktiveras
- Granska diagnostikinställning

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Samla in säkerhetsloggar från operativsystem

- Automatisk etablering av log analytics-övervakningsagenten bör aktiveras på din prenumeration
- Log Analytics-agenten ska installeras på virtuella datorer
- Log Analytics-agenten ska installeras på skalningsuppsättningar för virtuella datorer
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat
- Visa granskningsresultat från virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Aktivera aviseringar för avvikande aktivitet

- Standardprisnivå för Security Center-standard bör väljas
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Avancerad datasäkerhet bör aktiveras på dina SQL-hanterade instanser
- Avancerade typer av skydd mot hot bör ställas in på alla i avancerade datasäkerhetsinställningar för SQL-servern
- Avancerade typer av skydd mot hot bör ställas in på alla i SQL-hanterade instans avancerade datasäkerhetsinställningar

## <a name="28-centralize-anti-malware-logging"></a>2.8 Centralisera loggning av skadlig kod

- Microsoft Antimalware för Azure bör konfigureras för att automatiskt uppdatera skyddssignaturer
- Övervaka saknade slutpunktsskydd i Azure Security Center
- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Föra en inventering av administrativa konton

- Högst 3 ägare bör utses för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Använd särskilda administrativa konton

- Distribuera förutsättningar för granskning av virtuella Windows-datorer där gruppen Administratörer inte bara innehåller de angivna medlemmarna
- Visa granskningsresultat från virtuella Windows-datorer där gruppen Administratörer inte bara innehåller de angivna medlemmarna
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Visa granskningsresultat från virtuella Windows-datorer där gruppen Administratörer inte innehåller alla angivna medlemmar
- Distribuera förutsättningar för granskning av virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Visa granskningsresultat från virtuella Windows-datorer där gruppen Administratörer innehåller någon av de angivna medlemmarna
- Högst 3 ägare bör utses för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Använd multifaktorautentisering för all Azure Active Directory-baserad åtkomst

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- MFA ska aktiveras på konton med läsbehörighet för din prenumeration

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Logga och varna för misstänkt aktivitet från administrativa konton

- Standardprisnivå för Security Center-standard bör väljas

## <a name="39-use-azure-active-directory"></a>3.9 Använd Azure Active Directory

- En Azure Active Directory-administratör bör etableras för SQL-servrar
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering
- Kontrollera att Registrera dig med Azure Active Directory är aktiverat i API-appen
- Kontrollera att Registrera dig med Azure Active Directory är aktiverat på WEB App
- Kontrollera att Registrera dig med Azure Active Directory är aktiverat på Function App

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Regelbundet granska och förena användaråtkomst

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Föra en inventering av känslig information

- Känsliga data i DINA SQL-databaser bör klassificeras

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Kryptera all känslig information under transport

- Säker överföring till lagringskonton ska vara aktiverat
- Senaste TLS-versionen ska användas i din API-app
- Senaste TLS-versionen ska användas i din Webbapp
- Senaste TLS-versionen ska användas i din funktionsapp
- Funktionsappen ska endast vara tillgänglig via HTTPS
- Webbprogrammet ska endast vara tillgängligt via HTTPS
- API-app bör endast vara tillgänglig via HTTPS
- Framtvinga SSL-anslutning bör aktiveras för MySQL-databasservrar
- Framtvinga SSL-anslutning bör aktiveras för PostgreSQL-databasservrar
- Endast säkra anslutningar till Redis-cachen ska aktiveras

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

- Känsliga data i DINA SQL-databaser bör klassificeras
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar
- Avancerad datasäkerhet bör aktiveras på dina SQL-hanterade instanser

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Använd Azure RBAC för att kontrollera åtkomsten till resurser

- Rollbaserad åtkomstkontroll (RBAC) ska användas på Kubernetes Services
- Granskningsanvändning av anpassade RBAC-regler

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Kryptera känslig information i vila

- Transparent datakryptering i SQL-databaser bör aktiveras
- Diskkryptering bör tillämpas på virtuella datorer
- Obundna diskar ska krypteras
- SQL-server TDE-skydd ska krypteras med din egen nyckel
- SQL-hanterad instans TDE-skydd ska krypteras med din egen nyckel
- Variabler för automationskonto ska krypteras
- Service Fabric-kluster bör ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Logga och avisera om ändringar i kritiska Azure-resurser

- Azure Monitor bör samla in aktivitetsloggar från alla regioner

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Kör automatiserade verktyg för sårbarhetsskanning

- Sårbarhetsbedömning bör aktiveras på dina SQL-servrar
- Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser
- \[Bedömning\] av förhandsgranskningssårbarhet bör aktiveras på virtuella datorer
- Inställningar för bedömning av säkerhetsproblem för SQL-servern bör innehålla en e-postadress för att ta emot skanningsrapporter

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Distribuera lösning för hantering av automatisk operativsystemets korrigering

- Systemuppdateringar ska ha installerats på dina datorer
- Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras
- Se till att ".NET Framework"-versionen är den senaste, om den används som en del av funktionsappen
- Kontrollera att ".NET Framework"-versionen är den senaste, om den används som en del av webbappen
- Kontrollera att ".NET Framework"-versionen är den senaste, om den används som en del av API-appen

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Distribuera automatisk lösning för hantering av programkorrigering från tredje part

- Se till att "PHP-versionen" är den senaste, om den används som en del av Api-appen
- Se till att "PHP-versionen" är den senaste, om den används som en del av webbappen
- Se till att "PHP-versionen" är den senaste, om den används som en del av funktionsappen
- Se till att "Java-versionen" är den senaste, om den används som en del av webbappen
- Se till att "Java-versionen" är den senaste, om den används som en del av funktionsappen
- Se till att "Java-versionen" är den senaste, om den används som en del av Api-appen
- Se till att Python-versionen är den senaste, om den används som en del av webbappen
- Se till att Python-versionen är den senaste, om den används som en del av funktionsappen
- Se till att Python-versionen är den senaste, om den används som en del av Api-appen
- Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning
- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Säkerhetsproblem i behållarens säkerhetskonfigurationer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas

## <a name="68-use-only-approved-applications"></a>6.8 Använd endast godkända ansökningar

- Standardprisnivå för Security Center-standard bör väljas
- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="69-use-only-approved-azure-services"></a>6.9 Använd endast godkända Azure-tjänster

- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Lagringskonton ska migreras till nya Azure Resource Manager-resurser

## <a name="610-implement-approved-application-list"></a>6.10 Implementera godkänd ansökningslista

- Standardprisnivå för Security Center-standard bör väljas
- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Underhåll säkra Azure-resurskonfigurationer

- \[Förhandsversion:\]Säkerhetsprinciper för pod bör definieras på Kubernetes-tjänster

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Underhåll konfigurationer av säkra operativsystem

- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas
- Säkerhetsproblem i behållarens säkerhetskonfigurationer bör åtgärdas
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Implementera automatisk konfigurationsövervakning för Azure-tjänster

- \[Förhandsversion:\]Säkerhetsprinciper för pod bör definieras på Kubernetes-tjänster

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Implementera automatisk konfigurationsövervakning för operativsystem

- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas
- Säkerhetsproblem i behållarens säkerhetskonfigurationer bör åtgärdas
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas

## <a name="711-manage-azure-secrets-securely"></a>7.11 Hantera Azure-hemligheter på ett säkert sätt

- Key Vault-objekt ska kunna återställas

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Hantera identiteter på ett säkert och automatiskt sätt

- Hanterad identitet ska användas i funktionsappen
- Hanterad identitet ska användas i webbappen
- Hanterad identitet ska användas i din API-app

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Använd centralt hanterad programvara mot skadlig kod

- Övervaka saknade slutpunktsskydd i Azure Security Center
- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Förskansa filer som ska överföras till Azure-resurser som inte är beräkningsbaserade

- Standardprisnivå för Security Center-standard bör väljas

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Se till att programvara och signaturer mot skadlig kod uppdateras

- Microsoft Antimalware för Azure bör konfigureras för att automatiskt uppdatera skyddssignaturer

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Se till att regelbundna automatiska säkerhetskopieringar

- Långsiktig geo-redundant säkerhetskopiering bör aktiveras för Azure SQL-databaser
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database for MySQL
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database för PostgreSQL
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database för MariaDB
- Azure Backup bör aktiveras för virtuella datorer

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Utför kompletta systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

- Långsiktig geo-redundant säkerhetskopiering bör aktiveras för Azure SQL-databaser
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database for MySQL
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database för PostgreSQL
- Geo redundant säkerhetskopiering bör aktiveras för Azure Database för MariaDB
- Azure Backup bör aktiveras för virtuella datorer

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Säkerställa skydd av säkerhetskopior och kundhanterade nycklar

- Key Vault-objekt ska kunna återställas

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Skapa en incidentbedömning och prioriteringsprocedur

- Standardprisnivå för Security Center-standard bör väljas

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Ange kontaktinformation för säkerhetsincidenter och konfigurera varningsmeddelanden för säkerhetsincidenter

- En e-postadress för säkerhetskontakter ska anges för din prenumeration
- Ett telefonnummer för säkerhetskontakter ska anges för din prenumeration
- Avancerade datasäkerhetsinställningar för SQL-servern bör innehålla en e-postadress för att ta emot säkerhetsaviseringar
- Avancerade datasäkerhetsinställningar för SQL-hanterad instans bör innehålla en e-postadress för att ta emot säkerhetsaviseringar
- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade datasäkerhetsinställningar för SQL-servern
- E-postmeddelanden till administratörer och prenumerationsägare bör aktiveras i avancerade inställningar för SQL-hanterad instans

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollmappningen av Azure Security Benchmark-skissen besöker du Azure Policy i Azure-portalen för att tilldela initiativet:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).