---
title: Distribuera Windows 10 på Azure med värdrättigheter för Flera skapsmedel
description: Lär dig hur du maximerar fördelarna med Windows Software Assurance för att få lokala licenser till Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 9ff8cc64266375a2d439763b222870843136f67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101490"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Distribuera Windows 10 på Azure med värdrättigheter för Flera skapsmedel 
För kunder med Windows 10 Enterprise E3/E5 per användare eller Windows Virtual Desktop Access per användare (användarprenumerationslicenser eller tilläggslicenser för användarprenumerationer) kan du med flera värdrättigheter för Windows 10 ta med dina Windows 10-licenser till molnet och köra Windows 10 Virtuella datorer på Azure utan att betala för en annan licens. Mer information finns i [Multitenant Hosting för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> I den här artikeln visas hur du implementerar licensförmånen för Windows 10 Pro Desktop-avbildningar på Azure Marketplace.
> - För Windows 7-, 8.1-, 10 Enterprise-avbildningar (x64) på Azure Marketplace för MSDN-prenumerationer läser du [Windows-klienten i Azure för scenarier för utveckling/test](client-images.md)
> - För licensförmåner för Windows Server läser du [Azure Hybrid-användningsfördelar för Windows Server-avbildningar](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Distribuera Windows 10-avbildning från Azure Marketplace 
För Malldistributioner av Powershell, CLI och Azure Resource Manager finns Windows 10-avbildningen med följande utgivare, erbjudande, sku.

| Operativsystem  |      PublisherName      |  Erbjudande | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-Pron  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-Pron  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Ladda upp virtuell hårddisk för Windows 10 till Azure
Om du laddar upp en generaliserad Windows 10 VHD, observera Windows 10 inte har inbyggt administratörskonto aktiverat som standard. Om du vill aktivera det inbyggda administratörskontot inkluderar du följande kommando som en del av tillägget Anpassat skript.

```powershell
Net user <username> /active:yes
```

Följande powershell-kodavsnitt är att markera alla administratörskonton som aktiva, inklusive den inbyggda administratören. Det här exemplet är användbart om det inbyggda administratörsanvändarnamnet är okänt.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Mer information: 
* [Så här laddar du upp VIRTUELLD till Azure](upload-generalized-managed.md)
* [Förbereda en Virtuell Windows-hårddisk för att ladda upp till Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Distribuera Windows 10 med värdrättigheter för flera dragningsrätter
Kontrollera att du har [installerat och konfigurerat den senaste Azure PowerShell](/powershell/azure/overview). När du har förberett din virtuella hårddisk laddar du upp `Add-AzVhd` den virtuella hårddisken till ditt Azure Storage-konto med hjälp av cmdleten enligt följande:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Distribuera med Azure Resource Manager-malldistribution** I Resource Manager-mallarna kan `licenseType` ytterligare en parameter anges. Du kan läsa mer om [hur du skapar Azure Resource Manager-mallar](../../resource-group-authoring-templates.md). När du har laddat upp din virtuella hårddisk till Azure redigerar du Resource Manager-mallen så att licenstypen ingår som en del av beräkningsleverantören och distribuerar mallen som vanligt:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Distribuera via PowerShell** När du distribuerar din virtuella Windows Server-dator `-LicenseType`via PowerShell har du ytterligare en parameter för . När du har laddat upp din virtuella hårddisk `New-AzVM` till Azure skapar du en virtuell dator med och anger licensieringstypen enligt följande:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifiera att den virtuella datorn använder licensförmånen
När du har distribuerat den virtuella datorn via distributionsmetoden PowerShell eller Resource Manager kontrollerar du licenstypen med `Get-AzVM` följande:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Utdata liknar följande exempel för Windows 10 med rätt licenstyp:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Den här utdata står i kontrast till följande vm som distribueras utan Azure Hybrid Use Benefit-licensiering, till exempel en virtuell dator som distribueras direkt från Azure Gallery:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Ytterligare information om hur du går med i Azure AD
>[!NOTE]
>Azure etablerar alla virtuella Windows-datorer med inbyggt administratörskonto, som inte kan användas för att gå med i AAD. *Inställningar > konto > Access Work eller School > +Connect* fungerar till exempel inte. Du måste skapa och logga in som ett andra administratörskonto för att gå med i Azure AD manuellt. Du kan också konfigurera Azure AD med hjälp av ett etableringspaket, använd länken är avsnittet *Nästa steg för* att lära dig mer.
>
>

## <a name="next-steps"></a>Efterföljande moment
- Läs mer om [att konfigurera VDA för Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Läs mer om [Multitenant Hosting för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


