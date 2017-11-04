## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrera IaaS-resurser från den klassiska distributionsmodellen till Azure Resource Manager
Först, är det viktigt att förstå skillnaden mellan data-plan och management-plan åtgärder på infrastrukturen som en tjänst (IaaS)-resurser.

* *Kontroll och Management plan* beskriver anrop som kommer till kontroll och management-plan eller API: et för att ändra resurser. Åtgärder som exempelvis skapa en virtuell dator, starta om en virtuell dator och uppdatera ett virtuellt nätverk med ett nytt undernät, hanterar de resurser som körs. De påverkar direkt inte ansluta till de virtuella datorerna.
* *Dataplan* (program) beskriver körtiden för programmet och inbegriper interaktion med instanser som inte gå igenom Azure API. Till exempel åtkomst till din webbplats eller hämta data från en SQL Server-instans som körs eller MongoDB-servern är data interaktioner plana eller ett program. Andra exempel på Kopiera en blobb från ett lagringskonto och åtkomst till en offentlig IP-adress för att använda Remote Desktop Protocol (RDP) eller SSH (Secure Shell) till den virtuella datorn. Med de här åtgärderna kan programmet köras i beräknings-, nätverks- och lagringsresurser.

Dataplan är samma mellan den klassiska distributionsmodellen och Resource Manager stackar. Skillnaden är att Microsoft under migreringsprocessen, översätter representation av resurser från den klassiska distributionsmodellen som i Resource Manager-stacken. Du måste därför använda nya verktyg, API: er och SDK: er för att hantera dina resurser i Resource Manager-stacken.

![Diagram som visar skillnaden mellan kontroll och management-plan och dataplan](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> I vissa migreringsfall stoppas, frigörs och startas dina virtuella datorer om av Azure-plattformen. Detta leder till ett kort driftstopp för data-plan.
>

## <a name="the-migration-experience"></a>Migreringen
Innan du påbörjar migreringen:

* Se till att inga funktioner eller konfigurationer som inte stöds används av de resurser du vill migrera. Vanligtvis upptäcks de här problemen och plattformen genererar ett fel.
* Om du har virtuella datorer som inte ingår i ett virtuellt nätverk de stoppats och frigöra som en del av Förbered igen. Överväg att reservera IP-adressen innan Förbered igen om du inte vill förlora den offentliga IP-adressen. Om de virtuella datorerna är i ett virtuellt nätverk kan de inte stoppats och frigöra.
* Lägg migreringen utanför kontorstid för att lättare kunna hantera eventuella fel som kan uppstå.
* Ladda ned den aktuella konfigurationen av dina virtuella datorer med hjälp av PowerShell, CLI-kommandon (kommandoradsgränssnitt) eller REST-API:er för att underlätta verifieringen när förberedelsesteget är klart.
* Uppdatera ditt automation och operationalization skript för att hantera distributionsmodell Resource Manager innan du påbörjar migreringen. Du kan också utföra GET-åtgärder när resurserna är i förberedelsefasen.
* Utvärdera principerna för rollbaserad åtkomstkontroll (RBAC) som är konfigurerade för IaaS-resurser i den klassiska distributionsmodellen och planera för när migreringen är klar.

Arbetsflödet för migrering är följande:

![Diagram som visar arbetsflödet för migrering](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Åtgärder som beskrivs i följande avsnitt är alla idempotent. Om du har problem med än en funktion som inte stöds eller ett konfigurationsfel försök prepare, avbrytas eller genomföras igen. Azure försöker igen.
>
>

### <a name="validate"></a>Verifiera
Verifiering är det första steget under migreringen. Målet med det här steget är att analysera statusen för de resurser som du vill migrera i den klassiska distributionsmodellen. Åtgärden utvärderar om resurserna kan migrering (lyckade eller misslyckade).

Du väljer det virtuella nätverket eller en tjänst i molnet (om den inte är i ett virtuellt nätverk) som du vill validera för migrering. Om resursen inte är kompatibla av migrering listar Azure skälen varför.

#### <a name="checks-not-done-in-the-validate-operation"></a>Kontrollerar inte utförs i åtgärden validera

Validera åtgärden analyserar bara Tillståndet för resurser i den klassiska distributionsmodellen. Det kan söka efter alla fel och scenarier som inte stöds på grund av olika konfigurationer i den klassiska distributionsmodellen. Det går inte att söka efter alla problem som Azure Resource Manager-stacken kan ge resurser under migreringen. De här problemen kontrolleras endast om resurserna genomgår omvandling i nästa steg för migreringen (Förberedelseåtgärden). I följande tabell visas alla problem som inte incheckad verifiera igen:


|Nätverk kontrollerar inte i Validera åtgärden|
|-|
|Ett virtuellt nätverk med både ER och VPN-gatewayer.|
|En gateway-anslutningen för virtuella nätverk i frånkopplat tillstånd.|
|Alla ER kretsar har redan migrerats till Azure Resource Manager-stacken.|
|Kvot för Azure Resource Manager kontrollerar nätverksresurser. Till exempel: statisk offentlig IP-adress, dynamiska offentliga IP-adresser, läsa in belastningsutjämning, nätverkssäkerhetsgrupper, vägtabeller och nätverksgränssnitt. |
| Alla regler för inläsning av belastningsutjämnare är giltiga över distributionen och det virtuella nätverket. |
| Motstridiga privata IP-adresser mellan frigjorts stoppa virtuella datorer i samma virtuella nätverk. |

### <a name="prepare"></a>Förbereda
Förberedelse är det andra steget under migreringen. Målet med det här steget är att simulera omvandling av IaaS-resurser från den klassiska distributionsmodellen till Resource Manager-resurser. Förberedelseåtgärden visas dessutom denna sida-vid-sida att visualisera.

> [!NOTE] 
> Dina resurser i den klassiska distributionsmodellen ändras inte under det här steget. Det är en säker steget körs om du försöker ut migrering. 

Markerar du det virtuella nätverket eller Molntjänsten (om det inte är ett virtuellt nätverk) som du vill förbereda för migrering.

* Om resursen inte är kompatibla av migrering Azure stoppar migreringsprocessen och visar anledningen till varför prepare-åtgärden misslyckades.
* Om resursen har funktioner för migrering, låser Azure driften management-plan för resurser under migreringen. Du kan till exempel inte lägga till en datadisk i en virtuell dator under migreringen.

Azure sedan startar migreringen av metadata från den klassiska distributionsmodellen till Resource Manager för migrera resurserna.

När Förberedelseåtgärden är klar har möjlighet att visualisera resurser från klassiska distributionsmodellen och Resource Manager. För varje molntjänst i den klassiska distributionsmodellen skapar Azure-plattformen ett resursgruppnamn som har mönstret `cloud-service-name>-Migrated`.

> [!NOTE]
> Det går inte att markera namnet på en resursgrupp som skapats för migrerade resurser (det vill säga ”-migreras”). När migreringen är klar, men kan du använda funktionen flyttningen av Azure Resource Manager för att flytta resurser till valfri resursgrupp som du vill använda. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../articles/resource-group-move-resources.md).

Följande två skärmbilderna visar resultaten efter en utförts förbereda åtgärden. Den första som visar en resursgrupp som innehåller ursprungliga Molntjänsten. Den andra visas den nya ”-migreras” resursgruppen som innehåller de motsvarande Azure Resource Manager-resurserna.

![Skärmbild som visar ursprungliga Molntjänsten](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Skärmbild som visar Azure Resource Manager-resurser i prepare-åtgärd](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Här är en dolda titt på dina resurser efter slutförandet av förberedningsfasen. Observera att resursen i dataplan är samma. Den representeras både hantering plan (klassiska distributionsmodellen) och kontrollplan (Resource Manager).

![Diagram över förberedningsfasen](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuella datorer som inte ingår i ett virtuellt nätverk i den klassiska distributionsmodellen stoppas och frigöra i den här fasen av migreringen.
>

### <a name="check-manual-or-scripted"></a>Kontrollera (manuellt eller via skript)
I steget för att kontrollera har du möjlighet att använda konfigurationen som du hämtade tidigare för att verifiera att migreringen verkar vara korrekta. Du kan också kan du logga in på portalen och plats egenskaper och resurser för att verifiera att metadata migrering ser bra ut.

Om du migrerar ett virtuellt nätverk startas inte de flesta konfigurationer av virtuella datorer om. För program på dessa virtuella datorer kan du verifiera att programmet körs fortfarande.

Du kan testa dina övervaknings- och operativa skript om de virtuella datorerna som fungerar som förväntat och om skripten uppdaterade fungerar korrekt. Det finns bara stöd för GET-åtgärder när resurserna är i förberedelsefasen.

Det finns ingen set tidsfönstret innan som du behöver genomföra migreringen. Du kan ta den tid du vill i det här läget. Dock låses hanteringsplanet för de här resurserna tills du antingen avbryter eller checkar in.

Om problem skulle uppstå kan du alltid avbryta migreringen och gå tillbaka till den klassiska distributionsmodellen. När du går tillbaka öppnar Azure management-plan åtgärder på resurser, så att du kan återuppta normal drift på dessa virtuella datorer i den klassiska distributionsmodellen.

### <a name="abort"></a>Avbryta
Detta är ett valfritt steg om du vill återställa dina ändringar i den klassiska distributionsmodellen och stoppa migreringen. Den här åtgärden tar bort metadata Resource Manager (som skapats i förberedelsesteget) för dina resurser. 

![Diagram över Avbryt steg](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Den här åtgärden kan inte utföras när du har utlösts commit-åtgärden.     
>

### <a name="commit"></a>Checka in
När verifieringen är klar kan du checka in migreringen. Resurser visas inte längre i den klassiska distributionsmodellen och är bara tillgängliga i Resource Manager-distributionsmodellen. De migrerade resurserna kan bara hanteras i den nya portalen.

> [!NOTE]
> Det här är en idempotent åtgärd. Om det misslyckas, och försök igen. Om det fortfarande misslyckas, skapa ett supportärende eller skapa ett foruminlägg med ”ClassicIaaSMigration”-tagg på vår [VM forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Diagram över commit steg](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Flödesschema för migrering

Här är ett flödesschema som visar hur du fortsätter med migreringen:

![Skärmbild som visar migreringsstegen](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Översättning av den klassiska distributionsmodellen till Resource Manager-resurser
Du hittar den klassiska distributionsmodellen och Resource Manager-representation av resurser i följande tabell. Andra funktioner och resurser stöds för närvarande inte.

| Klassiskt läge | Resource Manager-läge | Anteckningar |
| --- | --- | --- |
| Molntjänstens namn |DNS-namn |Under migreringen skapas en ny resursgrupp för varje molntjänst med namngivningsmönstret `<cloudservicename>-migrated`. Den här resursgruppen innehåller alla dina resurser. Namnet på molntjänsten blir ett DNS-namn som kopplas till den offentliga ip-adressen. |
| Virtuell dator |Virtuell dator |Egenskaper för virtuella datorer migreras oförändrade. Vissa osProfile information, t.ex. datornamn, lagras inte i den klassiska distributionsmodellen och förblir tom efter migreringen. |
| Diskresurser anslutna till den virtuella datorn |Implicita diskar anslutna till den virtuella datorn |Diskarna utformas inte som toppresurser i Resource Manager-distributionsmodellen. De migreras som implicita diskar för den virtuella datorn. För närvarande finns bara stöd för diskar som är anslutna till en virtuell dator. Hanteraren för virtuella datorer kan nu använda storage-konton i den klassiska distributionsmodellen, vilket gör att diskarna som enkelt kan migreras utan några uppdateringar. |
| VM-tillägg |VM-tillägg |Alla resurstillägg utom XML-tillägg migreras från den klassiska distributionsmodellen. |
| Certifikat för virtuell dator |Certifikat i Azure Key Vault |Om en tjänst i molnet innehåller tjänstcertifikat, migreringen skapar en ny Azure nyckelvalv per tjänst i molnet och flyttar certifikaten till nyckelvalvet. De virtuella datorerna uppdateras för att hänvisa till certifikaten från nyckelvalvet. <br><br> Ta inte bort nyckelvalvet. Detta kan orsaka att den virtuella datorn i ett felaktigt tillstånd. |
| WinRM-konfiguration |WinRM-konfiguration under osProfile |Windows Remote Management-konfigurationen flyttas oförändrad under migreringen. |
| Egenskap för tillgänglighetsuppsättning |Resurs för tillgänglighetsuppsättning | Tillgänglighetsuppsättningen specifikationen är en egenskap på den virtuella datorn i den klassiska distributionsmodellen. Under migreringen blir tillgänglighetsuppsättningar en resurs på toppnivå. Följande konfigurationer stöds inte: flera tillgänglighetsuppsättningar per molntjänst eller en eller flera tillgänglighetsuppsättningar tillsammans med virtuella datorer som inte tillhör en tillgänglighetsuppsättning i en molntjänst. |
| Nätverkskonfiguration på en virtuell dator |Primärt nätverksgränssnitt |Nätverkskonfigurationen på en virtuell dator visas som en resurs för primärt nätverksgränssnitt efter migreringen. För virtuella datorer som inte tillhör något virtuellt nätverk ändras den interna ip-adressen under migreringen. |
| Flera nätverksgränssnitt på en virtuell dator |Nätverksgränssnitt |Om en virtuell dator har flera nätverksgränssnitt som är kopplade till den, blir en översta resurs som en del av migreringen, tillsammans med alla egenskaper i varje nätverksgränssnitt. |
| Belastningsutjämnad slutpunktsuppsättning |Belastningsutjämnare |I den klassiska distributionsmodellen kopplade plattformen en implicit belastningsutjämnare till varje tjänst i molnet. Under migreringen skapas en ny belastningsutjämnarresurs och den belastningsutjämnade slutpunktsuppsättningen blir belastningsutjämnarregler. |
| Ingående NAT-regler |Ingående NAT-regler |Indataslutpunkter som har angetts i den virtuella datorn omvandlas till inkommande regler för nätverksadressöversättning i belastningsutjämnaren under migreringen. |
| Virtuell ip-adress |Offentlig ip-adress med DNS-namn |Den virtuella IP-adressen blir en offentlig IP-adress och är kopplad till belastningsutjämnaren. En virtuell ip-adress kan bara migreras om en indataslutpunkt är kopplad till den. |
| Virtuellt nätverk |Virtuellt nätverk |Det virtuella nätverket migreras, med alla egenskaper, till Resource Manager-distributionsmodellen. En ny resursgrupp skapas med namnet `-migrated`. |
| Reserverade ip-adresser |Offentlig ip-adress med fast fördelningsmetod |Reserverade ip-adresser som är kopplade till belastningsutjämnaren migreras tillsammans med molntjänsten eller den virtuella datorn. För närvarande finns inte stöd för migrering av reserverade ip-adresser utan koppling. |
| Offentlig ip-adress per virtuell dator |Offentlig ip-adress med dynamisk fördelningsmetod |Den offentliga ip-adress som är kopplad till den virtuella datorn omvandlas som offentlig ip-adressresurs med fördelningsmetoden inställd på fas. |
| NSG:er |NSG:er |Nätverkssäkerhetsgrupper som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Nätverkssäkerhetsgruppen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för nätverkssäkerhetsgrupperna under migreringen. |
| DNS-servrar |DNS-servrar |DNS-servrar som är kopplade till ett virtuellt nätverk eller den virtuella datorn migreras under motsvarande resursmigrering tillsammans med alla egenskaper. |
| Användardefinierade vägar |Användardefinierade vägar |Användardefinierade vägar som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Den användardefinierade vägen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för den användardefinierade vägen under migreringen. |
| Egenskapen ip-vidarebefordran i en virtuell dators nätverkskonfiguration |Egenskapen ip-vidarebefordran på nätverkskortet |Egenskapen ip-vidarebefordran i en virtuell dator omvandlas till en egenskap i nätverksgränssnittet under migreringen. |
| Belastningsutjämnare med flera ip-adresser |Belastningsutjämnare med flera offentliga ip-resurser |Alla offentliga IP-Adressen som är associerade med belastningsutjämnaren konverteras till en offentlig IP-resurs och som är associerade med belastningsutjämnaren efter migreringen. |
| Interna DNS-namn på den virtuella datorn |Interna DNS-namn på nätverkskortet |Interna DNS-suffix för de virtuella datorerna migreras till en skrivskyddad egenskap med namnet ”InternalDomainNameSuffix” på nätverkskortet. Suffixet förblir oförändrad efter migreringen och VM-lösning bör fortsätta att fungera som tidigare. |
| Virtuell nätverksgateway |Virtuell nätverksgateway |Gateway-egenskaper för virtuella nätverk migreras oförändrat. Den virtuella ip-adressen som är kopplad till gatewayen ändras inte heller. |
| Lokal nätverksplats |Lokal nätverksgateway |Egenskaper för lokalt nätverk migreras oförändrat till en ny resurs som kallas en lokal nätverksgateway. Detta representerar adressprefix för lokal och fjärr-gateway-IP. |
| Anslutningsreferenser |Anslutning |Referenser för anslutningen mellan gateway och den lokala nätverksplatsen i nätverkskonfigurationen representeras av en ny resurs kallas anslutning. Alla egenskaper för anslutning referens i nätverket konfigurationsfiler kopieras oförändrade till resursen anslutning. Anslutningen mellan virtuella nätverk i den klassiska distributionsmodellen uppnås genom att skapa två IPsec-tunnlar till lokala nätverksplatser som representerar de virtuella nätverken. Detta omvandlas till typen virtuella nätverk-till-virtuell-nätverksanslutning i Resource Manager-modellen utan lokala nätverksgatewayer. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Ändringar av automatiseringstjänster och verktyg efter migreringen
Som en del av migreringen dina resurser från den klassiska distributionsmodellen till Resource Manager-distributionsmodellen, måste du uppdatera din befintliga automation eller verktygsuppsättning så att den fortsätter att fungera efter migreringen.
