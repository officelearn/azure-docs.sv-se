---
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: tagore
ms.openlocfilehash: b86e0d784d26e9e483dd12e20d45189ae8bfb9bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866188"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrera IaaS-resurser från den klassiska distributionsmodellen till Azure Resource Manager
För det första är det viktigt att förstå skillnaden mellan data-plan och hantering-plan verksamhet på infrastrukturen som en tjänst (IaaS) resurser.

* *Hanterings-/kontrollplan* beskriver de anrop som kommer in i hanterings-/kontrollplanet eller API:et för att ändra resurser. Åtgärder som exempelvis skapa en virtuell dator, starta om en virtuell dator och uppdatera ett virtuellt nätverk med ett nytt undernät, hanterar de resurser som körs. De påverkar inte direkt anslutning till de virtuella datorerna.
* *Dataplan* (program) beskriver körningen av själva programmet och innebär interaktion med instanser som inte går igenom Azure-API:et. Till exempel är dataplan- eller programinteraktioner att komma åt din webbplats eller hämta data från en SQL Server-instans eller en MongoDB-server. Andra exempel är att kopiera en blob från ett lagringskonto och komma åt en offentlig IP-adress för att använda RDP (Remote Desktop Protocol) eller Secure Shell (SSH) till den virtuella datorn. Med de här åtgärderna kan programmet köras i beräknings-, nätverks- och lagringsresurser.

Dataplanet är detsamma mellan den klassiska distributionsmodellen och Resource Manager-stackarna. Skillnaden är att Microsoft under migreringsprocessen översätter representationen av resurserna från den klassiska distributionsmodellen till den i Resurshanteraren-stacken. Därför måste du använda nya verktyg, API:er och SDK:er för att hantera dina resurser i Resurshanterarens stack.

![Diagram som visar skillnaden mellan hanterings-/kontrollplan och dataplan](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> I vissa migreringsfall stoppas, frigörs och startas dina virtuella datorer om av Azure-plattformen. Detta medför en kort data-plan driftstopp.
>

## <a name="the-migration-experience"></a>Migreringen
Innan du startar migreringen:

* Se till att inga funktioner eller konfigurationer som inte stöds används av de resurser du vill migrera. Vanligtvis upptäcks de här problemen och plattformen genererar ett fel.
* Om du har virtuella datorer som inte finns i ett virtuellt nätverk stoppas de och frigörs som en del av förbereda-åtgärden. Om du inte vill förlora den offentliga IP-adressen kan du överväga att reservera IP-adressen innan du utlöser förbereda åtgärden. Om de virtuella datorerna finns i ett virtuellt nätverk stoppas de inte och frigörs.
* Lägg migreringen utanför kontorstid för att lättare kunna hantera eventuella fel som kan uppstå.
* Ladda ned den aktuella konfigurationen av dina virtuella datorer med hjälp av PowerShell, CLI-kommandon (kommandoradsgränssnitt) eller REST-API:er för att underlätta verifieringen när förberedelsesteget är klart.
* Uppdatera dina automatiserings- och operationaliseringsskript för att hantera resurshanterarens distributionsmodell innan du startar migreringen. Du kan också utföra GET-åtgärder när resurserna är i förberedelsefasen.
* Utvärdera de RBAC-principer (Role-Based Access Control) som har konfigurerats för IaaS-resurserna i den klassiska distributionsmodellen och planera för när migreringen är klar.

Migreringsarbetsflödet är följande:

![Diagram som visar migreringsarbetsflödet](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> De åtgärder som beskrivs i följande avsnitt är alla idempotenta. Om du har ett annat problem än en funktion som inte stöds eller ett konfigurationsfel försöker du förbereda, avbryta eller utföra. Azure försöker åtgärden igen.
>
>

### <a name="validate"></a>Verifiera
Verifiering är det första steget under migreringen. Målet med det här steget är att analysera tillståndet för de resurser som du vill migrera i den klassiska distributionsmodellen. Åtgärden utvärderar om resurserna kan migrera (lyckas eller misslyckas).

Du väljer det virtuella nätverket eller en molntjänst (om den inte finns i ett virtuellt nätverk) som du vill validera för migrering. Om resursen inte kan migrera, azure listar orsakerna till.

#### <a name="checks-not-done-in-the-validate-operation"></a>Kontroller som inte görs i valideringsåtgärden

Validera-åtgärden analyserar bara tillståndet för resurserna i den klassiska distributionsmodellen. Den kan söka efter alla fel och scenarier som inte stöds på grund av olika konfigurationer i den klassiska distributionsmodellen. Det går inte att söka efter alla problem som Azure Resource Manager-stacken kan medföra för resurserna under migreringen. Dessa problem kontrolleras bara när resurserna genomgår omvandling i nästa steg i migreringen (förbereda åtgärden). I följande tabell visas alla problem som inte har kontrollerats i valideringsåtgärden:


|Nätverkskontroller som inte är i valideringsåtgärden|
|-|
|Ett virtuellt nätverk med både ER- och VPN-gateways.|
|En anslutning till virtuell nätverksgateway i frånkopplat tillstånd.|
|Alla ER-kretsar är förrenderade till Azure Resource Manager-stacken.|
|Azure Resource Manager-kvoten söker efter nätverksresurser. Till exempel: statisk offentlig IP, dynamiska offentliga IP-adresser, belastningsutjämnare, nätverkssäkerhetsgrupper, flödestabeller och nätverksgränssnitt. |
| Alla belastningsutjämnareregler är giltiga i hela distributionen och det virtuella nätverket. |
| Motstridiga privata IP-adresser mellan stop-deallocated virtuella datorer i samma virtuella nätverk. |

### <a name="prepare"></a>Förbereda
Förberedelse är det andra steget under migreringen. Målet med det här steget är att simulera omvandlingen av IaaS-resurserna från den klassiska distributionsmodellen till Resource Manager-resurser. Vidare presenterar förbereda åtgärden denna sida vid sida för dig att visualisera.

> [!NOTE] 
> Dina resurser i den klassiska distributionsmodellen ändras inte under det här steget. Det är ett säkert steg att köra om du provar migrering. 

Du väljer det virtuella nätverket eller molntjänsten (om det inte är ett virtuellt nätverk) som du vill förbereda för migrering.

* Om resursen inte kan migrera stoppar Azure migreringsprocessen och visar orsaken till att förbereda åtgärden misslyckades.
* Om resursen kan migrera låser Azure ned hanteringsplanåtgärderna för resurserna under migreringen. Du kan till exempel inte lägga till en datadisk i en virtuell dator under migreringen.

Azure startar sedan migrering av metadata från den klassiska distributionsmodellen till Resource Manager för migrerande resurser.

När förbereda åtgärden är klar har du möjlighet att visualisera resurserna i både den klassiska distributionsmodellen och Resource Manager. För varje molntjänst i den klassiska distributionsmodellen skapar Azure-plattformen ett resursgruppnamn som har mönstret `cloud-service-name>-Migrated`.

> [!NOTE]
> Det går inte att välja namnet på en resursgrupp som skapats för migrerade resurser (det vill än "-Migrerad"). När migreringen är klar kan du dock använda flyttfunktionen i Azure Resource Manager för att flytta resurser till vilken resursgrupp du vill. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../articles/resource-group-move-resources.md).

Följande två skärmbilder visar resultatet efter en lyckad förbereda operation. Den första visar en resursgrupp som innehåller den ursprungliga molntjänsten. Den andra visar den nya resursgruppen "Migrerad" som innehåller motsvarande Azure Resource Manager-resurser.

![Skärmbild som visar den ursprungliga molntjänsten](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Skärmbild som visar Azure Resource Manager-resurser i förbereda-åtgärden](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Här är en bakom kulisserna titta på dina resurser efter slutförandet av förbereda fasen. Observera att resursen i dataplanet är densamma. Den finns representerad i både hanteringsplanet (klassisk distributionsmodell) och kontrollplanet (Resource Manager).

![Diagram över preparera arrangerar gradvis](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuella datorer som inte finns i ett virtuellt nätverk i den klassiska distributionsmodellen stoppas och frigörs i den här fasen av migreringen.
>

### <a name="check-manual-or-scripted"></a>Kontrollera (manuellt eller via skript)
I kontrollsteget har du möjlighet att använda den konfiguration som du hämtade tidigare för att verifiera att migreringen ser korrekt ut. Du kan också logga in på portalen och avmarkera egenskaperna och resurserna för att verifiera att metadatamigrering ser bra ut.

Om du migrerar ett virtuellt nätverk startas inte de flesta konfigurationer av virtuella datorer om. För program på dessa virtuella datorer kan du verifiera att programmet fortfarande körs.

Du kan testa dina övervaknings- och driftskript för att se om de virtuella datorerna fungerar som förväntat och om de uppdaterade skripten fungerar korrekt. Det finns bara stöd för GET-åtgärder när resurserna är i förberedelsefasen.

Det finns ingen uppsättning tidsfönster innan som du måste utföra migreringen. Du kan ta den tid du vill i det här läget. Dock låses hanteringsplanet för de här resurserna tills du antingen avbryter eller checkar in.

Om problem skulle uppstå kan du alltid avbryta migreringen och gå tillbaka till den klassiska distributionsmodellen. När du har går tillbaka öppnar Azure hanteringsplanåtgärderna på resurserna, så att du kan återuppta normala åtgärder på dessa virtuella datorer i den klassiska distributionsmodellen.

### <a name="abort"></a>Avbryta
Detta är ett valfritt steg om du vill återställa ändringarna till den klassiska distributionsmodellen och stoppa migreringen. Den här åtgärden tar bort Resource Manager-metadata (som skapas i steget förbereda) för dina resurser. 

![Diagram över avbrottssteg](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Den här åtgärden kan inte utföras när du har utlöst commit-åtgärden.     
>

### <a name="commit"></a>Checka in
När verifieringen är klar kan du checka in migreringen. Resurser visas inte längre i den klassiska distributionsmodellen och är endast tillgängliga i resurshanterarens distributionsmodell. De migrerade resurserna kan bara hanteras i den nya portalen.

> [!NOTE]
> Det här är en idempotent åtgärd. Om det misslyckas, försök igen åtgärden. Om det fortsätter att misslyckas skapar du en supportbiljett eller skapar ett forum på [Microsoft Q&A](https://docs.microsoft.com/answers/index.html)
>
>

![Diagram över commit-steg](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Flödesschema för migrering

Här är ett flödesschema som visar hur du går vidare med migrering:

![Skärmbild som visar migreringsstegen](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Översättning av den klassiska distributionsmodellen till Resource Manager-resurser
Du hittar den klassiska distributionsmodellen och Resource Manager-representationerna av resurserna i följande tabell. Andra funktioner och resurser stöds för närvarande inte.

| Klassiskt läge | Resource Manager-läge | Anteckningar |
| --- | --- | --- |
| Molntjänstens namn |DNS-namn |Under migreringen skapas en ny resursgrupp för varje molntjänst med namngivningsmönstret `<cloudservicename>-migrated`. Den här resursgruppen innehåller alla dina resurser. Namnet på molntjänsten blir ett DNS-namn som kopplas till den offentliga ip-adressen. |
| Virtuell dator |Virtuell dator |Egenskaper för virtuella datorer migreras oförändrade. Viss osProfile-information, som datornamn, lagras inte i den klassiska distributionsmodellen och förblir tom efter migreringen. |
| Diskresurser anslutna till den virtuella datorn |Implicita diskar anslutna till den virtuella datorn |Diskarna utformas inte som toppresurser i Resource Manager-distributionsmodellen. De migreras som implicita diskar för den virtuella datorn. För närvarande finns bara stöd för diskar som är anslutna till en virtuell dator. Virtuella datorer i Resource Manager kan nu använda lagringskonton i den klassiska distributionsmodellen, vilket gör att diskarna enkelt kan migreras utan några uppdateringar. |
| VM-tillägg |VM-tillägg |Alla resurstillägg utom XML-tillägg migreras från den klassiska distributionsmodellen. |
| Certifikat för virtuell dator |Certifikat i Azure Key Vault |Om en molntjänst innehåller tjänstcertifikat skapar migreringen ett nytt Azure-nyckelvalv per molntjänst och flyttar certifikaten till nyckelvalvet. De virtuella datorerna uppdateras för att hänvisa till certifikaten från nyckelvalvet. <br><br> Ta inte bort nyckelvalvet. Detta kan leda till att den virtuella datorn hamnar i ett misslyckat tillstånd. |
| WinRM-konfiguration |WinRM-konfiguration under osProfile |Windows Remote Management-konfigurationen flyttas oförändrad under migreringen. |
| Egenskap för tillgänglighetsuppsättning |Resurs för tillgänglighetsuppsättning | Tillgänglighetsuppsättningsspecifikation är en egenskap på den virtuella datorn i den klassiska distributionsmodellen. Under migreringen blir tillgänglighetsuppsättningar en resurs på toppnivå. Följande konfigurationer stöds inte: flera tillgänglighetsuppsättningar per molntjänst eller en eller flera tillgänglighetsuppsättningar tillsammans med virtuella datorer som inte tillhör en tillgänglighetsuppsättning i en molntjänst. |
| Nätverkskonfiguration på en virtuell dator |Primärt nätverksgränssnitt |Nätverkskonfigurationen på en virtuell dator visas som en resurs för primärt nätverksgränssnitt efter migreringen. För virtuella datorer som inte tillhör något virtuellt nätverk ändras den interna ip-adressen under migreringen. |
| Flera nätverksgränssnitt på en virtuell dator |Nätverksgränssnitt |Om en virtuell dator har flera nätverksgränssnitt som är associerade med den, blir varje nätverksgränssnitt en resurs på den högsta nivån som en del av migreringen, tillsammans med alla egenskaper. |
| Belastningsutjämnad slutpunktsuppsättning |Lastbalanserare |I den klassiska distributionsmodellen kopplade plattformen en implicit lastbalanserare till varje tjänst i molnet. Under migreringen skapas en ny belastningsutjämnarresurs och den belastningsutjämnade slutpunktsuppsättningen blir belastningsutjämnarregler. |
| Ingående NAT-regler |Ingående NAT-regler |Indataslutpunkter som har angetts i den virtuella datorn omvandlas till inkommande regler för nätverksadressöversättning i lastbalanseraren under migreringen. |
| Virtuell ip-adress |Offentlig ip-adress med DNS-namn |Den virtuella IP-adressen blir en offentlig IP-adress och associeras med belastningsutjämnaren. En virtuell ip-adress kan bara migreras om en indataslutpunkt är kopplad till den. |
| Virtuellt nätverk |Virtuellt nätverk |Det virtuella nätverket migreras, med alla egenskaper, till Resource Manager-distributionsmodellen. En ny resursgrupp skapas med namnet `-migrated`. |
| Reserverade ip-adresser |Offentlig ip-adress med fast fördelningsmetod |Reserverade ip-adresser som är kopplade till lastbalanseraren migreras tillsammans med molntjänsten eller den virtuella datorn. För närvarande finns inte stöd för migrering av reserverade ip-adresser utan koppling. |
| Offentlig ip-adress per virtuell dator |Offentlig ip-adress med dynamisk fördelningsmetod |Den offentliga ip-adress som är kopplad till den virtuella datorn omvandlas som offentlig ip-adressresurs med fördelningsmetoden inställd på fas. |
| NSG:er |NSG:er |Nätverkssäkerhetsgrupper som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Nätverkssäkerhetsgruppen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för nätverkssäkerhetsgrupperna under migreringen. |
| DNS-servrar |DNS-servrar |DNS-servrar som är kopplade till ett virtuellt nätverk eller den virtuella datorn migreras under motsvarande resursmigrering tillsammans med alla egenskaper. |
| Användardefinierade vägar |Användardefinierade vägar |Användardefinierade vägar som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Den användardefinierade vägen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för den användardefinierade vägen under migreringen. |
| Egenskapen ip-vidarebefordran i en virtuell dators nätverkskonfiguration |Egenskapen ip-vidarebefordran på nätverkskortet |Egenskapen ip-vidarebefordran i en virtuell dator omvandlas till en egenskap i nätverksgränssnittet under migreringen. |
| Lastbalanserare med flera ip-adresser |Lastbalanserare med flera offentliga ip-resurser |Varje offentlig IP som är associerad med belastningsutjämnaren konverteras till en offentlig IP-resurs och associeras med belastningsutjämnaren efter migreringen. |
| Interna DNS-namn på den virtuella datorn |Interna DNS-namn på nätverkskortet |Interna DNS-suffix för de virtuella datorerna migreras till en skrivskyddad egenskap med namnet ”InternalDomainNameSuffix” på nätverkskortet. Suffixet förblir oförändrat efter migreringen och vm-lösningen ska fortsätta att fungera som tidigare. |
| Virtuell nätverksgateway |Virtuell nätverksgateway |Egenskaper för virtuell nätverksgateway migreras oförändrade. Den virtuella ip-adressen som är kopplad till gatewayen ändras inte heller. |
| Lokal nätverksplats |Lokal nätverksgateway |Egenskaper för lokala nätverkswebbplatser migreras oförändrade till en ny resurs som kallas en lokal nätverksgateway. Detta representerar lokala adressprefix och fjärrgateway-IP. |
| Anslutningsreferenser |Anslutning |Anslutningsreferenser mellan gatewayen och den lokala nätverksplatsen i nätverkskonfigurationen representeras av en ny resurs som kallas Anslutning. Alla egenskaper för anslutningsreferens i nätverkskonfigurationsfiler kopieras oförändrade till anslutningsresursen. Anslutningen mellan virtuella nätverk i den klassiska distributionsmodellen uppnås genom att skapa två IPsec-tunnlar till lokala nätverksplatser som representerar de virtuella nätverken. Detta omvandlas till anslutningstypen för virtuellt nätverk till virtuellt nätverk i Resource Manager-modellen, utan att lokala nätverksgateways krävs. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Ändringar av automatiseringstjänster och verktyg efter migreringen
Som en del av migrering av dina resurser från den klassiska distributionsmodellen till Resurshanterarens distributionsmodell måste du uppdatera din befintliga automatisering eller verktyg för att säkerställa att den fortsätter att fungera efter migreringen.
