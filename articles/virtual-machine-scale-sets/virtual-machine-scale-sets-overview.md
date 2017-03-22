---
title: "Översikt över skalningsuppsättningar för virtuella datorer i Azure | Microsoft Docs"
description: "Mer information om skalningsuppsättningar för virtuella datorer i Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/10/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 2071debe24ef2705b2ee05ce1210a813234cf277
ms.lasthandoff: 03/10/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Vad är skalningsuppsättningar för virtuella datorer i Azure?
Skalningsuppsättningar för virtuella datorer är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning identiska virtuella datorer. När alla virtuella datorer är likadant konfigurerade är skalningsuppsättningar utformade för att stödja äkta automatisk skalning – ingen företablering av virtuella datorer krävs. Det gör det enklare att bygga storskaliga tjänster som riktar in sig på big compute, stordata och arbetsbelastningar i behållare.

För program som behöver skala beräkningsresurser ut och in är skalningsåtgärderna balanserade implicit i fel- och uppdateringsdomäner. En introduktion till skalningsuppsättningar finns i [Azure-bloggmeddelandet](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Titta igenom dessa videor för mer information om skalningsuppsättningar:

* [Mark Russinovich berättar om Azure-skalningsuppsättningar](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Skaluppsättningar för virtuell dator med Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Skapa och hantera skalningsuppsättningar
Du kan skapa en skalningsuppsättning i [Azure Portal](https://portal.azure.com) genom att välja *Ny* och skriva in ”skalning” i sökfältet. I resultatet ser du ”VM-skalningsuppsättning”. Därifrån kan du fylla i fälten som krävs för att anpassa och distribuera din skalningsuppsättning. Observera att det även finns alternativ för att ställa in grundläggande regler för automatisk skalning som baseras på CPU-användning i portalen.

Skalningsuppsättningar kan också definieras och distribueras med JSON-mallar och [REST API:er](https://msdn.microsoft.com/library/mt589023.aspx) precis som enskilda virtuella datorer i Azure Resource Manager. Därför går det att använda alla standardmetoder för Azure Resource Manager-distribution. Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Några exempel på mallar för skalningsuppsättningar för virtuella datorer finns i Azure snabbstartsmallarna på GitHub-lagringsplatsen [här](https://github.com/Azure/azure-quickstart-templates) (titta efter mallar som innehåller *vmss* i rubriken).

Det finns en knapp som länkar till portalens distributionsfunktion på informationssidorna för dessa mallar. Distribuera skalningsuppsättningen genom att klicka på knappen och sedan fylla i alla parametrar som krävs i portalen. Om du är osäker på om en resurs stöder versaler eller både gemener och versaler är det säkrast att alltid använda parametervärden med gemener och siffror. Det finns också en praktisk videoanalys av en skalningsuppsättningsmall här:

[Analys av mall för VM-skalningsuppsättning](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-scale-set-out-and-in"></a>Skala ut eller skala in en skalningsuppsättning
Du kan ändra kapaciteten för en skalningsuppsättning i Azure Portal genom att klicka på avsnittet _Skalning_ under _Inställningar_. 

För att ändra skalningsuppsättningens kapacitet på kommandoraden tillhandahåller [Azure CLI](https://github.com/Azure/azure-cli) ett _skalningskommando_. Så här ställer du in en skalningsuppsättnings kapacitet på 10 virtuella datorer:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Ange antalet virtuella datorer i en skalningsuppsättning med PowerShell och kommandot _Update-AzureRmVmss_:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Om du vill öka eller minska antalet virtuella datorer i en skalningsuppsättning med hjälp av en Azure Resource Manager-mall ändrar du egenskapen för *kapacitet* och distribuerar mallen igen. Den här smidiga funktionen gör det enkelt att integrera skalningsuppsättningar med automatisk skalning i Azure. Du kan också skriva egna anpassade skalningslager om du behöver definiera anpassade skalningshändelser som inte stöds av funktionen för automatisk skalning i Azure. 

Om du omdistribuerar en Azure Resource Manager-mall för att ändra kapaciteten kan du definiera en mycket mindre mall som bara innehåller SKU-egenskapspaketet och den uppdaterade kapaciteten. Ett exempel på detta visas [här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

## <a name="autoscale"></a>Automatisk skalning

En skalningsuppsättning kan alternativt konfigureras med inställningarna för automatisk skalning när den skapas i Azure Portal, så att antalet virtuella datorer ökas eller minskas baserat på den genomsnittliga CPU-användning. Många av skalningsuppsättningsmallarna i [Azures snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates) definierar inställningarna för automatisk skalning. Du kan också lägga till inställningar för automatisk skalning till en befintlig skalningsuppsättning. Här följer ett exempel på ett Azure PowerShell-skript för att lägga till CPU-baserad automatisk skalning till en skalningsuppsättning:

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

 Du hittar en lista över giltiga mått för skalning här: [Supported metrics with Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md) (Mått som stöds av Azure Monitor) under rubriken _Microsoft.Compute/virtualMachineScaleSets_. Mer avancerade alternativ för automatisk skalning finns också, inklusive schemabaserad automatisk skalning och användning av webhook-konfigurationer för att integrera med aviseringssystem.

## <a name="monitoring-your-scale-set"></a>Övervaka din skalningsuppsättning
I [Azure Portal](https://portal.azure.com) anges skalningsuppsättningar och där visas även deras egenskaper. Portalen har även stöd för hanteringsåtgärder som kan utföras både på skalningsuppsättningar och på enskilda virtuella datorer i en skalningsuppsättning. Portalen innehåller också ett anpassningsbart resursanvändningsdiagram. Om du behöver se eller redigera den underliggande JSON-definitionen för en Azure-resurs kan du också använda [resursutforskaren i Azure](https://resources.azure.com). Skalningsuppsättningar är en resurs under Azure Resource Provider för Microsoft.Compute, så på den här webbplatsen kan du se dem om du expanderar följande länkar:

**Prenumerationer -> din prenumeration -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> din skalningsuppsättning -> osv.**

## <a name="scale-set-scenarios"></a>Scenarier för skalningsuppsättningar
Det här avsnittet innehåller några vanliga scenarier för skalningsuppsättningar. Vissa Azure-tjänster på högre nivåer (som Batch, Service Fabric, Azure Container Service) använder dessa scenarier.

* **RDP/SSH till skalningsuppsättningsinstanser** – en skalningsuppsättning skapas i ett virtuellt nätverk och enskilda virtuella datorer i skalningsuppsättningen allokeras inte till offentliga IP-adresser. Den här principen undviker utgifts- och administrationskostnader för att allokera separata offentliga IP-adresser för alla noder i rutnätsberäkningen. Du kan ansluta till dessa virtuella datorer från andra resurser i det virtuella nätverket, till exempel belastningsutjämnare och fristående virtuella datorer, som kan allokeras till offentliga IP-adresser.
* **Ansluta till virtuella datorer med NAT-regler** – Du kan skapa en offentlig IP-adress, tilldela den till en belastningsutjämnare och definiera en inkommande NAT-pool som mappar portar på IP-adressen till en port på en virtuell dator på skalningsuppsättningen. Exempel:
  
  | Källa | Källport | Mål | Målport |
  | --- | --- | --- | --- |
  |  Offentlig IP-adress |Port 50000 |vmss\_0 |Port 22 |
  |  Offentlig IP-adress |Port 50001 |vmss\_1 |Port 22 |
  |  Offentlig IP-adress |Port 50002 |vmss\_2 |Port 22 |
  
   I det här exemplet definieras NAT-regler för att aktivera en SSH-anslutning till varje virtuell dator i en skalningsuppsättning med en enda offentlig IP-adress: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Här är ett exempel på hur du gör samma sak med RDP och Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Ansluta till virtuella datorer med en "jumpbox"** – Om du skapar en skalningsuppsättning och en fristående virtuell dator i samma virtuella nätverk kan den fristående virtuella datorn och virtuella datorer i en skalningsuppsättning anslutas till varandra med sina interna IP-adresser som definieras av VNET/undernätet. Om du skapar en offentlig IP-adress och tilldelar den till den fristående virtuella datorn kan du använda RDP eller SSH för fristående virtuella datorn och sedan ansluta från den datorn till dina skalningsuppsättningsinstanser. Du kanske ser nu att en enkel skalningsuppsättning i sig är säkrare än en enkel fristående VM med en offentlig IP-adress i standardkonfigurationen.
  
   Den här mallen distribuerar till exempel en enkel skalningsuppsättning med en fristående virtuell dator: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Belastningsutjämning till skalningsuppsättningsinstanser** – Om du vill leverera arbete till ett beräkningskluster av virtuella datorer med en "resursallokeringsmetod" kan du konfigurera en Azure-belastningsutjämnare med regler för layer-4-belastningsutjämning. Du kan definiera avsökningar för att verifiera att programmet körs genom att skicka pingsignaler till portar med en angiven sökväg för protokoll, intervall och begäran. Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) stöder även skalningsuppsättningar tillsammans med layer-7 och mer avancerade scenarier för belastningsutjämning.
  
   I det här exemplet skapas en skalningsuppsättning som kör Apache-webbservrar och som använder en belastningsutjämnare för att utjämna belastningen som varje virtuell dator tar emot: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (titta på resurstypen Microsoft.Network/loadBalancers och networkProfile och extensionProfile i virtualMachineScaleSet)

   I det här exemplet används en programgateway. Linux:  [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) 

* **Distribuera en skalningsuppsättning som ett beräkningskluster i en PaaS-klusterhanterare** – skalningsuppsättningar beskrivs ibland som nästa generationens arbetsroll. Det är en giltig beskrivning, men den riskerar också att förväxla skalningsuppsättningsfunktioner med Azure Cloud Services-funktioner. I en mening erbjuder skalningsuppsättningar en äkta "arbetsroll" eller arbetsresurs på så sätt att de tillhandahåller en generaliserad beräkningsresurs som är plattforms-/körningsberoende, anpassningsbar och som integreras i Azure Resource Manager-IaaS.
  
   En Cloud Services-arbetsroll, som är begränsad vad gäller support för plattform/körning (endast Windows-plattformsavbildningar), innehåller även tjänster som VIP-byte, konfigurerbara uppgraderingsinställningar och körnings-/appdistributionsinställningar som antingen inte *ännu* är tillgängliga i skalningsuppsättningar, eller som kommer att lanseras av andra PaaS-tjänster på högre nivå, som Service Fabric. Du kan tänka på skalningsuppsättningar som en infrastruktur som stöder PaaS. PaaS-lösningar som [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) bygger på denna infrastruktur.
  
   Ett exempel på den här metoden är när [Azure Container Service](https://azure.microsoft.com/services/container-service/) distribuerar ett kluster baserat på skalningsuppsättningar med en behållardirigering: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="scale-set-performance-and-scale-guidance"></a>Prestanda för skalningsuppsättningar och riktlinjer för skalning
* En skalningsuppsättning har stöd för upp till 1 000 virtuella datorer. Om du skapar och laddar upp dina egna anpassade VM-avbildningar är gränsen 100. Faktorer att överväga när du använder stora skalningsuppsättningar finns i [Arbeta med stora skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md).
* Du behöver inte skapa Azure Storage-konton i förväg för att använda skalningsuppsättningar. Skalningsuppsättningar har stöd för Azure Managed Disks, vilket eliminerar prestandaproblem om antalet diskar per lagringskonto. Mer information finns i [skalningsuppsättningar och hanterade diskar för virtuella datorer i Azure](virtual-machine-scale-sets-managed-disks.md).
* Överväg att använda Azure Premium Storage i stället för standardlagring för snabbare, mer förutsägbara VM-etableringstider samt förbättrade IO-prestanda.
* Antalet virtuella datorer som du kan skapa begränsas av kärnkvoten i den region där du distribuerar. Du kan behöva kontakta kundsupport om du vill öka beräkningskvotgränsen även om du har en hög högsta gräns för kärnor som kan användas med Azures molntjänster i dag. För att köra frågan om kvot kör du följande Azure CLI-kommando: `azure vm list-usage` eller följande PowerShell-kommando: `Get-AzureRmVMUsage` (om du använder en tidigare version än PowerShell 1.0 ska du använda `Get-AzureVMUsage`).

## <a name="scale-set-frequently-asked-questions"></a>Vanliga frågor och svar om skalningsuppsättningar
**F.** Hur många virtuella datorer kan man ha i en skalningsuppsättning?

**S.** En skalningsuppsättning kan ha mellan 0 och 1 000 virtuella datorer baserade på plattformsavbildningar eller 0–100 virtuella datorer baserade på anpassade avbildningar. 

**F.** Kan datadiskar användas i skalningsuppsättningar?

**S.** Ja. En skalningsuppsättning kan definiera en konfiguration för anslutna dataenheter som gäller för alla virtuella datorer i uppsättningen. Mer information finns i (Azures skalningsuppsättningar och anslutna dataenheter)[virtual-machine-scale-sets-attached-disks.md]. Andra alternativ för att lagra data är:

* Azure-filer (delade SMB-enheter)
* OS-enhet
* Temp-enhet (lokal, backas inte upp av Azure Storage)
* Azure-datatjänst (t.ex. Azure-tabeller, Azure-blobbar)
* Extern datatjänst (t.ex. fjärrdatabas)

**F.** Vilka Azure-regioner har stöd för skalningsuppsättningar?

**S.** Alla regioner stöder skalningsuppsättningar.

**F.** Hur skapas en skalningsuppsättning med en anpassad avbildning?

**S.** Skapa en hanterad disk baserat på den anpassade virtuella hårddiskavbildningen (VHD) och referera till den i din mall för skalningsuppsättningen. Här är ett exempel: [https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**F.** Vilka virtuella datorer tas bort om jag minskar skalningsuppsättningens kapacitet från 20 till 15?

**S.** Virtuella datorer tas bort från skaluppsättningen jämnt bland uppgraderingsdomäner och feldomäner för att maximera tillgängligheten. Virtuella datorer med den högst id tas bort först.

**F.** Hur blir om det om jag sedan ökar kapaciteten från 15 till 18?

**S.** Om du ökar kapaciteten till 18 skapas 3 nya virtuella datorer. Varje gång ökas den virtuella datorinstansens id från det tidigare högsta värdet (t.ex. 20, 21, 22). Virtuella datorer balanseras mellan FD och UD.

**F.** Kan jag framtvinga en körning av aktivitetssekvensen när jag använder flera tillägg i en skalningsuppsättning?

**S.** Inte direkt, men för tillägget customScript kan skriptet vänta på ytterligare ett tillägg med att slutföra ([exempelvis genom att övervaka tilläggets logg](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Ytterligare hjälp med ordningsföljden för tillägg finns i det här blogginlägget: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Ordningsföljd för tillägg i Azure VM-skaluppsättningar).

**F.** Fungerar skalningsuppsättningar med Azures tillgänglighetsuppsättningar?

**S.** Ja. En skalningsuppsättning är en implicit tillgänglighetsuppsättning med 5 FD och 5 UD. Skalningsuppsättningar med mer än 100 virtuella datorer sträcker sig över flera "placeringsgrupper" som är likvärdiga med flera tillgänglighetsuppsättningar. Mer information om placeringsgrupper finns i [Arbeta med stora skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md). En tillgänglighetsuppsättning för virtuella datorer kan finnas i samma VNET som en skalningsuppsättning för virtuella datorer. En vanlig konfiguration är att placera virtuella kontrollnodsdatorer som ofta kräver unika konfigurationer i en tillgänglighetsuppsättning och datanoder i skalningsuppsättningen.

Fler vanliga frågor om skalningsuppsättningar finns i [Vanliga frågor och svar om skalningsuppsättningar för virtuella datorer i Azure](virtual-machine-scale-sets-faq.md).

