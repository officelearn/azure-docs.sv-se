---
title: Återställa Fjärrskrivbordstjänster eller dess administratörslösenord i en Windows VM | Microsoft-dokument
description: Lär dig hur du återställer ett kontolösenord eller Fjärrskrivbordstjänster på en Windows-virtuell dator med hjälp av Azure-portalen eller Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 580ec443dc087f270e30856c336a5699bbf1ae71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058444"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Återställa Fjärrskrivbordstjänster eller dess administratörslösenord i en Windows VM
Om du inte kan ansluta till en virtuell Dator (VM) i Windows kan du återställa lösenordet för den lokala administratören eller återställa konfigurationen för Fjärrskrivbordstjänster (stöds inte på Windows-domänkontrollanter). Om du vill återställa lösenordet använder du Azure-portalen eller tillägget för VM-åtkomst i Azure PowerShell. När du har loggat in till den virtuella datorn kan du återställa lösenordet för den lokala administratören.  
Om du använder PowerShell kontrollerar du att du har den [senaste PowerShell-modulen installerad och konfigurerad](/powershell/azure/overview) och är inloggad på din Azure-prenumeration. Du kan också [utföra de här stegen för virtuella datorer som skapas med den klassiska distributionsmodellen](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Du kan återställa Fjärrskrivbordstjänster och autentiseringsuppgifter på följande sätt:

- [Återställ med hjälp av Azure-portalen](#reset-by-using-the-azure-portal)

- [Återställ med VMAccess-tillägget och PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Återställ med hjälp av Azure-portalen

Logga först in på [Azure-portalen](https://portal.azure.com) och välj sedan **Virtuella datorer** på den vänstra menyn. 

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenordet för det lokala administratörskontot**

1. Välj din virtuella Windows-dator och välj sedan **Återställ lösenord** under **Support + Felsökning**. Fönstret **Återställ lösenord** visas.

2. Välj **Återställ lösenord,** ange ett användarnamn och ett lösenord och välj sedan **Uppdatera**. 

3. Försök ansluta till den virtuella datorn igen.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Återställa konfigurationen för fjärrskrivbordstjänster**

Den här processen aktiverar tjänsten Fjärrskrivbord i den virtuella datorn och skapar en brandväggsregel för standard RDP-porten 3389.

1. Välj din virtuella Windows-dator och välj sedan **Återställ lösenord** under **Support + Felsökning**. Fönstret **Återställ lösenord** visas. 

2. Välj **Endast Återställningskonfiguration** och välj sedan **Uppdatera**. 

3. Försök ansluta till den virtuella datorn igen.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Återställ med VMAccess-tillägget och PowerShell

Kontrollera först att du har den [senaste PowerShell-modulen installerad och konfigurerad](/powershell/azure/overview) och är inloggad på din Azure-prenumeration med hjälp av cmdleten [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenordet för det lokala administratörskontot**

- Återställ administratörslösenordet eller användarnamnet med Cmdleten [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. Inställningen `typeHandlerVersion` måste vara 2.0 eller högre eftersom version 1 är föråldrad. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Om du anger ett annat namn än det aktuella lokala administratörskontot på den virtuella datorn läggs ett lokalt administratörskonto till med det namnet och tilldela det angivna lösenordet till det kontot. Om det finns ett lokalt administratörskonto på den virtuella datorn återställs lösenordet för VMAccess-tillägget. Om kontot är inaktiverat aktiverar VMAccess-tillägget det.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Återställa konfigurationen för fjärrskrivbordstjänster**

1. Återställ fjärråtkomst till den virtuella datorn med [Cmdleten Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. I följande exempel återställs `myVMAccess` accesstillägget `myVM` som `myResourceGroup` namnges på den virtuella datorn som namnges i resursgruppen:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > När som helst kan en virtuell dator bara ha en enda vm-åtkomstagent. Om du vill ange egenskaper `-ForceRerun` för vm-åtkomstagenten använder du alternativet. När du `-ForceRerun`använder kontrollerar du att du använder samma namn för den vm-åtkomstagent som du kan ha använt i tidigare kommandon.

1. Om du fortfarande inte kan fjärransluta till din virtuella dator läser [du Felsöka fjärrskrivbordsanslutningar till en Virtuell Windows-baserad Virtuell Azure-dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du förlorar anslutningen till Windows-domänkontrollanten måste du återställa den från en säkerhetskopia av domänkontrollanten.

## <a name="next-steps"></a>Nästa steg

- Om Azure VM-åtkomsttillägget inte svarar och du inte kan återställa lösenordet kan du [återställa det lokala Windows-lösenordet offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här metoden är mer avancerad och kräver att du ansluter den virtuella hårddisken för den problematiska virtuella datorn till en annan virtuell dator. Följ stegen som dokumenteras i den här artikeln först och försök med metoden för återställning av offlinelösenord endast om dessa steg inte fungerar.

- [Lär dig mer om Azure VM-tillägg och -funktioner](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Anslut till en virtuell Azure-dator med RDP eller SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Felsöka anslutning till fjärrskrivbord till en Windows-baserad virtuell Azure-dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

