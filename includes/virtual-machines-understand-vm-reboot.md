Virtuella Azure-datorer (VM) kan ibland startas utan någon uppenbar anledning utan din har initierat åtgärden omstart. Den här artikeln innehåller åtgärder och händelser som kan orsaka virtuella datorer startas om och ger kunskaper om hur du undviker du problem med oväntad omstart eller minska effekten av sådana problem.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurera virtuella datorer för hög tillgänglighet
Det bästa sättet att skydda ett program som körs på Azure mot VM startar om och driftstopp är att konfigurera virtuella datorer för hög tillgänglighet.

Om du vill ange den här nivån i ditt program med redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse, är tillgänglig och uppfyller 99,95% [Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Mer information om tillgänglighetsuppsättningar finns i följande artiklar:

- [Hantera tillgängligheten för virtuella datorer](../articles/virtual-machines/windows/manage-availability.md)
- [Konfigurera tillgängligheten för virtuella datorer](../articles/virtual-machines/windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Information om hälsa 
Azure Resource Health är en tjänst som visar hälsotillståndet för enskilda Azure-resurser och tillämplig vägledning för att felsöka problem. I en molnmiljö där det går inte att direkt åtkomst till servrar eller infrastruktur, är syftet med Resource Health att minska den tid som du ägnar åt felsökning. I synnerhet är är målet att minska den tid som du ägnar åt att fastställa om roten för problemet ligger i programmet eller i en händelse i Azure-plattformen. Mer information finns i [förstå och använda Resource Health](../articles/resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Åtgärder och händelser som kan göra att den virtuella datorn startas om

### <a name="planned-maintenance"></a>Planerat underhåll
Microsoft Azure utför uppdateringar regelbundet världen att förbättra tillförlitlighet, prestanda och säkerhet för den underliggande VMs värd-infrastrukturen. Många av dessa uppdateringar, inklusive minne bevara uppdateringar utförs utan någon inverkan på din virtuella datorer eller molntjänster.

Vissa uppdateringar kräver en omstart. I sådana fall kan stänga de virtuella datorerna av medan vi korrigering infrastrukturen och sedan de virtuella datorerna startas om.

För att förstå vilka Azure planerat underhåll är och hur de kan påverka tillgängligheten för din virtuella Linux-datorer finns i artiklarna i den här listan. Artiklarna innehåller information om Azure planerade underhållet och hur du schemalägger planerat underhåll att ytterligare minska påverkan.

- [Planerat underhåll för virtuella datorer i Azure](../articles/virtual-machines/windows/planned-maintenance.md)
- [Så här schemalägger du planerat underhåll på virtuella Azure-datorer](../articles/virtual-machines/windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Minnesbevarande uppdateringar   
För den här klassen för uppdateringar i Microsoft Azure märker användarna utan inverkan på sina virtuella datorer som körs. Många av de här uppdateringarna är till komponenter eller tjänster som kan uppdateras utan att störa den instans som körs. Vissa är plattform infrastrukturuppdateringar på värdens operativsystem som kan användas utan en omstart av de virtuella datorerna.

Uppdateringarna minne bevara utförs med teknik som möjliggör Direktmigrering på plats. När den uppdateras den virtuella datorn placeras i en *pausats* tillstånd. Det här tillståndet bevarar minnet i RAM-minne medan det underliggande operativsystemet för värden får nödvändiga uppdateringar och korrigeringar. Den virtuella datorn återupptas inom 30 sekunder från paus. Klockan synkroniseras automatiskt när den virtuella datorn återupptas.

På grund av kort paus period minskar distribuera uppdateringar via den här mekanismen avsevärt inverkan på virtuella datorer. Inte alla uppdateringar kan inte distribueras på detta sätt. 

Flera instanser uppdateringar (för virtuella datorer i en tillgänglighetsuppsättning) är tillämpade en uppdateringsdomän i taget.

> [!NOTE]
> Linux-datorer som har gamla kernel-versioner som påverkas av en kernel-panik under den här metoden update. Undvik problemet genom att uppdatera till kernel version 3.10.0-327.10.1 eller senare. Mer information finns i [ett virtuella Azure Linux-datorn på en 3.10-baserade kernel panics efter en uppgradering av värden nod](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>Användarinitierad åtgärder för omstart eller avstängning
 
Om du utför en omstart från Azure-portalen, Azure PowerShell, kommandoradsgränssnitt eller återställa API, hittar du händelsen i den [Azure-aktivitetsloggen](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Om du utför åtgärden från den Virtuella datorns operativsystem kan hitta du händelsen i systemloggarna.

Andra scenarier som orsakar vanligen VM att starta om datorn innehåller flera konfigurationsändring åtgärder. Normalt visas ett varningsmeddelande som visar att köra en viss åtgärd leder till att en omstart av den virtuella datorn. Exempel inkluderar några VM storleksändring åtgärder, ändra lösenordet för administratörskontot och ange en statisk IP-adress.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center och Windows Update
Azure Security Center övervakar dagliga Windows och Linux virtuella datorer efter uppdateringar av operativsystemet. Security Center hämtar en lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services (WSUS), beroende på vilken tjänsten har konfigurerats på en Windows-VM. Säkerhetscenter kontrollerar också för de senaste uppdateringarna för Linux-system. Om den virtuella datorn saknas för en uppdatering av system, rekommenderar Security Center att du installerar uppdateringar. Tillämpning av uppdateringarna system styrs via Security Center i Azure-portalen. När du installerar vissa uppdateringar kan det krävas VM omstarter. Mer information finns i [tillämpa uppdateringar i Azure Security Center](../articles/security-center/security-center-apply-system-updates.md).

Som lokala servrar tvingar Azure inte uppdateringar från Windows Update till Windows Azure virtuella datorer, eftersom dessa datorer är avsedda att hanteras av användarna. Du är dock att lämna inställningen för automatisk uppdatering av Windows. Automatisk installation av uppdateringar från Windows Update kan också medföra omstarter när uppdateringarna tillämpas. Mer information finns i [Windows Update FAQ](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Andra situationer som påverkar tillgängligheten för den virtuella datorn
Det finns andra fall där Azure aktivt kan inaktivera användningen av en virtuell dator. Får du e-postmeddelanden innan den här åtgärden tas, så har du möjlighet att lösa eventuella underliggande problem. Exempel på problem som påverkar tillgängligheten till VM är säkerhetsöverträdelser och förfallodatum för betalningssätt.

### <a name="host-server-faults"></a>Värd-serverfel 
Den virtuella datorn finns på en fysisk server som körs i ett Azure-datacenter. Den fysiska servern kör en agent kallas Värdagenten förutom några andra Azure-komponenter. När dessa Azure programkomponenter på den fysiska servern inte svarar utlöser övervakningssystemet en omstart av värdservern att försöka återskapa. Den virtuella datorn är vanligtvis tillgänglig igen inom fem minuter och fortsätter att live på samma värddator som tidigare.

Serverfel orsakas vanligtvis av maskinvarufel, till exempel fel på en hårddisk eller en SSD-enhet. Azure kontinuerligt övervakar dessa händelser, identifierar underliggande buggar och samlar ut uppdateringar när minskning har implementerat och testas.

Eftersom vissa värden serverfel kan vara specifika för servern, kan en upprepad VM omstart situation förbättras genom att manuellt distribuera den virtuella datorn till en annan värdserver. Den här åtgärden kan aktiveras med hjälp av den **omdistribuera** alternativ på informationssidan för den virtuella datorn eller genom att stoppa och starta om den virtuella datorn i Azure-portalen.

### <a name="auto-recovery"></a>Automatisk återställning
Om värdservern inte kan startas om av någon anledning, initierar en automatisk återställning åtgärd att vidta felaktiga värdservern utanför rotation för ytterligare undersökning i Azure-plattformen. 

Alla virtuella datorer på värden flyttas automatiskt till en annan, felfri värdserver. Det är vanligtvis klart inom 15 minuter. Mer information om processen för automatisk återställning finns [automatisk återställning av virtuella datorer](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Oplanerat Underhåll
I sällsynta fall kan åtgärder i Azure-teamet behöver utföra underhållsaktiviteter för att säkerställa den övergripande hälsan för Azure-plattformen. Det här beteendet kan påverka tillgängligheten för Virtuella och den resulterar vanligtvis i samma åtgärd för automatisk återställning som tidigare beskrivits.  

Oplanerad maintenances inkluderar följande:

- Brådskande nod defragmentering
- Brådskande nätverket växeln uppdateringar

### <a name="vm-crashes"></a>VM kraschar
Virtuella datorer kan starta om på grund av problem med i Virtuellt datorn. Arbetsbelastning eller roll som körs på den virtuella datorn kan utlösa en bugg kontroll i gästoperativsystemet. Hjälp med att fastställa orsaken till kraschen visa i systemet och programloggarna för virtuella Windows-datorer och seriella loggarna för Linux virtuella datorer.

### <a name="storage-related-forced-shutdowns"></a>Lagringsrelaterade framtvingad avstängningar
Virtuella datorer i Azure förlitar sig på virtuella diskar för operativsystemet och lagring av data som finns i Azure Storage-infrastruktur. När mer än 120 sekunder påverkas tillgänglighet eller anslutningen mellan den virtuella datorn och de associerade virtuella diskarna, utför en framtvingad avstängning på de virtuella datorerna för att undvika att data skadas i Azure-plattformen. De virtuella datorerna är automatiskt igång igen efter lagringsanslutning har återställts. 

Varaktighet för avstängningen kan vara så kort som fem minuter, men kan vara betydligt längre. Följande är en av de särskilda fall som är associerad med lagringsrelaterade framtvingad avstängningar: 

**Överstiger IO begränsar**

Virtuella datorer kan vara tillfälligt stänga när i/o-begäranden är konsekvent begränsas eftersom i/o-åtgärder per sekund (IOPS) överskrider de i/o-gränserna för disken. (Standard disklagring är begränsad till 500 IOPS.) Om du vill undvika det här problemet använder disk striping eller konfigurera lagringsutrymmet i gäst-VM, beroende på arbetsbelastningen. Mer information finns i [Konfigurera virtuella Azure-datorer för optimala lagringsprestanda](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Högre IOPS-gränser är tillgängliga via Azure Premium-lagring med upp till 80 000 IOPS. Mer information finns i [Premium-lagring med höga prestanda](../articles/virtual-machines/windows/premium-storage.md).

### <a name="other-incidents"></a>Andra incidenter
I sällsynta fall kan kan ett omfattande problem påverka flera servrar i ett Azure-datacenter. Om det här problemet uppstår, skickar Azure-teamet e-postmeddelanden till berörda prenumerationer. Du kan kontrollera den [hälsoinstrumentpanel för Azure Service](https://azure.microsoft.com/status/) och Azure portal för status för pågående avbrott och tidigare incidenter.
