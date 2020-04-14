---
title: Skydd mot hot i Azure Security Center
description: I det här avsnittet beskrivs de resurser som skyddas av Azure Security Centers hotskyddsfunktioner
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: fdf22e4d981549b876a14aed2b0a1d7e0c76e40e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263462"
---
# <a name="threat-protection-in-azure-security-center"></a>Skydd mot hot i Azure Security Center

När Security Center upptäcker ett hot i något område i din miljö genererar det en avisering. Dessa aviseringar beskriver information om de berörda resurserna, föreslagna reparationssteg och i vissa fall ett alternativ för att utlösa en logikapp som svar.

Azure Security Centers hotskydd ger omfattande försvar för din miljö:

* **Hotskydd för Azure-beräkningsresurser:** Windows-datorer, Linux-datorer, Azure App Service och Azure-behållare

* **Skydd mot Azure-data:** SQL Database och SQL Data Warehouse, Azure Storage och Azure Cosmos DB

* **Hotskydd för Azure-tjänstlager:** Azure-nätverkslager, Azure-hanteringslag (Azure Resource Manager) (förhandsversion) och Azure Key Vault (förhandsversion)

Oavsett om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhetsprodukt kan du exportera den. Om du vill exportera aviseringarna till Azure Sentinel (eller ett SIEM från tredje part) eller något annat externt verktyg följer du anvisningarna i [Exportera aviseringar till ett SIEM](continuous-export.md). 




## <a name="threat-protection-for-windows-machines"></a>Hotskydd för Windows-maskiner<a name="windows-machines"></a>

Azure Security Center integreras med Azure-tjänster för att övervaka och skydda dina Windows-baserade datorer. Security Center presenterar aviseringar och reparationsförslag från alla dessa tjänster i ett lättanvänt format.

* **Microsoft Defender ATP** <a name="windows-atp"></a> - Security Center utökar sina molnarbetsbelastningsskyddsplattformar genom att integrera med Microsoft Defender Advanced Threat Protection (ATP). Tillsammans ger de omfattande funktioner för identifiering och svar av slutpunkter (EDR).

    > [!IMPORTANT]
    > Microsoft Defender ATP-sensorn aktiveras automatiskt på Windows-servrar som använder Security Center.

    När Microsoft Defender ATP upptäcker ett hot utlöser det en avisering. Aviseringen visas på instrumentpanelen för Säkerhetscenter. Från instrumentpanelen kan du pivotera till Microsoft Defender ATP-konsolen och utföra en detaljerad undersökning för att avslöja omfattningen av attacken. Mer information om Microsoft Defender ATP finns [i Inbyggda servrar till Microsoft Defender ATP-tjänsten](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Crash dump analys** <a name="windows-dump"></a> - När programvara kraschar, en krasch dump fångar en del av minnet vid tidpunkten för kraschen.

    En krasch kan ha orsakats av skadlig kod eller innehålla skadlig kod. För att undvika att upptäckas av säkerhetsprodukter använder olika former av skadlig kod en fillös attack, som undviker att skriva till disk eller kryptera programvarukomponenter skrivna till disk. Denna typ av attack är svår att upptäcka med hjälp av traditionella diskbaserade metoder.

    Men genom att använda minnesanalys kan du upptäcka den här typen av attack. Genom att analysera minnet i kraschdumpen kan Security Center identifiera de tekniker som attacken använder. Attacken kan till exempel försöka utnyttja sårbarheter i programvaran, komma åt konfidentiella data och i smyg finnas kvar i en komprometterade dator. Security Center gör detta arbete med minimal prestandapåverkan till värdar.

    Mer information om kraschdumpanalysvarningarna finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-windows).

* **Fillös attackidentifiering** <a name="windows-fileless"></a> - Fillösa attacker som är inriktade på slutpunkter är vanliga. För att undvika upptäckt injicerar fillösa attacker skadliga nyttolaster i minnet. Angripare nyttolaster kvarstår i minnet av komprometterade processer, och utföra ett brett spektrum av skadliga aktiviteter.

    Med fillös attack upptäckt, automatiserade minne kriminaltekniska tekniker identifiera fillösa attack verktygslådor, tekniker och beteenden. Den här lösningen söker regelbundet igenom din dator vid körning och extraherar insikter direkt från minnet av säkerhetskritiska processer.

    Den finner bevis för utnyttjande, kod injektion, och utförande av skadliga nyttolaster. Fillös attackidentifiering genererar detaljerade säkerhetsaviseringar för att påskynda aviseringstriage, korrelation och nedströms svarstid. Den här metoden kompletterar händelsebaserade EDR-lösningar som ger större identifieringstäckning.

    Mer information om aviseringar om identifiering av fillösa angrepp finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-windows).

> [!TIP]
> Du kan simulera Windows-aviseringar genom att hämta [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Hotskydd för Linux-maskiner<a name="linux-machines"></a>

Security Center samlar in granskningsposter från Linux-datorer med hjälp av **granskade**, en av de vanligaste Linux-granskningsramverken. granskas i huvudlinjen kärnan. 

* **Linux-granskningsaviseringar och Log Analytics-agentintegration** <a name="linux-auditd"></a> - Det granskade systemet består av ett delsystem på kärnnivå som ansvarar för övervakning av systemanrop. De filtreras med en angiven regeluppsättning och meddelanden skrivs för dem till en socket. Security Center integrerar funktioner från det granskade paketet i Log Analytics-agenten. Den här integreringen möjliggör insamling av granskade händelser i alla Linux-distributioner som stöds, utan några förutsättningar.

    granskade poster samlas in, berikas och sammanställs till händelser med hjälp av Log Analytics-agenten för Linux-agenten. Security Center lägger kontinuerligt till nya analyser som använder Linux-signaler för att upptäcka skadliga beteenden på moln och lokala Linux-datorer. I likhet med Windows-funktioner sträcker sig dessa analyser över misstänkta processer, tvivelaktiga inloggningsförsök, inläsning av kärnmoduler och andra aktiviteter. Dessa aktiviteter kan tyda på att en maskin antingen är under attack eller har brutits.  

    En lista över Linux-aviseringar finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-linux).

> [!TIP]
> Du kan simulera Linux-varningar genom att hämta [Azure Security Center Playbook: Linux Detections](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Hotskydd för Azure App Service<a name="app-services"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i Azure-myndigheter och suveräna molnregioner.

Security Center använder molnets skala för att identifiera attacker som riktar sig till program som körs över App Service. Angripare söker webbprogram för att hitta och utnyttja svagheter. Innan de dirigeras till specifika miljöer går begäranden till program som körs i Azure via flera gateways, där de inspekteras och loggas. Dessa data används sedan för att identifiera bedrifter och angripare och för att lära sig nya mönster som kommer att användas senare.

Genom att använda den synlighet som Azure har som molnleverantör analyserar Security Center interna loggar för apptjänsten för att identifiera attackmetodik för flera mål. Metod omfattar till exempel omfattande skanning och distribuerade attacker. Den här typen av angrepp kommer vanligtvis från en liten delmängd av IPs och visar mönster för crawlning till liknande slutpunkter på flera värdar. Attackerna söker efter en sårbar sida eller plugin, och kan inte identifieras ur synvinkel av en enda värd.

Om du kör en Windows-baserad App Service-plan har Security Center också åtkomst till underliggande sandlådor och virtuella datorer. Tillsammans med de loggdata som nämns ovan kan infrastrukturen berätta historien, från en ny attack som cirkulerar i naturen till kompromisser i kundmaskiner. Även om Security Center distribueras efter att en webbapp har utnyttjats kan den därför identifiera pågående attacker.

En lista över Azure App Service-aviseringar finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-azureappserv).

Mer information om App Service-abonnemang finns i [App Service-abonnemang](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Hotskydd för Azure-behållare<a name="azure-containers"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i Azure-myndigheter och suveräna molnregioner.

Security Center ger hotskydd i realtid för dina containermiljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Security Center ger skydd mot hot på olika nivåer: 

* **Värdnivå** – Security Centers agent (tillgänglig på standardnivån, se [priser](security-center-pricing.md) för mer information) övervakar Linux för misstänkta aktiviteter. Agenten utlöser aviseringar för misstänkta aktiviteter som kommer från noden eller en behållare som körs på den. Exempel på sådana aktiviteter är identifiering av webbskal och anslutning till kända misstänkta IP-adresser.

    För en djupare inblick i säkerheten i din containermiljö övervakar agenten behållarspecifika analyser. Det utlöser aviseringar för händelser som att skapa privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    >[!IMPORTANT]
    > Om du väljer att inte installera agenterna på dina värdar får du bara en delmängd av hotskyddsfördelarna och säkerhetsaviseringarna. Du får fortfarande aviseringar om nätverksanalys och kommunikation med skadliga servrar.

    En lista över aviseringar på värdnivå finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-containerhost).


* På **AKS-klusternivå**baseras hotskyddet på att analysera Kubernetes granskningsloggar. Om du vill aktivera den här **agentlös** övervakningen lägger du till alternativet Kubernetes i prenumerationen från sidan **Priser & inställningar** (se [priser](security-center-pricing.md)). Security Center övervakar dina AKS-hanterade tjänster med hjälp av loggarna som hämtas av AKS för att generera aviseringar på den här nivån. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga fästen.

    >[!NOTE]
    > Security Center genererar säkerhetsaviseringar för Azure Kubernetes Service-åtgärder och distributioner som inträffar när kubernetes-alternativet är aktiverat på prenumerationsinställningarna. 

    En lista över AKS-klusternivåaviseringar finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-akscluster).

Dessutom övervakar vårt globala team av säkerhetsforskare ständigt hotbilden. De lägger till behållarspecifika aviseringar och sårbarheter när de upptäcks.

> [!TIP]
> Du kan simulera behållaraviseringar genom att följa instruktionerna i [det här blogginlägget](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Hotskydd för SQL Database och SQL Data Warehouse<a name="data-sql"></a>

Avancerat skydd mot hot för Azure SQL Database identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Aviseringar visas när det finns misstänkta databasaktiviteter, potentiella sårbarheter eller SQL-injektionsattacker och avvikande databasåtkomst- och frågemönster.

Avancerat skydd mot hot för Azure SQL Database och SQL är en del av det enhetliga paketet [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) för avancerade SQL-säkerhetsfunktioner som täcker Azure SQL-databaser, hanterade Azure SQL-databashanteringsinstanser, Azure SQL Data Warehouse-databaser och SQL-servrar på Virtuella Azure-datorer.

Mer information finns i:

* [Aktivera avancerat skydd mot hot för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Aktivera avancerat skydd mot hot för SQL-servrar på virtuella Azure-datorer](security-center-iaas-advanced-data.md)
* [Listan över hotskyddsaviseringar för SQL Database och SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Hotskydd för Azure Storage<a name="azure-storage"></a>

Avancerat skydd mot hot för lagring identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Med det här skyddslageret kan du hantera hot utan att du behöver vara säkerhetsexpert och hjälpa dig att hantera dina säkerhetsövervakningssystem.

Avancerat hotskydd för Azure Storage är för närvarande endast tillgängligt för [Blob Storage](https://azure.microsoft.com/services/storage/blobs/). 

Den här tjänsten är tillgänglig i alla offentliga moln och amerikanska myndighetsmoln, men inga andra suveräna eller Azure-myndigheters molnregioner.

Information om priser, inklusive en kostnadsfri utvärderingsversion på 30 dagar, finns på [prissidan för Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Mer information finns i:

* [Aktivera avancerat skydd mot hot för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Listan över hotskyddsaviseringar för Azure Storage](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> Du kan simulera Azure Storage-aviseringar genom att följa instruktionerna i [det här blogginlägget](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Hotskydd för Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB-aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton.

Mer information finns i:

* [Avancerat skydd mot hot för Azure Cosmos DB (förhandsversion)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Listan över hotskyddsaviseringar för Azure Cosmos DB (förhandsversion)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Hotskydd för Azure-nätverkslager<a name="network-layer"></a>

Security Center nätverkslageranalys baseras på exempel [IPFIX-data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är pakethuvuden som samlas in av Azure core-routrar. Baserat på den här datafeeden använder Security Center maskininlärningsmodeller för att identifiera och flagga skadliga trafikaktiviteter. Security Center använder också Microsoft Threat Intelligence-databasen för att berika IP-adresser.

Vissa nätverkskonfigurationer kan begränsa Security Center från att generera aviseringar om misstänkt nätverksaktivitet. För security center för att generera nätverksaviseringar, se till att:

- Den virtuella datorn har en offentlig IP-adress (eller finns på en belastningsutjämnare med en offentlig IP-adress).

- Den virtuella datorns nätverksutgående trafik blockeras inte av en extern IDS-lösning.

- Den virtuella datorn har tilldelats samma IP-adress under hela den timme då den misstänkta kommunikationen inträffade. Detta gäller även virtuella datorer som skapats som en del av en hanterad tjänst (till exempel AKS, Databricks).

En lista över aviseringar för Azure-nätverkslager finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-azurenetlayer).

Mer information om hur Security Center kan använda nätverksrelaterade signaler för att tillämpa hotskydd finns [i Heuristiska DNS-identifieringar i Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Hotskydd för Azure-hanteringslagret (Azure Resource Manager) (förhandsversion)<a name ="management-layer"></a>

Security Centers skyddslager baserat på Azure Resource Manager är för närvarande i förhandsversion.

Security Center erbjuder ytterligare ett skyddslager med hjälp av Azure Resource Manager-händelser, som anses vara kontrollplanet för Azure. Genom att analysera Azure Resource Manager-posterna identifierar Security Center ovanliga eller potentiellt skadliga åtgärder i Azure-prenumerationsmiljön.

En lista över aviseringarna för Azure Resource Manager (Förhandsversion) finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Flera av de föregående analyserna drivs av Microsoft Cloud App Security. För att dra nytta av dessa analyser måste du aktivera en Cloud App Security-licens. Om du har en Cloud App Security-licens aktiveras dessa aviseringar som standard. Så här inaktiverar du aviseringarna:
>
> 1. Välj **Säkerhetsprincip**i **bladet Säkerhetscenter** . För den prenumeration som du vill ändra väljer du **Redigera inställningar**.
> 2. Välj **Hotidentifiering**.
> 3. Under **Aktivera integreringar avmarkerar** **du Tillåt Microsoft Cloud App Security för att komma åt mina data**och välj **Spara**.

>[!NOTE]
>Security Center lagrar säkerhetsrelaterade kunddata i samma geo som sin resurs. Om Microsoft ännu inte har distribuerat Security Center i resursens geo lagras data i USA. När Cloud App Security är aktiverat lagras den här informationen i enlighet med geo-platsreglerna för Cloud App Security. Mer information finns i [Datalagring för icke-regionala tjänster](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Hotskydd för Azure Key Vault (förhandsversion)<a name="azure-keyvault"></a>

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i Azure-myndigheter och suveräna molnregioner.

Azure Key Vault är en molntjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. 

Azure Security Center innehåller Azure-native, avancerat hotskydd för Azure Key Vault, vilket ger ett extra lager av säkerhetsinformation. Security Center upptäcker ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Med det här skyddslageret kan du hantera hot utan att vara säkerhetsexpert och utan att behöva hantera säkerhetsövervakningssystem från tredje part.  

När avvikande aktiviteter inträffar visar Security Center aviseringar och skickar dem eventuellt via e-post till prenumerationsadministratörer. Dessa aviseringar innehåller information om den misstänkta aktiviteten och rekommendationer om hur man undersöker och åtgärdar hot. 

En lista över aviseringarna för Azure Key Vault finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Hotskydd för andra Microsoft-tjänster<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Hotskydd för Azure WAF<a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webbprogram blir alltmer inriktade på skadliga attacker som utnyttjar allmänt kända sårbarheter. Application Gateway WAF baseras på core rule set 3.0 eller 2.2.9 från Open Web Application Security Project. WAF uppdateras automatiskt för att skydda mot nya sårbarheter. 

Om du har en licens för Azure WAF strömmas dina WAF-aviseringar till Security Center utan ytterligare konfiguration. Mer information om aviseringar som genereras av WAF finns i [CRS-regelgrupper och regler för webbprogram brandvägg.](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)


### <a name="threat-protection-for-azure-ddos-protection"></a>Hotskydd för Azure DDoS-skydd<a name="azure-ddos"></a>

DDoS-attacker (Distributed Denial of Service) är kända för att vara lätta att utföra. De har blivit ett stort säkerhetsproblem, särskilt om du flyttar dina program till molnet. 

En DDoS-attack försöker uttömma ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot alla slutpunkter som kan nås via Internet.

För att skydda dig mot DDoS-attacker köper du en licens för Azure DDoS Protection och ser till att du följer metodtips för programdesign. DDoS Protection tillhandahåller olika tjänstnivåer. Mer information finns i [översikt över Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

En lista över Azure DDoS Protection-aviseringar finns i [referenstabellen med aviseringar](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Nästa steg
Mer information om säkerhetsvarningar från dessa hotskyddsfunktioner finns i följande artiklar:

* [Referenstabell för alla Azure Security Center-aviseringar](alerts-reference.md)
* [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exportera säkerhetsaviseringar och rekommendationer (förhandsversion)](continuous-export.md)