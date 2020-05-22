---
title: Skydd mot hot i Azure Security Center
description: I det här avsnittet beskrivs de resurser som skyddas av Azure Security Centers hot skydds funktioner
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 850b06153a25020f36a4c7df1863e5a576495f3b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744165"
---
# <a name="threat-protection-in-azure-security-center"></a>Skydd mot hot i Azure Security Center

När Security Center identifierar ett hot i alla områden i miljön genererar den en avisering. De här aviseringarna beskriver information om de resurser som påverkas, föreslagna åtgärds steg och i vissa fall ett alternativ för att utlösa en logisk app som svar.

Azure Security Center s hot skydd ger omfattande försvar för din miljö:

* **Skydd mot hot för Azure Compute-resurser**: Windows-datorer, Linux-datorer, Azure App service och Azure-behållare

* **Skydd mot hot för Azure-data resurser**: SQL Database och SQL Data Warehouse, Azure Storage och Azure Cosmos DB

* **Hot skydd för Azure Service**Layers: Azure-nätverksnivå, Azure Management layer (Azure Resource Manager) (för hands version) och Azure Key Vault (förhands granskning)

Om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md). 

> [!NOTE]
> Aviseringar från olika källor kan ta olika tids mängder att visas. Till exempel kan aviseringar som kräver analys av nätverks trafiken ta längre tid än aviseringar som är relaterade till misstänkta processer som körs på virtuella datorer.

> [!TIP]
> Om du vill aktivera Security Center hot skydds funktioner måste du använda standard pris nivån för prenumerationen som innehåller de aktuella arbets belastningarna.
>
> Du kan aktivera skydd av hot för **Azure Storage konton** på prenumerations nivå eller resurs nivå.
> Du kan aktivera skydd mot hot för **Azure SQL Database SQL-servrar** på prenumerations nivå eller resurs nivå.
> Du kan aktivera skydd mot hot för **Azure Database for MariaDB/MySQL/postgresql** endast på resurs nivå.



## <a name="threat-protection-for-windows-machines"></a>Skydd mot hot för Windows-datorer<a name="windows-machines"></a>

Azure Security Center integreras med Azure-tjänster för att övervaka och skydda dina Windows-baserade datorer. Security Center visar aviseringar och reparations förslag från alla dessa tjänster i ett lättanvänt format.

* **Microsoft Defender Avancerat skydd (ATP)** <a name="windows-atp"></a> -Security Center utökar plattformarna för skydd av arbets belastnings skydd genom att integrera med Microsoft Defender Advanced Threat Protection (ATP). Tillsammans tillhandahåller de omfattande funktioner för slut punkts identifiering och-svar (EDR).

    > [!IMPORTANT]
    > Microsoft Defender ATP-sensorn aktive ras automatiskt på Windows-servrar som använder Security Center.

    När Microsoft Defender ATP identifierar ett hot utlöses en avisering. Aviseringen visas på instrument panelen för Security Center. Från instrument panelen kan du pivotera till Microsoft Defender ATP-konsolen och utföra en detaljerad undersökning för att få fram omfattningen av angreppet. För ytterligare information om Microsoft Defender ATP, se [onboard-servrar till Microsoft Defender ATP-tjänsten](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* Avkänning av filbaserad **attack** <a name="windows-fileless"></a> – Filspecifika attacker riktade mot slut punkterna är vanliga. För att undvika identifiering kan fil lösa angrepp injicera skadliga nytto laster i minnet. Angripares nytto laster finns kvar i minnet för komprometterade processer och utför en mängd olika skadliga aktiviteter.

    Med fil lösa angrepp kan automatiserade minnes kriminal tekniska-tekniker identifiera fillösa verktyg för attacker, tekniker och beteenden. Den här lösningen genomsöker regelbundet datorn vid körning och extraherar insikter direkt från minnet för säkerhets kritiska processer.

    Det hittar bevis för utnyttjande, kod inmatning och körning av skadliga nytto laster. Vid fil lös angrepp genereras detaljerade säkerhets aviseringar för att påskynda aviseringen prioritering, korrelation och underordnade svars tider. Den här metoden kompletterar event-baserade EDR-lösningar och ger större identifierings täckning.

    Mer information om aviseringar om filbaserad attack identifiering finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-windows).

> [!TIP]
> Du kan simulera Windows-aviseringar genom att hämta [Azure Security Center Spelbok: säkerhets aviseringar](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Skydd mot hot för Linux-datorer<a name="linux-machines"></a>

Security Center samlar in gransknings poster från Linux-datorer med hjälp av **granskning**, ett av de vanligaste Linux-gransknings ramverken. granskade liv i Mainline-kärnan. 

* **Granskade Linux-aviseringar och Log Analytics agent-integrering** <a name="linux-auditd"></a> – Det granskade systemet består av ett under system i kernel-nivå, som ansvarar för att övervaka system anrop. Den filtrerar dem efter en angiven regel uppsättning och skriver meddelanden för dem till en socket. Security Center integrerar funktioner från det granskade paketet i Log Analytics agenten. Den här integrationen aktiverar insamling av granskade händelser i alla Linux-distributioner som stöds, utan krav.

    granskade poster samlas in, berikas och aggregeras i händelser med hjälp av Log Analytics agent för Linux-agenten. Security Center lägger kontinuerligt till nya analyser som använder Linux-signaler för att identifiera skadliga beteenden i molnet och lokala Linux-datorer. På samma sätt som Windows-funktioner, är dessa analys omfång över misstänkta processer, misstänkta inloggnings försök, inläsning av kernel-modul och andra aktiviteter. Dessa aktiviteter kan tyda på att en dator är utsatt för en attack eller har brutits.  

    En lista över Linux-aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-linux).

> [!TIP]
> Du kan simulera Linux-aviseringar genom att ladda ned [Azure Security Center Spelbok: Linux-identifieringar](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Skydd mot hot för Azure App Service<a name="app-services"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

Security Center använder molnets skala för att identifiera angrepps mål program som körs över App Service. Angripare avsöker webb program för att hitta och utnyttja svagheter. Innan de dirigeras till vissa miljöer går förfrågningar till program som körs i Azure genom flera gatewayer, där de inspekteras och loggas. Dessa data används sedan för att identifiera sårbarheter och angripare och för att lära dig nya mönster som ska användas senare.

Genom att använda den synlighet som Azure har som moln leverantör analyserar Security Center App Service interna loggar för att identifiera angrepps metodik på flera mål. Metodik omfattar till exempel omfattande genomsökning och distribuerade attacker. Den här typen av angrepp kommer vanligt vis från en liten delmängd av IP-adresser och visar mönster för crawlning till liknande slut punkter på flera värdar. Attackerna söker efter en sårbar sida eller ett plugin-program och kan inte identifieras från en enda värds synvinkel.

Om du kör en Windows-baserad App Service plan har Security Center också åtkomst till de underliggande sand lådorna och de underliggande virtuella datorerna. Tillsammans med de loggdata som anges ovan kan infrastrukturen berätta för historien, från en ny attack som cirkulerar på vilda sätt att kompromissa med kund maskiner. Det innebär att även om Security Center distribueras efter att en webbapp har utnyttjats, kan det vara möjligt att identifiera pågående attacker.

En lista över Azure App Service aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureappserv).

Mer information om App Service-planer finns i [App Service planer](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Skydd mot hot för Azure-behållare<a name="azure-containers"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

Security Center tillhandahåller hot skydd i real tid för dina behållares miljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Security Center ger hot skydd på olika nivåer: 

* **Värdnivå** – Security Centers agent (tillgänglig på standard-nivån finns det information om [priser](security-center-pricing.md) ) övervakar Linux för misstänkta aktiviteter. Agenten utlöser aviseringar för misstänkta aktiviteter som härstammar från noden eller en behållare som körs på den. Exempel på sådana aktiviteter är identifiering av webb gränssnitt och anslutning med kända misstänkta IP-adresser.

    För en djupare insikt i säkerheten i din behållare miljö övervakar agenten molnbaserad analys. Den utlöser aviseringar om händelser som till exempel skapande av privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    >[!IMPORTANT]
    > Om du väljer att inte installera agenterna på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

    En lista över aviseringar om värd nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-containerhost).


* På **AKS-klustrets nivå**baseras hot skyddet på analys av Kubernetes gransknings loggar. Om du vill aktivera övervakning utan **agent** lägger du till alternativet Kubernetes i din prenumeration på sidan **pris & inställningar** (se [prissättning](security-center-pricing.md)). Om du vill generera aviseringar på den här nivån övervakar Security Center dina AKS-hanterade tjänster med hjälp av de loggar som hämtats av AKS. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar.

    >[!NOTE]
    > Security Center genererar säkerhets aviseringar för Azure Kubernetes service-åtgärder och distributioner som inträffar när alternativet Kubernetes har Aktiver ATS i prenumerations inställningarna. 

    En lista över AKS-aviseringar på kluster nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-akscluster).

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.

> [!TIP]
> Du kan simulera behållar aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Skydd mot hot för SQL Database och SQL Data Warehouse<a name="data-sql"></a>

Avancerat skydd för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Du ser aviseringar när det finns misstänkta databas aktiviteter, potentiella sårbarheter eller SQL-injektering, samt avvikande databas åtkomst och fråga mönster.

Avancerat skydd för Azure SQL Database och SQL är en del av det enhetliga paketet [Advanced Data Security (Ads)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) Unified för avancerade SQL-säkerhetsfunktioner, som omfattar Azure SQL-databaser, Azure SQL Database hanterade instanser, Azure SQL Data Warehouse databaser och SQL-servrar på Azure Virtual Machines.

Mer information finns i:

* [Så här aktiverar du Avancerat skydd för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Så här aktiverar du Avancerat skydd för SQL-servrar på Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Listan med aviseringar om skydd mot hot för SQL Database och SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Skydd mot hot för Azure Storage<a name="azure-storage"></a>

Avancerat skydd för lagring identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Det här skydds lagret gör att du kan åtgärda hot utan att du behöver vara säkerhets expert, och hjälper dig att hantera dina säkerhets övervaknings system.

Avancerat skydd för Azure Storage är för närvarande endast tillgängligt för [Blob Storage](https://azure.microsoft.com/services/storage/blobs/). 

Den här tjänsten är tillgänglig i alla offentliga moln och i amerikanska myndigheter, men inga andra suveräna eller Azure Government moln regioner.

För pris information, inklusive en kostnads fri 30-dagars utvärderings version, se [sidan Azure Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

Mer information finns i:

* [Så här aktiverar du Avancerat skydd för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Listan med aviseringar om skydd mot hot för Azure Storage](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> Du kan simulera Azure Storage aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Skydd mot hot för Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB-aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton.

Mer information finns i:

* [Avancerat skydd för Azure Cosmos DB (för hands version)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Listan med aviseringar för skydd mot Azure Cosmos DB (för hands version)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Hot skydd för Azure nätverks lager<a name="network-layer"></a>

Security Center Network Layer Analytics baseras på [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är paket rubriker som samlas in av Azure Core-routrar. Baserat på den här datafeeden använder Security Center maskin inlärnings modeller för att identifiera och flagga skadliga trafik aktiviteter. Security Center använder också Microsoft Threat Intelligence-databasen för att utöka IP-adresser.

Vissa nätverkskonfigurationer kan begränsa Security Center från att generera aviseringar om misstänkt nätverks aktivitet. För att Security Center generera nätverks aviseringar, se till att:

- Den virtuella datorn har en offentlig IP-adress (eller finns på en belastningsutjämnare med en offentlig IP-adress).

- Den virtuella datorns utgående trafik blockeras inte av en extern ID-lösning.

- Den virtuella datorn har tilldelats samma IP-adress för hela timmen då den misstänkta kommunikationen uppstod. Detta gäller även för virtuella datorer som skapats som en del av en hanterad tjänst (till exempel AKS, Databricks).

En lista över aviseringar för Azure nätverks lager finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azurenetlayer).

Mer information om hur Security Center kan använda nätverksrelaterade signaler för att använda hot skydd finns [i heuristisk DNS-identifieringar i Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Hot skydd för Azure Management Layer (Azure Resource Manager) (för hands version)<a name ="management-layer"></a>

Security Center skydds skikt baserat på Azure Resource Manager är för närvarande en för hands version.

Security Center erbjuder ett extra skydds lager genom att använda Azure Resource Manager händelser, som anses vara kontroll planet för Azure. Genom att analysera Azure Resource Manager poster Security Center identifiera ovanliga eller potentiellt skadliga åtgärder i Azure-prenumerationens miljö.

En lista över aviseringar om Azure Resource Manager (förhands granskning) finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Flera av föregående analyser drivs av Microsoft Cloud App Security. Om du vill dra nytta av dessa analyser måste du aktivera en Cloud App Security-licens. Om du har en Cloud App Security licens är de här aviseringarna aktiverade som standard. Så här inaktiverar du aviseringarna:
>
> 1. På bladet **Security Center** väljer du **säkerhets princip**. Välj **Redigera inställningar**för den prenumeration som du vill ändra.
> 2. Välj **hot identifiering**.
> 3. Under **Aktivera integreringar**, rensa **Tillåt Microsoft Cloud App Security åtkomst till mina data**och välj **Spara**.

>[!NOTE]
>Security Center lagrar säkerhetsrelaterad kund information i samma geo som dess resurs. Om Microsoft ännu inte har distribuerat Security Center i resursens geografiska område, lagrar data i USA. När Cloud App Security är aktive rad lagras informationen i enlighet med reglerna för geo-platsen för Cloud App Security. Mer information finns i [data lagring för icke-regionala tjänster](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Skydd mot Azure Key Vault (för hands version)<a name="azure-keyvault"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. 

Azure Security Center innehåller Azure-inbyggt Avancerat skydd för Azure Key Vault, vilket ger ytterligare ett lager av säkerhets information. Security Center identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Det här skydds lagret gör att du kan åtgärda hot utan att vara säkerhets expert och utan att behöva hantera säkerhets övervaknings system från tredje part.  

När avvikande aktiviteter inträffar kan Security Center Visa aviseringar och skicka dem via e-post till prenumerations administratörer. Dessa aviseringar innehåller information om den misstänkta aktiviteten och rekommendationer om hur du undersöker och åtgärdar hot. 

En lista över Azure Key Vault aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Skydd mot hot för andra Microsoft-tjänster<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Skydd mot hot för Azure-WAF<a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. Application Gateway-WAF baseras på kärn regel uppsättningen 3,0 eller 2.2.9 från det öppna säkerhets projektet för webb program. WAF uppdateras automatiskt för att skydda mot nya sårbarheter. 

Om du har en licens för Azure WAF strömmas dina WAF-aviseringar till Security Center utan ytterligare konfiguration som behövs. Mer information om de aviseringar som genererats av WAF finns i [regel grupper och regler för webb programs brand vägg](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Skydd mot hot för Azure DDoS Protection<a name="azure-ddos"></a>

DDoS-attacker (distributed denial of Service) är kända för att vara lätta att köra. De har blivit ett bra säkerhets problem, särskilt om du flyttar dina program till molnet. 

Ett DDoS-angrepp försöker att belasta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan rikta in sig på alla slut punkter som kan nås via Internet.

Du kan skydda dig mot DDoS-attacker genom att köpa en licens för Azure DDoS Protection och se till att du följer metod tips för program design. DDoS Protection tillhandahåller olika tjänst nivåer. Mer information finns i [Azure DDoS Protection översikt](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

En lista över Azure DDoS Protection aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Nästa steg
Mer information om säkerhets aviseringar från dessa hot Protection-funktioner finns i följande artiklar:

* [Referens tabell för alla Azure Security Center aviseringar](alerts-reference.md)
* [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exportera säkerhets aviseringar och rekommendationer (för hands version)](continuous-export.md)