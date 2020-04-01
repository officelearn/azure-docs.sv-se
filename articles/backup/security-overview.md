---
title: Översikt över säkerhetsfunktioner
description: Lär dig mer om säkerhetsfunktioner i Azure Backup som hjälper dig att skydda dina säkerhetskopierade data och uppfylla ditt företags säkerhetsbehov.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 91a0f29862b0c9c35e562c143e28ebbc6c39cf94
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423189"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Översikt över säkerhetsfunktioner i Azure Backup

Ett av de viktigaste stegen du kan vidta för att skydda dina data är att ha en tillförlitlig infrastruktur för säkerhetskopiering. Men det är lika viktigt att se till att dina data säkerhetskopieras på ett säkert sätt och att dina säkerhetskopior alltid skyddas. Azure Backup ger säkerhet till din säkerhetskopieringsmiljö – både när dina data är under överföring och i vila. I den här artikeln visas säkerhetsfunktioner i Azure Backup som hjälper dig att skydda dina säkerhetskopierade data och uppfylla ditt företags säkerhetsbehov.

## <a name="management-and-control-of-identity-and-user-access"></a>Hantering och kontroll av identitet och användaråtkomst

Med Azure Backup kan du hantera finkornig åtkomst med hjälp av [RBAC (Azure Role-Based Access Control).](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) RBAC kan du segregera uppgifter inom ditt team och bevilja endast den mängd tillgång till användare som krävs för att göra sitt jobb.

Azure Backup innehåller tre inbyggda roller för att styra säkerhetskopieringshantering:

* Deltagare för säkerhetskopiering – för att skapa och hantera säkerhetskopior, förutom att ta bort Valvet för Återställningstjänster och ge åtkomst till andra
* Säkerhetskopiera operatör - allt en deltagare gör förutom att ta bort principer för säkerhetskopiering och hantering av säkerhetskopiering
* Backup Reader - behörighet att visa alla säkerhetskopieringshanteringsåtgärder

Läs mer om [rollbaserad åtkomstkontroll för att hantera Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Azure Backup har flera säkerhetskontroller inbyggda i tjänsten för att förhindra, identifiera och svara på säkerhetsproblem. Läs mer om [säkerhetskontroller för Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separation mellan gäst- och Azure-lagring

Med Azure Backup, som inkluderar säkerhetskopiering av virtuella datorer och SQL och SAP HANA i säkerhetskopiering av virtuella datorer, lagras säkerhetskopieringsdata i Azure-lagring och gästen har ingen direkt åtkomst till säkerhetskopieringslagring eller dess innehåll.  Med säkerhetskopiering av virtuella datorer görs skapandet och lagringen för säkerhetskopiering av ögonblicksbilder av Azure-infrastruktur där gästen inte har någon annan inblandning än att skapa arbetsbelastningen för konsekventa säkerhetskopieringar av program.  Med SQL och SAP HANA får tillägget för säkerhetskopiering tillfällig åtkomst att skriva till specifika blobbar.  På så sätt kan inte befintliga säkerhetskopior manipuleras eller tas bort av gästen även i en komprometterad miljö.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Internet-anslutning krävs inte för azure vm-säkerhetskopiering

Säkerhetskopiering av virtuella Azure-datorer kräver förflyttning av data från den virtuella datorns disk till Recovery Services-valvet. All nödvändig kommunikation och dataöverföring sker dock bara i Azure-stamnätet utan att behöva komma åt ditt virtuella nätverk. Därför kräver säkerhetskopiering av virtuella Azure-datorer som placeras i skyddade nätverk inte att du tillåter åtkomst till några IPs eller FQDN.Therefore, backup of Azure VMs placed inside secured networks doesn't require you to allow access to any IPs or FQDNs.

## <a name="private-endpoints-for-azure-backup"></a>Privata slutpunkter för Azure-säkerhetskopiering

Du kan nu använda [privata slutpunkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för att säkerhetskopiera dina data på ett säkert sätt från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slutpunkten använder en IP från VNET-adressutrymmet för ditt valv, så du behöver inte exponera dina virtuella nätverk för offentliga IP-adresser. Privata slutpunkter kan användas för säkerhetskopiering och återställning av SQL- och SAP HANA-databaser som körs i dina virtuella Azure-datorer. Den kan också användas för dina lokala servrar med MARS-agenten.

>[!NOTE]
> Den här funktionen är för närvarande i begränsad tillgänglighet. Fyll i [den här enkäten](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) och [skicka e-post till oss](mailto:azbackupnetsec@microsoft.com) om du är intresserad av att använda privata slutpunkter för Azure Backup. Möjligheten att använda den här funktionen måste godkännas från Azure Backup-tjänsten.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Kryptering av data under transitering och i vila

Kryptering skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Inom Azure skyddas data under överföring mellan Azure-lagring och valvet av HTTPS. Dessa data finns kvar i Azure-stamnätet.

* Säkerhetskopierade data krypteras automatiskt med Microsoft-hanterade nycklar. Du kan också kryptera dina säkerhetskopierade hanterade disk-virtuella datorer i Recovery Services Vault med hjälp av [kundhanterade nycklar](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) som lagras i Azure Key Vault. Du behöver inte vidta några uttryckliga åtgärder för att aktivera den här krypteringen. Det gäller alla arbetsbelastningar som säkerhetskopieras till ditt Recovery Services-valv.

* Azure Backup stöder säkerhetskopiering och återställning av virtuella Azure-datorer som har sina OPERATIVSYSTEM/datadiskar krypterade med Azure Disk Encryption (ADE). [Läs mer om krypterade virtuella Azure-datorer och Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Skydd av säkerhetskopieringsdata från oavsiktliga borttagningar

Azure Backup innehåller säkerhetsfunktioner som skyddar säkerhetskopierade data även efter borttagning. Med mjuk borttagning, om användaren tar bort säkerhetskopian av en virtuell dator, sparas säkerhetskopieringsdata i ytterligare 14 dagar, vilket gör att det säkerhetskopierade objektet kan återställas utan dataförlust. Den ytterligare 14 dagars lagring av säkerhetskopierade data i tillståndet "mjuk borttagning" medför inga kostnader för kunden. [Läs mer om mjuk borttagning](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Övervakning och varningar om misstänkt aktivitet

Azure Backup tillhandahåller [inbyggda övervaknings- och aviseringsfunktioner](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) för att visa och konfigurera åtgärder för händelser relaterade till Azure Backup. [Säkerhetskopieringsrapporter](https://docs.microsoft.com/azure/backup/configure-reports) fungerar som ett enda mål för att spåra användning, granska säkerhetskopior och återställningar och identifiera viktiga trender på olika detaljnivåer. Om du använder Övervaknings- och rapporteringsverktygen för Azure Backup kan du varna dig för obehörig, misstänkt eller skadlig aktivitet så snart de inträffar.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Säkerhetsfunktioner för att skydda hybridsäkerhetskopior

Azure Backup-tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera och återställa filer, mappar och volym- eller systemtillståndet från en lokal dator till Azure. MARS tillhandahåller nu säkerhetsfunktioner för att skydda hybridsäkerhetskopior. Dessa funktioner omfattar:

* Ett extra autentiseringslagret läggs till när en kritisk åtgärd som att ändra en lösenfras utförs. Den här valideringen är att säkerställa att sådana åtgärder endast kan utföras av användare som har giltiga Azure-autentiseringsuppgifter. [Läs mer om funktioner som förhindrar attacker](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* Borttagna säkerhetskopierade data sparas i ytterligare 14 dagar från det datum då borttagningen skulle tas bort. Detta säkerställer återställning av data inom en viss tidsperiod, så det finns ingen dataförlust även om en attack inträffar. Dessutom upprätthålls ett större antal lägsta återställningspunkter för att skydda mot korrupta data. [Läs mer om hur du återställer borttagna säkerhetskopierade data](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

## <a name="compliance-with-standardized-security-requirements"></a>Överensstämmelse med standardiserade säkerhetskrav

För att hjälpa organisationer att uppfylla nationella, regionala och branschspecifika krav som styr insamling och användning av enskilda personers data, erbjuder Microsoft Azure & Azure Backup en omfattande uppsättning certifieringar och intyg. [Se listan över efterlevnadscertifieringar](compliance-offerings.md)

## <a name="next-steps"></a>Nästa steg

* [Säkerhetsfunktioner för att skydda molnarbetsbelastningar som använder Azure Backup](backup-azure-security-feature-cloud.md)
* [Säkerhetsfunktioner för att skydda hybridsäkerhetskopior som använder Azure Backup](backup-azure-security-feature.md)
