---
title: Det går inte att ansluta med RDP till en Windows-dator i Azure | Microsoft Docs
description: Felsöka problem när du inte kan ansluta till den virtuella datorn i Windows i Azure med hjälp av fjärrskrivbord
keywords: 'Fjärrskrivbordsfel, anslutning till fjärrskrivbord fel kan inte ansluta till virtuell dator, felsökning: fjärrskrivbord'
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: d446643b301e6e0c91a560745be6fc37e6d05fe5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413200"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Felsöka fjärrskrivbordsanslutningar till virtuella Azure-datorer
Remote Desktop Protocol (RDP)-anslutning till din Windows-baserade Azure-dator (VM) kan misslyckas av olika skäl, så att du inte kan komma åt den virtuella datorn. Problemet kan vara med Remote Desktop-tjänsten på den virtuella datorn, nätverksanslutningen eller fjärrskrivbord-klienten på värddatorn. Den här artikeln vägleder dig igenom några av de vanligaste sätt att lösa problem med RDP-anslutningen. 

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Snabbsteg för felsökning
Försök återansluta till den virtuella datorn efter varje felsökning steg:

1. Återställa konfigurationen av fjärrskrivbordet.
2. Kontrollera Nätverkssäkerhetsgrupp regler / Cloud Services-slutpunkter.
3. Granska loggarna för VM-konsolen.
4. Återställa nätverkskortet för den virtuella datorn.
5. Kontrollera Resurshälsa VM.
6. Återställa ditt lösenord för virtuell dator.
7. Starta om den virtuella datorn.
8. Distribuera om den virtuella datorn.

Läs vidare om du behöver mer detaljerade anvisningar och förklaringar. Kontrollera den lokala nätverksutrustning som routrar och brandväggar inte blockerar utgående TCP-port 3389, som anges i [detaljerad RDP felsökning](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Om den **Connect** knappen för den virtuella datorn är nedtonad i portalen och du inte är ansluten till Azure via en [Express Route](../../expressroute/expressroute-introduction.md) eller [plats-till-plats VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) anslutning, måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Du kan läsa mer om [offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Sätt att felsöka problem med RDP
Du kan felsöka virtuella datorer som skapas med hjälp av Resource Manager-distributionsmodellen genom att använda någon av följande metoder:

* [Azure-portalen](#using-the-azure-portal) – det är bra om du behöver att snabbt återställa RDP-konfiguration eller autentiseringsuppgifter och du inte har Azure-verktygen installerats.
* [Azure PowerShell](#using-azure-powershell) – om du är nöjd med en PowerShell-kommandotolk snabbt återställa RDP-konfigurationen eller autentiseringsuppgifter med hjälp av Azure PowerShell-cmdlets.

Du hittar också anvisningar om hur du felsöker virtuella datorer som skapas med hjälp av den [klassiska distributionsmodellen](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Felsök med hjälp av Azure-portalen
När du har varje fel, försök att ansluta till den virtuella datorn igen. Om du fortfarande inte kan ansluta kan du försöka nästa steg.

1. **Återställ RDP-anslutningen**. Den här åtgärden återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, till exempel.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till den **stöd + felsökning** i slutet av listan. Klicka på den **Återställ lösenord** knappen. Ange den **läge** till **Återställ endast konfiguration** och klicka sedan på den **uppdatering** knappen:
   
    ![Återställ RDP-konfiguration i Azure portal](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Kontrollera nätverkssäkerhetsgruppsregler**. Använd [Kontrollera IP-flöde](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska reglerna för effektiva säkerhetsgrupper för att säkerställa att inkommande ”Tillåt” NSG-regeln finns och är prioriterad för RDP-porten (standard: 3389). Mer information finns i [med effektiva säkerhetsreglerna för felsökning av VM infrastrukturtrafiken rör](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Granska startdiagnostik**. Den här åtgärden granskar loggarna för VM-konsolen för att avgöra om den virtuella datorn rapporterar ett problem. Inte alla virtuella datorer har startdiagnostik aktiverat, så att den här åtgärden kan vara valfritt.
   
    Specifika felsökningsanvisningar ligger utanför omfånget för den här artikeln, men kan tyda på ett bredare problem som påverkar RDP-anslutningen. Läs mer om hur du granskar konsolloggarna och VM-skärmbild [Startdiagnostik för virtuella datorer](boot-diagnostics.md).

4. **Återställa nätverkskortet för den virtuella datorn**. Mer information finns i [återställa NIC för virtuella Azure Windows-datorer](reset-network-interface.md).
5. **Kontrollera Resurshälsa VM**. Den här åtgärden kontrollerar att det finns inga kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till den **stöd + felsökning** i slutet av listan. Klicka på den **resurshälsa** knappen. En felfri VM-rapporter som **tillgänglig**:
   
    ![Kontrollera resurshälsa för virtuell dator i Azure portal](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Återställa autentiseringsuppgifter**. Den här åtgärden återställer lösenordet till ett lokalt administratörskonto när du är osäker eller har glömt autentiseringsuppgifterna.  När du är inloggad på den virtuella datorn, bör du återställa lösenordet för användaren.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till den **stöd + felsökning** i slutet av listan. Klicka på den **Återställ lösenord** knappen. Kontrollera att den **läge** är inställd på **Återställ lösenord** och ange sedan ditt användarnamn och ett nytt lösenord. Klicka slutligen på den **uppdatering** knappen:
   
    ![Återställa autentiseringsuppgifter i Azure portal](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Starta om den virtuella datorn**. Den här åtgärden kan korrigera eventuella underliggande problem som har Virtuellt datorn.
   
    Välj den virtuella datorn i Azure-portalen och klicka på den **översikt** fliken. Klicka på den **starta om** knappen:
   
    ![Starta om den virtuella datorn i Azure portal](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Distribuera om den virtuella datorn**. Den här åtgärden distribuerar om den virtuella datorn till en annan värd i Azure för att åtgärda eventuella underliggande plattformen eller nätverksproblem.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till den **stöd + felsökning** i slutet av listan. Klicka på den **omdistribuera** knappen och klicka sedan på **omdistribuera**:
   
    ![Distribuera om den virtuella datorn i Azure portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    När den här åtgärden är klar differentierande diskdata går förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn uppdateras.

9. **Kontrollera routning**. Använda Network Watcher [nästa hopp](../../network-watcher/network-watcher-check-next-hop-portal.md) möjlighet att bekräfta att en väg inte förhindrar trafik dirigeras till eller från en virtuell dator. Du kan också gå igenom effektiva vägar för att se alla effektiva vägar för ett nätverksgränssnitt. Mer information finns i [använda effektiva vägar för felsökning av VM infrastrukturtrafiken rör](../../virtual-network/diagnose-network-routing-problem.md).

10. Se till att alla lokala brandväggen eller en brandvägg på din dator tillåter utgående TCP 3389-trafik till Azure.

Om det fortfarande uppstår problem när RDP, kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerad RDP felsökning begrepp och steg](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Felsök med hjälp av Azure PowerShell
Om du inte redan gjort [installera och konfigurera den senaste Azure PowerShell](/powershell/azure/overview).

I följande exempel används variablerna som `myResourceGroup`, `myVM`, och `myVMAccessExtension`. Ersätt dessa variabelnamn och platser med dina egna värden.

> [!NOTE]
> Du återställer användarens autentiseringsuppgifter och RDP-konfigurationen med hjälp av den [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. I följande exempel `myVMAccessExtension` är ett namn som du anger som en del av processen. Om du tidigare har arbetat med VMAccessAgent, kan du hämta namnet på befintliga tillägget med hjälp av `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` att kontrollera egenskaperna för den virtuella datorn. Om du vill visa namnet, tittar du under avsnittet ”tillägg” i utdata.

När du har varje fel, försök att ansluta till den virtuella datorn igen. Om du fortfarande inte kan ansluta kan du försöka nästa steg.

1. **Återställ RDP-anslutningen**. Den här åtgärden återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, till exempel.
   
    I följande exempel återställer RDP-anslutningen på en virtuell dator med namnet `myVM` i den `WestUS` plats och i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Kontrollera nätverkssäkerhetsgruppsregler**. Den här åtgärden kontrollerar att du har en regel i Nätverkssäkerhetsgruppen för att tillåta RDP-trafik. Standardporten för RDP är TCP-port 3389. En regel för att tillåta RDP-trafik kan inte skapas automatiskt när du skapar den virtuella datorn.
   
    Först, tilldela alla konfigurationsdata för Nätverkssäkerhetsgruppen för den `$rules` variabeln. I följande exempel hämtas information om Nätverkssäkerhetsgruppen med namnet `myNetworkSecurityGroup` i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Nu kan visa de regler som har konfigurerats för den här Nätverkssäkerhetsgruppen. Kontrollera att det finns en regel för att tillåta TCP-port 3389 för inkommande anslutningar på följande sätt:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    I följande exempel visas en giltig säkerhetsregel som tillåter RDP-trafik. Du kan se `Protocol`, `DestinationPortRange`, `Access`, och `Direction` är korrekt konfigurerade:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Om du inte har en regel som tillåter RDP-trafik, [skapa en regel för Nätverkssäkerhetsgrupp](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tillåt TCP-port 3389.
3. **Återställa autentiseringsuppgifter**. Den här åtgärden återställer lösenordet för det lokala administratörskontot som du anger när du är osäker på eller har glömt autentiseringsuppgifterna.
   
    Först måste ange användarnamnet och ett nytt lösenord genom att tilldela autentiseringsuppgifter för att den `$cred` variabeln enligt följande:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Nu kan du uppdatera autentiseringsuppgifterna på den virtuella datorn. I följande exempel uppdaterar autentiseringsuppgifterna på en virtuell dator med namnet `myVM` i den `WestUS` plats och i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Starta om den virtuella datorn**. Den här åtgärden kan korrigera eventuella underliggande problem som har Virtuellt datorn.
   
    I följande exempel startar om den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Distribuera om den virtuella datorn**. Den här åtgärden distribuerar om den virtuella datorn till en annan värd i Azure för att åtgärda eventuella underliggande plattformen eller nätverksproblem.
   
    I följande exempel distribuerar om den virtuella datorn med namnet `myVM` i den `WestUS` plats och i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Kontrollera routning**. Använda Network Watcher [nästa hopp](../../network-watcher/network-watcher-check-next-hop-portal.md) möjlighet att bekräfta att en väg inte förhindrar trafik dirigeras till eller från en virtuell dator. Du kan också gå igenom effektiva vägar för att se alla effektiva vägar för ett nätverksgränssnitt. Mer information finns i [använda effektiva vägar för felsökning av VM infrastrukturtrafiken rör](../../virtual-network/diagnose-network-routing-problem.md).

7. Se till att alla lokala brandväggen eller en brandvägg på din dator tillåter utgående TCP 3389-trafik till Azure.

Om det fortfarande uppstår problem när RDP, kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerad RDP felsökning begrepp och steg](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Felsökning av virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen
Försök återansluta till den virtuella datorn efter varje steg i felsökningen.

1. **Återställ RDP-anslutningen**. Den här åtgärden återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, till exempel.
   
    Välj din virtuella dator i Azure-portalen. Klicka på den **... Mer** knappen och klicka sedan på **Återställ fjärråtkomst**:
   
    ![Återställ RDP-konfiguration i Azure portal](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Kontrollera Cloud Services-slutpunkter**. Den här åtgärden kontrollerar att du har slutpunkter i Cloud Services för att tillåta RDP-trafik. Standardporten för RDP är TCP-port 3389. En regel för att tillåta RDP-trafik kan inte skapas automatiskt när du skapar den virtuella datorn.
   
   Välj din virtuella dator i Azure-portalen. Klicka på den **slutpunkter** knappen för att visa de slutpunkter som har konfigurerats för den virtuella datorn. Kontrollera att slutpunkter finns som tillåter RDP-trafik på TCP-port 3389.
   
   I följande exempel visas giltig slutpunkter som tillåter RDP-trafik:
   
   ![Kontrollera Cloud Services-slutpunkter i Azure portal](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Om du inte har en slutpunkt som tillåter RDP-trafik, [skapa en slutpunkt för Cloud Services](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Tillåta TCP till privat port 3389.
3. **Granska startdiagnostik**. Den här åtgärden granskar loggarna för VM-konsolen för att avgöra om den virtuella datorn rapporterar ett problem. Inte alla virtuella datorer har startdiagnostik aktiverat, så att den här åtgärden kan vara valfritt.
   
    Specifika felsökningsanvisningar ligger utanför omfånget för den här artikeln, men kan tyda på ett bredare problem som påverkar RDP-anslutningen. Läs mer om hur du granskar konsolloggarna och VM-skärmbild [Startdiagnostik för virtuella datorer](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Kontrollera Resurshälsa VM**. Den här åtgärden kontrollerar att det finns inga kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till den **stöd + felsökning** i slutet av listan. Klicka på den **Resource Health** knappen. En felfri VM-rapporter som **tillgänglig**:
   
    ![Kontrollera resurshälsa för virtuell dator i Azure portal](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Återställa autentiseringsuppgifter**. Den här åtgärden återställer lösenordet för det lokala administratörskontot som du anger när du är osäker eller har glömt autentiseringsuppgifterna.  När du är inloggad på den virtuella datorn, bör du återställa lösenordet för användaren.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till den **stöd + felsökning** i slutet av listan. Klicka på den **Återställ lösenord** knappen. Ange ditt användarnamn och ett nytt lösenord. Klicka slutligen på den **spara** knappen:
   
    ![Återställa autentiseringsuppgifter i Azure portal](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Starta om den virtuella datorn**. Den här åtgärden kan korrigera eventuella underliggande problem som har Virtuellt datorn.
   
    Välj den virtuella datorn i Azure-portalen och klicka på den **översikt** fliken. Klicka på den **starta om** knappen:
   
    ![Starta om den virtuella datorn i Azure portal](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Se till att alla lokala brandväggen eller en brandvägg på din dator tillåter utgående TCP 3389-trafik till Azure.

Om det fortfarande uppstår problem när RDP, kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerad RDP felsökning begrepp och steg](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Felsöka specifika fel med RDP
Du kan stöta på ett specifikt felmeddelande när du försöker ansluta till den virtuella datorn via RDP. Följande är de vanligaste felmeddelandena:

* [Fjärrsessionen kopplades från eftersom det finns inga fjärranslutna servrar för fjärrskrivbordslicenser tillgängliga för att tillhandahålla en licens](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Fjärrskrivbordet kan inte hitta datorn ”name”](troubleshoot-specific-rdp-errors.md#rdpname).
* [Ett autentiseringsfel inträffade. Det går inte att kontakta den lokala säkerhetskontrollen](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows-säkerhetsfel: dina autentiseringsuppgifter inte fungerade](troubleshoot-specific-rdp-errors.md#wincred).
* [Den här datorn kan inte ansluta till fjärrdatorn](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Ytterligare resurser
Om ingen av dessa fel uppstod och du fortfarande inte kan ansluta till den virtuella datorn via fjärrskrivbord, Läs den detaljerade [felsökningsguide för Remote Desktop](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Felsökningssteg i åtkomst till program som körs på en virtuell dator, finns i [Felsök åtkomst till ett program som körs på en Azure VM](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Om du har problem med hjälp av SSH (Secure Shell) kan ansluta till en Linux-VM i Azure finns i [Felsök SSH-anslutningar till en Linux-VM i Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

