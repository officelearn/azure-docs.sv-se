<a name="tellmevm"></a>

## <a name="tell-me-about-virtual-machines"></a>Om virtuella datorer
Azure Virtual Machines låter dig skapa och använda virtuella datorer i molnet. Virtuell datorteknologi tillhandahåller något som kallas *infrastruktur som en tjänst (IaaS)* och kan användas på en rad olika sätt. Några exempel är:

* **Virtuella datorer (VM) för testning och utveckling.** Utvecklingsgrupper använder ofta VM:ar eftersom de erbjuder ett snabbt, enkelt sätt att skapa en dator med specifika konfigurationer som krävs för att koda och testa ett program. Azure Virtual Machines erbjuder ett enkelt och ekonomiskt sätt att skapa VM:ar, använda dem och sedan ta bort dem när de inte längre behövs.
* **Köra program i molnet.** Det är ekonomiskt meningsfullt att köra vissa program i det offentliga molnet. Ett exempel är ett program där efterfrågan ökar kraftigt och tillfälligt. Även om du skulle kunna utrusta ett eget datacenter med tillräckligt med maskinvara för att hantera efterfrågan vid en kraftig, tillfällig ökning, skulle maskinvaran vara underutnyttjad den mesta tiden. Om du kör programmet på Azure, kan du betala för extra VM:ar bara när du behöver dem och stänga ner dem när de inte behövs längre. Eller, om vi antar att du är en startup som behöver dataresurser på begäran, snabbt och utan bindning. Azure kan då återigen vara rätt val.
* **Utöka ditt eget datacenter till det offentliga molnet.** Med Azure Virtual Network kan organisationen skapa ett virtuellt nätverk (VNET) som agerar som en utökning av det egna nätverket på plats och sedan lägga till VM:ar till det virtuella nätverket. Det gör att program som [SharePoint](../articles/virtual-machines/windows/sharepoint-farm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), [SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) och fler, kan köras direkt på en Azure-VM. Den här metoden kan vara lättare att distribuera och mindre kostsam än att köra dem i virtuella datorer i ditt eget datacenter.   
* **Katastrofåterställning** Istället för att kontinuerligt behöva betala för ett datacenter för säkerhetskopiering som sällan används, låter IaaS-baserad haveriberedskap dig betala för de dataresurser du behöver bara när du verkligen behöver dem.  Om ditt primära datacenter exempelvis skulle krascha, kan du skapa VM:ar som körs på Azure för att hantera nödvändiga program och stänga dem när de inte längre behövs.

Precis som andra virtuella datorer, består en VM i Azure av ett operativsystem, ett lagringsutrymme och nätverksfunktioner och kan köra en rad olika program. Du kan använda en avbildning från Azure, eller någon av dess partners, eller använda din egen. Exempel inkluderar olika versioner, utgåvor och konfigurationer av:

* Linux-servrar som Suse, Ubuntu och CentOS
* Windows Server 
* SQL Server
* BizTalk Server 
* SharePoint Server

Virtuella datorer använder virtuella hårddiskar (VHD:ar) för att lagra sitt operativsystem (OS) och sin data. VHD:ar används också för de avbildningar du kan välja mellan för att installera ett operativsystem. Följande bild visar det, samt två verktyg för att skapa och hantera dina VM:ar.

<a name="fig_createvms"></a>
![vm_diagram](./media/virtual-machines-choose-me-content/diagram.png)

**Bild: Azure Virtual Machines erbjuder infrastruktur som en tjänst.**

VM:ar kan hanteras från en webbläsarbaserad portal, kommandoradsverktyg med skriptstöd, eller direkt från REST-API:n. Microsoft-partners som RightScale och ScaleXtreme erbjuder också hanteringstjänster som förlitar sig på REST API. 

Förutom operativsystemet, finns det andra konfigurationsval med VM:ar:

* Storleken, vilket avgör faktorer som hur många diskar du kan ansluta och processorkraften. Azure erbjuder en rad olika storlekar för att passa en mängd olika användningar. Mer information finns i [Storlekar för virtuella datorer](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  
* Azure-regionen där din nya VM ska ligga, exempelvis USA, Europa eller Asien. 
* VM-tillägg som ger din virtuella dator ytterligare funktioner, som att köra antivirus eller använda sig av Desired State Configuration-funktionen i Windows PowerShell.

Andra fördelar med att använda VM:ar är:

**Betala per användning** – Azure debiterar ett pris per timme baserat på storleken och operativsystemet för den virtuella datoren. För delar av timmar tar Azure bara betalt för användning per minut. Lagringsutrymme prissätts och debiteras separat. Mer information finns i [Prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).

**Återhämtning** – Azure övervakar den fysiska maskinvara som är värd för varje VM som körs. Om en fysisk server som kör en VM skulle krascha, märker Azure det och flyttar din VM till ny maskinvara och startar om den. Den här processen kallas ibland tjänsteläkning. Azure skyddar också data i virtuella datorer genom att ha redundanta kopior av VHD:ar i blobblagring. 

