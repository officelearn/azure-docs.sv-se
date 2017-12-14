---
title: "Skillnader och överväganden för virtuella datorer i Azure-stacken | Microsoft Docs"
description: "Läs mer om skillnader och överväganden när du arbetar med virtuella datorer i Azure-stacken."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: mabrigg
ms.openlocfilehash: fe655facf4da99d951a430db8ce603cc0ec7f224
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Överväganden för virtuella datorer i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Virtuella datorer är en på begäran, skalbara datorresurser som erbjuds av Azure-stacken. När du använder virtuella datorer, måste du förstå att det är skillnad mellan de funktioner som är tillgängliga i Azure och Azure-stacken. Den här artikeln innehåller en översikt över unika överväganden för virtuella datorer och dess funktioner i Azure-stacken. Mer information om övergripande skillnader mellan Azure-stacken och Azure, finns det [nyckeln överväganden](azure-stack-considerations.md) artikel.

## <a name="cheat-sheet-virtual-machine-differences"></a>Cheat blad: virtuella skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
| Avbildningar av virtuella datorer | Azure Marketplace innehåller bilder som du kan använda för att skapa en virtuell dator. Finns det [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) sidan om du vill visa en lista över bilder som är tillgängliga i Azure Marketplace. | Som standard, det är inte alla bilder tillgängliga i stacken för Azure marketplace. Molnadministratören Azure Stack ska publicera eller hämta bilder till stacken för Azure marketplace innan användarna kan använda dem. |
| Storlekar för virtuella datorer | Azure stöder en mängd olika storlekar för virtuella datorer. Mer information om tillgängliga storlekar och alternativ, referera till den [Windows storlekar för virtuella datorer](../../virtual-machines/virtual-machines-windows-sizes.md) och [Linux virtuella datorstorlekar](../../virtual-machines/linux/sizes.md) avsnitt. | Azure-stacken stöder en delmängd av storlekar för virtuella datorer som är tillgängliga i Azure. Om du vill visa listan över storlekar som stöds finns i den [storlekar för virtuella datorer](#virtual-machine-sizes) i den här artikeln. |
| Kvoter för virtuell dator | [Kvotgränser](../../azure-subscription-service-limits.md#service-specific-limits) ställs in av Microsoft | Molnadministratören Azure stacken måste tilldela kvoter innan de erbjuder virtuella datorer till sina användare. |
| Tillägg för virtuell dator |Azure stöder en mängd olika tillägg för virtuell dator. Mer information om tillgängliga tillägg, referera till den [tillägg för virtuell dator och funktioner](../../virtual-machines/windows/extensions-features.md) artikel.| Azure-stacken stöder en delmängd av tillägg som är tillgängliga i Azure och var och en av tillägget har specifika versioner. Azure-stacken molnadministratören kan välja vilka tillägg som ska göras tillgänglig för användarna. Om du vill visa en lista över tillägg som stöds, referera till den [virtuella datortillägg](#virtual-machine-extensions) i den här artikeln. |
| Nätverk för virtuella datorer | Offentliga IP-adresser som tilldelats virtuella dator är tillgängliga via Internet.<br><br><br>Virtuella Azure-datorer har ett fast DNS-namn | Offentliga IP-adresser som är tilldelade till en virtuell klientdator är tillgängliga i Azure-stacken Development Kit-miljön. Användarna måste ha åtkomst till Azure-stacken Development Kit via [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) eller [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) att ansluta till en virtuell dator som har skapats i Azure-stacken.<br><br>Virtuella datorer skapas i en specifik Azure Stack-instans har ett DNS-namn baserat på värdet som har konfigurerats av administratören för molnet. |
| Den virtuella datorns lagringsutrymme | Stöder [hanterade diskar.](../../virtual-machines/windows/managed-disks-overview.md) | Hanterade diskar stöds inte ännu i Azure-stacken. |
| API-versioner | Azure har alltid de senaste API-versionerna för funktionerna för virtuell dator. | Azure-stacken stöder specifika Azure-tjänster och specifika API-versioner för dessa tjänster. Om du vill visa listan över API-versioner som stöds finns i den [API-versioner](#api-versions) i den här artikeln. |
|Tillgänglighetsuppsättningar för virtuell dator|Flera feldomäner (2 eller 3 per region)<br>Flera domäner för uppdatering<br>Hantering av disk|Enkel feldomän<br>Domän för enskild uppdatering<br>Inget stöd för hanterade diskar|
|Skalningsuppsättningar för virtuella datorer|Autoskala som stöds|Autoskala stöds inte.<br>Lägga till fler instanser i en skala som anges med portalen, Resource Manager-mallar eller PowerShell.

## <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer 

Azure-stacken Development Kit stöder följande storlekar: 

| Typ | Storlek | Rad storlekar som stöds |
| --- | --- | --- |
|Generellt syfte |Basic A|A0 - A4|
|Generellt syfte |Standard A|A0–A7|
|Generellt syfte |D-serien|D1 - D4|
|Generellt syfte |Dv2-serien|D1_v2 - D5_v2|
|Generellt syfte |DS-serien|DS1 - DS4|
|Generellt syfte |DSv2-serien|DS1_v2 - DS5_v2|
|Minnesoptimerad|DS-serien|DS11 - DS14|
|Minnesoptimerad |DSv2-serien|DS11_v2 - DS14_v2|

Storlekar för virtuella datorer och deras associerad resurs kvantiteter stämmer överens mellan Azure-stacken och Azure. Detta inkluderar till exempel hur mycket minne, antal kärnor och nummer eller storlek för datadiskar som kan skapas. Prestanda i samma VM-storlek i Azure-stacken beror dock på underliggande egenskaperna för en viss Azure Stack-miljö.

## <a name="virtual-machine-extensions"></a>Tillägg för virtuell dator 

 Azure-stacken Development Kit stöder följande virtuella tillägget versioner:

![VM-tillägg](media/azure-stack-vm-considerations/vm-extensions.png)

Använd följande PowerShell-skript för att hämta listan över tillägg för virtuell dator som är tillgängliga i Azure Stack-miljö:

```powershell 
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize 
```

## <a name="api-versions"></a>API-versioner 

Funktioner på virtuella datorer i Azure-stacken Development Kit stöder följande API-versioner:

![Resurstyper för VM](media/azure-stack-vm-considerations/vm-resoource-types.png)

Du kan använda följande PowerShell-skript för att hämta API-versioner för virtuell dator-funktioner som är tillgängliga i Azure Stack-miljö:

```powershell 
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
Listan över resurstyper som stöds och API-versioner kan variera om operatorn molnet uppdaterar din Azure Stack-miljö till en nyare version.

## <a name="next-steps"></a>Nästa steg

[Skapa en Windows-dator med PowerShell i Azure-stacken](azure-stack-quick-create-vm-windows-powershell.md)
