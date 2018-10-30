---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: dc871b29cdafa57d337f9be6cf01e76212f31b67
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227144"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrera IaaS-resurser från den klassiska distributionsmodellen till Azure Resource Manager
Först, är det viktigt att förstå skillnaden mellan dataplanet och Hanteringsplanet åtgärder på infrastrukturen som en tjänst (IaaS)-resurser.

* *Hanterings-/ kontrollplanet* beskriver de anrop som kommer till hanterings-/ kontrollplanet eller API för att ändra resurser. Åtgärder som exempelvis skapa en virtuell dator, starta om en virtuell dator och uppdatera ett virtuellt nätverk med ett nytt undernät, hanterar de resurser som körs. De påverkar direkt inte ansluta till de virtuella datorerna.
* *Dataplanet* (program) beskriver körtiden för själva programmet och omfattar interaktionen med instanser som inte passerar via API i Azure. Till exempel åtkomst till din webbplats eller hämta data från en SQL Server-instans som körs eller en MongoDB-servern är data plana eller ett program interaktioner. Andra exempel är kopierar en blob från ett lagringskonto och åtkomst till en offentlig IP-adress för att använda Remote Desktop Protocol (RDP) eller Secure Shell (SSH) till den virtuella datorn. Med de här åtgärderna kan programmet köras i beräknings-, nätverks- och lagringsresurser.

Dataplanet är samma mellan den klassiska distributionsmodellen och Resource Manager-stackar. Skillnaden är att Microsoft under migreringsprocessen, omvandlar en representation av resurser från den klassiska distributionsmodellen som i Resource Manager-stacken. Därför kan behöva du använda nya verktyg, API: er och SDK: er för att hantera dina resurser i Resource Manager-stacken.

![Diagram som visar skillnaden mellan hanterings-/ kontrollplanet och dataplanet](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> I vissa migreringsfall stoppas, frigörs och startas dina virtuella datorer om av Azure-plattformen. Detta leder till ett kort driftstopp för dataplanet.
>

## <a name="the-migration-experience"></a>Migreringen
Innan du påbörjar migreringen:

* Se till att inga funktioner eller konfigurationer som inte stöds används av de resurser du vill migrera. Vanligtvis upptäcks de här problemen och plattformen genererar ett fel.
* Om du har virtuella datorer som inte ingår i ett virtuellt nätverk kan de stoppas och frigörs under Förberedelseåtgärden. Om du inte vill förlora den offentliga IP-adressen kan du överväga att reservera IP-adressen innan du aktiverar Förberedelseåtgärden. Om de virtuella datorerna är i ett virtuellt nätverk kan de inte stoppas och frigörs.
* Lägg migreringen utanför kontorstid för att lättare kunna hantera eventuella fel som kan uppstå.
* Ladda ned den aktuella konfigurationen av dina virtuella datorer med hjälp av PowerShell, CLI-kommandon (kommandoradsgränssnitt) eller REST-API:er för att underlätta verifieringen när förberedelsesteget är klart.
* Uppdatera dina automation och driftsättning skript för att hantera Resource Manager-distributionsmodellen innan du påbörjar migreringen. Du kan också utföra GET-åtgärder när resurserna är i förberedelsefasen.
* Utvärdera de principer för rollbaserad åtkomstkontroll (RBAC) som är konfigurerade på IaaS-resurser i den klassiska distributionsmodellen och planera för när migreringen är klar.

Arbetsflödet för migrering är följande:

![Diagram som visar migreringsarbetsflödet](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Åtgärderna som beskrivs i följande avsnitt är alla idempotenta. Om du har andra problem än en funktion som inte stöds eller ett konfigurationsfel, försöka förbereda, avbryta eller utför-åtgärden. Azure utförs åtgärden igen.
>
>

### <a name="validate"></a>Verifiera
Verifiering är det första steget under migreringen. Målet med det här steget är att analysera statusen för de resurser som du vill migrera i den klassiska distributionsmodellen. Åtgärden utvärderar om resurserna kan migreras (lyckade eller misslyckade).

Du väljer det virtuella nätverket eller en molntjänst (om det inte är i ett virtuellt nätverk) som du vill verifiera för migrering. Om resursen inte kan migreras visas Azure skälen varför.

#### <a name="checks-not-done-in-the-validate-operation"></a>Kontrollerar inte har gjort i verifiering

Verifiering analyserar endast tillståndet för resurserna i den klassiska distributionsmodellen. Det kan söka efter alla fel och scenarier som inte stöds på grund av olika konfigurationer i den klassiska distributionsmodellen. Det går inte att söka efter alla problem som Azure Resource Manager-stacken kan kräva för resurserna under migreringen. De här problemen kontrolleras endast om resurserna som genomgår omvandling i nästa steg i migreringen (Förberedelseåtgärden). I följande tabell visas alla problem som inte incheckad verifiering:


|Nätverk checkar inte in verifiering|
|-|
|Ett virtuellt nätverk som har både ER och VPN-gatewayer.|
|En gateway-anslutning för virtuellt nätverk i frånkopplat läge.|
|Alla ER kretsar har redan migrerats till Azure Resource Manager-stacken.|
|Azure Resource Manager-kvot kontrollerar nätverksresurser. Till exempel: statisk offentlig IP-adress, dynamiska offentliga IP-adresser, läsa in belastningsutjämnare, nätverkssäkerhetsgrupper, routningstabeller och nätverksgränssnitt. |
| Alla regler för belastningsutjämnaren är giltig för distribution och det virtuella nätverket. |
| Motstridiga privata IP-adresser mellan frigörandet av virtuella datorer i samma virtuella nätverk. |

### <a name="prepare"></a>Förbereda
Förberedelse är det andra steget under migreringen. Målet med det här steget är att simulera omvandlingen av IaaS-resurser från den klassiska distributionsmodellen till Resource Manager-resurser. Dessutom anger Förberedelseåtgärden denna sida vid sida vid sida.

> [!NOTE] 
> Dina resurser i den klassiska distributionsmodellen ändras inte under det här steget. Det är ett säkert att köra om du försöker ut migrering. 

Du väljer det virtuella nätverket eller Molntjänsten (om det inte är ett virtuellt nätverk) som du vill förbereda för migrering.

* Om resursen inte kan migreras stoppas migreringen Azure och visar en lista över anledningen till varför Förbered-åtgärden misslyckades.
* Om en resurs kan migreras låser Azure hanteringsplanåtgärder för resurserna under migreringen. Du kan till exempel inte lägga till en datadisk i en virtuell dator under migreringen.

Azure startar sedan migreringen av metadata från den klassiska distributionsmodellen till Resource Manager för de migrerande resurserna.

När Förberedelseåtgärden är klar har du möjlighet att visa resurserna i både den klassiska distributionsmodellen och Resource Manager. För varje molntjänst i den klassiska distributionsmodellen skapar Azure-plattformen ett resursgruppnamn som har mönstret `cloud-service-name>-Migrated`.

> [!NOTE]
> Det går inte att markera namnet på en resursgrupp som skapats för migrerade resurser (det vill säga ”-migreras”). När migreringen är klar, men kan du använda funktionen flytten av Azure Resource Manager för att flytta resurser till valfri resursgrupp som du vill. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../articles/resource-group-move-resources.md).

Följande två skärmbilderna visar resultaten efter en lyckad Förbered-åtgärden. Den första som visar en resursgrupp som innehåller den ursprungliga Molntjänsten. Den andra mallen visas den nya ”-migreras” resursgruppen som innehåller motsvarande Azure Resource Manager-resurser.

![Skärmbild som visar ursprungliga Molntjänsten](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Skärmbild som visar Azure Resource Manager-resurser i Förberedelseåtgärden](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Här är en bakgrunden titt på resurserna i fasen Förbered är färdig. Observera att resursen i dataplanet är samma. Den visas i både Hanteringsplanet (klassisk distributionsmodell) och kontrollplanet (Resource Manager).

![Diagram över fasen Förbered](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuella datorer som inte ingår i ett virtuellt nätverk i den klassiska distributionsmodellen stoppas och frigörs i den här fasen av migreringen.
>

### <a name="check-manual-or-scripted"></a>Kontrollera (manuellt eller via skript)
I steget där du har möjlighet att använda den konfiguration som du hämtade tidigare för att verifiera att migreringen ser korrekt ut. Du kan också kan du logga in på portalen och stickkontrollera egenskaper och resurser för att verifiera att metadatamigreringen ser bra ut.

Om du migrerar ett virtuellt nätverk startas inte de flesta konfigurationer av virtuella datorer om. För program på de virtuella datorerna, kan du verifiera att programmet fortfarande körs.

Du kan testa dina övervaknings- och operativa skript för att se om de virtuella datorerna fungerar som förväntat, och om de uppdaterade skripten fungerar korrekt. Det finns bara stöd för GET-åtgärder när resurserna är i förberedelsefasen.

Det finns inga set tidsfönster när du måste checka in migreringen. Du kan ta den tid du vill i det här läget. Dock låses hanteringsplanet för de här resurserna tills du antingen avbryter eller checkar in.

Om problem skulle uppstå kan du alltid avbryta migreringen och gå tillbaka till den klassiska distributionsmodellen. När du går tillbaka öppnar Azure hanteringsplanåtgärder för de resurser, så att du kan återställa normal drift på de virtuella datorerna i den klassiska distributionsmodellen.

### <a name="abort"></a>Avbryta
Det här är ett valfritt steg om du vill återställa ändringarna till den klassiska distributionsmodellen och stoppa migreringen. Den här åtgärden tar bort Resource Manager-metadata (som skapats i förberedelsesteget) för dina resurser. 

![Diagram över steg för att avbryta](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Den här åtgärden kan inte utföras när du har aktiverat incheckning.     
>

### <a name="commit"></a>Checka in
När verifieringen är klar kan du checka in migreringen. Resurser visas inte längre i den klassiska distributionsmodellen och är endast tillgängliga i Resource Manager-distributionsmodellen. De migrerade resurserna kan bara hanteras i den nya portalen.

> [!NOTE]
> Det här är en idempotent åtgärd. Om det misslyckas och försök igen. Om det fortsätter att misslyckas, skapa ett supportärende eller skapa ett foruminlägg med en ”ClassicIaaSMigration” tagga vår [VM forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Diagram över commit steg](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Flödesschema för migrering av

Här är ett flödesschema som visar hur du fortsätter med migreringen:

![Skärmbild som visar migreringsstegen](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Översättning av den klassiska distributionsmodellen till Resource Manager-resurser
Du hittar den klassiska distributionsmodellen och Resource Manager-representation av resurserna i följande tabell. Andra funktioner och resurser stöds för närvarande inte.

| Klassiskt läge | Resource Manager-läge | Anteckningar |
| --- | --- | --- |
| Molntjänstens namn |DNS-namn |Under migreringen skapas en ny resursgrupp för varje molntjänst med namngivningsmönstret `<cloudservicename>-migrated`. Den här resursgruppen innehåller alla dina resurser. Namnet på molntjänsten blir ett DNS-namn som kopplas till den offentliga ip-adressen. |
| Virtuell dator |Virtuell dator |Egenskaper för virtuella datorer migreras oförändrade. Del osProfile-information, t.ex. datornamn, lagras inte i den klassiska distributionsmodellen utan förblir tom efter migreringen. |
| Diskresurser anslutna till den virtuella datorn |Implicita diskar anslutna till den virtuella datorn |Diskarna utformas inte som toppresurser i Resource Manager-distributionsmodellen. De migreras som implicita diskar för den virtuella datorn. För närvarande finns bara stöd för diskar som är anslutna till en virtuell dator. Resource Manager-VM kan nu använda storage-konton i den klassiska distributionsmodellen, vilket gör att diskarna så att de enkelt migreras utan någon uppdatering. |
| VM-tillägg |VM-tillägg |Alla resurstillägg utom XML-tillägg migreras från den klassiska distributionsmodellen. |
| Certifikat för virtuell dator |Certifikat i Azure Key Vault |Om en molntjänst innehåller tjänstcertifikat kan migreringen skapar en ny Azure key vault per molntjänst och certifikaten flyttas till nyckelvalvet. De virtuella datorerna uppdateras för att hänvisa till certifikaten från nyckelvalvet. <br><br> Ta inte bort nyckelvalvet. Detta kan medföra att den virtuella datorn hamnar i ett felaktigt tillstånd. |
| WinRM-konfiguration |WinRM-konfiguration under osProfile |Windows Remote Management-konfigurationen flyttas oförändrad under migreringen. |
| Egenskap för tillgänglighetsuppsättning |Resurs för tillgänglighetsuppsättning | Specifikationen för tillgänglighetsuppsättningen är en egenskap på den virtuella datorn i den klassiska distributionsmodellen. Under migreringen blir tillgänglighetsuppsättningar en resurs på toppnivå. Följande konfigurationer stöds inte: flera tillgänglighetsuppsättningar per molntjänst eller en eller flera tillgänglighetsuppsättningar tillsammans med virtuella datorer som inte tillhör en tillgänglighetsuppsättning i en molntjänst. |
| Nätverkskonfiguration på en virtuell dator |Primärt nätverksgränssnitt |Nätverkskonfigurationen på en virtuell dator visas som en resurs för primärt nätverksgränssnitt efter migreringen. För virtuella datorer som inte tillhör något virtuellt nätverk ändras den interna ip-adressen under migreringen. |
| Flera nätverksgränssnitt på en virtuell dator |Nätverksgränssnitt |Om en virtuell dator har flera nätverksgränssnitt som är kopplade till den, blir varje nätverksgränssnitt en resurs på toppnivå under migreringen, tillsammans med alla egenskaper. |
| Belastningsutjämnad slutpunktsuppsättning |Lastbalanserare |I den klassiska distributionsmodellen kopplade plattformen en implicit lastbalanserare till varje tjänst i molnet. Under migreringen skapas en ny belastningsutjämnarresurs och den belastningsutjämnade slutpunktsuppsättningen blir belastningsutjämnarregler. |
| Ingående NAT-regler |Ingående NAT-regler |Indataslutpunkter som har angetts i den virtuella datorn omvandlas till inkommande regler för nätverksadressöversättning i lastbalanseraren under migreringen. |
| Virtuell ip-adress |Offentlig ip-adress med DNS-namn |Den virtuella IP-adressen blir en offentlig IP-adress och är associerat med belastningsutjämnaren. En virtuell ip-adress kan bara migreras om en indataslutpunkt är kopplad till den. |
| Virtuellt nätverk |Virtuellt nätverk |Det virtuella nätverket migreras, med alla egenskaper, till Resource Manager-distributionsmodellen. En ny resursgrupp skapas med namnet `-migrated`. |
| Reserverade ip-adresser |Offentlig ip-adress med fast fördelningsmetod |Reserverade ip-adresser som är kopplade till lastbalanseraren migreras tillsammans med molntjänsten eller den virtuella datorn. För närvarande finns inte stöd för migrering av reserverade ip-adresser utan koppling. |
| Offentlig ip-adress per virtuell dator |Offentlig ip-adress med dynamisk fördelningsmetod |Den offentliga ip-adress som är kopplad till den virtuella datorn omvandlas som offentlig ip-adressresurs med fördelningsmetoden inställd på fas. |
| NSG:er |NSG:er |Nätverkssäkerhetsgrupper som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Nätverkssäkerhetsgruppen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för nätverkssäkerhetsgrupperna under migreringen. |
| DNS-servrar |DNS-servrar |DNS-servrar som är kopplade till ett virtuellt nätverk eller den virtuella datorn migreras under motsvarande resursmigrering tillsammans med alla egenskaper. |
| Användardefinierade vägar |Användardefinierade vägar |Användardefinierade vägar som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Den användardefinierade vägen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för den användardefinierade vägen under migreringen. |
| Egenskapen ip-vidarebefordran i en virtuell dators nätverkskonfiguration |Egenskapen ip-vidarebefordran på nätverkskortet |Egenskapen ip-vidarebefordran i en virtuell dator omvandlas till en egenskap i nätverksgränssnittet under migreringen. |
| Lastbalanserare med flera ip-adresser |Lastbalanserare med flera offentliga ip-resurser |Varje offentlig IP-adress som är associerade med belastningsutjämnaren omvandlas till en offentlig IP-resurs och som är associerade med belastningsutjämnaren efter migreringen. |
| Interna DNS-namn på den virtuella datorn |Interna DNS-namn på nätverkskortet |Interna DNS-suffix för de virtuella datorerna migreras till en skrivskyddad egenskap med namnet ”InternalDomainNameSuffix” på nätverkskortet. Suffixet förblir oförändrad efter migreringen och virtuell datormatchning bör fortsätta att fungera som tidigare. |
| Virtuell nätverksgateway |Virtuell nätverksgateway |Gateway-egenskaper för virtuella nätverk migreras oförändrade. Den virtuella ip-adressen som är kopplad till gatewayen ändras inte heller. |
| Lokal nätverksplats |Lokal nätverksgateway |Egenskaperna för lokal nätverksplats migreras oförändrade till en ny resurs som kallas en lokal nätverksgateway. Detta motsvarar lokala adressprefix och fjärrgateway-IP. |
| Anslutningsreferenser |Anslutning |Anslutningsreferenser mellan gateway och lokal nätverksplats i nätverkskonfigurationen representeras av en ny resurs med namnet Connection. Alla egenskaper för anslutningsreferensen i nätverkskonfigurationen kopieras oförändrade till Connection-resursen. Anslutningar mellan virtuella nätverk i den klassiska distributionsmodellen uppnås genom att skapa två IPsec-tunnlar till lokala nätverksplatser som motsvarar de virtuella nätverken. Det här omvandlas till anslutningstypen för virtuellt nätverk-till-virtuell-nätverk i Resource Manager-modellen utan lokala nätverksgatewayer. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Ändringar av automatiseringstjänster och verktyg efter migreringen
Som en del av dina resurser från den klassiska distributionsmodellen till Resource Manager-distributionsmodellen, måste du uppdatera dina befintliga automatiseringstjänster och verktyg så att de fortsätter att fungera efter migreringen.
