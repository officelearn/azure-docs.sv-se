---
title: "Återställ lösenordet eller fjärrskrivbord konfigurationen på en Windows-dator i Azure | Microsoft Docs"
description: "Lär dig hur du återställer lösenordet för ett konto eller Remote Desktop services på en Windows-VM som skapats med hjälp av den klassiska distributionsmodellen med hjälp av Azure-portalen eller Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 43e5cf1ab3bc3121d7e3915ea0785998e0ee2fc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Så här återställer du tjänsten Remote Desktop eller dess inloggningslösenord i en Windows-VM som skapats med hjälp av den klassiska distributionsmodellen
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Du kan också [utför de här stegen för virtuella datorer som skapats med Resource Manager-distributionsmodellen](../reset-rdp.md).

Om du inte kan ansluta till en Windows-dator (VM), kan du återställa det lokala administratörslösenordet eller Återställ konfigurationen för tjänsten Remote Desktop. Du kan använda Azure-portalen eller tillägget för virtuell dator åtkomst i Azure PowerShell för att återställa lösenordet.

## <a name="ways-to-reset-configuration-or-credentials"></a>Olika sätt att återställa konfigurationen eller autentiseringsuppgifter
Du kan återställa Remote Desktop services och autentiseringsuppgifter på några olika sätt, beroende på dina behov:

- [Återställa med hjälp av Azure portal](#azure-portal)
- [Återställa med hjälp av Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Du kan använda den [Azure-portalen](https://portal.azure.com) att återställa tjänsten Remote Desktop. Om du vill expandera portalmenyn tre staplarna i det övre vänstra hörnet och klicka sedan på **virtuella datorer (klassisk)**:

![Bläddra efter din Azure VM](./media/reset-rdp/Portal-Select-Classic-VM.png)

Välj din Windows-dator och klicka sedan på **Återställ fjärråtkomst...** . Följande dialogruta visas att återställa Remote Desktop-konfigurationen:

![Återställ RDP-konfigurationssidan](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Du kan också återställa användarnamnet och lösenordet för det lokala administratörskontot. Klicka på den virtuella datorn **stöd + felsökning** > **Återställ lösenord**. Bladet för återställning av lösenord visas:

![Sidan för återställning av lösenord](./media/reset-rdp/Portal-PW-Reset-Windows.png)

När du anger användarnamn och lösenord, klickar du på **spara**.

## <a name="vmaccess-extension-and-powershell"></a>VMAccess-tillägget och PowerShell
Kontrollera att den Virtuella Datoragenten är installerad på den virtuella datorn. VMAccess-tillägget måste inte installeras innan du kan använda den, så länge som den Virtuella Datoragenten är tillgänglig. Kontrollera att den Virtuella Datoragenten är installerad med hjälp av följande kommando. (Ersätt ”myCloudService” och ”myVM” med namnen på Molntjänsten och den virtuella datorn respektive. Du kan lära dig dessa namn genom att köra `Get-AzureVM` utan några parametrar.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Om den **write-host** kommando visar **SANT**, VM-agenten är installerad. Om det visar **FALSKT**, finns i anvisningarna och en länk för hämtning i den [Agent för VM-tillägg - del 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure blogginlägg.

Om du har skapat den virtuella datorn med hjälp av portalen kontrollerar om `$vm.GetInstance().ProvisionGuestAgent` returnerar **SANT**. Om inte, kan du ändra det genom att använda det här kommandot:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Det här kommandot förhindrar felmeddelande när du kör den **Set AzureVMExtension** i nästa steg: ”etablera Gästagenten måste aktiveras på det Virtuella datorobjektet innan du anger IaaS-VM Access-tillägg”.

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenord för lokala administratörskontot**
Skapar autentiseringsuppgifter logga in med det aktuella kontonamnet för lokal administratör och ett nytt lösenord och kör sedan den `Set-AzureVMAccessExtension` på följande sätt.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Om du anger ett annat namn än det aktuella kontot VMAccess-tillägget byter namn på det lokala administratörskontot, tilldelar lösenordet till kontot och utfärdar ett fjärrskrivbord utloggning. Om det lokala administratörskontot har inaktiverats kan det VMAccess-tillägget.

Dessa kommandon kan du också återställa konfigurationen av Remote Desktop-tjänsten.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Återställ konfigurationen för tjänsten Remote Desktop**
Återställ konfigurationen för tjänsten fjärrskrivbord genom att köra följande kommando:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

VMAccess-tillägget körs två kommandon på den virtuella datorn:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Detta kommando aktiverar den inbyggda gruppen för Windows-brandväggen som tillåter inkommande fjärrskrivbord trafik som använder TCP-port 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Detta kommando ställer fDenyTSConnections registervärdet till 0, aktivera anslutningar till fjärrskrivbord.

## <a name="next-steps"></a>Nästa steg
Om tillägget för virtuell dator i Azure åtkomst svarar inte och det inte går att återställa lösenordet, kan du [återställa det lokala Windows-lösenordet offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här metoden är en process i mer avancerade och du måste ansluta den virtuella hårddisken av problematiska VM till en annan virtuell dator. Följ stegen i den här artikeln först och endast försök offline lösenordsmetoden återställning som en sista utväg.

[Azure VM-tillägg och funktioner](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Ansluta till en virtuell Azure-dator med RDP eller SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Felsöka anslutningar till fjärrskrivbord till en Windows-baserad Azure virtuell-dator](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

