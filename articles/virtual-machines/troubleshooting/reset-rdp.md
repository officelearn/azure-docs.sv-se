---
title: Återställa Remote Desktop Services eller dess administratörslösenordet, i en virtuell Windows-dator | Microsoft Docs
description: Lär dig hur du återställer ett lösenord eller Remote Desktop Services på en virtuell Windows-dator med hjälp av Azure-portalen eller Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 77eb372bbe3647e03919aae21087d3270c22148a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506566"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Återställa Remote Desktop Services eller dess administratörslösenordet, i en virtuell Windows-dator
Om du inte kan ansluta till en Windows-dator (VM), kan du återställa ditt lösenord för lokal administratör eller återställa Remote Desktop Services-konfigurationen (stöds inte på Windows-domänkontrollanter). Om du vill återställa lösenordet använder du Azure-portalen eller tillägget för VM-åtkomst i Azure PowerShell. När du har loggat in till den virtuella datorn kan du återställa lösenordet för den lokala administratören.  
Om du använder PowerShell, se till att du har den [senaste PowerShell-modulen installeras och konfigureras](/powershell/azure/overview) och har loggat in på Azure-prenumerationen. Du kan också [utföra de här stegen för virtuella datorer som skapats med den klassiska distributionsmodellen](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Du kan återställa Remote Desktop Services och autentiseringsuppgifter på följande sätt:

- [Återställa med hjälp av Azure portal](#reset-by-using-the-azure-portal)

- [Återställa med hjälp av VMAccess-tillägget och PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Återställa med hjälp av Azure portal

Logga först in på den [Azure-portalen](https://portal.azure.com) och välj sedan **virtuella datorer** på den vänstra menyn. 

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenordet för lokala administratörskontot**

1. Välj din virtuella Windows-dator och därefter **Återställ lösenord** under **stöd + felsökning**. Den **Återställ lösenord** -fönstret visas.

1. Välj **Återställ lösenord**, ange ett användarnamn och ett lösenord och välj sedan **uppdatering**. 

1. Vill du försöka ansluta till den virtuella datorn igen.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Återställ Remote Desktop Services-konfiguration**

1. Välj din virtuella Windows-dator och därefter **Återställ lösenord** under **stöd + felsökning**. Den **Återställ lösenord** -fönstret visas. 

1. Välj **Återställ endast konfiguration** och välj sedan **uppdatering**. 

1. Vill du försöka ansluta till den virtuella datorn igen.


## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Återställa med hjälp av VMAccess-tillägget och PowerShell

Kontrollera först att du har den [senaste PowerShell-modulen installeras och konfigureras](/powershell/azure/overview) och har loggat in på Azure-prenumerationen med hjälp av den [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet.

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenordet för lokala administratörskontot**

- Återställ administratörsnamn för lösenord eller användarnamn med den [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. Den `typeHandlerVersion` inställningen måste vara 2.0 eller senare, eftersom version 1 är föråldrad. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzureRmAccount 
    Select-AzureRMSubscription -SubscriptionId $SubID 
    Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Om du anger ett annat namn än det aktuella lokala administratörskontot på den virtuella datorn ska VMAccess-tillägget lägga till ett lokalt administratörskonto med det namnet och tilldela angivna lösenordet till det kontot. Om det lokala administratörskontot på den virtuella datorn finns ska VMAccess-tillägget återställa lösenordet. Om kontot är inaktiverat kan det VMAccess-tillägget.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Återställ Remote Desktop Services-konfiguration**

1. Återställ fjärråtkomst till den virtuella datorn med den [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. I följande exempel återställs access-tillägg med namnet `myVMAccess` på den virtuella datorn med namnet `myVM` i den `myResourceGroup` resursgrupp:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > En virtuell dator kan ha endast en enskild virtuell dator åtkomst agent när som helst. Ange agentegenskaper för åtkomst till virtuell dator och den `-ForceRerun` alternativet. När du använder `-ForceRerun`, kontrollera att du använder samma namn för åtkomst till VM-agenten som du kan ha använt i föregående kommandon.

1. Om du fortfarande inte kan ansluta via en fjärranslutning till din virtuella dator, se [felsöka fjärrskrivbordsanslutningar till en Windows-baserade Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du förlorar anslutningen till Windows-domänkontrollant måste du återställa den från en säkerhetskopia av domain controller.

## <a name="next-steps"></a>Nästa steg

- Om access-tillägg för virtuella Azure-datorn inte svarar och det inte går att återställa lösenordet, kan du [återställa det lokala Windows-lösenordet offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här metoden är mer avancerad och kräver att du kan ansluta den virtuella hårddisken för den problematiska virtuella datorn till en annan virtuell dator. Följ stegen i den här artikeln först och försök offline återställning lösenordsmetoden endast om de här stegen inte fungerar.

- [Lär dig mer om Azure VM-tillägg och funktioner](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Ansluta till en Azure-dator med RDP eller SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Felsöka fjärrskrivbordsanslutningar till en Windows-baserade Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

