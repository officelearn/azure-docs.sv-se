## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Innebörd av migrering av IaaS-resurser från klassiskt läge till Resource Manager-läge
Innan vi går in på detaljerna ska vi titta på skillnaden mellan åtgärder på dataplanet och hanteringsplanet för IaaS-resurserna.

* *Hanterings-/kontrollplanet* beskriver de anrop som kommer till hanterings-/kontrollplanet eller API:et för att ändra resurser. Åtgärder som exempelvis skapa en virtuell dator, starta om en virtuell dator och uppdatera ett virtuellt nätverk med ett nytt undernät, hanterar de resurser som körs. De har ingen direkt effekt på anslutningen till instanserna.
* *Dataplanet* (program) beskriver körtiden för själva programmet och omfattar interaktionen med instanser som inte passerar via Azure-API:et. När du öppnar din webbplats eller hämtar data från en aktiv SQL Server-instans eller en MongoDB-server, betraktas det som en dataplan- eller programinteraktion. Kopiera en blobb från ett lagringskonto och öppna en offentlig ip-adress till RDP eller SSH på den virtuella datorn är också åtgärder på dataplanet. Med de här åtgärderna kan programmet köras i beräknings-, nätverks- och lagringsresurser.

![Skärmbild som visar skillnaden mellan hanterings-/kontrollplanet och dataplanet](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)

> [!NOTE]
> I vissa migreringsfall stoppas, frigörs och startas dina virtuella datorer om av Azure-plattformen. Det medför ett kort driftstopp för dataplanet.
>
>

## <a name="the-migration-experience"></a>Migreringen
Innan du påbörjar migreringen bör du göra följande:

* Se till att inga funktioner eller konfigurationer som inte stöds används av de resurser du vill migrera. Vanligtvis upptäcks de här problemen och plattformen genererar ett fel.
* Om du har virtuella datorer som inte tillhör ett virtuellt nätverk stoppas de och frigörs under förberedelseåtgärden. Om du inte vill förlora den offentliga ip-adressen kan du prova att reservera ip-adressen innan du aktiverar förberedelseåtgärden. Om de virtuella datorerna finns i ett virtuellt nätverk kommer de dock varken att stoppas eller frigöras.
* Lägg migreringen utanför kontorstid för att lättare kunna hantera eventuella fel som kan uppstå.
* Ladda ned den aktuella konfigurationen av dina virtuella datorer med hjälp av PowerShell, CLI-kommandon (kommandoradsgränssnitt) eller REST-API:er för att underlätta verifieringen när förberedelsesteget är klart.
* Uppdatera skript för automatisering/drift för att hantera Resource Manager-distributionsmodellen innan du påbörjar migreringen. Du kan också utföra GET-åtgärder när resurserna är i förberedelsefasen.
* Utvärdera de RBAC-principer som har konfigurerats på de klassiska IaaS-resurserna och planera för tiden efter migreringen.

Migreringsarbetsflödet ser ut så här

![Skärmbild som visar migreringsarbetsflödet](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Alla åtgärder beskrivs i följande avsnitt är idempotenta. Om du har andra problem än med funktioner som inte stöds eller konfigurationsfel, kan du försöka göra om åtgärden för att förbereda, avbryta eller checka in. Åtgärden utförs igen på Azure-plattformen.
>
>

### <a name="validate"></a>Verifiera
Verifiering är det första steget under migreringen. Målet är att analysera data i bakgrunden för resurserna under migrering och returnera lyckat/misslyckat för huruvida resurserna kan migreras.

Du väljer det virtuella nätverk eller den värdbaserade tjänst (om det inte är ett virtuellt nätverk) som du vill verifiera för migrering.

* Om en resurs inte kan migreras visas en lista med alla orsaker till detta på Azure-plattformen.

När lagringstjänster verifieras finns det migrerade kontot i en resursgrupp med samma namn som lagringskontot följt av texten ” -Migrated”.  Om lagringskontot exempelvis har namnet ”mystorage”, finns den Azure Resource Manager-aktiverade resursen i en resursgrupp med namnet ”mystorage-Migrated” och innehåller ett lagringskonto med namnet ”mystorage”.

### <a name="prepare"></a>Förbereda
Förberedelse är det andra steget under migreringen. Målet med det här steget är att simulera omvandlingen av IaaS-resurser från klassiskt läge till Resource Manager-resurser och presentera dem sida vid sida.

Du väljer det virtuella nätverk eller den värdbaserade tjänst (om det inte är ett virtuellt nätverk) som du vill förbereda för migrering.

* Om en resurs inte kan migreras stoppas migreringen och en lista visas på Azure-plattformen med orsakerna till detta.
* Om resursen kan migreras låser först Azure-plattformen alla hanteringsplanåtgärder för resurserna under migreringen. Du kan till exempel inte lägga till en datadisk i en virtuell dator under migreringen.

Azure-plattformen startar sedan migreringen av metadata från klassiskt läge till Resource Manager-läge för de migrerande resurserna.

När förberedelseåtgärden är klar har du möjlighet att visa resurserna i både klassiskt läge och Resource Manager-läge. För varje molntjänst i den klassiska distributionsmodellen skapar Azure-plattformen ett resursgruppnamn som har mönstret `cloud-service-name>-Migrated`.

> [!NOTE]
> Du kan inte ändra namn på den resursgrupp som har skapats för migrerade resurser (t.ex ”-Migrerad”) men när migreringen är klar kan du använda flyttfunktionen i Azure Resource Manager för att flytta resurser till valfri resursgrupp. Mer information om det här finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../articles/resource-group-move-resources.md)

Här är två skärmar som visar resultatet av en lyckad förberedelseåtgärd. På den första skärmen visas en resursgrupp som innehåller den ursprungliga molntjänsten. På den andra skärmen visas den nya resursgruppen ”-Migrerad” som innehåller motsvarande Azure Resource Manager-resurser.

![Skärmbild som visar Portal klassisk molntjänst](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Skärmbild som visar Portal Azure Resource Manager-resurser i förberedelsesteget](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

> [!NOTE]
> Virtuella datorer som inte tillhör ett klassiskt virtuellt nätverk stoppas och frigörs under den här fasen av migreringen.
>
>

### <a name="check-manual-or-scripted"></a>Kontrollera (manuellt eller via skript)
Under kontrollfasen kan du, om du vill, använda den konfiguration som du laddade ned tidigare för att kontrollera att migreringen ser bra ut. Eller också kan du logga in på portalen och stickkontrollera egenskaper och resurser för att verifiera att metadatamigreringen ser bra ut.

Om du migrerar ett virtuellt nätverk startas inte de flesta konfigurationer av virtuella datorer om. Du kan kontrollera att program på de här virtuella datorerna fortfarande är aktiva och körs.

Du kan testa dina skript för övervakning/automatisering och drift för att se om de virtuella datorerna och de uppdaterade skripten fungerar som de ska. Det finns bara stöd för GET-åtgärder när resurserna är i förberedelsefasen.

Det finns ingen tidsgräns för när du måste checka in migreringen. Du kan ta den tid du vill i det här läget. Dock låses hanteringsplanet för de här resurserna tills du antingen avbryter eller checkar in.

Om problem skulle uppstå kan du alltid avbryta migreringen och gå tillbaka till den klassiska distributionsmodellen. När du har gått tillbaka öppnar Azure-plattformen hanteringsplanåtgärderna för resurser så att du kan fortsätta med vanliga åtgärder på de virtuella datorerna i den klassiska distributionsmodellen.

### <a name="abort"></a>Avbryta
Avbryta är ett valfritt steg du kan använda för att återställa ändringarna till den klassiska distributionsmodellen och stoppa migreringen.

> [!NOTE]
> Du kan inte utföra den här åtgärden när du har aktiverat incheckning.     
>
>

### <a name="commit"></a>Checka in
När verifieringen är klar kan du checka in migreringen. Resurserna visas inte längre i den klassiska modellen utan är bara tillgängliga i Resource Manager-distributionsmodellen. De migrerade resurserna kan bara hanteras i den nya portalen.

> [!NOTE]
> Det här är en idempotent åtgärd. Om den misslyckas rekommenderas du att försöka igen. Om felet återkommer kan du skapa ett supportärende eller göra ett foruminlägg med en ClassicIaaSMigration-tagg på vårt [forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>
<br>
Här är ett flödesschema över stegen i en migreringsprocess

![Skärmbild som visar migreringsstegen](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-classic-to-azure-resource-manager-resources"></a>Omvandling av klassiska resurser till Azure Resource Manager-resurser
I följande tabell visas resurserna i både klassiskt läge och Resource Manager-läge. Andra funktioner och resurser stöds för närvarande inte.

| Klassiskt läge | Resource Manager-läge | Detaljerad information |
| --- | --- | --- |
| Molntjänstens namn |DNS-namn |Under migreringen skapas en ny resursgrupp för varje molntjänst med namngivningsmönstret `<cloudservicename>-migrated`. Den här resursgruppen innehåller alla dina resurser. Namnet på molntjänsten blir ett DNS-namn som kopplas till den offentliga ip-adressen. |
| Virtuell dator |Virtuell dator |Egenskaper för virtuella datorer migreras oförändrade. En del osProfile-information, till exempel datornamn, lagras inte i den klassiska distributionsmodellen utan förblir tom efter migreringen. |
| Diskresurser anslutna till den virtuella datorn |Implicita diskar anslutna till den virtuella datorn |Diskarna utformas inte som toppresurser i Resource Manager-distributionsmodellen. De migreras som implicita diskar för den virtuella datorn. För närvarande finns bara stöd för diskar som är anslutna till en virtuell dator. Virtuella Resource Manager-datorer kan nu använda klassiska lagringskonton vilket gör det möjligt att migrera diskar utan någon uppdatering. |
| VM-tillägg |VM-tillägg |Alla resurstillägg utom XML-tillägg migreras från den klassiska distributionsmodellen. |
| Certifikat för virtuell dator |Certifikat i Azure Key Vault |Om en molntjänst innehåller tjänstcertifikat skapas en ny Azure Key Vault per molntjänst och certifikaten flyttas till nyckelvalvet. De virtuella datorerna uppdateras för att hänvisa till certifikaten från nyckelvalvet. <br><br> **OBS!** Ta inte bort nyckelvalvet eftersom detta kan medföra att den virtuella datorn hamnar i feltillstånd. Vi arbetar på att förbättra saker och ting i serverdelen så att nyckelvalv ska kunna tas bort eller flyttas med virtuella datorn till en ny prenumeration. |
| WinRM-konfiguration |WinRM-konfiguration under osProfile |Windows Remote Management-konfigurationen flyttas oförändrad under migreringen. |
| Egenskap för tillgänglighetsuppsättning |Resurs för tillgänglighetsuppsättning | Specifikationen för tillgänglighetsuppsättningen var en egenskap på den virtuella datorn i den klassiska distributionsmodellen. Under migreringen blir tillgänglighetsuppsättningar en resurs på toppnivå. Följande konfigurationer stöds inte: flera tillgänglighetsuppsättningar per molntjänst eller en eller flera tillgänglighetsuppsättningar tillsammans med virtuella datorer som inte tillhör en tillgänglighetsuppsättning i en molntjänst. |
| Nätverkskonfiguration på en virtuell dator |Primärt nätverksgränssnitt |Nätverkskonfigurationen på en virtuell dator visas som en resurs för primärt nätverksgränssnitt efter migreringen. För virtuella datorer som inte tillhör något virtuellt nätverk ändras den interna ip-adressen under migreringen. |
| Flera nätverksgränssnitt på en virtuell dator |Nätverksgränssnitt |Om flera nätverksgränssnitt är kopplade till en virtuell dator blir varje nätverksgränssnitt en resurs på toppnivå under migreringen i Resource Manager-distributionsmodellen, tillsammans med alla egenskaper. |
| Belastningsutjämnad slutpunktsuppsättning |Belastningsutjämnare |I den klassiska distributionsmodellen kopplade plattformen en implicit belastningsutjämnare till varje tjänst i molnet. Under migreringen skapas en ny belastningsutjämnarresurs och den belastningsutjämnade slutpunktsuppsättningen blir belastningsutjämnarregler. |
| Ingående NAT-regler |Ingående NAT-regler |Indataslutpunkter som har angetts i den virtuella datorn omvandlas till inkommande regler för nätverksadressöversättning i belastningsutjämnaren under migreringen. |
| Virtuell ip-adress |Offentlig ip-adress med DNS-namn |Den virtuella ip-adressen blir en offentlig ip-adress och kopplas till belastningsutjämnaren. En virtuell ip-adress kan bara migreras om en indataslutpunkt är kopplad till den. |
| Virtuellt nätverk |Virtuellt nätverk |Det virtuella nätverket migreras, med alla egenskaper, till Resource Manager-distributionsmodellen. En ny resursgrupp skapas med namnet `-migrated`. |
| Reserverade ip-adresser |Offentlig ip-adress med fast fördelningsmetod |Reserverade ip-adresser som är kopplade till belastningsutjämnaren migreras tillsammans med molntjänsten eller den virtuella datorn. För närvarande finns inte stöd för migrering av reserverade ip-adresser utan koppling. |
| Offentlig ip-adress per virtuell dator |Offentlig ip-adress med dynamisk fördelningsmetod |Den offentliga ip-adress som är kopplad till den virtuella datorn omvandlas som offentlig ip-adressresurs med fördelningsmetoden inställd på fas. |
| NSG:er |NSG:er |Nätverkssäkerhetsgrupper som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Nätverkssäkerhetsgruppen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för nätverkssäkerhetsgrupperna under migreringen. |
| DNS-servrar |DNS-servrar |DNS-servrar som är kopplade till ett virtuellt nätverk eller den virtuella datorn migreras under motsvarande resursmigrering tillsammans med alla egenskaper. |
| Användardefinierade vägar |Användardefinierade vägar |Användardefinierade vägar som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Den användardefinierade vägen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för den användardefinierade vägen under migreringen. |
| Egenskapen ip-vidarebefordran i en virtuell dators nätverkskonfiguration |Egenskapen ip-vidarebefordran på nätverkskortet |Egenskapen ip-vidarebefordran i en virtuell dator omvandlas till en egenskap i nätverksgränssnittet under migreringen. |
| Belastningsutjämnare med flera ip-adresser |Belastningsutjämnare med flera offentliga ip-resurser |Alla offentliga ip-adresser som är kopplade till belastningsutjämnaren omvandlas till en offentlig ip-resurs och kopplas till belastningsutjämnaren efter migreringen. |
| Interna DNS-namn på den virtuella datorn |Interna DNS-namn på nätverkskortet |Interna DNS-suffix för de virtuella datorerna migreras till en skrivskyddad egenskap med namnet ”InternalDomainNameSuffix” på nätverkskortet. Suffixet förblir oförändrad efter migreringen och virtuell datormatchning bör fortsätta att fungera som tidigare. |
| Virtuell nätverksgateway |Virtuell nätverksgateway |Egenskaperna för virtuell nätverksgateway migreras oförändrade. Den virtuella ip-adressen som är kopplad till gatewayen ändras inte heller. |
| Lokal nätverksplats |Lokal nätverksgateway |Egenskaperna för lokal nätverksplats migreras oförändrade till en ny resurs med namnet lokala nätverksgateway. Det här motsvarar lokala adressprefix och fjärrgateway-ip. |
| Anslutningsreferenser |Anslutning |Anslutningsreferenser mellan gateway och lokal nätverksplats i nätverkskonfigurationen visas som en nyskapad resurs med namnet Connection i Resource Manager efter migreringen. Alla egenskaper för anslutningsreferensen i nätverkskonfigurationen kopieras oförändrade till den nya Connection-resursen. Anslutningar mellan virtuella nätverk i klassiskt läge uppnås genom att skapa två IPsec-tunnlar till lokala nätverksplatser som motsvarar de virtuella nätverken. Det här omvandlas till anslutningstypen Vnet2Vnet i Resource Manager-modellen utan att några lokala nätverksgatewayer krävs. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Ändringar av automatiseringstjänster och verktyg efter migreringen
Under migreringen av dina resurser från den klassiska distributionsmodellen till Resource Manager-distributionsmodellen måste du uppdatera dina befintliga automatiseringstjänster och verktyg så att de fortsätter att fungera efter migreringen.
