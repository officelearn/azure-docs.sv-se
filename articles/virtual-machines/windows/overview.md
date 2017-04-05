---
title: "Översikt över Windows Virtual Machines | Microsoft Docs"
description: "Läs mer om hur du skapar och hanterar virtuella Windows-datorer i Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d128d1f5bdf054accac87631a5d6a65f3bb15ea6
ms.lasthandoff: 04/03/2017


---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Översikt över virtuella Windows-datorer i Azure
Virtuella Azure-datorer (Virtual Machines, VM) är en av flera typer av [behovsbaserade och skalbara datorresurser](../../app-service-web/choose-web-site-cloud-service-vm.md) som Azure erbjuder. Normalt använder du en virtuell dator om du behöver mer kontroll över datormiljön än vad de andra alternativen erbjuder. Den här artikeln innehåller information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar den och hur du hanterar den.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du behöver dock fortfarande underhålla den virtuella datorn genom att utföra uppgifter, som att konfigurera, korrigera och underhålla programvaran som körs på den virtuella datorn.

Virtuella datorer i Azure kan användas på olika sätt. Några exempel är:

* **Utveckling och tester** – Virtuella datorer i Azure erbjuder ett snabbt och enkelt sätt att skapa en dator med specifika konfigurationer som krävs för att koda och testa ett program.
* **Program i molnet** – Eftersom behovet av ditt program kan variera kan det vara bra ur ekonomisk synpunkt att köra den på en virtuell dator i Azure. Du betalar extra för virtuella datorer när du behöver dem och stänger av dem när du inte behöver dem.
* **Utökat datacenter** – Virtuella datorer i ett virtuellt nätverk i Azure kan enkelt anslutas till organisationens nätverk.

Antalet virtuella datorer som programmet använder kan skalas upp och ned beroende på vilka behov du har.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Vad behöver jag tänka på innan jag skapar en virtuell dator?
Det finns alltid en rad [överväganden vid utformning](infrastructure-virtual-machine-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) när du utökar en programinfrastruktur i Azure. Följande aspekter av en virtuell dator är viktiga att tänka på innan du börjar:

* Programresursernas namn
* Lagringsplatsen för resurserna
* Den virtuella datorns storlek
* Det högsta antalet virtuella datorer som kan skapas
* Operativsystemet som körs på den virtuella datorn
* Konfigurationen av den virtuella datorn när den startats
* Relaterade resurser som krävs för den virtuella datorn

### <a name="naming"></a>Namngivning
En virtuell dator har ett tilldelat [namn](infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och ett datornamn som konfigurerats som en del av operativsystemet. En virtuell dators namn kan bestå av upp till 15 tecken.

Om du använder Azure för att skapa operativsystemsdisken är datornamnet och namnet på den virtuella datorn detsamma. Om du [laddar upp och använder en egen avbildning](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) som innehåller ett tidigare konfigurerat operativsystem, och du använder den för att skapa en virtuell dator, kan namnen bli olika. Vi rekommenderar att du ger datorn samma namn i operativsystemet som i den virtuella datorn när du laddar upp en egen avbildningsfil.

### <a name="locations"></a>Platser
Alla resurser som skapats i Azure fördelas på flera [geografiska områden](https://azure.microsoft.com/regions/) runtom i världen. Vanligtvis kallas regionen **plats** när du skapar en virtuell dator. För en virtuell dator anger platsen var de virtuella hårddiskarna lagras.

I den här tabellen finns några exempel på hur du kan hämta en lista över tillgängliga platser.

| Metod | Beskrivning |
| --- | --- |
| Azure Portal |Välj en plats i listan när du skapar en virtuell dator. |
| Azure PowerShell |Använd kommandot [Get-AzureRmLocation](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/get-azurermlocation). |
| REST API |Använd åtgärden [List locations](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations) (Listplatser). |

### <a name="vm-size"></a>Storlek på virtuell dator
[Storleken](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) på den virtuella datorn som du använder bestäms av den arbetsbelastning som du vill köra. Storleken som du väljer avgör sedan faktorer som processorkraft, minne och lagringskapacitet. Azure erbjuder en rad olika storlekar för att passa en mängd olika användningar.

Azure debiterar ett [pris per timme](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) baserat på storleken och operativsystemet för den virtuella datorn. För delar av timmar tar Azure bara betalt för användningen per minut. Lagringsutrymme prissätts och debiteras separat.

### <a name="vm-limits"></a>Begränsningar för den virtuella datorn
Prenumerationen har [standardkvotgränser](../../azure-subscription-service-limits.md) som kan påverka ditt projekt om många virtuella datorer distribueras. Den aktuella gränsen på basis av per prenumeration är 20 virtuella datorer per region. Begränsningen kan ökas om du anmäler ett supportärende och begär en ökning.

### <a name="operating-system-disks-and-images"></a>Operativsystemsdiskar och avbildningar
Virtuella datorer använder [virtuella hårddiskar (VHD:ar)](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att lagra sitt operativsystem (OS) och data. VHD:ar används också för de avbildningar du kan välja mellan för att installera ett operativsystem. 

Azure tillhandahåller många [Marketplace-avbildningar](https://azure.microsoft.com/marketplace/virtual-machines/) som kan användas med olika versioner och typer av Windows Server-operativsystem. Marketplace-avbildningar identifieras av avbildningens utgivare, erbjudande, sku och version (vanligtvis anges versionen som den senaste). 

I den här tabellen hittar du några olika sätt för att hitta informationen om en avbildning.

| Metod | Beskrivning |
| --- | --- |
| Azure Portal |Värdena anges automatiskt åt dig när du väljer en avbildning som du vill använda. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher) -Location "location"<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer) -Location "location" -Publisher "publisherName"<BR>[Get-AzureRMVMImageSku](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagesku) -Location "location" -Publisher "publisherName" -Offer "offerName" |
| REST API:er |[Lista över avbildningsutgivare](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Lista över avbildningserbjudanden](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Lista över avbildnings-SKU:er](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |

Du kan välja att [ladda upp och använda en egen avbildning](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du gör det används inte utgivarnamn, erbjudande och sku.

### <a name="extensions"></a>Tillägg
[Tillägg](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för virtuella datorer ger din virtuella dator fler funktioner genom konfiguration efter distribution och automatiserade uppgifter.

Dessa vanliga uppgifter kan utföras med hjälp av tillägg:

* **Köra anpassade skript** – [Tillägget för anpassat skript](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hjälper dig att konfigurera arbetsbelastningar på den virtuella datorn genom att köra skriptet när den virtuella datorn har etablerats.
* **Distribuera och hantera konfigurationer** – Med [PowerShell-tillägget för önskad tillståndskonfiguration (DSC)](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kan du konfigurera DSC på en virtuell dator så att du kan hantera konfigurationer och miljöer.
* **Samla in diagnostikdata** – Med [Azure Diagnostics-tillägget](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kan du konfigurera den virtuella datorn så att den samlar in diagnostikdata som kan användas för att övervaka hälsotillståndet för programmet.

### <a name="related-resources"></a>Relaterade resurser
Resurserna i den här tabellen används av den virtuella datorn och måste finnas eller skapas när den virtuella datorn skapas.

| Resurs | Krävs | Beskrivning |
| --- | --- | --- |
| [Resursgrupp](../../azure-resource-manager/resource-group-overview.md) |Ja |Den virtuella datorn måste ingå i en resursgrupp. |
| [Lagringskonto](../../storage/storage-create-storage-account.md) |Ja |Den virtuella datorn behöver lagringskontot för att kunna lagra sina virtuella hårddiskar. |
| [Virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) |Ja |Den virtuella datorn måste ingå i ett virtuellt nätverk. |
| [Offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Nej |Den virtuella datorn kan tilldelas en offentlig IP-adress för att möjliggöra fjärråtkomst till den. |
| [Nätverksgränssnitt](../../virtual-network/virtual-network-network-interface.md) |Ja |Den virtuella datorn behöver nätverksgränssnittet för att kunna kommunicera i nätverket. |
| [Datadiskar](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nej |Den virtuella datorn kan innehålla datadiskar för att expandera lagringskapaciteten. |

## <a name="how-do-i-create-my-first-vm"></a>Hur skapar jag min första virtuella dator?
Du har flera valmöjligheter när du skapar en virtuell dator. Valen du gör beror på vilken miljö du använder. 

Den här tabellen innehåller information som hjälper dig att komma igång med din virtuella dator.

| Metod | Artikel |
| --- | --- |
| Azure Portal |[Skapa en virtuell dator som kör Windows med hjälp av portalen](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Mallar |[Skapa en virtuell Windows-dator med en Resource Manager-mall](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Skapa en virtuell Windows-dator med PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Klient-SDK: er |[Distribuera Azure-resurser med C#](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API:er |[Skapa eller uppdatera en virtuell dator](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |

Man hoppas att det aldrig händer, men ibland går något fel. Om det händer kan du läsa informationen i [Felsöka distributionsproblem i Resource Manager när du skapar en virtuell Windows-dator i Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Hur hanterar jag den virtuella datorn som jag skapat?
Virtuella datorer kan hanteras från en webbläsarbaserad portal, kommandoradsverktyg med skriptstöd eller direkt från API:erna. Några vanliga hanteringsuppgifter som du kan utföra är att få information om en virtuell dator, logga in på en virtuell dator, hantera tillgänglighet och göra säkerhetskopior.

### <a name="get-information-about-a-vm"></a>Hämta information om en virtuell dator
I den här tabellen visar vi några exempel på hur du kan få information om en virtuell dator.

| Metod | Beskrivning |
| --- | --- |
| Azure Portal |På hubbmenyn klickar du på **Virtuella datorer** och sedan väljer du den virtuella datorn från listan. På bladet för den virtuella datorn har du åtkomst till översikter med information, inställning av värden och övervakning av mått. |
| Azure PowerShell |Information om hur du använder PowerShell för att hantera virtuella datorer finns i [Hantera Azure Virtual Machines med hjälp av Resource Manager och PowerShell](ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| REST API |Använd åtgärden [Get VM information](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) (Hämta information om virtuell dator) för att få information om en virtuell dator. |
| Klient-SDK: er |Information om hur du använder C# för att hantera virtuella datorer finns i [Hantera Azure Virtual Machines med hjälp av Azure Resource Manager och C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |

### <a name="log-on-to-the-vm"></a>Logga in på den virtuella datorn
Du använder knappen Anslut på Azure Portal för att [starta en fjärrskrivbordssession (RDP)](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ibland kan det bli fel när någon försöker använda en fjärranslutning. Om det händer kan du läsa hjälpinformationen i [Felsöka Remote Desktop-anslutningar till en virtuell dator i Azure-dator som kör Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="manage-availability"></a>Hantera tillgänglighet
Det är viktigt att förstå hur du [säkerställer hög tillgänglighet](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för ditt program. I den här konfigurationen skapar du flera virtuella datorer för att försäkra dig om att minst en körs.

För att distributionen ska kunna omfattas av vårt 99,95 VM-serviceavtal behöver du distribuera två eller flera virtuella datorer som kör arbetsbelastningen i en [tillgänglighetsuppsättning](infrastructure-availability-sets-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här konfigurationen säkerställer att dina virtuella datorer distribueras via flera feldomäner och på värdar med olika underhållsfönster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) förklaras den garanterade tillgängligheten för Azure som helhet.

### <a name="back-up-the-vm"></a>Säkerhetskopiera den virtuella datorn
Ett [Recovery Services-valv](../../backup/backup-introduction-to-azure-backup.md) används för att skydda data och tillgångar i både Azure Backup- och Azure Site Recovery-tjänster. Du kan använda ett Recovery Services-valv till att [distribuera och hantera säkerhetskopior för Resource Manager-distribuerade virtuella datorer som använder PowerShell](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Nästa steg
* Om du har för avsikt att arbeta med virtuella datorer i Linux kan du läsa mer i [Azure och Linux](../linux/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ta reda på mer om riktlinjerna för konfiguration av infrastrukturen i [Genomgång av en exempelinfrastruktur i Azure](infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Se till att du följer [Metodtips för att köra en virtuell Windows-dator på Azure](guidance-compute-single-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


