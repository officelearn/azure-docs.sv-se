---
title: Återställ lösenordet eller fjärrskrivbord konfigurationen på en virtuell Windows-dator | Microsoft Docs
description: Lär dig hur du återställer lösenordet för ett konto eller Remote Desktop services på en virtuell Windows-dator med hjälp av Azure-portalen eller Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: cynthn
ms.openlocfilehash: b61b7501c94e9682a3b324488caf119ce4aad3df
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267211"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Så här återställer du tjänsten Remote Desktop eller dess inloggningslösenord i en Windows VM
Om du inte kan ansluta till en Windows-dator (VM), kan du återställa det lokala administratörslösenordet eller återställa konfigurationen av Remote Desktop-tjänsten (stöds inte på Windows-domänkontrollanter). Du kan använda Azure-portalen eller tillägget för virtuell dator åtkomst i Azure PowerShell för att återställa lösenordet. När du har loggat in på den virtuella datorn, ska du återställa lösenordet för användaren.  
Om du använder PowerShell, se till att du har den [senaste PowerShell-modulen installerad och konfigurerad](/powershell/azure/overview) och är inloggad på Azure-prenumerationen. Du kan också [utför de här stegen för virtuella datorer som skapats med den klassiska distributionsmodellen](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Olika sätt att återställa konfigurationen eller autentiseringsuppgifter
Du kan återställa Remote Desktop services och autentiseringsuppgifter på några olika sätt, beroende på dina behov:

- [Återställa med hjälp av Azure portal](#azure-portal)
- [Återställa med hjälp av Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Om du vill expandera portalmenyn tre staplarna i det övre vänstra hörnet och klicka sedan på **virtuella datorer**:

![Bläddra efter din Azure VM](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenord för lokala administratörskontot**

Välj din Windows-dator och klicka sedan **stöd + felsökning** > **Återställ lösenord**. Bladet för återställning av lösenord visas:

![Sidan för återställning av lösenord](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Ange användarnamnet och lösenordet och klicka sedan på **uppdatering**. Försök att ansluta till den virtuella datorn igen.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Återställ konfigurationen för tjänsten Remote Desktop**

Välj din Windows-dator och klicka sedan **stöd + felsökning** > **Återställ lösenord**. Bladet för återställning av lösenord visas. 

![Återställ RDP-konfigurationen](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Välj **bara återställa konfigurationen** i den nedrullningsbara menyn Klicka **uppdatering**. Försök att ansluta till den virtuella datorn igen.


## <a name="vmaccess-extension-and-powershell"></a>VMAccess-tillägget och PowerShell
Se till att du har den [senaste PowerShell-modulen installerad och konfigurerad](/powershell/azure/overview) och är inloggad på Azure-prenumerationen med den `Connect-AzureRmAccount` cmdlet.

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenord för lokala administratörskontot**
Återställ administratör lösenord eller användarnamn namn med den [Set AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. TypeHandlerVersion måste vara 2.0 eller senare, som version 1 är föråldrat. 

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
> Om du anger ett annat namn än det aktuella lokala administratörskontot på den virtuella datorn VMAccess-tillägget Lägg till ett lokalt administratörskonto med det namnet, och tilldela det angivna lösenordet för kontot. Om det lokala administratörskontot på den virtuella datorn finns det att återställa lösenordet och om kontot är inaktiverat VMAccess-tillägget aktiverar det.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Återställ konfigurationen för tjänsten Remote Desktop**
Återställ fjärråtkomst till den virtuella datorn med den [Set AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. I följande exempel återställs access-tillägg med namnet `myVMAccess` på den virtuella datorn med namnet `myVM` i den `myResourceGroup` resursgrupp:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> En virtuell dator kan ha endast en enskild VM åtkomst agent när som helst. Att ange den VM agentegenskaper, den `-ForceRerun` alternativet kan användas. När du använder `-ForceRerun`, se till att använda samma namn för virtuell dator åtkomst agenten som används i alla tidigare angivna kommandon.

Om du fortfarande inte kan ansluta via en fjärranslutning till den virtuella datorn, se fler steg försöka på [felsöka fjärrskrivbordsanslutningar till en Windows-baserad Azure virtuella](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du förlorar anslutningen till domänkontrollanten i Windows kan behöver du återställa från en säkerhetskopia för domain controller.

## <a name="next-steps"></a>Nästa steg
Om tillägget för virtuell dator i Azure åtkomst svarar inte och det inte går att återställa lösenordet, kan du [återställa det lokala Windows-lösenordet offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här metoden är en process i mer avancerade och du måste ansluta den virtuella hårddisken av problematiska VM till en annan virtuell dator. Följ stegen i den här artikeln först och endast försök offline lösenordsmetoden återställning som en sista utväg.

[Azure VM-tillägg och funktioner](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Ansluta till en virtuell Azure-dator med RDP eller SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Felsöka anslutningar till fjärrskrivbord till en Windows-baserad Azure virtuell-dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

