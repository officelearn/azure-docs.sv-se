---
title: Vanliga frågor och svar (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Vanliga frågor och svar om Azure SQL Managed instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: d2e4b07c97e09fce5cdaa034e2fe67a18ef0d7f1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171167"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln innehåller de vanligaste frågorna om [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funktioner som stöds

**Var kan jag hitta en lista över funktioner som stöds på SQL-hanterad instans?**

En lista över funktioner som stöds i SQL-hanterad instans finns i [funktioner för Azure SQL-hanterad instans](../database/features-comparison.md).

Skillnader i syntax och beteende mellan Azure SQL-hanterad instans och SQL Server finns i [skillnader i T-SQL från SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Teknisk specifikation, resurs gränser och andra begränsningar
 
**Var kan jag hitta tekniska egenskaper och resurs gränser för SQL-hanterad instans?**

Information om tillgängliga egenskaper för maskin varu skapande finns i [tekniska skillnader i maskin varu generationer](resource-limits.md#hardware-generation-characteristics).
För tillgängliga tjänst nivåer och deras egenskaper, se [tekniska skillnader mellan tjänst nivåer](resource-limits.md#service-tier-characteristics).

**Vilken service nivå är jag berättigad till?**

Alla kunder är berättigade till alla tjänst nivåer. Men om du vill byta ut befintliga licenser för rabatterade priser på Azure SQL-hanterad instans med hjälp av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/), bör du tänka på att SQL Server Enterprise Edition-kunder med Software Assurance är berättigade till [generell användning](../database/service-tier-general-purpose.md) eller [affärskritisk](../database/service-tier-business-critical.md) prestanda nivåer och SQL Server Standard Edition-kunder med Software Assurance är berättigade till generell användning prestanda nivå. Mer information finns i [vissa rättigheter för AHB](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Vilka prenumerations typer stöds för SQL-hanterad instans?**

En lista över prenumerations typer som stöds finns i [prenumerations typer som stöds](resource-limits.md#supported-subscription-types). 

**Vilka Azure-regioner stöds?**

Hanterade instanser kan skapas i de flesta Azure-regioner. Se [regioner som stöds för SQL-hanterad instans](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du behöver hanterad instans i en region som för närvarande inte stöds [skickar du en supportbegäran via Azure Portal](../database/quota-increase-request.md).

**Finns det några kvot begränsningar för distributioner av SQL-hanterade instanser?**

Den hanterade instansen har två standard gränser: begränsa antalet undernät som du kan använda och en gräns för antalet virtuella kärnor som du kan etablera. Gränserna varierar mellan olika prenumerations typer och regioner. En lista över begränsningar för regionala resurser efter prenumerations typ finns i tabell från [regional resurs begränsning](resource-limits.md#regional-resource-limitations). Detta är mjuka gränser som kan ökas på begäran. Om du behöver etablera fler hanterade instanser i dina aktuella regioner skickar du en supportbegäran för att öka kvoten med hjälp av Azure Portal. Mer information finns i [begäran om kvot ökning för Azure SQL Database](../database/quota-increase-request.md).

**Kan jag öka antalet databas gränser (100) på min hanterade instans på begäran?**

Nej, och för närvarande finns det inga bekräftade planer för att öka antalet databaser på SQL-hanterad instans. 

**Var kan jag migrera om jag har mer än 8TB data?**
Du kan överväga att migrera till andra Azure-varianter som passar din arbets belastning: [Azure SQL Database storskalig](../database/service-tier-hyperscale.md) eller [SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Var kan jag migrera om jag har särskilda maskin varu krav som större RAM till vCore-förhållande eller flera processorer?**
Du kan överväga att migrera till [SQL Server på Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) eller [Azure SQL Database](../database/sql-database-paas-overview.md) minne/CPU optimerad.

## <a name="known-issues--bugs"></a>Kända problem & buggar

**Var hittar jag kända problem och buggar?**

Information om buggar och kända problem finns i [kända problem](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nya funktioner

**Var hittar jag de senaste funktionerna och funktionerna i den offentliga för hands versionen?**

För nya och förhands gransknings funktioner, se [viktig information](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Skapa, uppdatera, ta bort eller flytta SQL-hanterad instans

**Hur kan jag etablera SQL-hanterad instans?**

Du kan etablera en instans från [Azure Portal](instance-create-quickstart.md), [POWERSHELL](scripts/create-configure-managed-instance-powershell.md), [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) och [arm-mallar](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Kan jag etablera hanterade instanser i en befintlig prenumeration?**

Ja, du kan etablera en hanterad instans i en befintlig prenumeration om prenumerationen tillhör de [prenumerations typer som stöds](resource-limits.md#supported-subscription-types).

**Varför kunde jag inte etablera en hanterad instans i under nätet vars namn börjar med en siffra?**

Detta är en aktuell begränsning för den underliggande komponenten som verifierar under nätets namn mot regex ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Alla namn som skickar regex och är giltiga under näts namn stöds för närvarande.

**Hur kan jag skala min hanterade instans?**

Du kan skala din hanterade instans från [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [POWERSHELL](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update) eller [arm-mallar](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Kan jag flytta min hanterade instans från en region till en annan?**

Ja, det kan du. Instruktioner finns i [Flytta resurser mellan regioner](../database/move-resources-across-regions.md).

**Hur kan jag ta bort min hanterade instans?**

Du kan ta bort hanterade instanser via Azure Portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete) eller [Resource Manager REST-API: er](https://docs.microsoft.com/rest/api/sql/managedinstances/delete).

**Hur lång tid tar det att skapa eller uppdatera en instans eller återställa en databas?**

Förväntad tid för att skapa en ny hanterad instans eller ändra tjänst nivåerna (virtuella kärnor, Storage), är beroende av flera faktorer. Se [hanterings åtgärder](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Namngivningskonventioner

**Kan en hanterad instans ha samma namn som en SQL Server lokal instans?**

Det finns inte stöd för att ändra namnet på en hanterad instans.

**Kan jag ändra DNS-zonens prefix?**

Ja, standard-DNS-zon för hanterade instanser *. Database.Windows.net* kan ändras. 

Om du vill använda en annan DNS-zon i stället för standard, till exempel *. contoso.com*: 
- Använd CliConfig för att definiera ett alias. Verktyget är bara ett gränssnitt för register inställningar, så det kan göras med hjälp av en grup princip eller ett skript.
- Använd *CNAME* med alternativet *TrustServerCertificate = True* .

## <a name="move-a-database-from-sql-managed-instance"></a>Flytta en databas från SQL-hanterad instans 

**Hur kan jag flytta en databas från SQL-hanterad instans tillbaka till SQL Server eller Azure SQL Database?**

Du kan [Exportera en databas till BACPAC](../database/database-export.md) och sedan [Importera BACPAC-filen](../database/database-import.md). Detta är den rekommenderade metoden om databasen är mindre än 100 GB.

Transaktionsreplikering kan användas om alla tabeller i databasen har primära nycklar.

`COPY_ONLY`Det går inte att återställa inbyggda säkerhets kopior från SQL Managed Instance till SQL Server eftersom SQL-hanterad instans har en högre databas version jämfört med SQL Server.

## <a name="migrate-an-instance-database"></a>Migrera en instans databas

**Hur kan jag migrera min instans databas till Azure SQL Database?**

Ett alternativ är att [Exportera databasen till en BACPAC](../database/database-export.md) och sedan [Importera BACPAC-filen](../database/database-import.md). 

Detta är den rekommenderade metoden om databasen är mindre än 100 GB. Transaktionsreplikering kan användas om alla tabeller i databasen har primära nycklar.

## <a name="switch-hardware-generation"></a>Växla maskin varu generering 

**Kan jag byta maskin varu generering för SQL-hanterad instans mellan gen 4 och gen 5 Online?**

Automatisk Online-växling mellan maskin varu generationer är möjlig om båda maskin varu generationerna är tillgängliga i den region där SQL-hanterad instans är etablerad. I det här fallet kan du kontrol lera [sidan vCore modell översikt](../database/service-tiers-vcore.md), som förklarar hur du växlar mellan maskin varu generationer.

Detta är en tids krävande åtgärd, eftersom en ny hanterad instans tillhandahålls i bakgrunden och databaserna automatiskt överförs mellan de gamla och nya instanserna, med en snabb redundans i slutet av processen. 

**Vad händer om båda maskin varu generationerna inte stöds i samma region?**

Om båda maskin varu generationerna inte stöds i samma region, är det möjligt att ändra maskin varu generationen, men måste utföras manuellt. Detta kräver att du etablerar en ny instans i den region där den önskade maskin varu generationen är tillgänglig och manuellt säkerhetskopierar och återställer data mellan de gamla och nya instanserna.

**Vad händer om det inte finns tillräckligt med IP-adresser för att utföra uppdaterings åtgärden?**

Om det inte finns tillräckligt med IP-adresser i under nätet där din hanterade instans är etablerad måste du skapa ett nytt undernät och en ny hanterad instans i det. Vi föreslår också att det nya under nätet skapas med fler IP-adresser allokerade så att framtida uppdaterings åtgärder undviker liknande situationer. (För rätt under näts storlek kontrollerar du [hur storleken på ett VNet-undernät ska fastställas](vnet-subnet-determine-size.md).) När den nya instansen har allokerats kan du manuellt säkerhetskopiera och återställa data mellan de gamla och nya instanserna eller utföra återställning mellan olika [tidpunkter](point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Justera prestanda

**Hur gör jag för att finjustera prestanda för SQL-hanterad instans?**

SQL-hanterad instans i Generell användning nivån använder Fjärrlagring, så storleken på data-och loggfiler är till för prestanda. Mer information finns i [effekten av logg fils storleken på generell användning prestanda för SQL-hanterad instans](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Om din arbets belastning består av många små transaktioner bör du överväga att byta Anslutnings typ från proxy till omdirigeringsläge.

## <a name="maximum-storage-size"></a>Maximal lagrings storlek

**Vad är den maximala lagrings storleken för SQL-hanterad instans?**

Lagrings storleken för SQL-hanterad instans beror på den valda tjänst nivån (Generell användning eller Affärskritisk). Information om lagrings begränsningar för dessa tjänst nivåer finns i [Egenskaper för tjänst nivå](../database/service-tiers-general-purpose-business-critical.md).

  
## <a name="networking-requirements"></a>Nätverks krav 

**Vilka är de aktuella inkommande/utgående NSG-begränsningarna i under nätet för hanterade instanser?**

De nödvändiga reglerna för NSG och UDR dokumenteras [här](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)och anges automatiskt av tjänsten.
Tänk på att dessa regler är bara de som vi behöver för att underhålla tjänsten. Om du vill ansluta till en hanterad instans och använda olika funktioner måste du ange ytterligare, funktions regler som du behöver underhålla.

**Hur kan jag ange regler för inkommande NSG på hanterings portar?**

SQL-hanterad instans ansvarar för att ange regler för hanterings portar. Detta uppnås med hjälp av funktioner som heter [service-Aided under näts konfiguration](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Detta är för att säkerställa oavbrutet flöde av hanterings trafik för att uppfylla ett service avtal.

**Kan jag hämta de käll-IP-intervall som används för inkommande hanterings trafik?**

Ja. Du kan analysera trafiken som kommer via nätverks säkerhets gruppen genom att [konfigurera Network Watcher flödes loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

**Kan jag ange NSG för att kontrol lera åtkomsten till data slut punkten (port 1433)?**

Ja. När en hanterad instans har tillhandahållits kan du ange NSG som styr inkommande åtkomst till port 1433. Vi rekommenderar att du begränsar dess IP-intervall så mycket som möjligt.

**Kan jag ange NVA eller lokal brand vägg för att filtrera utgående hanterings trafik baserat på FQDN?**

Nej. Detta stöds inte av flera skäl:
-   Routnings trafik som representerar svar på inkommande hanterings begär Anden blir asymmetrisk och fungerar inte.
-   Routning av trafik som går till lagring påverkas av begränsningar och svars tider för data flödet så på det sättet att vi inte kan tillhandahålla förväntade tjänste kvalitet och tillgänglighet.
-   De här konfigurationerna är beroende av erfarenheten fel känsliga och stöds inte.

**Kan jag ange NVA eller brand vägg för utgående trafik som inte hanterar hanteringen?**

Ja. Det enklaste sättet att åstadkomma detta är att lägga till 0/0-regeln i en UDR som är kopplad till under nätet för hanterade instanser för att dirigera trafik via NVA.
 
**Hur många IP-adresser behöver jag för en hanterad instans?**

Under nätet måste ha tillräckligt många tillgängliga [IP-adresser](connectivity-architecture-overview.md#network-requirements). Information om hur du fastställer VNet-undernätets storlek för SQL-hanterad instans finns i [bestämma storlek och intervall för en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**Vad händer om det inte finns tillräckligt med IP-adresser för att utföra instans uppdaterings åtgärden?**

Om det inte finns tillräckligt med [IP-adresser](connectivity-architecture-overview.md#network-requirements) i under nätet där din hanterade instans är etablerad måste du skapa ett nytt undernät och en ny hanterad instans i det. Vi föreslår också att det nya under nätet skapas med fler IP-adresser allokerade så att framtida uppdaterings åtgärder undviker liknande situationer. När den nya instansen har allokerats kan du manuellt säkerhetskopiera och återställa data mellan de gamla och nya instanserna eller utföra återställning mellan olika [tidpunkter](point-in-time-restore.md?tabs=azure-powershell).

**Behöver jag ett tomt undernät för att skapa en hanterad instans?**

Nej. Du kan antingen använda ett tomt undernät eller ett undernät som redan innehåller hanterade instanser. 

**Kan jag ändra under nätets adress intervall?**

Inte om det finns hanterade instanser i. Det här är en begränsning i Azure nätverks infrastrukturen. Du får bara [lägga till ytterligare adress utrymme i ett tomt undernät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**Kan jag flytta min hanterade instans till ett annat undernät?**

Nej. Det här är en aktuell design begränsning för hanterade instanser. Du kan dock etablera en ny instans i ett annat undernät och manuellt säkerhetskopiera och återställa data mellan den gamla och den nya instansen eller utföra återställning mellan olika [tidpunkter](point-in-time-restore.md?tabs=azure-powershell).

**Behöver jag ett tomt virtuellt nätverk för att skapa en hanterad instans?**

Det här krävs inte. Du kan antingen [skapa ett virtuellt nätverk för Azure SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) eller [Konfigurera ett befintligt virtuellt nätverk för Azure SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Kan jag placera en hanterad instans med andra tjänster i ett undernät?**

Nej. För närvarande har vi inte stöd för att placera en hanterad instans i ett undernät som redan innehåller andra resurs typer.


## <a name="mitigate-data-exfiltration-risks"></a>Minimera data exfiltrering-risker  

**Hur kan jag minimera data exfiltrering riskerna?**

För att minimera eventuella data exfiltrerings risker rekommenderar vi att du använder en uppsättning säkerhets inställningar och kontroller:

- Aktivera [Transparent datakryptering (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) på alla databaser.
- Inaktivera CLR (Common Language Runtime). Detta rekommenderas även lokalt.
- Använd endast Azure Active Directory (Azure AD)-autentisering.
- Få åtkomst till instansen med ett konto med låg privilegie rad DBA.
- Konfigurera åtkomst till JIT-bygel för kontot sysadmin.
- Aktivera [SQL-granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)och integrera den med aviserings metoder.
- Aktivera [hot identifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) från paketet [Advanced Data Security (Ads)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Användnings fall med kostnads besparingar

**Var kan jag hitta användnings fall och resulterande kostnads besparingar med SQL-hanterad instans?**

Fallstudier om SQL-hanterade instanser:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

För att få en bättre förståelse för de fördelar, kostnader och risker som är kopplade till distributionen av den hanterade Azure SQL-instansen finns det också en Forrester-undersökning: [den totala ekonomiska effekten av Microsoft Azure SQL Database hanterade instansen](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns"></a>DNS

**Kan jag konfigurera en anpassad DNS för SQL-hanterad instans?**

Ja. Se [Konfigurera en anpassad DNS för Azure SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

**Kan jag göra DNS-uppdatering?**

För närvarande tillhandahåller vi inte någon funktion för att uppdatera DNS-serverkonfigurationen för SQL-hanterad instans.

DNS-konfigurationen uppdateras slutligen:

- När DHCP-lånet upphör att gälla.
- Vid plattforms uppgradering.

Som en lösning kan du nedgradera SQL-hanterad instans till 4 virtuella kärnor och uppgradera den igen efteråt. Detta har en sido effekt på att uppdatera DNS-konfigurationen.


## <a name="ip-address"></a>IP-adress

**Kan jag ansluta till SQL-hanterad instans med en IP-adress?**

Det finns inte stöd för att ansluta till SQL-hanterad instans med en IP-adress. Det SQL-hanterade instans värd namnet mappar till en belastningsutjämnare framför det virtuella SQL-hanterade instans klustret. Eftersom ett virtuellt kluster kan vara värd för flera hanterade instanser, kan inte anslutningar dirigeras till rätt hanterad instans utan att namnet uttryckligen anges.

Mer information om arkitektur för virtuella kluster i SQL-hanterad instans finns i [arkitektur för virtuella kluster anslutningar](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Kan SQL-hanterad instans ha en statisk IP-adress?**

I sällsynta men nödvändiga situationer kan vi behöva utföra en online-migrering av SQL-hanterad instans till ett nytt virtuellt kluster. Vid behov är migreringen på grund av ändringar i vår teknik stack som syftar till att förbättra säkerheten och tillförlitligheten för tjänsten. Om du migrerar till ett nytt virtuellt kluster ändras IP-adressen som är mappad till värd namnet för SQL-hanterad instans. SQL Managed instance service har inte stöd för statisk IP-adress och förbehåller sig rätten att ändra den utan att Observera att den är en del av vanliga underhålls cykler.

Därför förhindrar vi starkt att du förlitar dig på oföränderlighets i IP-adressen eftersom det kan orsaka onödigt drift stopp.

## <a name="change-time-zone"></a>Ändra tidszon

**Kan jag ändra tids zonen för en befintlig hanterad instans?**

Tids zons konfigurationen kan ställas in när en hanterad instans skapas för första gången. Det finns inte stöd för att ändra tids zonen för en befintlig hanterad instans. Mer information finns i [begränsningar för tids zoner](timezones-overview.md#limitations).

I lösningarna ingår att skapa en ny hanterad instans med rätt tidszon och sedan antingen utföra en manuell säkerhets kopiering och återställning, eller vad vi rekommenderar, utföra en [kors inaktive rad återställning av en viss tidpunkt](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="security-and-database-encryption"></a>Säkerhet och databas kryptering

**Är sysadmin-serverrollen tillgänglig för SQL-hanterad instans?**

Ja, kunder kan skapa inloggningar som är medlemmar i sysadmin-rollen.  Kunder som antar behörigheten sysadmin förutsätter också ansvar för drift av instansen, vilket kan påverka SLA-åtagandet negativt. Information om hur du lägger till inloggning till sysadmin-serverrollen finns i [Azure AD-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication).

**Stöds transparent datakryptering för SQL-hanterad instans?**

Ja, transparent datakryptering stöds för SQL-hanterad instans. Mer information finns i [Transparent datakryptering för SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal).

**Kan jag utnyttja modellen "ta med din egen nyckel" för TDE?**

Ja, Azure Key Vault för BYOK-scenario är tillgängligt för Azure SQL-hanterad instans. Mer information finns i [Transparent datakryptering med kundhanterad nyckel](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Kan jag migrera en krypterad SQL Server databas?**

Ja, det kan du. Om du vill migrera en krypterad SQL Server databas måste du exportera och importera dina befintliga certifikat till en hanterad instans och sedan ta en fullständig säkerhets kopia av databasen och återställa den i en hanterad instans. 

Du kan också använda [Azure Database migration service](https://azure.microsoft.com/services/database-migration/) för att migrera TDE-krypterade databaser.

**Hur konfigurerar jag TDE skydds rotation för SQL-hanterad instans?**

Du kan rotera TDE-skydd för en hanterad instans med hjälp av Azure Cloud Shell. Instruktioner finns i [Transparent datakryptering i SQL-hanterad instans med hjälp av din egen nyckel från Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Kan jag återställa min krypterade databas till SQL-hanterad instans?**

Ja, du behöver inte dekryptera databasen för att återställa den till SQL-hanterad instans. Du måste ange ett certifikat/en nyckel som används som krypterings nyckel skydd på käll systemet till SQL-hanterad instans för att kunna läsa data från den krypterade säkerhets kopierings filen. Det finns två möjliga sätt att göra det:

- *Ladda upp certifikat – skydd mot SQL-hanterad instans*. Det kan bara göras med PowerShell. I [exempel skriptet](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) beskrivs hela processen.
- *Överför asymmetrisk nyckel-skydd till Azure Key Vault-och Point SQL-hanterad instans till den*. Den här metoden liknar BYOK-TDE, som också använder Key Vault-integrering för att lagra krypterings nyckeln. Om du inte vill använda nyckeln som krypterings nyckel skydd och bara vill göra nyckeln tillgänglig för SQL-hanterad instans för att återställa krypterade databaser, följ instruktionerna för att [Konfigurera BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)och markera inte kryss rutan **gör den markerade nyckeln till standard TDE-skyddet**.

När du har gjort krypterings skyddet tillgängligt för SQL-hanterad instans kan du fortsätta med standard proceduren för databas återställning.

## <a name="purchasing-models-and-benefits"></a>Inköps modeller och förmåner

**Vilka inköps modeller är tillgängliga för SQL-hanterad instans?**

SQL-hanterad instans erbjuder [vCore-baserad inköps modell](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Vilka kostnads förmåner är tillgängliga för SQL-hanterad instans?**

Du kan spara kostnader med Azure SQL-förmåner på följande sätt:
-   Maximera befintliga investeringar i lokala licenser och Spara upp till 55 procent med [Azure Hybrid-förmån](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell). 
-   Genomför en reservation för beräknings resurser och Spara upp till 33 procent med [reserverad instans förmån](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). Kombinera detta med Azure Hybrid-förmånen och Spara upp till 82 procent. 
-   Spara upp till 55 procent jämfört med priser för [Azure dev/test-](https://azure.microsoft.com/pricing/dev-test/) priser som erbjuder rabatterade priser för dina kontinuerliga utvecklings-och testnings arbets belastningar.

**Vem är berättigad till reserverad instans?**

För att vara kvalificerad för reserverad instans förmån måste prenumerations typen vara ett Enterprise Agreement (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Mer information om reservationer finns i [reserverad instans förmån](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). 

**Är det möjligt att avbryta, byta ut eller återbetala reservationer?**

Du kan avbryta, byta ut eller återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Fakturering för hanterad instans och lagring av säkerhets kopior

**Vad är pris alternativen för SQL-hanterad instans?**

Se [prissättnings sida](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)för att utforska pris alternativ för hanterade instanser.

**Hur kan jag spåra fakturerings kostnader för min hanterade instans?**

Du kan göra det med hjälp av [Azure Cost Management-lösningen](https://docs.microsoft.com/azure/cost-management-billing/). Gå till **prenumerationer** i [Azure Portal](https://portal.azure.com) och välj **kostnads analys**. 

Använd alternativet **ackumulerade kostnader** och filtrera sedan efter **resurs typ** som `microsoft.sql/managedinstances` .

**Hur mycket kostnader för automatisk säkerhets kopiering?**

Du får lika stor mängd ledigt lagrings utrymme som det reserverade lagrings utrymmet för lagrings utrymmet, oavsett vilken kvarhållning av säkerhets kopior som angetts. Om lagrings förbrukningen för säkerhets kopian finns inom det allokerade lagrings utrymmet för ledig säkerhets kopiering får du ingen extra kostnad för automatiska säkerhets kopieringar på den hanterade instansen. därför är det kostnads fritt. Om du överskrider användningen av lagrings utrymme för säkerhets kopior över det lediga utrymmet kommer kostnaderna för cirka $0,20-$0,24 per GB/månad i USA-regioner, eller så kan du se sidan med priser för information om din region. Mer information finns i avsnittet om [förklarad lagrings förbrukning](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Hur kan jag övervaka fakturerings kostnader för lagrings förbrukningen för säkerhets kopiering?**

Du kan övervaka kostnaden för lagring av säkerhets kopior via Azure Portal. Mer information finns i [övervaka kostnader för automatisk säkerhets kopiering](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs). 

**Hur kan jag optimera mina kostnader för säkerhets kopiering av lagring på den hanterade instansen?**

Information om hur du optimerar kostnaderna för lagring av säkerhets kopior finns i [fin justering av säkerhets kopiering på SQL Managed instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="password-policy"></a>Lösen ords princip 

**Vilka lösen ords principer används för SQL-inloggningar med SQL-hanterade instanser?**

SQL-hanterad instans lösen ords princip för SQL-inloggningar ärver Azures plattforms principer som tillämpas på virtuella datorer som utgör den hanterade instansen. För tillfället går det inte att ändra någon av dessa inställningar eftersom inställningarna definieras av Azure och ärvs av en hanterad instans.

 > [!IMPORTANT]
 > Azure-plattformen kan ändra princip kraven utan att meddela tjänster som förlitar sig på dessa principer.

**Vad är nuvarande Azure Platform-principer?**

Varje inloggning måste ange sitt lösen ord vid inloggningen och ändra dess lösen ord när den når maximal ålder.

| **Princip** | **Säkerhetsinställning** |
| --- | --- |
| Högsta ålder för lösen ord | 42 dagar |
| Lägsta ålder för lösen ord | 1 dag |
| Minsta längd på lösenord | 10 tecken |
| Lösen ordet måste uppfylla komplexitets kraven | Enabled |

**Är det möjligt att inaktivera lösen ords komplexitet och förfallo datum i SQL-hanterad instans på inloggnings nivå?**

Ja, det är möjligt att kontrol lera CHECK_POLICY och CHECK_EXPIRATION fält på inloggnings nivå. Du kan kontrol lera aktuella inställningar genom att köra följande T-SQL-kommando:

```sql
SELECT *
FROM sys.sql_logins
```

Därefter kan du ändra angivna inloggnings inställningar genom att köra:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(Ersätt "test" med önskat inloggnings namn och justera princip-och förfallo värden)

## <a name="azure-feedback-and-support"></a>Feedback och support för Azure

**Var kan jag lämna mina idéer för förbättringar av SQL-hanterade instanser?**

Du kan rösta på en ny hanterad instans funktion eller införa en ny förbättrings idé om röstning på [SQL Managed instance feedback forum](https://feedback.azure.com/forums/915676-sql-managed-instance). På så sätt kan du bidra till produkt utvecklingen och hjälpa oss att prioritera våra potentiella förbättringar.

**Hur gör jag för att skapa en support förfrågan för Azure?**

Information om hur du skapar en support förfrågan för Azure finns i [så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

