---
title: Återställ lösenord eller konfigurationen av Fjärrskrivbordet på en virtuell Windows-dator | Microsoft Docs
description: Lär dig hur du återställer lösenordet för ett konto eller Remote Desktop services på en Windows-dator med hjälp av Azure portal eller Azure PowerShell.
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
ms.date: 03/23/2018
ms.author: genli
ms.openlocfilehash: 08461811203232d5db1ae9c8f34f4ac180b6b0ce
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268316"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Så här återställer du Fjärrskrivbordstjänsten eller dess inloggningslösenord i en virtuell Windows-dator
Om du inte kan ansluta till en Windows-dator (VM), kan du återställa det lokala administratörslösenordet eller återställa konfigurationen av fjärrskrivbord (stöds inte på Windows-domänkontrollanter). Du kan använda Azure portal eller VM Access-tillägg i Azure PowerShell för att återställa lösenordet. När du är inloggad på den virtuella datorn, bör du återställa lösenordet för användaren.  
Om du använder PowerShell, se till att du har den [senaste PowerShell-modulen installeras och konfigureras](/powershell/azure/overview) och har loggat in på Azure-prenumerationen. Du kan också [utför de här stegen för virtuella datorer som skapas med den klassiska distributionsmodellen](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Sätt att återställa konfigurationen eller autentiseringsuppgifter
Du kan återställa Remote Desktop services och autentiseringsuppgifter på ett par olika sätt, beroende på dina behov:

- [Återställ med Azure portal](#azure-portal)
- [Återställa med hjälp av Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Om du vill expandera menyn portal, klicka på de tre staplarna i det övre vänstra hörnet och klicka sedan på **virtuella datorer**:

![Bläddra efter en Azure virtuell dator](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenordet för lokala administratörskontot**

Välj din Windows-dator och klicka sedan **stöd + felsökning** > **Återställ lösenord**. På bladet för återställning av lösenord visas:

![Sidan för lösenordsåterställning](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Ange användarnamnet och ett nytt lösenord och klicka sedan på **uppdatering**. Vill du försöka ansluta till den virtuella datorn igen.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Återställa konfigurationen av fjärrskrivbord**

Välj din Windows-dator och klicka sedan **stöd + felsökning** > **Återställ lösenord**. På bladet för återställning av lösenord visas. 

![Återställ RDP-konfiguration](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Välj **Återställ endast konfiguration** från den nedrullningsbara menyn, klicka sedan på **uppdatering**. Vill du försöka ansluta till den virtuella datorn igen.


## <a name="vmaccess-extension-and-powershell"></a>VMAccess-tillägget och PowerShell
Se till att du har den [senaste PowerShell-modulen installeras och konfigureras](/powershell/azure/overview) och har loggat in på Azure-prenumerationen med den `Connect-AzureRmAccount` cmdlet.

### <a name="reset-the-local-administrator-account-password"></a>**Återställa lösenordet för lokala administratörskontot**
Återställ administratörsnamn för lösenord eller användarnamn med den [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. TypeHandlerVersion måste vara 2.0 eller senare, som version 1 är föråldrad. 

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
> Om du anger ett annat namn än det aktuella lokala administratörskontot på den virtuella datorn ska VMAccess-tillägget lägga till ett lokalt administratörskonto med det namnet och tilldela angivna lösenordet till det kontot. Om det lokala administratörskontot på den virtuella datorn finns, återställs lösenordet och om kontot är inaktiverat VMAccess-tillägget aktiverar det.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Återställa konfigurationen av fjärrskrivbord**
Återställ fjärråtkomst till den virtuella datorn med den [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. I följande exempel återställs access-tillägg med namnet `myVMAccess` på den virtuella datorn med namnet `myVM` i den `myResourceGroup` resursgrupp:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> En virtuell dator kan ha endast en enskild virtuell dator åtkomst agent när som helst. Att ange den virtuella datorn agentegenskaper, den `-ForceRerun` alternativet kan användas. När du använder `-ForceRerun`, se till att använda samma namn för VM-åtkomst agenten som används i föregående kommandon.

Om du fortfarande inte kan ansluta via en fjärranslutning till din virtuella dator, se fler steg att prova på [felsöka fjärrskrivbordsanslutningar till en Windows-baserade Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du förlorar anslutningen till Windows-domänkontrollant måste du återställa den från en säkerhetskopia av domain controller.

## <a name="next-steps"></a>Nästa steg
Om access-tillägg för virtuella Azure-datorn svarar inte och det inte går att återställa lösenordet, kan du [återställa det lokala Windows-lösenordet offline](../windows/reset-local-password-without-agent.md). Den här metoden är en mer avancerad process och kräver att du kan ansluta den virtuella hårddisken för den problematiska virtuella datorn till en annan virtuell dator. Följ stegen i den här artikeln först och endast försök offline lösenordsmetoden återställning som en sista utväg.

[Azure VM-tillägg och funktioner](../extensions/features-windows.md)

[Ansluta till en Azure-dator med RDP eller SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Felsöka fjärrskrivbordsanslutningar till en Windows-baserade Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

