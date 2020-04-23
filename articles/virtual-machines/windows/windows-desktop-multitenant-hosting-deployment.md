---
title: Så här distribuerar du Windows 10 på Azure med värd rättigheter för flera innehavare
description: Lär dig hur du maximerar dina Windows Software Assurance-förmåner för att ta med lokala licenser till Azure
author: xujing
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: c85eef1a5d035e23c7e63632ac92c21440b15cae
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101560"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Så här distribuerar du Windows 10 på Azure med värd rättigheter för flera innehavare 
För kunder med Windows 10 Enterprise E3/E5 per användare eller Windows anslutning för virtuella skriv bord per användare (användar prenumerations licenser eller användar prenumerations licenser), kan du använda värd rättigheterna för flera innehavare för Windows 10 för att ta med Windows 10-licenser till molnet och köra Windows 10 Virtual Machines på Azure utan att betala för en annan licens. Mer information finns i [värd för flera innehavare för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Den här artikeln visar hur du implementerar licens förmånerna för Windows 10 Pro Desktop-avbildningar på Azure Marketplace.
> - För Windows 7, 8,1, 10 Enterprise (x64)-avbildningar på Azure Marketplace för MSDN-prenumerationer, se [Windows-klient i Azure för utveckling/testnings scenarier](client-images.md)
> - För Windows Server-licensierings förmåner, se [Azure Hybrid Use Benefits för Windows Server-avbildningar](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Distribuera Windows 10-avbildning från Azure Marketplace 
För PowerShell-, CLI-och Azure Resource Manager mall-distributioner kan Windows 10-avbildningen hittas med följande publishername, erbjudande, SKU.

| Operativsystem  |      PublisherName      |  Erbjudande | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2 – Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2 – ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3 – Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3 – ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Laddar upp Windows 10-VHD till Azure
Observera att om du överför en generaliserad Windows 10-hårddisk har Windows 10 inte inbyggt administratörs konto aktiverat som standard. Om du vill aktivera det inbyggda administratörs kontot inkluderar du följande kommando som en del av det anpassade skript tillägget.

```powershell
Net user <username> /active:yes
```

Följande PowerShell-kodfragment är att markera alla administratörs konton som aktiva, inklusive den inbyggda administratören. Det här exemplet är användbart om det inbyggda administratörs användar namnet är okänt.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Mer information: 
* [Ladda upp VHD till Azure](upload-generalized-managed.md)
* [Förbereda en virtuell Windows-hårddisk att ladda upp till Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Distribuera Windows 10 med värd rättigheter för flera innehavare
Kontrol lera att du har [installerat och konfigurerat de senaste Azure PowerShell](/powershell/azure/overview). När du har för berett din virtuella hård disk laddar du upp den virtuella hård disken `Add-AzVhd` till ditt Azure Storage-konto med cmdleten på följande sätt:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Distribuera med Azure Resource Manager mall distribution** I dina Resource Manager-mallar kan du ange ytterligare `licenseType` en parameter för. Du kan läsa mer om hur du [redigerar Azure Resource Manager mallar](../../resource-group-authoring-templates.md). När du har laddat upp den virtuella hård disken till Azure redigerar du Resource Manager-mallen för att inkludera licens typen som en del av beräknings leverantören och distribuera mallen som vanligt:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Distribuera via PowerShell** När du distribuerar en virtuell Windows Server-dator via PowerShell har du ytterligare en `-LicenseType`parameter för. När du har laddat upp den virtuella hård disken till Azure skapar du `New-AzVM` en virtuell dator med hjälp av och anger licensierings typen enligt följande:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifiera att din virtuella dator utnyttjar licens förmånen
När du har distribuerat den virtuella datorn via distributions metoden PowerShell eller Resource Manager, kontrollerar du licens typen med `Get-AzVM` enligt följande:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Utdata liknar följande exempel för Windows 10 med rätt licens typ:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Den här utmatningen kontrasterar med följande virtuella dator som distribuerats utan Azure Hybrid Use Benefit Licensing, till exempel en virtuell dator som distribueras direkt från Azure-galleriet:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Mer information om att ansluta till Azure AD
>[!NOTE]
>Azure tillhandahåller alla virtuella Windows-datorer med inbyggt administratörs konto, som inte kan användas för att ansluta till AAD. Exempelvis fungerar inte *inställningar > konto > åtkomst till arbete eller skola > + Connect* . Du måste skapa och logga in som ett andra administratörs konto för att kunna ansluta till Azure AD manuellt. Du kan också konfigurera Azure AD med hjälp av ett konfigurations paket. Använd länken i avsnittet *Nästa steg* om du vill veta mer.
>
>

## <a name="next-steps"></a>Efterföljande moment
- Läs mer om hur du [konfigurerar VDA för Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Läs mer om [värd för flera innehavare för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


