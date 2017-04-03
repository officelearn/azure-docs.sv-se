Den här artikeln innehåller en uppsättning beprövade metoder för att köra en virtuell Linux-dator på Azure, med hänsyn till skalbarhet, tillgänglighet, hanterbarhet och säkerhet. Azure har stöd för körning av olika populära Linux-distributioner, inklusive CentOS, Debian, Red Hat Enterprise, Ubuntu och FreeBSD. Mer information finns i [Azure och Linux][azure-linux].

> [!NOTE]
> Azure har två olika distributionsmodeller: [Resource Manager][resource-manager-overview] och klassisk. Den här artikeln använder Resurshanteraren, som Microsoft rekommenderar för nya distributioner.
> 
> 

Du bör inte använda en enskild virtuell dator för verksamhetskritiska arbetsbelastningar, eftersom den skapar en enskild felkälla. För högre tillgänglighet kan du distribuera flera virtuella datorer i en [tillgänglighetsuppsättning][availability-set]. Mer information finns i [Köra flera virtuella datorer på Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Arkitekturdiagram
Att etablera en virtuell dator i Azure innebär att flytta fler delar än bara själva det virtuella nätverket. Det finns beräknings-, nätverks- och lagringselement som du behöver tänka på.

> Ett Visio-dokument som innehåller det här arkitekturdiagrammet finns tillgängligt för hämtning från [Microsofts hämtningscenter][visio-download]. Det här diagrammet finns på sidan ”Beräkna – enskild virtuell dator”.
> 
> 

![[0]][0]

* **Resursgrupp.** En [*resursgrupp*][resource-manager-overview] är en behållare som innehåller närliggande resurser. Skapa en resursgrupp för att lagra resurser för den här virtuella datorn.
* **Virtuell dator**. Du kan etablera en virtuell dator från en lista över publicerade avbildningar eller från en virtuell hårddiskfil (VHD) som du överför till Azure Blob Storage.
* **OS-disk.** OS-disken är en virtuell hårddisk som lagras i [Azure Storage][azure-storage]. Det innebär att den finns kvar även om värddatorn stängs av. Operativsystemets disk är `/dev/sda1`.
* **Tillfällig disk.** Den virtuella datorn skapas med en tillfällig disk. Den här disken lagras på en fysisk enhet på värddatorn. Det sparas *inte* i Azure Storage och kan tas bort under omstarter och andra livscykelhändelser för virtuella datorer. Använd enbart den här disken för tillfälliga data, till exempel växlingsfiler. Den tillfälliga disken är `/dev/sdb1` och är monterad på `/mnt/resource` eller `/mnt`.
* **Datadiskar.** En [datadisk][data-disk] är en permanent virtuell hårddisk för programdata. Datadiskar lagras i Azure Storage, som OS-disken.
* **Virtuellt nätverk och undernät.** Varje virtuell dator i Azure distribueras till ett virtuellt nätverk som delas upp ytterligare i undernät.
* **Offentlig IP-adress.** En offentlig IP-adress krävs för att kommunicera med den virtuella datorn&mdash;till exempel via SSH.
* **Nätverksgränssnitt**. Nätverkskortet som gör det möjligt för den virtuella datorn att kommunicera med det virtuella nätverket.
* **Nätverkssäkerhetsgrupp (NSG)**. [NSG][nsg] används för att tillåta/neka nätverkstrafik till undernätet. Du kan associera en NSG med ett enskilt nätverkskort eller ett undernät. Om du kopplar den till ett undernät gäller NSG-reglerna för alla virtuella datorer i det här undernätet.
* **Diagnostik.** Diagnostisk loggning är avgörande för att hantera och felsöka den virtuella datorn.

## <a name="recommendations"></a>Rekommendationer

Följande rekommendationer gäller för de flesta scenarier. Följ dessa rekommendationer om du inte har ett visst krav som åsidosätter dem. 

### <a name="vm-recommendations"></a>Rekommendationer för virtuella datorer

Azure erbjuder många olika virtuella datorstorlekar, men vi rekommenderar DS - och GS-serien eftersom dessa datorstorlekar har stöd för [Premium Storage][premium-storage]. Välj en av dessa datorer såvida du inte har en särskild arbetsbelastning som databehandling med höga prestanda. Mer information finns i [Storlekar för virtuella datorer][virtual-machine-sizes].

Om du flyttar en befintlig arbetsbelastning till Azure börjar du med den virtuella datorstorlek som närmast motsvarar dina lokala servrar. Mät sedan prestanda hos din faktiska arbetsbelastning med avseende på processor, minne, och i/o-åtgärder för disken per sekund (IOPS) och justera storleken om det behövs. Om du behöver flera nätverkskort för den virtuella datorn måste du vara medveten om att det högsta antalet nätverkskort är en funktion av den [virtuella datorstorleken][vm-size-tables].

När du etablerar den virtuella datorn och andra resurser måste du ange en region. Vanligtvis väljer du en region som är närmast dina interna användare eller kunder. Alla storlekar på virtuella datorer finns dock inte tillgängliga i samtliga regioner. Mer information finns i [Tjänster per region][services-by-region]. Om du vill visa storlekarna på de virtuella datorer som finns i en viss region kör du följande kommando i Azure-kommandoradsgränssnittet (CLI):

```
azure vm sizes --location <location>
```

Information om hur du väljer en publicerad virtuell datoravbildning finns i [Välja virtuella Linux-datoravbildningar med Azure CLI][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Disk- och lagringsrekommendationer

För bästa i/o-diskprestanda rekommenderar vi [Premium Storage][premium-storage], som lagrar data på SSD-enheter. Kostnaden baseras på storleken på den allokerade disken. IOPS och dataflöde (dataöverföringshastighet) beror också på diskstorleken, så när du etablerar en disk bör du fundera över alla tre faktorer (kapacitet, IOPS och dataflöde). 

Skapa separata Azure-lagringskonton för varje virtuell dator för att lagra de virtuella hårddiskarna (VHD) för att undvika att IOPS-gränserna för lagringskonton överskrids. 

Lägg till en eller flera datadiskar. När du skapar en virtuell hårddisk är den oformaterad. Logga in på den virtuella datorn om du vill formatera disken. I Linux-gränssnittet visas datadiskar som `/dev/sdc`, `/dev/sdd` och så vidare. Du kan köra `lsblk` för att visa blockenheterna, inklusive diskarna, i listan. Skapa en partition och ett filsystem och montera disken om du vill använda en datadisk. Exempel:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Om du har ett stort antal datadiskar bör du tänka på de totala i/o-gränserna för lagringskontot. Mer information finns i [diskgränser för virtuella datorer][vm-disk-limits].

När du lägger till en datadisk tilldelas disken ett logiskt enhetsnummer-ID (LUN-ID). Alternativt kan du ange LUN-ID &mdash; till exempel om du byter ut en disk och vill behålla samma LUN-ID, eller om du har ett program som söker efter ett visst LUN-ID. Tänk dock på att LUN-ID:na måste vara unika för varje disk.

Du kanske vill ändra i/o-schemaläggaren för att optimera prestanda på SSD:erna, eftersom diskarna för virtuella datorer med Premium Storage-konton är SSD:er. En vanlig rekommendation är att använda NOOP-schemaläggaren för SSD:er, men du bör använda ett verktyg som [iostat] att övervaka diskens i/o-prestanda för en viss arbetsbelastning.

Skapa ett separat lagringskonto för diagnostikloggar för bästa prestanda. Ett standardkonto för lokalt redundant lagring (LRS) är tillräckligt för diagnostikloggar.

### <a name="network-recommendations"></a>Nätverksrekommendationer

Den offentliga IP-adressen kan vara dynamisk eller statisk. Standardvärdet är dynamiskt.

* Reservera en [statisk IP-adress][static-ip] om du behöver en fast IP-adress som inte ändras &mdash; till exempel, om du behöver skapa en A-post i DNS eller behöver lägga till IP-adressen i listan över säkra adresser.
* Du kan också skapa ett fullständigt domännamn (FQDN) för IP-adressen. Du kan sedan registrera en [CNAME-post][cname-record] i DNS som pekar på det fullständiga domännamnet. Mer information finns i [Skapa ett fullständigt domännamn i Azure Portal][fqdn].

Alla nätverkssäkerhetsgrupper innehåller en uppsättning [standardregler][nsg-default-rules], inklusive en regel som blockerar all inkommande Internettrafik. Standardreglerna kan inte tas bort, men andra regler kan åsidosätta dem. Om du vill aktivera Internettrafik skapar du regler som tillåter inkommande trafik till specifika portar &mdash; till exempel port 80 för HTTP.  

Om du vill aktivera SSH lägger du till en regel i NSG som tillåter inkommande trafik till TCP-port 22.

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden

Skala upp eller ned [genom att ändra storleken på den virtuella datorn][vm-resize]. 

Om du vill skala ut vågrätt placerar du två eller flera virtuella datorer i en tillgänglighetsuppsättning bakom en belastningsutjämnare. Mer information finns i [Köra flera virtuella datorer på Azure][multi-vm].

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

Distribuera flera virtuella datorer i en tillgänglighetsuppsättning om du vill ha högre tillgänglighet. Det innehåller också ett [serviceavtal][vm-sla] (SLA) på högre nivå. 

Din virtuella dator kan påverkas av [planerat underhåll] [ planned-maintenance] eller [oplanerat underhåll][manage-vm-availability]. Du kan använda [omstartsloggarna för virtuella datorer][reboot-logs] för att bedöma om en omstart av en virtuell dator orsakades av planerat underhåll.

Virtuella hårddiskar lagras i [Azure Storage][azure-storage], och Azure-lagringsutrymmet replikeras för hållbarhet och tillgänglighet.

För att skydda mot dataförluster under normal drift (t.ex, på grund av fel från användarens sida), bör du även implementera säkerhetskopieringar vid vissa tidpunkter med [blob-ögonblicksbilder][blob-snapshot] eller något annat verktyg.

## <a name="manageability-considerations"></a>Överväganden för hantering

**Resursgrupper.** Placera nära sammankopplade resurser som delar samma livscykel i samma [resursgrupp][resource-manager-overview]. Med hjälp av resursgrupper kan du distribuera och övervaka resurser som en grupp och summera faktureringskostnaderna per resursgrupp. Du kan också ta bort resurser som en uppsättning, vilket är mycket användbart vid testdistributioner. Ge resurserna meningsfulla namn. Detta gör det lättare att hitta en specifik resurs och att förstå dess roll. Se [Rekommenderade namnkonventioner för Azure-resurser][naming conventions].

**SSH**. Innan du skapar en virtuell Linux-dator skapar du ett 2 048-bitars offentligt/privat RSA-nyckelpar. Använd fil för offentlig nyckel när du skapar den virtuella datorn. Mer information finns i [Använd SSH med Linux och Mac på Azure][ssh-linux].

**Diagnostik av virtuella datorer.** Aktivera övervakning och diagnostik, inklusive grundläggande hälsomätvärden, diagnostikinfrastrukturloggar och [startdiagnostik][boot-diagnostics]. Startdiagnostik hjälper dig att diagnostisera startfel om den virtuella datorn går över till ett icke startbart tillstånd. Mer information finns i [Aktivera övervakning och diagnostik][enable-monitoring].  

Följande CLI-kommando aktiverar diagnostik:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Stoppa en virtuell dator.** Azure gör skillnad mellan tillståndet ”stoppad” och tillståndet ”frigjord”. Du debiteras när den virtuella datorns status stoppas, men inte när den virtuella datorn frigörs.

Du kan använda följande CLI-kommando för att frigöra en virtuell dator:

```
azure vm deallocate <resource-group> <vm-name>
```

I Azure-portalen tar knappen **Stoppa** bort den virtuella datorn. Om du stänger av via operativsystemet när du är inloggad stoppas den virtuella datorn, men frigörs dock *inte*, så du kommer fortfarande att debiteras.

**Ta bort en virtuell dator.** Om du tar bort en virtuell dator, tas inte de virtuella hårddiskarna bort. Det innebär att du kan ta bort den virtuella datorn på ett säkert sätt utan att förlora data. Men kommer du fortfarande att debiteras för lagring. Ta bort den virtuella hårddisken genom att ta bort filen från [Blob Storage][blob-storage].

Om du vill förhindra oavsiktlig borttagning använder du ett [resurslås] [resource-lock] och låser hela resursgruppen eller enskilda resurser, till exempel den virtuella datorn. 

## <a name="security-considerations"></a>Säkerhetsöverväganden

Automatisera uppdateringar av operativsystem med hjälp av det virtuella datortillägget [OSPatching]. Installera det här tillägget när du etablerar den virtuella datorn. Du kan ange hur ofta du ska installera uppdateringar och om du vill starta om efter korrigering.

Använd [rollbaserad åtkomstkontroll] [ rbac] (RBAC) för att kontrollera åtkomsten till de Azure-resurser som du distribuerar. Med RBAC kan du tilldela medlemmarna i DevOps-gruppen auktoriseringsroller. Till exempel kan den som har rollen Läsare visa Azure-resurser, men inte skapa, hantera eller ta bort dem. Vissa roller är specifika för vissa Azure-resurstyper. Exempelvis kan rollen Virtuell datordeltagare starta om eller frigöra en virtuell dator, återställa administratörslösenordet, skapa en virtuell dator och så vidare. Andra [inbyggda RBAC-roller][rbac-roles] som kan vara användbara för denna referensarkitektur är bland annat [DevTest Labs-användare] [rbac-devtest] och [Nätverksdeltagare][rbac-network]. 

En användare kan tilldelas till flera roller och du kan skapa egna roller för ännu mer detaljerade behörigheter.

d> [!NOTE]
> RBAC begränsar inte de åtgärder som en användare som är inloggad på en virtuell dator kan utföra. Dessa behörigheter avgörs av kontotypen i gästoperativsystemet.   
> 
> 

Använd [granskningsloggar][audit-logs] om du vill visa etableringsåtgärder och andra virtuella datorhändelser.

Överväg att använda [Azure Disk Encryption][disk-encryption] om du behöver kryptera operativsystemet och datadiskarna. 

## <a name="solution-deployment"></a>Lösningsdistribution
En distribution för denna referensarkitektur finns tillgänglig på [GitHub][github-folder]. Den innehåller ett virtuellt nätverk, NSG och en enda virtuell dator. Följ anvisningarna nedan om du vill distribuera arkitekturen: 

1. Högerklicka på knappen nedan och välj antingen ”Öppna länk i ny flik” eller ”Öppna länk i nytt fönster”.
   [![Distribuera till Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. När länken har öppnats i Azure-portalen måste du ange värden för vissa inställningar: 
   
   * Namnet **Resursgrupp** har redan definierats i parameterfilen så välj **Skapa nytt** och ange `ra-single-vm-rg` i textrutan.
   * Välj region från den listrutan **Plats**.
   * Redigera inte textrutorna för **mallrots-URI** eller **parameterrots-URI**.
.   *Välj * *linux* * i listrutan * *OS-typ**.
   * Granska villkoren och klicka sedan i kryssrutan **Jag godkänner villkoren ovan**.
   * Klicka på **Köp**.
3. Vänta tills distributionen har slutförts.
4. Parameterfilerna innehåller ett hårdkodat administratörsanvändarnamn och -lösenord och du bör ändra båda direkt. Klicka på den virtuella datorn med namnet `ra-single-vm0 ` i Azure Portal. Klicka på **Återställ lösenord** i avsnittet **Support och felsökning**. Välj **Återställ lösenord** i listrutan **Läge** och välj sedan ett nytt **Användarnamn** och **Lösenord**. Klicka på **Uppdatera** för att spara det nya användarnamnet och lösenordet.

## <a name="next-steps"></a>Nästa steg
Distribuera två eller flera virtuella datorer bakom en belastningsutjämnare för högre tillgänglighet. Mer information finns i [Köra flera virtuella datorer på Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "En enda virtuell Linux-datorarkitektur i Azure"

