---
title: Skillnader och överväganden för virtuella datorer i Azure Stack | Microsoft Docs
description: Läs mer om skillnader och överväganden när du arbetar med virtuella datorer i Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: f1088e63b33d7c0a00777d7a06e6e80244acc84d
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954874"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Att tänka på när virtuella datorer i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack-datorer tillhandahåller behovsbaserade och skalbara datorresurser. Innan du distribuerar virtuella datorer (VM), måste du förstå skillnaderna mellan VM-funktionerna i Azure Stack och Microsoft Azure. Den här artikeln beskriver skillnaderna och identifierar viktiga överväganden för planering av distributioner av virtuella datorer. Läs mer om övergripande skillnader mellan Azure Stack och Azure, i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

## <a name="cheat-sheet-virtual-machine-differences"></a>Lathund: VM-skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
| Avbildningar av virtuella datorer | Azure Marketplace innehåller avbildningar som du kan använda för att skapa en virtuell dator. Se den [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) sidan för att se listan över avbildningar som är tillgängliga i Azure Marketplace. | Som standard, det finns inte alla avbildningar i Azure Stack marketplace. Molnadministratören Azure Stack ska publicera eller hämta bilder till stacken för Azure marketplace innan användarna kan använda dem. |
| Storlekar för virtuella datorer | Azure stöder en mängd olika storlekar för virtuella datorer. Mer information om tillgängliga storlekar och alternativ, som avser den [Windows storlekar på virtuella datorer](../../virtual-machines/virtual-machines-windows-sizes.md) och [storlekar för Linux virtuella datorer](../../virtual-machines/linux/sizes.md) ämnen. | Azure Stack har stöd för en delmängd av storlekar för virtuella datorer som är tillgängliga i Azure. Om du vill visa en lista med storlekar som stöds, referera till den [storlekar för virtuella datorer](#virtual-machine-sizes) i den här artikeln. |
| Kvoter för virtuell dator | [Kvotgränser](../../azure-subscription-service-limits.md#service-specific-limits) ställs in med Microsoft | Administratör för Azure Stack-moln måste tilldela kvoter innan de erbjuder virtuella datorer till sina användare. |
| Tillägg för virtuell dator |Azure stöder en mängd olika tillägg för virtuell dator. Mer information om tillgängliga tillägg, referera till den [virtuella datorer, tillägg och funktioner](../../virtual-machines/windows/extensions-features.md) artikeln.| Azure Stack stöd för en delmängd av tillägg som är tillgängliga i Azure och var och en av tillägget har specifika versioner. Administratör för Azure Stack-moln kan välja vilka tillägg som ska göras tillgänglig för för sina användare. Om du vill visa en lista över tillägg som stöds, referera till den [tillägg för virtuell dator](#virtual-machine-extensions) i den här artikeln. |
| Nätverk för virtuella datorer | Offentliga IP-adresser som tilldelats virtuella dator är tillgängliga via Internet.<br><br><br>Azure-datorer har en fast DNS-namn | Offentliga IP-adresser som tilldelats en virtuell klientdator är tillgängliga i Azure Stack Development Kit-miljön. En användare måste ha åtkomst till Azure Stack Development Kit via [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) eller [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) att ansluta till en virtuell dator som har skapats i Azure Stack.<br><br>Virtuella datorer som skapats i en specifik Azure Stack-instans har ett DNS-namn som baseras på värdet som har konfigurerats av molnadministratören. |
| Lagring för virtuell dator | Stöder [hanterade diskar.](../../virtual-machines/windows/managed-disks-overview.md) | Hanterade diskar stöds i Azure Stack med version 1808 och senare. |
| Prestanda för virtuella datorer-diskar | Beror på disktyp och storlek. | Beror på storleken på virtuella datorer med diskar är anslutna för att referera till den [VM-storlekar som stöds i Azure Stack](azure-stack-vm-sizes.md) artikeln.
| API-versioner | Azure har alltid de senaste API-versionerna för alla VM-funktioner. | Azure Stack har stöd för specifika Azure-tjänster och specifika API-versioner för dessa tjänster. Om du vill visa listan över API-versioner som stöds, referera till den [API-versioner](#api-versions) i den här artikeln. |
|Tillgänglighetsuppsättningar för virtuella datorer|Flera feldomäner (2 eller 3 per region)<br>Flera uppdateringsdomäner<br>Hanterade disksupport|Flera feldomäner (2 eller 3 per region)<br>Flera uppdateringsdomäner (upp till 20)<br>Inget stöd för hanterad disk|
|Skalningsuppsättningar för virtuella datorer|Automatisk skalning som stöds|Autoskala stöds inte.<br>Lägga till fler instanser i en skalningsuppsättning med portalen, Resource Manager-mallar eller PowerShell.

## <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

Azure Stack inför resursgränser för att undvika över förbrukningen av resurser (server lokalt och tjänstnivå.) Dessa gränser förbättrar klient-upplevelse genom att minska effekten av resursanvändningen för andra klienter.

- För nätverk utgående data från den virtuella datorn finns bandbredd caps på plats. Gör inställningar i Azure Stack är samma som caps i Azure.
- Azure Stack implementerar storage IOPS-gränserna för att undvika grundläggande överförbrukning av resurser av klienter för lagringsåtkomst för storage-resurser.
- För virtuella datorer med flera anslutna datadiskar är maximalt dataflöde för varje datadisk 500 IOPS för hårddiskar och 2300 IOPS för SSD: er.

I följande tabell visas de virtuella datorer som stöds på Azure Stack tillsammans med deras konfiguration:

| Typ           | Storlek          | Rad stöds storlekar |
| ---------------| ------------- | ------------------------ |
|Generellt syfte |Basic A        |[A0 – A4](azure-stack-vm-sizes.md#basic-a)                   |
|Generellt syfte |Standard A     |[A0 – A7](azure-stack-vm-sizes.md#standard-a)              |
|Generellt syfte |D-serien       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Generellt syfte |Dv2-serien     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Generellt syfte |DS-serien      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Generellt syfte |DSv2-serien    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Minnesoptimerad|D-serien       |[D11 – D14](azure-stack-vm-sizes.md#mo-d)            |
|Minnesoptimerad|DS-serien      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Minnesoptimerad|Dv2-serien     |[D11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Minnesoptimerad|DSv2-serien –  |[DS11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Storlekar för virtuella datorer och deras associerade resursen kvantiteter är konsekvent mellan Azure Stack och Azure. Detta inkluderar mängden minne, antal kärnor och antal/storleken på datadiskar som kan skapas. Prestanda för virtuella datorer med samma storlek beror dock på underliggande egenskaperna för en viss Azure Stack-miljö.

## <a name="virtual-machine-extensions"></a>Tillägg för virtuell dator

 Azure Stack innehåller en liten uppsättning tillägg. Uppdateringar och ytterligare tillägg är tillgänglig via Marketplace-syndikering.

Använd följande PowerShell-skript för att hämta listan över tillägg för virtuell dator som är tillgängliga i Azure Stack-miljön:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>API-versioner

Funktioner för virtuella datorer i Azure Stack stöd för följande API-versioner:

![VM-resurstyper](media/azure-stack-vm-considerations/vm-resoource-types.png)

Du kan använda följande PowerShell-skript för att hämta API-versioner för VM-funktioner som är tillgängliga i Azure Stack-miljön:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

Listan över resurstyper som stöds och API-versioner kan variera om operatorn molnet uppdaterar Azure Stack-miljön till en nyare version.

## <a name="windows-activation"></a>Windows-aktivering

Windows-produkter måste användas i enlighet med Microsoft-licensvillkoren och produktanvändningsrättigheterna. Azure Stack använder [automatisk aktivering av virtuell dator](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) för att aktivera Windows Server-datorer (VM).

- Azure Stack-värden aktiverar Windows med AVMA-nycklar för Windows Server 2016. Alla virtuella datorer som kör Windows Server 2012 eller senare aktiveras automatiskt.
- Virtuella datorer som kör Windows Server 2008 R2 aktiveras inte automatiskt och måste aktiveras med hjälp av [MAK-aktivering](https://technet.microsoft.com/library/ff793438.aspx). Om du vill använda MAK-aktivering, måste du ange dina egna produktnyckeln.

Microsoft Azure använder KMS-aktivering för att aktivera Windows-datorer. Om du flyttar en virtuell dator från Azure Stack i Azure och stöter aktivera problem, se [felsöka Azure Windows VM aktiveringsproblem](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Mer information finns på den [felsökning av Windows-aktivering fel på Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) Azures Teamblogg inlägget.

## <a name="next-steps"></a>Nästa steg

[Skapa en Windows-dator med PowerShell i Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
