---
title: "Översikt över skaluppsättningar för virtuella datorer | Microsoft Docs"
description: "Mer information om skaluppsättningar för virtuell dator"
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
ms.date: 1/25/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 868d65642ab9ca3e1c35d33a7cb9e9dd8c31c430
ms.openlocfilehash: f945ac0357c11c70780dea8e62f094457a213d96


---
# <a name="virtual-machine-scale-sets-overview"></a>Översikt över skaluppsättningar för virtuella datorer
Skaluppsättningar för virtuella datorer är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning identiska virtuella datorer. När alla virtuella datorer är likadant konfigurerade är VM-skaluppsättningar utformade för att stödja äkta autoskalning – ingen företablering av VM krävs. Det gör det enklare att bygga storskaliga tjänster som riktar in sig på big compute, stordata och arbetsbelastningar i behållare.

För program som behöver skala beräkningsresurser ut och in är skalningsåtgärderna balanserade implicit i fel- och uppdateringsdomäner. En introduktion till VM-skaluppsättningar finns i [Azure-bloggmeddelandet](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Titta igenom dessa videor för mer information om VM-skaluppsättningar:

* [Mark Russinovich berättar om Azure-skaluppsättningar](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Skaluppsättningar för virtuell dator med Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Skapa och hantera VM-skaluppsättningar
Du kan skapa en VM-skaluppsättning i [Azure Portal](https://portal.azure.com) genom att välja *Ny* och skriva ”skala” i sökfältet. I resultatet ser du ”skaluppsättning för virtuell dator”. Därifrån kan du fylla i fälten som krävs för att anpassa och distribuera din skaluppsättning. Observera det även finns alternativ i portalen för att ställa in grundläggande regler för automatisk skalning som baseras på CPU-användning.

VM-skaluppsättningar kan också definieras och distribueras med JSON-mallar och [REST API:er](https://msdn.microsoft.com/library/mt589023.aspx) precis som enskilda Azure Resource Manager-mallar. Därför går det att använda alla standardmetoder för Azure Resource Manager-distribution. Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Några exempel på mallar för VM-skaluppsättningar finns i Azure snabbstarts-mallarna i GitHub-lagringsplatsen [här.](https://github.com/Azure/azure-quickstart-templates) (leta efter mallar med *vmss* i titeln)

På informationssidorna för mallarna finns en knapp som länkar till portalens distributionsfunktion. Klicka på knappen för att distribuera VM-skaluppsättningen och fyll sedan i alla parametrar som krävs i portalen. Om du är osäker på om en resurs stöder versaler eller både gemener och versaler är det säkrast att alltid använda parametervärden med gemener och nummer. Det finns också en praktisk videoanalys av en mall för VM-skaluppsättningar här:

[Analys av mall för VM-skaluppsättning](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Skala ut eller in en VM-skaluppsättning
Om du vill öka eller minska antalet virtuella datorer i en VM-skaluppsättning ändrar du bara egenskapen för *kapacitet* och distribuerar mallen igen. Tack vare enkelheten är det lätt att skriva ett eget anpassat skalningslager om du vill definiera anpassade skalhändelser som inte stöds av Azure-autoskalning.

Om du omdistribuerar en mall för att ändra kapaciteten kan du definiera en mycket mindre mall som bara innehåller SKU-egenskapspaketet och den uppdaterade kapaciteten. Ett exempel på detta visas [här.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Om du vill gå igenom de steg som kan skapa en skaluppsättning som skalas automatiskt kan du läsa [Skala automatiskt datorer i en skalningsuppsättning för virtuella datorer](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>Övervaka din VM-skaluppsättning
[Azure Portal](https://portal.azure.com) visar skalningsuppsättningar och grundläggande egenskaper och åtgärder, inklusive listor över virtuella datorer i uppsättningen och ett diagram över resursanvändningen. Om du vill ha mer information kan du använda [resursutforskaren i Azure](https://resources.azure.com) för att visa VM-skaluppsättningar. VM-skaluppsättningar är en resurs under Microsoft.Compute, så på den webbplatsen kan du se dem om du expanderar följande länkar:

**Prenumerationer -> din prenumeration -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> din VM-skalningsuppsättning -> osv.**

## <a name="vm-scale-set-scenarios"></a>Scenarier för VM-skaluppsättningar
Det här avsnittet innehåller några vanliga scenarier för VM-skaluppsättningar. Vissa Azure-tjänster på högre nivåer (som Batch, Service Fabric, Azure Container Service) använder dessa scenarier.

* **RDP / SSH till VM-skaluppsättningsinstanser** – en VM-skaluppsättning skapas i ett virtuellt nätverk och enskilda virtuella datorer i skaluppsättningen allokeras inte till offentliga IP-adresser. Detta är bra eftersom du normalt inte vill ha omkostnaderna och hanteringen som det innebär att allokera separata offentliga IP-adresser till alla tillståndslösa resurser i beräkningsrutnätet. Du kan enkelt ansluta till de virtuella datorerna från andra resurser i ditt VNET, däribland sådana som har offentliga IP-adresser som belastningsutjämnare eller fristående virtuella datorer.
* **Ansluta till virtuella datorer med NAT-regler** – Du kan skapa en offentlig IP-adress, tilldela den till en belastningsutjämnare och definiera en inkommande NAT-pool som mappar portar på IP-adressen till en port på en virtuell dator på VM-skalningsuppsättningen. Exempel:
  
  | Källa | Källport | Mål | Målport |
  | --- | --- | --- | --- |
  |  Offentlig IP-adress |Port 50000 |vmss\_0 |Port 22 |
  |  Offentlig IP-adress |Port 50001 |vmss\_1 |Port 22 |
  |  Offentlig IP-adress |Port 50002 |vmss\_2 |Port 22 |
  
   Här är ett exempel på hur du skapar en VM-skaluppsättning som använder NAT-regler för att aktivera SSH-anslutning till varje virtuell dator i en skaluppsättning med en enda offentlig IP-adress: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Här är ett exempel på hur du gör samma sak med RDP och Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Ansluta till virtuella datorer med en "jumpbox"** – Om du skapar en VM-skaluppsättning och en fristående virtuell dator i samma VNET kan den fristående virtuella datorn och en virtuell dator i en VM-skaluppsättning anslutas till varandra med sina interna IP-adresser som definieras av VNET/undernätet. Om du skapar en offentlig IP-adress och tilldelar den till fristående virtuella datorer kan du använda RDP eller SSH för fristående virtuella datorer och sedan ansluta från den datorn till dina VM-skaluppsättningsinstanser. Du kanske ser nu att en enkel VM-skaluppsättning i sig är säkrare än en enkel fristående VM med en offentlig IP-adress i standardkonfigurationen.
  
   Den här mallen distribuerar till exempel en enkel skalningsuppsättning med en fristående virtuell dator: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Belastningsutjämning till VM-skalningsuppsättningsinstanser** – Om du vill leverera arbete till ett beräkningskluster av virtuella datorer med en "resursallokeringsmetod" kan du konfigurera en Azure-belastningsutjämnare med regler för layer-4-belastningsutjämning. Du kan definiera avsökningar för att verifiera att programmet körs genom att skicka pingsignaler till portar med en angiven sökväg för protokoll, intervall och begäran. Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) stöder även skalningsuppsättningar tillsammans med layer-7 och mer avancerade scenarier för belastningsutjämning.
  
   Här är ett exempel som skapar en VM-skalningsuppsättning som kör Apache-webbservrar och som använder en belastningsutjämnare för att utjämna belastningen som varje virtuell dator tar emot: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (titta på resurstypen Microsoft.Network/loadBalancers och networkProfile och extensionProfile i virtualMachineScaleSet)
* **Distribuera en VM-skaluppsättning som ett beräkningskluster i en PaaS-klusterhanterare** – VM-skaluppsättningar beskrivs ibland som nästa generationens arbetsroll. Det är en giltig beskrivning, men den riskerar också att förväxla skalningsuppsättningsfunktioner med PaaS v1-arbetsrollfunktioner. I en mening erbjuder skalningsuppsättningar för virtuella datorer en äkta "arbetsroll" eller arbetsresurs på så sätt att de tillhandahåller en generaliserad beräkningsresurs som är plattforms-/körningsberoende, anpassningsbar och integreras i Azure Resource Manager-IaaS.
  
   En PaaS v1-arbetsroll, som är begränsad vad gäller support för plattform/körning (endast Windows-avbildningar), innehåller även tjänster som VIP-byte, konfigurerbara uppgraderingsinställningar och körnings-/appdistributionsinställningar som antingen inte *ännu* är tillgängliga i VM-skaluppsättningar, eller som kommer att lanseras av andra PaaS-tjänster på högre nivå, som Service Fabric. Med detta i kan åtanke kan du titta på VM-skaluppsättningar som en infrastruktur som stöder PaaS. Dvs. PaaS-lösningar som Service Fabric eller klusterhanterare som Mesos kan byggas ovanpå VM-skaluppsättningar som ett skalbart beräkningslager.
  
   Ett exempel på den här metoden är när Azure Container Service distribuerar ett kluster baserat på skalningsuppsättningar med en behållardirigering: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Riktlinjer för prestanda och skalning för VM-skaluppsättningar
* Planera inte för fler än 20 virtuella datorer per lagringskonto när du använder skalningsuppsättningar med ohanterade diskar (om du inte ställer in egenskapen *overprovision* på "falskt" som innebär att du kan gå upp till 40). Gränserna per lagringskonto gäller inte med hanterade diskar.
* Sprid ut de första bokstäverna i lagringskontonamnen så mycket som möjligt när du använder skalningsuppsättningar med ohanterade diskar. Exemplen på VMSS-mallar i [Azure-snabbstartmallar](https://github.com/Azure/azure-quickstart-templates/) ger exempel på hur du ska gå tillväga.
* Om du använder egna virtuella datorer med ohanterade diskar bör du inte planera för fler än 40 virtuella datorer per VM-skalningsuppsättning i ett enda lagringskonto. Du behöver kopiera avbildningen till lagringskontot innan du kan börja distribuera VM-skaluppsättningen. Skalningsuppsättningar med hanterade diskar stöder upp till 100 anpassade avbildningar av virtuella datorer. Läs vanliga frågor och svar för mer information.
* Planera inte för fler än 4096 virtuella datorer per VNET.
* Antalet virtuella datorer som du kan skapa begränsas av kärnkvoten i den region där du distribuerar. Du kan behöva kontakta kundsupport om du vill öka beräkningskvotgränsen även om du har en hög högsta gräns för kärnor som kan användas med molntjänster eller IaaS v1 idag. För att köra frågan om kvot kan du köra följande Azure CLI-kommando: `azure vm list-usage` och följande PowerShell-kommando: `Get-AzureRmVMUsage` (om du använder en tidigare version än PowerShell 1.0 ska du använda `Get-AzureVMUsage`).

## <a name="vm-scale-set-frequently-asked-questions"></a>Vanliga frågor och svar om VM-skaluppsättningar
**F.** Hur många virtuella datorer man ha i en VM-skaluppsättning?

**S.** Med hanterade diskar kan din skalningsuppsättning ha mellan 0 och 1 000 virtuella datorer baserade på plattformsavbildningar eller 0-100 virtuella datorer baserade på anpassade avbildningar. För ohanterade diskar (där du själv definierar dina egna lagringskonton) är gränsen 100 virtuella datorer för plattformsavbildningar och upp till 40 för anpassade avbildningar (om egenskapen *overprovision* är inställd på "falskt", 20 som standard), eftersom anpassade bilder med ohanterade diskar är begränsade till ett enda lagringskonto.

**F.** Stöds datadiskar inom VM-skaluppsättningar?

**S.** Ja. En skalningsuppsättning som definierats med hanterad lagring kan definiera en konfiguration för anslutna dataenheter som gäller för alla virtuella datorer i uppsättningen. Skalningsuppsättningar som inte är definierade med hanterad lagring har inga anslutna dataenheter. Andra alternativ för att lagra data är:

* Azure-filer (delade SMB-enheter)
* OS-enhet
* Temp-enhet (lokal, backas inte upp av Azure Storage)
* Azure-datatjänsten (t.ex. Azure-tabeller, Azure-blobar)
* Extern datatjänst (t.ex. fjärrdatabas)

**F.** Vilka Azure-regioner stöder VM-skaluppsättningar?

**S.** Alla regioner stöder skaluppsättningar för virtuella datorer.

**F.** Hur skapas en VM-skaluppsättning med en anpassad avbildning?

**S.** Lämna vhdContainers-egenskapen tom (eller utelämna den) och ange avbildningens egenskaps-URI. Exampel: [201-vmss-windows-customimage].(https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-customimage)


**F.** Vilka virtuella datorer tas bort om jag minskar VM-skaluppsättningens kapacitet från 20 till 15?

**S.** Virtuella datorer tas bort från skaluppsättningen jämnt bland uppgraderingsdomäner och feldomäner för att maximera tillgängligheten. Virtuella datorer med den högst id tas bort först.

**F.** Hur blir om det om jag sedan ökar kapaciteten från 15 till 18?

**S.** Om du ökar kapaciteten till 18 skapas 3 nya virtuella datorer. Varje gång ökas den virtuella datorinstansens ID från det tidigare högsta värdet (t.ex. 20, 21, 22). Virtuella datorer balanseras mellan FD och UD.

**F.** Kan jag framtvinga en körning av aktivitetssekvensen när jag använder flera tillägg i en VM-skaluppsättning?

**S.** Inte direkt, men för tillägget customScript kan skriptet vänta på ytterligare ett tillägg med att slutföra ([exempelvis genom att övervaka tilläggets logg](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Ytterligare hjälp med ordningsföljden för tillägg finns i det här blogginlägget: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Ordningsföljd för tillägg i Azure VM-skaluppsättningar).

**F.** Fungerar VM-skaluppsättningar med Azures tillgänglighetsuppsättningar?

**S.** Ja. En VM-skaluppsättning är en implicit tillgänglighetsuppsättning med 5 FD och 5 UD.  Du behöver inte konfigurera något under virtualMachineProfile. Skalningsuppsättningar för virtuella datorer med mer än 100 virtuella datorer sträcker sig över flera "placeringsgrupper" som är likvärdiga med flera tillgänglighetsuppsättningar. En tillgänglighetsuppsättning för virtuella datorer kan finnas i samma VNET som en skalningsuppsättning för virtuella datorer. En vanlig konfiguration är att placera virtuella kontrollnodsdatorer som ofta kräver unika konfigurationer i tillgänglighetsuppsättningen och datanoder i skalningsuppsättningen.



<!--HONumber=Feb17_HO1-->


