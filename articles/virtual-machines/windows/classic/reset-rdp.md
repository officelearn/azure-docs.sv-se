---
title: Återställ lösenord eller konfigurationen av Fjärrskrivbordet på en Windows-dator i Azure | Microsoft Docs
description: Lär dig hur du återställer lösenordet för ett konto eller Remote Desktop services på en virtuell Windows-dator som skapats med hjälp av den klassiska distributionsmodellen med hjälp av Azure portal eller Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.openlocfilehash: e91f75299dddf0ac173499a531ea959292082a9f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246556"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Så här återställer du Fjärrskrivbordstjänsten eller dess inloggningslösenord i en Windows VM som skapats med den klassiska distributionsmodellen
> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Du kan också [utför de här stegen för virtuella datorer som skapas med Resource Manager-distributionsmodellen](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Om du inte kan ansluta till en Windows-dator (VM), kan du återställa det lokala administratörslösenordet eller återställa konfigurationen av fjärrskrivbord. Du kan använda Azure portal eller VM Access-tillägg i Azure PowerShell för att återställa lösenordet.

## <a name="ways-to-reset-configuration-or-credentials"></a>Sätt att återställa konfigurationen eller autentiseringsuppgifter
Du kan återställa Remote Desktop services och autentiseringsuppgifter på ett par olika sätt, beroende på dina behov:

- [Återställ med Azure portal](#azure-portal)
- [Återställa med hjälp av Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Du kan använda den [Azure-portalen](https://portal.azure.com) återställa Fjärrskrivbordstjänsten. Om du vill expandera menyn portal, klicka på de tre staplarna i det övre vänstra hörnet och klicka sedan på **virtuella datorer (klassiska)**:

![Bläddra efter en Azure virtuell dator](./media/reset-rdp/Portal-Select-Classic-VM.png)

Välj din Windows-dator och klicka sedan på **Återställ fjärråtkomst...** . Följande dialogruta att återställa Remote Desktop-konfigurationen:

![Återställ RDP-konfigurationssidan](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Du kan också återställa användarnamnet och lösenordet för det lokala administratörskontot. I den virtuella datorn klickar du på **stöd + felsökning** > **Återställ lösenord**. På bladet för återställning av lösenord visas:

![Sidan för lösenordsåterställning](./media/reset-rdp/Portal-PW-Reset-Windows.png)

När du anger nytt användarnamn och lösenord, klickar du på **spara**.

## <a name="vmaccess-extension-and-powershell"></a>VMAccess-tillägget och PowerShell
Kontrollera att den Virtuella Datoragenten är installerad på den virtuella datorn. VMAccess-tillägget behöver inte vara installerad innan du kan använda den, så länge som den Virtuella Datoragenten är tillgänglig. Kontrollera att VM-agenten har installerats med hjälp av följande kommando. (Ersätt ”myCloudService” och ”myVM” med namnen på din molntjänst och den virtuella datorn, respektive. Du kan lära dig dessa namn genom att köra `Get-AzureVM` utan några parametrar.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Om den **write-host** kommandot visar **SANT**, VM-agenten är installerad. Om den visar **FALSKT**, se instruktionerna och en länk för nedladdning i den [VM-agenten och tillägg – del 2](https://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure-blogginlägget.

Om du har skapat den virtuella datorn med hjälp av portalen kontrollerar om `$vm.GetInstance().ProvisionGuestAgent` returnerar **SANT**. Om inte, du kan ange det med hjälp av det här kommandot:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Följande fel förhindrar att det här kommandot när du kör den **Set-AzureVMExtension** i nästa steg: ”etablera Gästagenten måste aktiveras på det Virtuella datorobjektet innan du anger IaaS VM Access-tillägg”.

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenordet för lokala administratörskontot**
Skapar autentiseringsuppgifter logga in med det aktuella kontonamnet för lokal administratör och ett nytt lösenord och kör sedan den `Set-AzureVMAccessExtension` på följande sätt.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Om du anger ett annat namn än det aktuella kontot VMAccess-tillägget byter namn på det lokala administratörskontot, tilldelar lösenordet till kontot och utfärdar ett fjärrskrivbord utloggning. Om det lokala administratörskontot är inaktiverat kan det VMAccess-tillägget.

Dessa kommandon kan du också återställa konfigurationen av fjärrskrivbord.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Återställa konfigurationen av fjärrskrivbord**
Om du vill återställa konfigurationen av fjärrskrivbord, kör du följande kommando:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

VMAccess-tillägget körs två kommandon på den virtuella datorn:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Detta kommando aktiverar den inbyggda gruppen för Windows-brandväggen som tillåter inkommande Remote Desktop-trafik som använder TCP-port 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Det här kommandot anger fDenyTSConnections registervärdet till 0, aktivera anslutningar till fjärrskrivbord.

## <a name="next-steps"></a>Nästa steg
Om access-tillägg för virtuella Azure-datorn svarar inte och det inte går att återställa lösenordet, kan du [återställa det lokala Windows-lösenordet offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här metoden är en mer avancerad process och kräver att du kan ansluta den virtuella hårddisken för den problematiska virtuella datorn till en annan virtuell dator. Följ stegen i den här artikeln först och endast försök offline lösenordsmetoden återställning som en sista utväg.

[Azure VM-tillägg och funktioner](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Ansluta till en Azure-dator med RDP eller SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)

[Felsöka fjärrskrivbordsanslutningar till en Windows-baserade Azure VM](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

