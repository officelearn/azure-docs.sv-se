

För program som behöver skala beräkningsresurser ut och in är skalningsåtgärderna balanserade implicit i fel- och uppdateringsdomäner. En introduktion till VM-skalningsuppsättningar finns i ett nytt [Azure-bloggmeddelande](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/).

Titta igenom dessa videor för mer information om VM-skaluppsättningar:

* [Mark Russinovich berättar om Azure-skaluppsättningar](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Skaluppsättningar för virtuell dator med Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Skapa och hantera VM-skaluppsättningar
VM-skalningsuppsättningar kan definieras och distribueras med JSON-mallar och [REST API:er](https://msdn.microsoft.com/library/mt589023.aspx) precis som enskilda Azure Resource Manager-mallar. Därför går det att använda alla standardmetoder för Azure Resource Manager-distribution. Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](../articles/resource-group-authoring-templates.md).

Några exempel på mallar för VM-skalningsuppsättningar finns i Azure-snabbstartsmallarna på GitHub-lagringsplatsen:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) (leta efter mallar med *vmss* i namnet).

På informationssidorna för mallarna finns en knapp som länkar till portalens distributionsfunktion. Klicka på knappen för att distribuera VM-skaluppsättningen och fyll sedan i alla parametrar som krävs i portalen. Om du är osäker på om en resurs stöder versaler eller både gemener och versaler är det säkrast att alltid använda parametervärden med gemener. Det finns också en praktisk videoanalys av en mall för VM-skaluppsättningar här:

[Analys av mall för VM-skaluppsättning](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Skala ut eller in en VM-skaluppsättning
Om du vill öka eller minska antalet virtuella datorer i en VM-skaluppsättning ändrar du bara egenskapen för *kapacitet* och distribuerar mallen igen. Tack vare enkelheten är det lätt att skriva ett eget anpassat skalningslager om du vill definiera anpassade skalhändelser som inte stöds av Azure-autoskalning.

Om du omdistribuerar en mall för att ändra kapaciteten kan du definiera en mycket mindre mall som bara innehåller SKU:n och den uppdaterade kapaciteten. Ett exempel på detta visas här: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Om du vill gå igenom de steg som kan skapa en skaluppsättning som skalas automatiskt kan du läsa [Skala automatiskt datorer i en skalningsuppsättning för virtuella datorer](../articles/virtual-machines/windows/vmss-powershell-creating.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="monitoring-your-vm-scale-set"></a>Övervaka din VM-skaluppsättning
Om du vill visa VM-skalningsuppsättningar rekommenderas att du använder [resursutforskaren i Azure](https://resources.azure.com). VM-skaluppsättningar är en resurs under Microsoft.Compute, så på den webbplatsen kan du se dem om du expanderar följande länkar:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Scenarier för VM-skaluppsättningar
Det här avsnittet innehåller några vanliga scenarier för VM-skaluppsättningar. Vissa Azure-tjänster på högre nivåer (som Batch, Service Fabric, Azure Container Service) använder dessa scenarier.

* **RDP / SSH till VM-skalningsuppsättningsinstanser** – en VM-skalningsuppsättning skapas i ett virtuellt nätverk och enskilda virtuella datorer allokeras inte till offentliga IP-adresser. Detta är bra eftersom du normalt inte vill ha omkostnaderna och hanteringen som det innebär att allokera separata IP-adresser till alla tillståndslösa resurser i beräkningsrutnätet. Du kan enkelt ansluta till de virtuella datorerna från andra resurser i ditt VNET, däribland sådana som har offentliga IP-adresser som belastningsutjämnare eller fristående virtuella datorer.
* **Ansluta till virtuella datorer med NAT-regler** – Du kan skapa en offentlig IP-adress, tilldela den till en belastningsutjämnare och definiera inkommande NAT-regler som mappar en port i IP-adressen till en port på en virtuell dator i VM-skaluppsättningen. T.ex.
  
   Offentlig IP->Port 50000 -> vmss\_0->Port 22  Offentlig IP->Port 50001 -> vmss\_1->Port 22  Offentlig IP->Port 50002-> vmss\_2->Port 22
  
   Här är ett exempel på hur du skapar en VM-skaluppsättning som använder NAT-regler för att aktivera SSH-anslutning till varje virtuell dator i en skaluppsättning med en enda offentlig IP-adress: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Här är ett exempel på hur du gör samma sak med RDP och Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Ansluta till virtuella datorer med en "jumpbox"** – Om du skapar en VM-skaluppsättning och en fristående virtuell dator i samma VNET kan den fristående virtuella datorn och en virtuell dator i en VM-skaluppsättning anslutas till varandra med sina interna IP-adresser som definieras av VNET/undernätet. Om du skapar en offentlig IP-adress och tilldelar den till fristående virtuella datorer kan du använda RDP eller SSH för fristående virtuella datorer och sedan ansluta från den datorn till dina VM-skaluppsättningsinstanser. Du kanske ser nu att en enkel VM-skaluppsättning i sig är säkrare än en enkel fristående VM med en offentlig IP-adress i standardkonfigurationen.
  
   För att illustrera ett exempel på den här metoden skapar den här mallen ett enkelt Mesos-kluster som består av en fristående virtuell huvuddator som hanterar ett VM-skalningsuppsättningsbaserat kluster av virtuella datorer: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)
* **Belastningsfördelning med resursallokering till VM-skalningsuppsättningsinstanser** – Om du vill leverera arbete till ett beräkningskluster av virtuella datorer med en "resursallokeringsmetod" kan du konfigurera en Azure-belastningsutjämnare med regler för belastningsutjämning. Du kan också definiera avsökningar för att verifiera att programmet körs genom att skicka pingsignaler till portar med en angiven sökväg för protokoll, intervall och begäran.
  
   Här är ett exempel som skapar en VM-skalningsuppsättning med virtuella datorer som kör IIS-webbserver och som använder en belastningsutjämnare för att balansera belastningen som varje VM får. Det använder också HTTP-protokollet för att pinga en viss URL på varje virtuell dator: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) – se resurstypen Microsoft.Network/loadBalancers och networkProfile och extensionProfile i virtualMachineScaleSet.
* **Distribuera en VM-skaluppsättning som ett beräkningskluster i en PaaS-klusterhanterare** – VM-skaluppsättningar beskrivs ibland som nästa generationens arbetsroll. Det är en giltig beskrivning, men den riskerar också att förväxla skaluppsättningsfunktioner med PaaS v1-arbetsrollfunktioner. I en mening erbjuder VM-skaluppsättningar en äkta ”arbetsroll” eller arbetsresurs på så sätt att de tillhandahåller en generaliserad beräkningsresurs som är plattforms-/körningsberoende, anpassningsbar och integreras i Azure Resource Manager-IaaS.
  
   En PaaS v1-arbetsroll, som är begränsad vad gäller support för plattform/körning (endast Windows-avbildningar), innehåller även tjänster som VIP-byte, konfigurerbara uppgraderingsinställningar och körnings-/appdistributionsinställningar som antingen inte *ännu* är tillgängliga i VM-skaluppsättningar, eller som kommer att lanseras av andra PaaS-tjänster på högre nivå, som Service Fabric. Med detta i kan åtanke kan du titta på VM-skaluppsättningar som en infrastruktur som stöder PaaS. Dvs. PaaS-lösningar som Service Fabric eller klusterhanterare som Mesos kan byggas ovanpå VM-skaluppsättningar som ett skalbart beräkningslager.
  
   Här är ett exempel på ett Mesos-kluster som distribuerar en VM-skalningsuppsättning i samma VNET som en fristående virtuell dator. Den fristående virtuella datorn är en överordnad Mesos-nod och VM-skalningsuppsättningen representerar en uppsättning underordnade noder: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Kommande versioner av [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) distribuerar mer komplexa/förstärkta versioner av det här scenariot utifrån VM-skaluppsättningar.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Riktlinjer för prestanda och skalning för VM-skaluppsättningar
* Skapa inte fler än 500 virtuella datorer i flera VM-skalningsuppsättningar i taget när du använder den allmänt tillgängliga förhandsversionen.
* Planera inte för fler än 40 virtuella datorer per lagringskonto.
* Sprid ut de första bokstäverna i lagringskontonamnen så mycket som möjligt.  Exemplen på VMSS-mallar i [Azure-snabbstartmallar](https://github.com/Azure/azure-quickstart-templates/) ger exempel på hur du ska gå tillväga.
* Om du använder egna virtuella datorer ska du planera för fler än 40 virtuella datorer på VM-skaluppsättning i ett enda lagringskonto.  Du behöver kopiera avbildningen till lagringskontot innan du kan börja distribuera VM-skaluppsättningen. Läs vanliga frågor och svar för mer information.
* Planera inte för fler än 2 048 virtuella datorer per VNET.  Den här gränsen kommer att vara större i framtiden.
* Antalet virtuella datorer som du kan skapa begränsas av Compute-kärnkvoten i varje region. Du kan behöva kontakta kundsupport om du vill öka beräkningskvotgränsen även om du har en hög högsta gräns för kärnor som kan användas med molntjänster eller IaaS v1 idag. För att köra frågan om kvot kan du köra följande Azure CLI-kommando: *azure vm list-usage* och följande PowerShell-kommando: *Get-AzureRmVMUsage* (om du använder en tidigare version än PowerShell 1.0 ska du använda *Get-AzureVMUsage*).

## <a name="vm-scale-set-frequently-asked-questions"></a>Vanliga frågor och svar om VM-skaluppsättningar
**F.** Hur många virtuella datorer man ha i en VM-skaluppsättning?

**S.** 100 om du använder plattformsavbildningar som kan distribueras på flera lagringskonton. Om du använder anpassade avbildningar kan du ha upp till 40 virtuella datorer eftersom anpassade avbildningar är begränsade till ett enda lagringskonto i förhandsversionen.

**F** Vilka andra resursbegränsningar finns det för VM-skalningsuppsättningar?

**S.** Du får högst skapa 500 virtuella datorer i flera skalningsuppsättningar per region under förhandsversionsperioden. Gränserna för den befintliga [Azure-prenumerationstjänsten/](../articles/azure-subscription-service-limits.md) gäller.

**F.** Stöds datadiskar inom VM-skaluppsättningar?

**S.** Inte i den första versionen. Alternativen för att lagra data är:

* Azure-filer (delade SMB-enheter)
* OS-enhet
* Temp-enhet (lokal, backas inte upp av Azure Storage)
* Extern datatjänst (t.ex. fjärrdatabas, Azure-tabeller, Azure-blobar)

**F.** Vilka Azure-regioner har stöd för VM-skalningsuppsättningar?

**S.** Alla regioner som stöder Azure Resource Manager stöder VM-skaluppsättningar.

**F.** Hur skapar jag en VM-skalningsuppsättning med en anpassad avbildning?

**S.** Lämna egenskapen vhdContainers tom, till exempel:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**F.** Vilka virtuella datorer tas bort om jag minskar VM-skaluppsättningens kapacitet från 20 till 15?

**S.** Virtuella datorer tas bort från skaluppsättningen jämnt bland uppgraderingsdomäner och feldomäner för att maximera tillgängligheten.

**F.** Hur blir om det om jag sedan ökar kapaciteten från 15 till 18?

**S.** Om du ökar till 18 skapas virtuella datorer med index 15, 16 och 17. I båda fallen balanseras virtuella datorer mellan FD och UD.

**F.** Kan jag framtvinga en körning av aktivitetssekvensen när jag använder flera tillägg i en VM-skaluppsättning?

**S.** Inte direkt, men för tillägget customScript kan ditt skript vänta på att ett annat tillägg ska slutföras (till exempel genom att övervaka tilläggsloggen – se [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**F.** Fungerar VM-skaluppsättningar med Azures tillgänglighetsuppsättningar?

**S.** Ja. En VM-skalningsuppsättning är en implicit tillgänglighetsuppsättning med 3 FD och 5 UD. Du behöver inte konfigurera något under virtualMachineProfile. I framtida versioner kommer VM-skaluppsättningar sannolikt att sträcka sig över flera klienter, men för närvarande är en skaluppsättning en enda tillgänglighetsuppsättning.

