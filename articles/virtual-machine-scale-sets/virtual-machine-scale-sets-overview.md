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
ms.date: 07/03/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 8b2fbc230faf01797109114d6ebdffe5ec50e48b
ms.contentlocale: sv-se
ms.lasthandoff: 07/04/2017


---
# Vad är skalningsuppsättningar för virtuella datorer i Azure?
<a id="what-are-virtual-machine-scale-sets-in-azure" class="xliff"></a>
Skalningsuppsättningar för virtuella datorer är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning identiska virtuella datorer. När alla virtuella datorer har konfigurerats på samma sätt skapas skalningsuppsättningar för att stödja sann autoskalning. Ingen företablering av virtuella datorer krävs. Det gör det enklare att skapa storskaliga tjänster som riktar sig mot Big Compute, stordata och arbetsbelastningar i behållare.

För program som behöver skala beräkningsresurser ut och in är skalningsåtgärderna balanserade implicit i fel- och uppdateringsdomäner. En mer omfattande introduktion till skalningsuppsättningar finns i [Azure-bloggmeddelandet](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Titta igenom dessa videor för mer information om skalningsuppsättningar:

* [Mark Russinovich berättar om Azure-skalningsuppsättningar](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Skaluppsättningar för virtuell dator med Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## Skapa och hantera skalningsuppsättningar
<a id="creating-and-managing-scale-sets" class="xliff"></a>
Du kan skapa en skalningsuppsättning i [Azure Portal](https://portal.azure.com) genom att välja **Ny** och skriva in **skalning** i sökfältet. Bland resultaten ser du **Skalningsuppsättning för virtuella datorer**. Därifrån kan du fylla i fälten som krävs för att anpassa och distribuera din skalningsuppsättning. Observera att det även finns alternativ för att ställa in grundläggande regler för automatisk skalning som baseras på processoranvändningen i portalen.

Du kan definiera och distribuera skalningsuppsättningar med hjälp av JSON-mallar och [REST API:er](https://msdn.microsoft.com/library/mt589023.aspx) precis som enskilda virtuella Azure Resource Manager-datorer. Därför går det att använda alla standardmetoder för Azure Resource Manager-distribution. Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Du hittar en uppsättning exempelmappar för skalningsuppsättningar till virtuella datorer på [Azure Quickstart-mallarnas GitHub lagringsplats](https://github.com/Azure/azure-quickstart-templates). (Leta efter mallar med **vmss** i titeln.)

För mallexemplen för snabbstart kopplar knappen ”Distribuera till Azure” i readme varje mall till funktionen portaldistribuering. Distribuera skalningsuppsättningen genom att klicka på knappen och sedan fylla i alla parametrar som krävs i portalen. 

## Skala ut eller skala in en skalningsuppsättning
<a id="scaling-a-scale-set-out-and-in" class="xliff"></a>
Du kan ändra kapaciteten för en skalningsuppsättning i Azure Portal genom att klicka på avsnittet **Skalning** under **Inställningar**. 

Om du vill ändra skalningsuppsättningens kapacitet på kommandoraden använder du kommandot **scale** i [Azure CLI](https://github.com/Azure/azure-cli). Du använder exempelvis följande kommando om du vill ställa in en skalningsuppsättnings kapacitet på 10 virtuella datorer:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Ange antalet virtuella datorer i en skalningsuppsättning med hjälp av PowerShell och kommandot **Update-AzureRmVmss**:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Om du vill öka eller minska antalet virtuella datorer i en skalningsuppsättning med hjälp av en Azure Resource Manager-mall ändrar du egenskapen för **kapacitet** och distribuerar mallen igen. Den här smidiga funktionen gör det enkelt att integrera skalningsuppsättningar med automatisk skalning i Azure. Du kan också skriva egna anpassade skalningslager om du behöver definiera anpassade skalningshändelser som inte stöds av funktionen för automatisk skalning i Azure. 

Om du omdistribuerar en Azure Resource Manager-mall för att ändra kapaciteten kan du definiera en mycket mindre mall som bara innehåller **SKU**-egenskapspaketet och den uppdaterade kapaciteten. [Här är ett exempel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

## Automatisk skalning
<a id="autoscale" class="xliff"></a>

En skalningsuppsättning kan alternativt konfigureras med automatiska skalningsinställningar när den skapas i Azure Portal. Antalet virtuella datorer kan sedan ökas eller minskas utifrån genomsnittlig processoranvändning. 

Många av skalningsuppsättningsmallarna i [Azures snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates) definierar inställningarna för automatisk skalning. Du kan också lägga till inställningar för automatisk skalning till en befintlig skalningsuppsättning. Det här Azure PowerShell-skript lägger exempelvis till processorbaserad automatisk skalning till en skalningsuppsättning:

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

Du hittar en lista över giltiga mått för skalning här: [Supported metrics with Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md) (Mått som stöds av Azure Monitor) under rubriken ”Microsoft.Compute/virtualMachineScaleSets”. Mer avancerade alternativ för automatisk skalning finns också, inklusive schemabaserad automatisk skalning och användning av webhook-konfigurationer för att integrera med aviseringssystem.

## Övervaka din skalningsuppsättning
<a id="monitoring-your-scale-set" class="xliff"></a>
I [Azure Portal](https://portal.azure.com) anges skalningsuppsättningar och där visas även deras egenskaper. Portalen har också stöd för hanteringsåtgärder. Du kan utföra hanteringsåtgärder på både skalningsuppsättningar och enskilda virtuella datorer i en skalningsuppsättning. Portalen innehåller också ett anpassningsbart resursanvändningsdiagram. 

Om du behöver visa eller redigera den underliggande JSON-definitionen för en Azure-resurs kan du även använda [Azure Resource Explorer](https://resources.azure.com). Skalningsuppsättningar är en resurs under resursprovidern Microsoft.Compute Azure. Du kan se dem genom att expandera följande länkar från den här platsen:

**Prenumerationer** > **din prenumeration** > **resursgrupper**  > **providrar** > **Microsoft.Compute** >  **virtualMachineScaleSets** > **din skalningsuppsättning** -> osv.

## Scenarier för skalningsuppsättningar
<a id="scale-set-scenarios" class="xliff"></a>
Det här avsnittet innehåller några vanliga scenarier för skalningsuppsättningar. Vissa Azure-tjänster på högre nivåer (som Batch, Service Fabric och Container Service) använder dessa scenarier.

* **Använd RDP eller SSH för att ansluta till skalningsuppsättningsinstanser**: En skalningsuppsättning skapas i ett virtuellt nätverk och enskilda virtuella datorer i skalningsuppsättningen allokeras inte till offentliga IP-adresser som standard. Den här principen undviker utgifts- och administrationskostnader för att allokera separata offentliga IP-adresser för alla noder i rutnätsberäkningen. Om du behöver hänvisa externa anslutningar till skaluppsättningen för virtuella datorer kan du konfigurera en skala som automatiskt tilldelar offentliga IP-adresser till nya virtuella datorer. Du kan alternativt ansluta till dessa virtuella datorer från andra resurser i det virtuella nätverket, till exempel belastningsutjämnare och fristående virtuella datorer, som kan allokeras till offentliga IP-adresser. 
* **Ansluta till virtuella datorer med NAT-regler**: Du kan skapa en offentlig IP-adress, tilldela den en belastningsutjämnare och definiera en inkommande NAT-pool. De här åtgärderna mappar portar på IP-adressen till en port på en virtuell dator i skalningsuppsättningen. Exempel:
  
  | Källa | Källport | Mål | Målport |
  | --- | --- | --- | --- |
  |  Offentlig IP-adress |Port 50000 |vmss\_0 |Port 22 |
  |  Offentlig IP-adress |Port 50001 |vmss\_1 |Port 22 |
  |  Offentlig IP-adress |Port 50002 |vmss\_2 |Port 22 |
  
   I [det här exemplet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat) definieras NAT-regler för att aktivera en SSH-anslutning till varje virtuell dator i en skalningsuppsättning med hjälp av en offentlig IP-adress.
  
   [Det här exemplet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) gör samma sak med RDP och Windows.
* **Ansluta till virtuella datorer med en "jumpbox"**:Om du skapar en skalningsuppsättning och en fristående virtuell dator i samma virtuella nätverk kan den fristående virtuella datorn och den virtuella datorn i en skalningsuppsättning anslutas till varandra via de interna IP-adresser som definieras av det virtuella nätverket/undernätet. Om du skapar en offentlig IP-adress och tilldelar den fristående virtuella datorn kan du använda RDP eller SSH för att ansluta till den fristående virtuella datorn. Du kan sedan ansluta från den datorn till dina skalningsuppsättningsinstanser. Nu kanske du ser att en enkel skalningsuppsättning i sig är säkrare än en enkel fristående virtuell dator med en offentlig IP-adress i standardkonfigurationen.
  
   Till exempel distribuerar [den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox) en enkel skalningsuppsättning med en fristående virtuell dator. 
* **Belastningsutjämning till skalningsuppsättningsinstanser**: Om du vill leverera arbete till ett beräkningskluster av virtuella datorer med en resursallokeringsmetod kan du konfigurera en Azure-belastningsutjämnare med regler för layer-4-belastningsutjämning på motsvarande sätt. Du kan definiera avsökningar för att verifiera att programmet körs genom att skicka pingsignaler till portar med en angiven sökväg för protokoll, intervall och begäran. [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) stöder även skalningsuppsättningar tillsammans med layer-7 och mer avancerade scenarier för belastningsutjämning.
  
   [Det här exemplet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) skapar en skalningsuppsättning som kör Apache-webbservrar och använder en belastningsutjämnare för att jämna ut den belastning som mottas av varje virtuell dator. (Titta på resurstypen Microsoft.Network/loadBalancers och networkProfile och extensionProfile i virtualMachineScaleSet)

   För [det här Linux-exemplet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) och [Windows-exemplet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) används Application Gateway.  

* **Distribuera en skalningsuppsättning som ett beräkningskluster i en PaaS-klusterhanterare**: Skalningsuppsättningar beskrivs ibland som nästa generationens arbetsroll. Det är en giltig beskrivning, men den riskerar också att förväxla skalningsuppsättningsfunktioner med Azure Cloud Services-funktioner. I en viss mening ger skalningsuppsättningar en sann arbetsroll eller arbetsresurs. Detta är en generaliserad beräkningsresurs som är plattforms-/körtidsoberoende, kan anpassas och integreras i Azure Resource Manager IaaS.
  
   En Cloud Services-arbetsroll är begränsad i termer av plattforms-/körtidsstöd (endast Windows-plattformsavbildningar). Men även tjänster som VIP-växling, konfigurerbara inställningar för uppgradering och körtids-/appdistributionsspecifika inställningar finns med. Dessa tjänster är *ännu* inte tillgängliga i skalningsuppsättningar eller också levereras de av andra PaaS-tjänster på högre nivå som Azure Service Fabric. Du kan tänka på skalningsuppsättningar som en infrastruktur som stöder PaaS. PaaS-lösningar som [Service Fabric](https://azure.microsoft.com/services/service-fabric/) bygger på denna infrastruktur.
  
   I [det här exemplet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) med den här metoden distribuerar [Azure Container Service](https://azure.microsoft.com/services/container-service/) ett kluster baserat på skalningsuppsättningar med en behållardirigerare.

## Prestanda för skalningsuppsättningar och riktlinjer för skalning
<a id="scale-set-performance-and-scale-guidance" class="xliff"></a>
* En skalningsuppsättning har stöd för upp till 1 000 virtuella datorer. Om du skapar och laddar upp dina egna anpassade VM-avbildningar är gränsen 100. Faktorer att överväga när du använder stora skalningsuppsättningar finns i [Arbeta med stora skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md).
* Du behöver inte skapa Azure Storage-konton i förväg för att använda skalningsuppsättningar. Skalningsuppsättningar har stöd för Azure-hanterade diskar, vilket eliminerar prestandaproblem relaterade till antalet diskar per lagringskonto. Mer information finns i [skalningsuppsättningar och hanterade diskar för virtuella datorer i Azure](virtual-machine-scale-sets-managed-disks.md).
* Överväg att använda Azure Premium Storage i stället för Azure Storage för snabbare, mer förutsägbara etableringstider för virtuella datorer samt förbättrade IO-prestanda.
* Antalet virtuella datorer som du kan skapa begränsas av kärnkvoten i den region där du distribuerar. Du kan behöva kontakta kundsupporten om du vill öka beräkningskvotgränsen även om du har en hög högsta gräns för kärnor som kan användas med Azure Cloud Services i dag. Om du vill fråga din kvot kör du följande Azure CLI-kommando: `azure vm list-usage`. Eller kör detta PowerShell-kommando: `Get-AzureRmVMUsage`.

## Vanliga frågor och svar om skalningsuppsättningar
<a id="frequently-asked-questions-for-scale-sets" class="xliff"></a>
**F.** Hur många virtuella datorer kan man ha i en skalningsuppsättning?

**S.** En skalningsuppsättning kan innehålla mellan 0 och 1 000 virtuella datorer baserade på plattformsavbildningar eller 0–100 virtuella datorer baserade på anpassade avbildningar. 

**F.** Kan datadiskar användas i skalningsuppsättningar?

**S.** Ja. En skalningsuppsättning kan definiera en konfiguration för anslutna datadiskar som gäller för alla virtuella datorer i uppsättningen. Mer information finns i [Azure scale sets and attached data disks (Azure-skalningsuppsättningar och anslutna datadiskar)](virtual-machine-scale-sets-attached-disks.md). Andra alternativ för att lagra data är:

* Azure-filer (delade SMB-enheter)
* OS-enhet
* Temporär enhet (lokal, backas inte upp av Azure Storage)
* Azure-datatjänst (t.ex. Azure-tabeller, Azure-blobbar)
* Extern datatjänst (t.ex. fjärrdatabas)

**F.** Vilka Azure-regioner har stöd för skalningsuppsättningar?

**S.** Alla regioner stöder skalningsuppsättningar.

**F.** Hur skapar jag en skalningsuppsättning med en anpassad avbildning?

**S.** Skapa en hanterad disk baserat på den anpassade virtuella hårddiskavbildningen (VHD) och referera till den i din mall för skalningsuppsättningen. [Här är ett exempel](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**F.** Vilka virtuella datorer tas bort om jag minskar skalningsuppsättningens kapacitet från 20 till 15?

**S.** Virtuella datorer tas bort från skalningsuppsättningen jämnt bland uppgraderingsdomäner och feldomäner för att maximera tillgängligheten. Virtuella datorer med högst ID tas bort först.

**F.** Hur blir om det om jag sedan ökar kapaciteten från 15 till 18?

**S.** Om du ökar kapaciteten till 18 skapas 3 nya virtuella datorer. Varje gång ökas den virtuella datorinstansens ID från det tidigare högsta värdet (t.ex. 20, 21, 22). Virtuella datorer balanseras mellan feldomäner och uppdateringsdomäner.

**F.** Kan jag framtvinga en körning av sekvensen när jag använder flera tillägg i en skalningsuppsättning?

**S.** Inte direkt, men för tillägget customScript kan skriptet vänta på att ytterligare ett tillägg slutförs. Ytterligare hjälp med ordningsföljden för tillägg finns i det här blogginlägget: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Ordningsföljd för tillägg till skalningsuppsättningar för virtuella datorer i Azure).

**F.** Fungerar skalningsuppsättningar med Azures tillgänglighetsuppsättningar?

**S.** Ja. En skalningsuppsättning är en implicit tillgänglighetsuppsättning med 5 feldomäner och 5 uppdateringsdomäner. Skalningsuppsättningar med mer än 100 virtuella datorer sträcker sig över flera *placeringsgrupper* som är likvärdiga med flera tillgänglighetsuppsättningar. Mer information om placeringsgrupper finns i [Arbeta med stora skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md). En tillgänglighetsuppsättning för virtuella datorer kan finnas i samma virtuella nätverk som en skalningsuppsättning för virtuella datorer. En vanlig konfiguration är att placera virtuella kontrollnodsdatorer (som ofta kräver unika konfigurationer) i en tillgänglighetsuppsättning och placera datanoder i skalningsuppsättningen.

Fler svar på frågor om skalningsuppsättningar finns i [Azure virtual machine scale sets FAQ (Vanliga frågor och svar om skalningsuppsättningar för virtuella datorer i Azure)](virtual-machine-scale-sets-faq.md).

