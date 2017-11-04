---
title: "Hur du distribuerar Windows 10 på Azure med Multitenant värd rättigheter"
description: "Lär dig att maximera dina Windows Software Assurance-förmåner för att försätta lokalt licenser i Azure"
services: virtual-machines-windows
documentationcenter: 
author: xujing
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 8/20/2017
ms.author: xujing
ms.openlocfilehash: e3209abd17c7ba3e39a67f834be69f113c27a021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Hur du distribuerar Windows 10 på Azure med Multitenant värd rättigheter 
För kunder med Windows 10 Enterprise E3/E5 per användare eller Windows virtuella skrivbordet åtkomst per användare (användare prenumerationslicenser eller tillägg användarlicenser prenumeration) kan Multitenant värd rättigheter för Windows 10 du hämta din Windows 10-licenser till molnet och kör Windows 10-datorer i Azure utan att betala för en annan licens. Mer information finns [Multitenant värd för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Den här artikeln lär du implementera licensierings-förmån för Windows 10 Desktop-avbildningar. Du kan referera till följande för [Azure Hybrid använda fördelar för Windows Server-avbildningar](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Distribuera Windows 10-avbildning från Azure Marketplace 
Windows 10-avbildning hittar du med följande publishername, erbjudande, sku för Powershell, CLI och Azure Resource Manager distributioner för mallen.

| Operativsystem  |      PublisherName      |  Erbjudande | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows 10  | RS2 Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows 10  | RS2 ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Överför Windows 10-VHD till Azure
Om du överför en generaliserad Windows 10-VHD, Lägg märke till Windows 10 inte har inbyggda administratörskontot aktiverat som standard. Om du vill aktivera det inbyggda administratörskontot lägga in följande kommando som en del av tillägget för anpassat skript.

```powershell
Net user <username> /active:yes
```

Följande powershell-fragment är att markera alla administratörskonton som aktiv, inklusive det inbyggda administratörskontot. Det här exemplet är användbart om de inbyggda administratörsanvändarnamnet är okänd.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Mer information: 
* [Hur man överför VHD till Azure](upload-generalized-managed.md)
* [Hur du förbereder en Windows-VHD att överföra till Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Distribuera Windows 10 med flera värd rättigheter
Kontrollera att du har [installerat och konfigurerat den senaste Azure PowerShell](/powershell/azure/overview). När du har skapat den virtuella Hårddisken kan överföra den virtuella Hårddisken till din Azure Storage-konto med den `Add-AzureRmVhd` cmdlet enligt följande:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Distribuera med Azure Resource Manager malldistribution** i Resource Manager-mallar, en ytterligare parameter för `licenseType` kan anges. Du kan läsa mer om [skapa mallar för Azure Resource Manager](../../resource-group-authoring-templates.md). När du har den virtuella Hårddisken överförs till Azure kan redigera Resource Manager-mall om du vill inkludera licenstypen som en del av compute-providern och distribuera mallen som vanligt:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**Distribuera via PowerShell** när du distribuerar Windows Server-VM via PowerShell kan du ha en ytterligare parameter för `-LicenseType`. När du har den virtuella Hårddisken överförs till Azure kan du skapa en virtuell dator med hjälp av `New-AzureRmVM` och ange licensiering på följande sätt:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Kontrollera den virtuella datorn använder licensiering fördelen
När du har distribuerat den virtuella datorn via antingen PowerShell eller Resource Manager distributionsmetoden, kontrollera licenstypen med `Get-AzureRmVM` på följande sätt:
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Utdata liknar följande exempel för Windows 10 med rätt licenstyp:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Detta utdata skiljer från med följande VM distribueras utan Azure Hybrid Använd förmånen licensiering, till exempel en virtuell dator distribueras direkt från Azure-galleriet:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Mer Information om att ansluta till Azure AD
>[!NOTE]
>Azure tillhandahåller alla virtuella Windows-datorer med det inbyggda administratörskontot som inte kan användas för att ansluta till AAD. Till exempel *Inställningar > konto > åtkomst till arbetsplats eller skola > + Anslut* fungerar inte. Du måste skapa och logga in som ett andra administratörskonto att ansluta till Azure AD mannually. Du kan också konfigurera Azure AD med hjälp av ett etableringspaket, Använd länken är den *nästa steg* avsnittet om du vill veta mer.
>
>

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [konfigurera VDA för Windows 10](https://docs.microsoft.com/en-us/windows/deployment/vda-subscription-activation)
- Lär dig mer om [Multitenant värd för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


