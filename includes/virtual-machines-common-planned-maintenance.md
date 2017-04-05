

## <a name="memory-preserving-updates"></a>Minnesbevarande uppdateringar
För en klass med uppdateringar i Microsoft Azure kan kunder inte se någon inverkan på sina virtuella datorer som körs. Många av de här uppdateringarna är till komponenter eller tjänster som kan uppdateras utan att störa den instans som körs. Några av de här uppdateringarna är plattformsinfrastrukturuppdateringar i värdoperativsystemet som kan tillämpas utan att kräva en fullständig omstart av de virtuella datorerna.

De här uppdateringarna åstadkoms med teknik som aktiverar livemigrering på plats, vilket även kallas för en ”minnesbevarande” uppdatering. Vid uppdateringen placeras den virtuella datorn i ett ”pausat” läge, och bevarar minnet i RAM-minnet, medan det underliggande operativsystemet får nödvändiga uppdateringar och korrigeringar. Den virtuella datorn återupptas inom 30 sekunder efter paus. När den virtuella datorn har återupptagits synkroniseras klockan automatiskt.

Alla uppdateringar kan inte distribueras med den här mekanismen, men med tanke på den korta pausperioden minskar distributionen av uppdateringar på det här sättet avsevärt inverkan på virtuella datorer.

Multi-instansuppdateringar (för virtuella datorer i en tillgänglighetsuppsättning) tillämpas med en uppdateringsdomän i taget.  

## <a name="virtual-machine-configurations"></a>Konfigurationer av virtuella datorer
Det finns två typer av virtuella datorkonfigurationer: multi-instans och en instans. I en multi-instanskonfiguration placeras liknande virtuella datorer i en tillgänglighetsuppsättning.

Multi-instanskonfigurationen ger redundans för fysiska datorer, kraft och nätverk, och vi rekommenderar att du kontrollerar programmets tillgänglighet. Alla virtuella datorer i tillgänglighetsuppsättningen ska ha samma syfte för programmet.

Mer information om hur du konfigurerar dina virtuella datorer för hög tillgänglighet finns i [Manage the availability of your Windows virtual machines](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Hantera tillgängligheten för dina virtuella Windows-datorer) eller [Manage the availability of your Linux virtual machines](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Hantera tillgängligheten för dina virtuella Linux-datorer).

En konfiguration med en instans används däremot för fristående virtuella datorer som inte är placerade i en tillgänglighetsuppsättning. Sådana virtuella datorer uppfyller inte kraven för serviceavtalet (SLA) som kräver distribution av två eller flera virtuella datorer i samma tillgänglighetsuppsättning.

Mer information om serviceavtal finns i avsnittet om molntjänster och virtuella datorer ("Cloud Services and Virtual Machines)" i [serviceavtalen](https://azure.microsoft.com/support/legal/sla/).

## <a name="multi-instance-configuration-updates"></a>Konfigurationsuppdateringar med multi-instans
Under planerat underhåll uppdaterar Azure-plattformen först uppsättningen virtuella datorer som finns i en multi-instanskonfiguration. Uppdateringen orsakar en omstart av de virtuella datorerna med cirka 15 minuters avbrott.

En uppdatering av en multi-instanskonfiguration förutsätter att varje virtuell dator har en liknande funktion som de övriga i tillgänglighetsuppsättningen. Med den här inställningen uppdateras virtuella datorer på ett sätt som bevarar tillgängligheten under hela processen.

Varje virtuell dator i en tillgänglighetsuppsättning tilldelas en uppdateringsdomän och en feldomän av den underliggande Azure-plattformen. Varje uppdateringsdomän är en grupp med virtuella datorer som startas om i samma tidsfönster. Varje feldomän är en grupp av virtuella datorer som delar samma strömkälla och nätverksswitch.


Mer information om uppdateringsdomäner och feldomäner finns i [Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Om du vill upprätthålla tillgänglighet via en uppdatering utför Azure underhåll av uppdateringsdomänen och uppdaterar en domän i taget. Underhållet i en uppdateringsdomän består av nedstängning av alla virtuella datorer i domänen, tillämpning av uppdateringen till värddatorerna och sedan omstart av de virtuella datorerna. När underhållet i domänen är klart upprepar Azure processen med nästa uppdateringsdomän och fortsätter med varje domän tills alla är uppdaterade.

Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om. Idag erbjuder Azure meddelanden en vecka i förväg för planerat underhåll av virtuella datorer i en multi-instanskonfiguration.

När en virtuell dator har återställts följer här ett exempel på vad Windows Loggboken kan visa:

<!--Image reference-->
![][image2]


Använd loggboken för att rapportera vilka virtuella datorer som konfigureras i en multi-instanskonfiguration med Azure Portal, Azure PowerShell eller Azure CLI. Med Azure Portal kan du exempelvis lägga till _tillgänglighetsuppsättningen_ till bläddringsdialogrutan **Virtuella datorer (klassiska)**. De virtuella datorer som rapporterar samma tillgänglighetsuppsättning är en del av multi-instanskonfigurationen. I följande exempel består multi-instanskonfigurationen av de virtuella datorerna SQLContoso01 och SQLContoso02.

<!--Image reference-->
  ![Vyn Virtuella datorer (klassiska) från Azure Portal][image4]

## <a name="single-instance-configuration-updates"></a>Konfigurationsuppdateringar med en instans
När uppdateringarna av multi-instanskonfigurationen är klara utför Azure uppdateringar för konfigurationer med en instans. De här uppdateringarna också orsaka omstart av dina virtuella datorer som inte körs i tillgänglighetsuppsättningar.

> [!NOTE]
> Om en tillgänglighetsuppsättning endast har en virtuell instans som körs behandlar Azure-plattformen den som en multi-instanskonfigurationsuppdatering.
>

Underhåll i en konfiguration med en instans innebär att stänga av varje virtuell dator som körs på en värddator, uppdatera värddatorn och sedan starta om de virtuella datorerna. Underhållet kräver cirka 15 minuters avbrott. Det planerade underhållet körs för alla virtuella datorer i en region i en enda underhållsperiod.


Planerat underhåll påverkar tillgängligheten för ditt program för konfigurationer med en instans. Azure skickar meddelanden en vecka i förväg för planerat underhåll av virtuella datorer i en konfiguration med en instans.

## <a name="email-notification"></a>E-postavisering
För virtuella datorer som har konfigurerats med en instans eller multi-instanser skickar Azure en e-postavisering om det kommande planerade underhållet (en vecka i förväg). E-postmeddelandet skickas till prenumerationens administratörs och medadministratörens e-postkonton. Här är ett exempel på den här typen av e-postmeddelande:

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>Regionpar

När underhåll körs uppdaterar Azure endast instanser av virtuella datorer i en enda region för paren. När du till exempel uppdaterar de virtuella datorerna i regionen USA, norra centrala så uppdaterar inte Azure inte alla virtuella datorer i regionen USA, södra centrala på samma gång. Detta schemaläggs vid en separat tid för att möjliggöra redundans eller belastningsutjämning mellan regioner. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra.

Se följande tabell för aktuella regionspar:

| Region 1 | Region 2 |
|:--- | ---:|
| Östra USA |Västra USA |
| Östra USA 2 |Centrala USA |
| Norra centrala USA |Södra centrala USA |
| Västra centrala USA |Västra USA 2 |
| Östra Kanada |Centrala Kanada |
| Södra Brasilien |Södra centrala USA |
| Iowa (USA-förvaltad region) |Virginia (USA-förvaltad region) |
| US DoD, östra |US DoD, centrala |
| Norra Europa |Västra Europa |
| Storbritannien, västra |Storbritannien, södra |
| Centrala Tyskland |Nordöstra Tyskland |
| Sydostasien |Östasien |
| Sydöstra Australien |Östra Australien |
| Centrala Indien |Södra Indien |
| Västra Indien |Södra Indien |
| Östra Japan |Västra Japan |
| Centrala Korea |Sydkorea |
| Östra Kina |Norra Kina |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/windows/manage-availability.md#Understand-planned-versus-unplanned-maintenance/
