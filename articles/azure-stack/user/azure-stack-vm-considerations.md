---
title: Skillnader och överväganden för virtuella datorer i Azure-stacken | Microsoft Docs
description: Läs mer om skillnader och överväganden när du arbetar med virtuella datorer i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: brenduns
ms.openlocfilehash: 324fa19aa97cead44f38d07a2fd0765048cd6238
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605396"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Överväganden för att använda virtuella datorer i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Stack virtuella datorer innehåller på begäran skalbara datorresurser. Innan du distribuerar virtuella datorer (VM), måste du förstå skillnaderna mellan de virtuella funktionerna i Azure-stacken och Microsoft Azure. Den här artikeln beskriver dessa skillnader och identifierar viktiga överväganden för planering av distribution av virtuella datorer. Mer information om övergripande skillnader mellan Azure-stacken och Azure, finns det [nyckeln överväganden](azure-stack-considerations.md) artikel.

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
|Tillgänglighetsuppsättningar för virtuell dator|Flera feldomäner (2 eller 3 per region)<br>Flera domäner för uppdatering<br>Hantering av disk|Flera feldomäner (2 eller 3 per region)<br>Flera update domäner (upp till 20)<br>Inget stöd för hanterade diskar|
|Skalningsuppsättningar för virtuella datorer|Autoskala som stöds|Autoskala stöds inte.<br>Lägga till fler instanser i en skala som anges med portalen, Resource Manager-mallar eller PowerShell.

## <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

Azure-stacken inför gränserna för att undvika över förbrukningen av resurser (server lokala och servicenivåer.) Dessa gränser förbättra upplevelsen klient genom att minska effekten av resurser som används av andra klienter.

- För nätverk utgång från den virtuella datorn finns bandbredd caps på plats. Versaler i Azure-stacken är samma som caps i Azure.
- För lagringsresurser implementerar Azure Stack IOPS lagringsgränser för att undvika grundläggande överförbrukning av resurser av klienter för åtkomst till lagring.
- För virtuella datorer med flera anslutna diskar är det största genomflödet i varje datadisk 500 IOPS för HHDs och 2300 IOPS för SSD-enheter.

I följande tabell visas de virtuella datorerna som stöds på Azure-Stack tillsammans med deras konfiguration:

| Typ           | Storlek          | Rad storlekar som stöds |
| ---------------| ------------- | ------------------------ |
|Generellt syfte |Basic A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Generellt syfte |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Generellt syfte |D-serien       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Generellt syfte |Dv2-serien     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Generellt syfte |DS-serien      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Generellt syfte |DSv2-serien    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Minnesoptimerad|D-serien       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Minnesoptimerad|DS-serien      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Minnesoptimerad|Dv2-serien     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Minnesoptimerad|DSv2-serien-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Storlekar för virtuella datorer och deras associerad resurs kvantiteter stämmer överens mellan Azure-stacken och Azure. Detta inkluderar mängden minne, antal kärnor och nummer eller storlek för datadiskar som kan skapas. Prestanda för virtuella datorer med samma storlek beror dock på underliggande egenskaperna för en viss Azure Stack-miljö.

## <a name="virtual-machine-extensions"></a>Tillägg för virtuell dator

 Azure-stacken innehåller en liten uppsättning tillägg. Uppdateringar och ytterligare tillägg är tillgängligt via Marketplace-syndikeringsfeed.

Använd följande PowerShell-skript för att hämta listan över tillägg för virtuell dator som är tillgängliga i Azure Stack-miljö:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>API-versioner

Funktioner på virtuella datorer i Azure-stacken stöder följande API-versioner:

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

## <a name="windows-activation"></a>Windows-aktivering

Windows-produkter måste användas i enlighet med användarrättigheter och licensvillkoren för Microsoft. Azure-stacken använder [automatisk aktivering av Virtuella](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) för att aktivera Windows Server virtuella maskiner (VMs).

- Azure Stack-värd aktiverar Windows med AVMA-nycklar för Windows Server 2016. Alla virtuella datorer som kör Windows Server 2012 eller senare aktiveras automatiskt.
- Virtuella datorer som kör Windows Server 2008 R2 aktiveras inte automatiskt och måste aktiveras med hjälp av [MAK-aktivering](https://technet.microsoft.com/library/ff793438.aspx). Om du vill använda MAK-aktivering, måste du ange egna produktnyckel.

Microsoft Azure använder KMS-aktivering för att aktivera Windows-datorer. Om du flyttar en virtuell dator från Azure-stacken till Azure och stöter aktivera problem, se [aktiveringsproblem för felsökning av Windows Azure virtuella](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Mer information finns på den [felsökning av Windows-aktivering fel på Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) Azure Support Teamblogg post.

## <a name="next-steps"></a>Nästa steg

[Skapa en Windows-dator med PowerShell i Azure-stacken](azure-stack-quick-create-vm-windows-powershell.md)