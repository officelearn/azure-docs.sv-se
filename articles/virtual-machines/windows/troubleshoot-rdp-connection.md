---
title: "Det går inte att ansluta till RDP till en virtuell Windows-dator i Azure | Microsoft Docs"
description: "Felsökning av problem när du inte ansluta till din Windows-dator i Azure med hjälp av fjärrskrivbord"
keywords: "Remote desktop-fel, fel anslutning till fjärrskrivbord kan inte ansluta till en virtuell dator, felsökning: fjärrskrivbord"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 74beeffe04fa4b6dba4db8687bdebe79e0d750f4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Felsöka anslutningar till fjärrskrivbord till en virtuell Azure-dator
Remote Desktop Protocol (RDP)-anslutning till din Windows-baserad Azure virtuell dator (VM) kan misslyckas av olika skäl, så att du inte har åtkomst till den virtuella datorn. Problemet kan vara med Remote Desktop-tjänsten på den virtuella datorn, nätverksanslutningen eller fjärrskrivbordsklienten på värddatorn. Den här artikeln hjälper dig att några av de vanligaste metoderna för att lösa RDP-anslutningsproblem. 

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Snabbsteg för felsökning
Försök ansluta till den virtuella datorn efter varje felsökning steg:

1. Återställ konfigurationen för fjärrskrivbord.
2. Kontrollera Nätverkssäkerhetsgruppen regler / Cloud Services-slutpunkter.
3. Granska loggarna för VM-konsolen.
4. Återställa nätverkskortet för den virtuella datorn.
5. Kontrollera hälsotillståndet för VM-resurs.
6. Återställa lösenordet VM.
7. Starta om den virtuella datorn.
8. Distribuera den virtuella datorn.

Läs om du behöver mer detaljerade anvisningar och förklaringar. Kontrollera den lokala nätverksutrustning som routrar och brandväggar inte blockerar utgående TCP-port 3389, enligt beskrivningen i [detaljerad felsökning RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Om den **Anslut** knappen för den virtuella datorn är grå ut i portalen och du inte är ansluten till Azure via en [Expressroute](../../expressroute/expressroute-introduction.md) eller [plats-till-plats VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) anslutning, måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Du kan läsa mer om [offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Sätt att felsöka problem med RDP
Du kan felsöka virtuella datorer som skapades med Resource Manager-distributionsmodellen med hjälp av någon av följande metoder:

* [Azure-portalen](#using-the-azure-portal) – bra om du behöver att snabbt återställa RDP-konfigurationen eller autentiseringsuppgifter och du inte har Azure verktygen som installeras.
* [Azure PowerShell](#using-azure-powershell) - om du är nöjd med PowerShell-Kommandotolken snabbt återställa RDP-konfigurationen eller autentiseringsuppgifter med hjälp av Azure PowerShell-cmdlets.

Du kan också hitta steg om felsökning av virtuella datorer som skapats med hjälp av den [klassiska distributionsmodellen](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Felsöka med Azure-portalen
Försök ansluta till den virtuella datorn igen efter varje steg i felsökningen. Om du fortfarande inte kan ansluta försöker du med nästa steg.

1. **Återställer RDP-anslutning**. Den här åtgärden återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, t.ex.
   
    Välj den virtuella datorn i Azure-portalen. Bläddra nedåt inställningsfönstret till den **stöd + felsökning** avsnittet nästan längst ned i listan. Klicka på den **Återställ lösenord** knappen. Ange den **läge** till **bara återställa konfigurationen** och klicka sedan på den **uppdatering** knappen:
   
    ![Återställa RDP-konfigurationen i Azure-portalen](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Kontrollera Nätverkssäkerhetsgruppen regler**. Använd [Kontrollera IP-flöde](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska effektiva regler för nätverkssäkerhetsgrupper för att säkerställa inkommande ”Tillåt” NSG regel finns och prioriteras för RDP-porten (standard 3389). Mer information finns i [med effektiva säkerhetsregler för att felsöka VM infrastrukturtrafiken rör](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

3. **Granska VM startdiagnostikinställningar**. Den här åtgärden granskar loggarna för VM-konsolen för att avgöra om den virtuella datorn rapporterar ett problem. Inte alla virtuella datorer har startdiagnostikinställningar aktiverat, så den här åtgärden kan vara valfri.
   
    Specifika felsökningsanvisningar ligger utanför omfånget för den här artikeln, men kan tyda på ett större problem som påverkar RDP-anslutning. Mer information om hur du granskar loggarna för konsolen och skärmbilden Virtuella finns [Startdiagnostikinställningar för virtuella datorer](boot-diagnostics.md).

4. **Återställa nätverkskortet för den virtuella datorn**. Mer information finns i [återställa NIC för Azure Windows VM](reset-network-interface.md).
5. **Kontrollera VM Resource Health**. Den här åtgärden kontrollerar att det inte finns några kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj den virtuella datorn i Azure-portalen. Bläddra nedåt inställningsfönstret till den **stöd + felsökning** avsnittet nästan längst ned i listan. Klicka på den **Resource health** knappen. En felfri VM rapporter som **tillgänglig**:
   
    ![Kontrollera tillstånd för VM-resurs i Azure-portalen](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Återställa autentiseringsuppgifter**. Den här åtgärden återställer lösenordet för ett lokalt administratörskonto när du är osäker på eller har glömt autentiseringsuppgifterna.
   
    Välj den virtuella datorn i Azure-portalen. Bläddra nedåt inställningsfönstret till den **stöd + felsökning** avsnittet nästan längst ned i listan. Klicka på den **Återställ lösenord** knappen. Kontrollera att den **läge** är inställd på **Återställ lösenord** och ange ditt användarnamn och ett nytt lösenord. Klicka slutligen på den **uppdatering** knappen:
   
    ![Återställa autentiseringsuppgifter i Azure-portalen](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Starta om den virtuella datorn**. Den här åtgärden kan korrigera eventuella underliggande problem som har med Virtuellt datorn.
   
    Välj den virtuella datorn i Azure-portalen och klicka på den **översikt** fliken. Klicka på den **starta om** knappen:
   
    ![Starta om den virtuella datorn i Azure-portalen](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Distribuera den virtuella datorn**. Den här åtgärden återdistribuerar den virtuella datorn till en annan värd i Azure för att åtgärda eventuella underliggande plattformen eller nätverksproblem.
   
    Välj den virtuella datorn i Azure-portalen. Bläddra nedåt inställningsfönstret till den **stöd + felsökning** avsnittet nästan längst ned i listan. Klicka på den **omdistribuera** knappen och klicka sedan på **omdistribuera**:
   
    ![Distribuera den virtuella datorn i Azure-portalen](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    När den här åtgärden har slutförts tillfälliga diskdata går förlorad och dynamiska IP-adresser som är associerade med den virtuella datorn har uppdaterats.

Om det fortfarande uppstår problem med RDP, kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerad felsökning koncept och steg RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Felsöka med hjälp av Azure PowerShell
Om du inte redan har gjort [installera och konfigurera den senaste Azure PowerShell](/powershell/azure/overview).

I följande exempel används variabler som `myResourceGroup`, `myVM`, och `myVMAccessExtension`. Ersätt dessa variabelnamn och platser med egna värden.

> [!NOTE]
> Du återställer användarens autentiseringsuppgifter och RDP-konfigurationen med hjälp av den [Set AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-cmdlet. I följande exempel `myVMAccessExtension` är ett namn som du anger som en del av processen. Om du tidigare har arbetat med VMAccessAgent, kan du hämta namnet på befintliga tillägget med hjälp av `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` att kontrollera egenskaperna för den virtuella datorn. Om du vill visa namnet, tittar du under avsnittet ”tillägg” i utdata.

Försök ansluta till den virtuella datorn igen efter varje steg i felsökningen. Om du fortfarande inte kan ansluta försöker du med nästa steg.

1. **Återställer RDP-anslutning**. Den här åtgärden återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, t.ex.
   
    I följande exempel återställer RDP-anslutning på en virtuell dator med namnet `myVM` i den `WestUS` plats och i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Kontrollera Nätverkssäkerhetsgruppen regler**. Den här åtgärden kontrollerar du att en regel i säkerhetsgrupp för nätverk som tillåter RDP-trafik. Standardporten för RDP är TCP-port 3389. En regel för att tillåta RDP-trafik kan inte skapas automatiskt när du skapar den virtuella datorn.
   
    Först tilldela alla konfigurationsdata för din Nätverkssäkerhetsgruppen till den `$rules` variabeln. I följande exempel hämtar information om nätverket säkerhetsgrupp med namnet `myNetworkSecurityGroup` i resursgrupp med namnet `myResourceGroup`:
   
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
   
    Om du inte har en regel som tillåter RDP-trafik [skapa en regel för Nätverkssäkerhetsgruppen](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tillåt TCP-port 3389.
3. **Återställa autentiseringsuppgifter**. Den här åtgärden återställer lösenordet för det lokala administratörskontot som du anger när du är osäker på eller har glömt autentiseringsuppgifterna.
   
    Först måste ange användarnamn och ett nytt lösenord genom att tilldela autentiseringsuppgifter för att den `$cred` variabeln på följande sätt:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Nu kan du uppdatera autentiseringsuppgifterna på den virtuella datorn. I följande exempel uppdateras autentiseringsuppgifter på en virtuell dator med namnet `myVM` i den `WestUS` plats och i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Starta om den virtuella datorn**. Den här åtgärden kan korrigera eventuella underliggande problem som har med Virtuellt datorn.
   
    I följande exempel startar om den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`:
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Distribuera den virtuella datorn**. Den här åtgärden återdistribuerar den virtuella datorn till en annan värd i Azure för att åtgärda eventuella underliggande plattformen eller nätverksproblem.
   
    I följande exempel återdistribuerar den virtuella datorn med namnet `myVM` i den `WestUS` plats och i resursgruppen med namnet `myResourceGroup`:
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Om det fortfarande uppstår problem med RDP, kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerad felsökning koncept och steg RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Felsökning av virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen
Försök ansluta till den virtuella datorn efter varje steg i felsökningen.

1. **Återställer RDP-anslutning**. Den här åtgärden återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, t.ex.
   
    Välj den virtuella datorn i Azure-portalen. Klicka på den **... Flera** knappen och klicka sedan på **Återställ fjärråtkomst**:
   
    ![Återställa RDP-konfigurationen i Azure-portalen](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Verifiera molntjänster slutpunkter**. Den här åtgärden kontrollerar du att slutpunkter i dina molntjänster som tillåter RDP-trafik. Standardporten för RDP är TCP-port 3389. En regel för att tillåta RDP-trafik kan inte skapas automatiskt när du skapar den virtuella datorn.
   
   Välj den virtuella datorn i Azure-portalen. Klicka på den **slutpunkter** för att visa de slutpunkter som för tillfället konfigurerats för den virtuella datorn. Kontrollera att slutpunkter finns som tillåter RDP-trafik på TCP-port 3389.
   
   I följande exempel visas giltig slutpunkter som tillåter RDP-trafik:
   
   ![Verifiera molntjänster slutpunkter i Azure-portalen](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Om du inte har en slutpunkt som tillåter RDP-trafik [skapa en slutpunkt för molntjänster](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Låt TCP privat port 3389.
3. **Granska VM startdiagnostikinställningar**. Den här åtgärden granskar loggarna för VM-konsolen för att avgöra om den virtuella datorn rapporterar ett problem. Inte alla virtuella datorer har startdiagnostikinställningar aktiverat, så den här åtgärden kan vara valfri.
   
    Specifika felsökningsanvisningar ligger utanför omfånget för den här artikeln, men kan tyda på ett större problem som påverkar RDP-anslutning. Mer information om hur du granskar loggarna för konsolen och skärmbilden Virtuella finns [Startdiagnostikinställningar för virtuella datorer](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Kontrollera VM Resource Health**. Den här åtgärden kontrollerar att det inte finns några kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj den virtuella datorn i Azure-portalen. Bläddra nedåt inställningsfönstret till den **stöd + felsökning** avsnittet nästan längst ned i listan. Klicka på den **Resource Health** knappen. En felfri VM rapporter som **tillgänglig**:
   
    ![Kontrollera tillstånd för VM-resurs i Azure-portalen](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Återställa autentiseringsuppgifter**. Den här åtgärden återställer lösenordet för det lokala administratörskontot som du anger när du är osäker på eller har glömt autentiseringsuppgifterna.
   
    Välj den virtuella datorn i Azure-portalen. Bläddra nedåt inställningsfönstret till den **stöd + felsökning** avsnittet nästan längst ned i listan. Klicka på den **Återställ lösenord** knappen. Ange ditt användarnamn och ett nytt lösenord. Klicka slutligen på den **spara** knappen:
   
    ![Återställa autentiseringsuppgifter i Azure-portalen](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Starta om den virtuella datorn**. Den här åtgärden kan korrigera eventuella underliggande problem som har med Virtuellt datorn.
   
    Välj den virtuella datorn i Azure-portalen och klicka på den **översikt** fliken. Klicka på den **starta om** knappen:
   
    ![Starta om den virtuella datorn i Azure-portalen](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

Om det fortfarande uppstår problem med RDP, kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerad felsökning koncept och steg RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Felsöka specifika RDP-fel
Du kan stöta på ett visst felmeddelande när du försöker ansluta till den virtuella datorn via RDP. Följande är de vanligaste felmeddelandena:

* [Fjärrsessionen kopplades från eftersom det finns inga fjärranslutna servrar för fjärrskrivbordslicenser kan tillhandahålla en licens](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Fjärrskrivbordet kan inte hitta datorn ”name”](troubleshoot-specific-rdp-errors.md#rdpname).
* [Ett autentiseringsfel har uppstått. Det går inte att kontakta den lokala säkerhetskontrollen](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows-Security-fel: dina autentiseringsuppgifter fungerar inte](troubleshoot-specific-rdp-errors.md#wincred).
* [Den här datorn kan inte ansluta till fjärrdatorn](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Ytterligare resurser
Om ingen av dessa fel uppstod och du kan fortfarande inte kan ansluta till den virtuella datorn via fjärrskrivbord, läsa den detaljerade [felsökningsguide för fjärrskrivbord](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Felsökningssteg för åtkomst till program som körs på en virtuell dator, finns i [Felsök åtkomst till ett program som körs på en Azure VM](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Om du har problem med hjälp av SSH (Secure Shell) kan ansluta till en Linux-VM i Azure finns [felsökning av SSH-anslutningar till en Linux-VM i Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

