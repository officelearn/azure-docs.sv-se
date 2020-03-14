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
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 031423d72aecacc1feed692aaa32bcd1ff7efbab
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282710"
---
# <a name="threat-protection-in-azure-security-center"></a>Skydd mot hot i Azure Security Center

Den här sidan beskriver kortfattat källorna till de säkerhets aviseringar som visas av Azure Security Center för användare på standard pris nivån.

När Security Center identifierar ett hot i något av de områden i miljön som anges nedan, genererar en avisering. De här aviseringarna beskriver information om de resurser som påverkas, föreslagna åtgärds steg och i vissa fall ett alternativ för att utlösa en logisk app som svar.

Om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhets produkt, kan du exportera den. Följ anvisningarna i [Exportera aviseringar och rekommendationer](continuous-export.md)för att exportera aviseringar till Azure Sentinel (eller en tredjeparts Siem) eller andra externa platser. 




## Skydd mot hot för Windows-datorer<a name="windows-machines"></a>

Azure Security Center integreras med Azure-tjänster för att övervaka och skydda dina Windows-baserade datorer. Security Center visar aviseringar och reparations förslag från alla dessa tjänster i ett lättanvänt format.

* **Microsoft Defender ATP** <a name="windows-atp"></a> – Security Center utökar sina moln skydds plattformar genom att integrera med Microsoft Defender Advanced Threat Protection (ATP). Tillsammans tillhandahåller de omfattande funktioner för slut punkts identifiering och-svar (EDR).

    > [!IMPORTANT]
    > Microsoft Defender ATP-sensorn aktive ras automatiskt på Windows-servrar som använder Security Center.

    När Microsoft Defender ATP identifierar ett hot utlöses en avisering. Aviseringen visas på instrument panelen för Security Center. Från instrument panelen kan du pivotera till Microsoft Defender ATP-konsolen och utföra en detaljerad undersökning för att få fram omfattningen av angreppet. För ytterligare information om Microsoft Defender ATP, se [onboard-servrar till Microsoft Defender ATP-tjänsten](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analys** <a name="windows-dump"></a> av krasch dum par – när program vara kraschar fångar en kraschdump en del av minnet vid tidpunkten för kraschen.

    En krasch kan ha orsakats av skadlig kod eller innehålla skadlig kod. För att undvika att de upptäcks av säkerhets produkter använder olika former av skadlig kod en fil lös attack, vilket förhindrar skrivning till disk eller kryptering av program varu komponenter som skrivs till disk. Den här typen av attack är svår att identifiera med hjälp av traditionella diskbaserade metoder.

    Genom att använda minnes analyser kan du dock identifiera den här typen av attack. Genom att analysera minnet i krasch dumpningen kan Security Center identifiera de tekniker som angreppet använder. Till exempel kan angreppet försöka utnyttja sårbarheter i program varan, komma åt konfidentiella data och ligger gömda kvar på en komprometterad dator. Security Center fungerar detta med minimal prestanda påverkan på värdar.

    Mer information om analys aviseringar för krasch dumpning finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-windows).

* **Filbaserad attack identifiering** <a name="windows-fileless"></a> – filåterställda attacker riktade mot slut punkterna är vanliga. För att undvika identifiering kan fil lösa angrepp injicera skadliga nytto laster i minnet. Angripares nytto laster finns kvar i minnet för komprometterade processer och utför en mängd olika skadliga aktiviteter.

    Med fil lösa angrepp kan automatiserade minnes kriminal tekniska-tekniker identifiera fillösa verktyg för attacker, tekniker och beteenden. Den här lösningen genomsöker regelbundet datorn vid körning och extraherar insikter direkt från minnet för säkerhets kritiska processer.

    Det hittar bevis för utnyttjande, kod inmatning och körning av skadliga nytto laster. Vid fil lös angrepp genereras detaljerade säkerhets aviseringar för att påskynda aviseringen prioritering, korrelation och underordnade svars tider. Den här metoden kompletterar event-baserade EDR-lösningar och ger större identifierings täckning.

    Mer information om aviseringar om filbaserad attack identifiering finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-windows).

> [!TIP]
> Du kan simulera Windows-aviseringar genom att hämta [Azure Security Center Spelbok: säkerhets aviseringar](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## Skydd mot hot för Linux-datorer<a name="linux-machines"></a>

Security Center samlar in gransknings poster från Linux-datorer med hjälp av **granskning**, ett av de vanligaste Linux-gransknings ramverken. granskade liv i Mainline-kärnan. 

* <a name="linux-auditd"></a> **Integration av Linux-granskade varningar och Microsoft Monitoring Agent (MMA)** – det granskade systemet består av ett under system i kernel-nivå, som ansvarar för att övervaka system anrop. Den filtrerar dem efter en angiven regel uppsättning och skriver meddelanden för dem till en socket. Security Center integrerar funktioner från det granskade paketet i Microsoft Monitoring Agent (MMA). Den här integrationen aktiverar insamling av granskade händelser i alla Linux-distributioner som stöds, utan krav.  

    granskade poster samlas in, berikas och sammanställs i händelser med hjälp av Linux MMA-agenten. Security Center lägger kontinuerligt till nya analyser som använder Linux-signaler för att identifiera skadliga beteenden i molnet och lokala Linux-datorer. I likhet med Windows-funktioner, är dessa analys omfång över misstänkta processer, misstänkta-inloggnings försök, inläsning av kernel-modul och andra aktiviteter. Dessa aktiviteter kan tyda på att en dator är utsatt för en attack eller har brutits.  

    En lista över Linux-aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-linux).

> [!TIP]
> Du kan simulera Linux-aviseringar genom att ladda ned [Azure Security Center Spelbok: Linux-identifieringar](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## Skydd mot hot för Azure App Service<a name="app-services"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

Security Center använder molnets skala för att identifiera angrepps mål program som körs över App Service. Angripare avsöker webb program för att hitta och utnyttja svagheter. Innan de dirigeras till vissa miljöer går förfrågningar till program som körs i Azure genom flera gatewayer, där de inspekteras och loggas. Dessa data används sedan för att identifiera sårbarheter och angripare och för att lära dig nya mönster som ska användas senare.

Genom att använda den synlighet som Azure har som moln leverantör analyserar Security Center App Service interna loggar för att identifiera angrepps metodik på flera mål. Metodik omfattar till exempel omfattande genomsökning och distribuerade attacker. Den här typen av angrepp kommer vanligt vis från en liten delmängd av IP-adresser och visar mönster för crawlning till liknande slut punkter på flera värdar. Attackerna söker efter en sårbar sida eller ett plugin-program och kan inte identifieras från en enda värds synvinkel.

Om du kör en Windows-baserad App Service plan har Security Center också åtkomst till de underliggande sand lådorna och de underliggande virtuella datorerna. Tillsammans med de loggdata som anges ovan kan infrastrukturen berätta för historien, från en ny attack som cirkulerar på vilda sätt att kompromissa med kund maskiner. Det innebär att även om Security Center distribueras efter att en webbapp har utnyttjats, kan det vara möjligt att identifiera pågående attacker.

En lista över Azure App Service aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureappserv).

Mer information om App Service-planer finns i [App Service planer](https://azure.microsoft.com/pricing/details/app-service/plans/).





## Skydd mot hot för Azure-behållare<a name="azure-containers"></a>

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







## Hot skydd för Azure nätverks lager<a name="network-layer"></a>

Security Center Network Layer Analytics baseras på [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är paket rubriker som samlas in av Azure Core-routrar. Baserat på den här datafeeden använder Security Center maskin inlärnings modeller för att identifiera och flagga skadliga trafik aktiviteter. Security Center använder också Microsoft Threat Intelligence-databasen för att utöka IP-adresser.

Vissa nätverkskonfigurationer kan begränsa Security Center från att generera aviseringar om misstänkt nätverks aktivitet. För att Security Center generera nätverks aviseringar, se till att:

- Den virtuella datorn har en offentlig IP-adress (eller finns på en belastningsutjämnare med en offentlig IP-adress).

- Den virtuella datorns utgående trafik blockeras inte av en extern ID-lösning.

- Den virtuella datorn har tilldelats samma IP-adress för hela timmen då den misstänkta kommunikationen uppstod. Detta gäller även för virtuella datorer som skapats som en del av en hanterad tjänst (till exempel AKS, Databricks).

En lista över aviseringar för Azure nätverks lager finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azurenetlayer).

Mer information om hur Security Center kan använda nätverksrelaterade signaler för att använda hot skydd finns [i heuristisk DNS-identifieringar i Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).







## Skydd mot Azure Key Vault (för hands version)<a name="azure-keyvault"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. 

Azure Security Center innehåller Azure-inbyggt Avancerat skydd för Azure Key Vault, vilket ger ytterligare ett lager av säkerhets information. Security Center identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Det här skydds lagret gör att du kan åtgärda hot utan att vara säkerhets expert och utan att behöva hantera säkerhets övervaknings system från tredje part.  

När avvikande aktiviteter inträffar kan Security Center Visa aviseringar och skicka dem via e-post till prenumerations administratörer. Dessa aviseringar innehåller information om den misstänkta aktiviteten och rekommendationer om hur du undersöker och åtgärdar hot. 

En lista över Azure Key Vault aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azurekv).








## Skydd mot hot för SQL Database och SQL Data Warehouse<a name="data-sql"></a>

Avancerat skydd för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Du ser aviseringar när det finns misstänkta databas aktiviteter, potentiella sårbarheter eller SQL-injektering, samt avvikande databas åtkomst och fråga mönster.

Avancerat skydd för Azure SQL Database och SQL är en del av det enhetliga paketet [Advanced Data Security (Ads)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) Unified för avancerade SQL-säkerhetsfunktioner, som omfattar Azure SQL-databaser, Azure SQL Database hanterade instanser, Azure SQL Data Warehouse databaser och SQL-servrar på Azure Virtual Machines.

Mer information finns i:

* [Så här aktiverar du Avancerat skydd för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Så här aktiverar du Avancerat skydd för SQL-servrar på Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Listan med aviseringar om skydd mot hot för SQL Database och SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)









## Skydd mot hot för Azure Storage<a name="azure-storage"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

Avancerat skydd för lagring (som för närvarande endast är tillgängligt för Blob Storage) identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Det här skydds lagret gör att du kan åtgärda hot utan att du behöver vara säkerhets expert, och hjälper dig att hantera dina säkerhets övervaknings system.

Mer information finns i:

* [Så här aktiverar du Avancerat skydd för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Listan med aviseringar om skydd mot hot för Azure Storage](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> Du kan simulera Azure Storage aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).






## Skydd mot hot för Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB-aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton.

Mer information finns i:

* [Avancerat skydd för Azure Cosmos DB (för hands version)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Listan med aviseringar för skydd mot Azure Cosmos DB (för hands version)](alerts-reference.md#alerts-azurecosmos)







## Hot skydd för Azure Management Layer (Azure Resource Manager) (för hands version)<a name ="management-layer"></a>

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



## Säkerhets aviseringar från andra Microsoft-tjänster<a name="alerts-other"></a>

### Skydd mot hot för Azure-WAF<a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. Application Gateway-WAF baseras på kärn regel uppsättningen 3,0 eller 2.2.9 från det öppna säkerhets projektet för webb program. WAF uppdateras automatiskt för att skydda mot nya sårbarheter. 

Om du har en licens för Azure WAF strömmas dina WAF-aviseringar till Security Center utan ytterligare konfiguration som behövs. Mer information om de aviseringar som genererats av WAF finns i [regel grupper och regler för webb programs brand vägg](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### Skydd mot hot för Azure DDoS Protection<a name="azure-ddos"></a>

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