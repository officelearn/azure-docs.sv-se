


Den här artikeln tar upp några vanliga frågor som användare ställer om virtuella Azure-datorer som skapats med den klassiska distributionsmodellen.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>Kan jag migrera min virtuella dator som skapats med den klassiska distributionsmodellen till den nya Resource Manager-modellen?
Ja. Anvisningar om hur du migrerar finns i:

* [Migrera från klassisk till Azure Resource Manager med Azure PowerShell](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Migrera från klassisk till Azure Resource Manager med Azure CLI](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Du kan köra de senaste versionerna av Windows Server, samt en mängd olika Linux-distributioner. Information om support finns i:

• För virtuella Windows-datorer – [Microsofts serverprogramsupport för virtuella Azure-datorer](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• För virtuella Linux-datorer – [Linux på Azure-godkända distributioner](http://go.microsoft.com/fwlink/p/?LinkId=393551)

För Windows-klientavbildningar är vissa versioner av Windows 7 och Windows 8.1 tillgängliga för MSDN Azure-förmånsprenumeranter och MSDN-utvecklings- och testningsprenumeranter som betalar per användning, för utvecklings- och testningsuppgifter. Mer information, bland annat instruktioner och begränsningar, finns i [Windows-klientavbildningar för MSDN-prenumeranter](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## <a name="why-are-affinity-groups-being-deprecated"></a>Varför blir tillhörighetsgrupper inaktuella?
Tillhörighetsgrupper är ett äldre koncept för en geografisk gruppering av en kunds molntjänstdistributioner och lagringskonton i Azure. De tillhandahölls ursprungligen för att förbättra VM-till-VM-nätverksprestanda i de tidiga Azure-nätverksdesignerna. De hade också stöd för den första versionen av virtuella nätverk (VNet), som var begränsade till en liten uppsättning maskinvara i en region.

Det aktuella Azure-nätverket i en region har utformats så att tillhörighetsgrupper inte längre behövs. Virtuella nätverk har också ett regionalt omfång, så en tillhörighetsgrupp behövs inte längre när du använder ett virtuellt nätverk. På grund av dessa förbättringar rekommenderar vi inte längre att kunder använder tillhörighetsgrupper eftersom de kan vara begränsande i vissa situationer. Med tillhörighetsgrupper associeras dina virtuella datorer i onödan med specifik maskinvara som begränsar urvalet av VM-storlekar som är tillgängliga för dig. Det kan också leda till kapacitetsrelaterade fel när du försöker lägga till nya virtuella datorer om den maskinvara som är associerad med tillhörighetsgruppen nästan är full.

Funktioner för tillhörighetsgrupper är redan inaktuella i Azure Resource Manager-distributionsmodellen och Azure-portalen. För den klassiska Azure-portalen upphör stöd för att skapa tillhörighetsgrupper och skapa lagringsresurser som är fästa på en tillhörighetsgrupp. Du behöver inte ändra befintliga molntjänster som använder en tillhörighetsgrupp. Men du bör inte använda tillhörighetsgrupper för nya molntjänster om inte en Azure-supportpersonal rekommenderar dem.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje datadisk kan vara upp till 1 TB. Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ett Azure-lagringskonto innehåller lagringsutrymme för operativsystemsdisken och eventuella datadiskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](http://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Vilka typer av virtuella hårddiskar kan jag använda?
Azure har bara stöd för fasta virtuella hårddiskar i VHD-format. Om du har en VHDX-disk som du vill använda i Azure måste du först konvertera den med hjälp av Hyper-V Manager eller cmdleten [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656). När du har gjort det använder du cmdleten [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (i Service Management-läge) för att ladda upp VHD-disken till ett lagringskonto i Azure så att du kan använda den med virtuella datorer.

* Linux-instruktioner finns i [Skapa och ladda upp en virtuell hårddisk som innehåller operativsystemet Linux](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Är de här virtuella datorerna samma sak som virtuella Hyper-V-datorer?
På många sätt liknar de virtuella Hyper-V-datorer i ”Generation 1”, men de är inte exakt samma. Båda typerna tillhandahåller virtualiserad maskinvara och de virtuella hårddiskarna i VHD-format är kompatibla. Det innebär att du kan flytta dem mellan Hyper-V och Azure. Det finns tre huvudsakliga skillnader som ibland överraskar Hyper-V-användare:

* Azure ger inte konsolåtkomst till en virtuell dator. Det går inte att komma åt en virtuell dator förrän den har startat klart.
* Virtuella Azure-datorer i de flesta [storlekar](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) har endast 1 virtuellt nätverkskort, vilket innebär att de även kan ha endast 1 extern IP-adress. (För storlekarna A8 och A9 används ett andra nätverkskort för programkommunikation mellan instanser i begränsade scenarier.)
* Virtuella Azure-datorer stöder inte funktioner för virtuella Hyper-V-datorer i Generation 2. Mer information om dessa funktioner finns i [Specifikationer för virtuell dator för Hyper-V](http://technet.microsoft.com/library/dn592184.aspx) och [Översikt över virtuella Generation 2-datorer](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Kan dessa virtuella datorer använda min befintliga, lokala nätverksinfrastruktur?
För virtuella datorer som skapats med den klassiska distributionsmodellen kan du använda Azure Virtual Network för att utöka den befintliga infrastrukturen. Metoden liknar att öppna ett avdelningskontor. Du kan etablera och hantera virtuella privata nätverk (VPN) i Azure samt ansluta dem säkert till lokal IT-infrastruktur. Mer information finns i [Översikt över Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

Du måste ange det nätverk som du vill att den virtuella datorn ska tillhöra när du skapar den virtuella datorn. Du kan inte ansluta en befintlig virtuell dator till ett virtuellt nätverk. Du kan dock komma runt detta genom att koppla från den virtuella hårddisken (VHD) från den befintliga virtuella datorn och sedan använda den för att skapa en ny virtuell dator med önskad nätverkskonfiguration.

## <a name="how-can-i-access--my-virtual-machine"></a>Hur kommer jag åt min virtuella dator?
Du måste upprätta en fjärranslutning för att logga in på den virtuella datorn med hjälp av Anslutning till fjärrskrivbord för en virtuell Windows-dator eller SSH (Secure Shell) för en virtuell Linux-dator. Anvisningar finns i:

* [Så här loggar du in på en virtuell dator som kör Windows Server](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Högst 2 samtidiga anslutningar stöds, om inte servern är konfigurerad som en Fjärrskrivbordstjänster-sessionsvärd.  
* [Så här loggar du in på en virtuell dator som kör Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Som standard tillåter SSH högst 10 samtidiga anslutningar. Du kan öka detta antal genom att redigera konfigurationsfilen.

Om du har problem med Fjärrskrivbord eller SSH kan du åtgärda problemet genom att installera och använda tillägget [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ytterligare alternativ för virtuella Windows-datorer:

* Hitta den virtuella datorn i Azure-portalen och klicka sedan på **Återställ fjärråtkomst** från kommandofältet.
* Läs [Felsöka fjärrskrivbordsanslutningar till en Windows-baserad virtuell Azure-dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Använd Windows PowerShell-fjärrkommunikation för att ansluta till den virtuella datorn eller skapa ytterligare slutpunkter så att andra resurser kan ansluta till den virtuella datorn. Mer information finns i [Konfigurera slutpunkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Om du är bekant med Hyper-V kanske du letar efter ett verktyg som liknar VMConnect. Azure tillhandahåller inget liknande verktyg eftersom konsolåtkomst till en virtuell dator inte stöds.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>Kan jag använda den temporära disken (enheten D: för Windows eller /dev/sdb1 för Linux) för att lagra data?
Du bör inte använda den temporära disken (som standard enheten D: för Windows eller /dev/sdb1 för Linux) för att lagra data. De är bara tillfälligt lagringsutrymme, så du riskerar att förlora data som inte kan återställas. Det kan ske när den virtuella datorn flyttas till en annan värd. Storleksändring av en virtuell dator, uppdatering av värden eller ett maskinvarufel på värden är några av orsakerna till att en virtuell dator kan flyttas.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hur ändrar jag enhetsbeteckningen för den temporära disken?
På en virtuell Windows-dator ändrar du enhetsbeteckningen genom att flytta växlingsfilen och omtilldela enhetsbeteckningar, men du måste se till att du utför stegen i en viss ordning. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Hur uppgraderar jag gästoperativsystemet?
Termen uppgradering innebär vanligtvis att flytta till en senare version av operativsystemet, men att behålla samma maskinvara. För virtuella Azure-datorer skiljer sig processen för att flytta till en senare version åt för Linux och Windows:

* För virtuella Linux-datorer använder du lämpliga pakethanteringsverktyg och -procedurer för distributionen.
* För en virtuell Windows-dator måste du migrera servern med något som liknar Migreringsverktyg för Windows Server. Försök inte uppgradera gästoperativsystemet medan det finns på Azure. Det stöds inte på grund av risken att förlora åtkomst till den virtuella datorn. Om problem uppstår under uppgraderingen kan du förlora möjligheten att starta en fjärrskrivbordssession och skulle inte kunna felsöka problemen.

Allmän information om verktyg och processer för att migrera en Windows-server finns i [Migrera roller och funktioner till Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Vad är standardanvändarnamnet och -lösenordet på den virtuella datorn?
Avbildningarna som tillhandahålls av Azure har inget förkonfigurerat användarnamn och lösenord. När du skapar en virtuell dator med en av dessa avbildningar måste du ange ett användarnamn och lösenord som du använder för att logga in på den virtuella datorn.

Om du har glömt användarnamnet eller lösenordet och har installerat den virtuella datoragenten kan du åtgärda problemet genom att installera och använda tillägget [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ytterligare information:

* För Linux-bilder om du använder Azure portal 'azureuser' anges som en Standardanvändarnamnet, men du kan ändra detta genom att använda 'Från galleriet' i stället för ”snabbt skapa” sätt att skapa den virtuella datorn. Med 'Från galleri' kan du också ange om du vill använda ett lösenord, en SSH-nyckel eller båda för att logga in. Användarkontot är en icke-privilegierad användare som har 'sudo'-åtkomst att köra privilegierade kommandon. 'Root'-kontot är inaktiverat.
* För Windows-avbildningar måste du ange ett användarnamn och lösenord när du skapar den virtuella datorn. Kontot läggs till i gruppen Administratörer.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Kan Azure köra antivirus på mina virtuella datorer?
Azure erbjuder flera alternativ för antiviruslösningar, men det är upp till dig att hantera det. Du kan till exempel behöva en separat prenumeration på program mot skadlig kod och du måste ange när du vill köra genomsökningar och installera uppdateringar. Du kan lägga till stöd för antivirus med ett VM-tillägg för Microsoft Antimalware, Symantec Endpoint Protection eller TrendMicro Deep Security Agent när du skapar en virtuell Windows-dator eller vid ett senare tillfälle. Med Symantec- och TrendMicro-tilläggen kan du använda en kostnadsfri utvärderingsprenumeration under begränsad tid eller en befintlig företagsprenumeration. Microsoft Antimalware är kostnadsfritt. Mer information finns i:

* [Så installerar och konfigurerar du Symantec Endpoint Protection på virtuella Azure-datorer](http://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Så installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en virtuell Azure-dator](http://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Vad har jag för alternativ för säkerhetskopiering och återställning?
Azure Backup finns som förhandsversion i vissa regioner. Mer information finns i [Säkerhetskopiera virtuella Azure-datorer](../articles/backup/backup-azure-arm-vms.md). Det finns andra lösningar från certifierade partner. Om du vill ta reda på vad som finns för närvarande kan du söka på Azure Marketplace.

Ett ytterligare alternativ är att använda funktionerna för ögonblicksbilder för blob-lagring. Om du vill göra det måste du stänga av den virtuella datorn före en åtgärd som är beroende av en blob-ögonblicksbild. Det sparar väntande dataskrivningar och sätter filsystemet i ett konsekvent tillstånd.

## <a name="how-does-azure-charge-for-my-vm"></a>Hur debiterar Azure för min virtuella dator?
Azure debiterar ett pris per timme baserat på storleken och operativsystemet för den virtuella datorn. För delar av timmar tar Azure bara betalt för användning per minut. Om du skapar den virtuella datorn med en VM-avbildning som innehåller vissa förinstallerade program kan ytterligare programkostnader per timme tillkomma. Azure debiterar separat för lagringsutrymme för den virtuella datorns operativsystem och datadiskar. Tillfällig disklagring är kostnadsfri.

Du debiteras när VM-statusen är Körs eller Stoppad, men du debiteras inte när VM-statusen är Stoppad (Avallokerad). Om du vill sätta en virtuell dator i tillståndet Stoppad (Avallokerad) gör du något av följande:

* Stänga av eller ta bort den virtuella datorn från Azure-portalen.
* Använd cmdleten Stop-AzureVM, som finns i Azure PowerShell-modulen.
* Använd åtgärden för att stänga av roll i Service Management REST-API:t och ange StoppedDeallocated för PostShutdownAction-elementet.

Mer information finns i [Prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Startar Azure om den virtuella datorn för underhåll?
Azure startar ibland om den virtuella datorn som en del av regelbundna, planerade underhållsuppdateringar i Azure-datacentren.

Oplanerade underhållshändelser kan inträffa när Azure upptäcker ett allvarligt maskinvaruproblem som påverkar den virtuella datorn. Vid oplanerade händelser migrerar Azure automatiskt den virtuella datorn till en felfri värd och startar om den virtuella datorn.

För en fristående virtuell dator (vilket innebär att den virtuella datorn inte ingår i en tillgänglighetsuppsättning) meddelar Azure prenumerationens tjänstadministratör via e-post minst en vecka före planerat underhåll eftersom de virtuella datorerna kan startas om under uppdateringen. Program som körs på de virtuella datorerna kan råka ut för avbrott.

Du kan också använda Azure-portalen eller Azure PowerShell om du vill visa omstart loggar när omstarten uppstod på grund av planerat underhåll. Mer information finns i [Visa VM-omstartsloggar](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

För redundans kan du placera två eller flera virtuella datorer med liknande konfiguration i samma tillgänglighetsuppsättning. På så sätt ser du till att minst en virtuell dator är tillgänglig under planerat eller oplanerat underhåll. Azure garanterar vissa nivåer av VM-tillgänglighet för den här konfigurationen. Mer information finns i [Hantera tillgängligheten av virtuella datorer](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Ytterligare resurser
[Om Azure Virtual Machines](../articles/virtual-machines/virtual-machines-linux-about.md)

[Skapa och hantera virtuella Linux-datorer med Azure CLI](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Skapa och hantera virtuella Windows-datorer med Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

