---
title: Det går inte att ansluta med RDP till en Windows VM i Azure | Microsoft-dokument
description: Felsöka problem när du inte kan ansluta till din virtuella Windows-dator i Azure med fjärrskrivbord
keywords: Fel på fjärrskrivbord,anslutningsfel för fjärrskrivbord, kan inte ansluta till virtuell dator, felsökning av fjärrskrivbord
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: cbca8e631da8b99aa0ea4bdc6d099f3dbd2ed9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916616"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Felsöka anslutning till fjärrskrivbord till en virtuell Azure-dator
Det kan finnas flera orsaker till att det uppstår problem med RDP-anslutningen till den Windows-baserade virtuella Azure-datorn så att du inte kan komma åt den virtuella datorn. Problemet kan röra fjärrskrivbordstjänsten på den virtuella datorn, nätverksanslutningen eller fjärrskrivbordsklienten på din värddator. Den här artikeln vägleder dig igenom några av de vanligaste sätten att lösa problem med RDP-anslutningen. 

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Snabba felsökningssteg
Efter varje felsökningssteg kan du försöka ansluta till den virtuella datorn igen:

1. Återställ konfigurationen för fjärrskrivbord.
2. Kontrollera nätverkssäkerhetsgruppsregler/molntjänstslutpunkter.
3. Granska VM-konsolloggar.
4. Återställ nätverkskortet för den virtuella datorn.
5. Kontrollera vm-resurshälsan.
6. Återställ lösenordet för den virtuella datorn.
7. Starta om den virtuella datorn.
8. Distribuera om den virtuella datorn.

Fortsätt läsa om du behöver mer detaljerade steg och förklaringar. Kontrollera att lokal nätverksutrustning som routrar och brandväggar inte blockerar utgående TCP-port 3389, vilket anges i [detaljerade felsökningsscenarier för fjärrskrivbordstjänster](detailed-troubleshoot-rdp.md).

> [!TIP]
> Om **knappen Anslut** för den virtuella datorn är nedtonad i portalen och du inte är ansluten till Azure via en [Express Route-](../../expressroute/expressroute-introduction.md) eller [Site-to-Site VPN-anslutning](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Du kan läsa mer om [offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Olika sätt att felsöka RDP-problem
Du kan felsöka virtuella datorer som skapats med hjälp av Resurshanterarens distributionsmodell med någon av följande metoder:

* Azure portal - bra om du snabbt behöver återställa RDP-konfigurationen eller användarautentiseringsuppgifter och du inte har Azure-verktyg installerade.
* Azure PowerShell – om du är bekväm med en PowerShell-prompt återställer du snabbt RDP-konfigurationen eller användarautentiseringsuppgifterna med Azure PowerShell-cmdletar.

Du kan också hitta steg på felsökning av virtuella datorer som skapats med den [klassiska distributionsmodellen](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Felsöka med Azure-portalen
Försök ansluta till den virtuella datorn igen efter varje felsökningssteg. Om du fortfarande inte kan ansluta provar du nästa steg.

1. **Återställ RDP-anslutningen**. Det här felsökningssteget återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller windows-brandväggsregler blockerar rdp, till exempel.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till avsnittet **Support + Felsökning** längst ned i listan. Klicka på knappen **Återställ lösenord.** Ställ in att **konfigurationen endast ska** **återställas** och klicka sedan på knappen **Uppdatera:**
   
    ![Återställa RDP-konfigurationen i Azure-portalen](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Verifiera regler för nätverkssäkerhetsgruppen**. Använd [IP-flödeskontroll](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska gällande säkerhetsgruppsregler för att säkerställa att den inkommande NSG-regeln finns och prioriteras för RDP-port (standard 3389). Mer information finns i [Använda effektiva säkerhetsregler för att felsöka vm-trafikflödet](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Granska VM-startdiagnostik**. Det här felsökningssteget granskar vm-konsolloggarna för att avgöra om den virtuella datorn rapporterar ett problem. Alla virtuella datorer har inte startdiagnostik aktiverad, så det här felsökningssteget kan vara valfritt.
   
    Specifika felsökningssteg ligger utanför den här artikelns omfattning, men kan tyda på ett större problem som påverkar RDP-anslutningen. Mer information om hur du granskar konsolloggarna och skärmbilden av den virtuella datorn finns i [Startdiagnostik för virtuella datorer](boot-diagnostics.md).

4. **Återställ nätverkskortet för den virtuella datorn**. Mer information finns i [hur du återställer nätverkskort för Azure Windows VM](../windows/reset-network-interface.md).
5. **Kontrollera vm-resurshälsan**. Det här felsökningssteget verifierar att det inte finns några kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till avsnittet **Support + Felsökning** längst ned i listan. Klicka på hälsoknappen **Resurs.** En felfri virtuell dator rapporterar som **tillgänglig:**
   
    ![Kontrollera vm-resurshälsa i Azure-portalen](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Återställ användaruppgifter**. Det här felsökningssteget återställer lösenordet på ett lokalt administratörskonto när du är osäker eller har glömt autentiseringsuppgifterna.  När du har loggat in på den virtuella datorn bör du återställa lösenordet för den användaren.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till avsnittet **Support + Felsökning** längst ned i listan. Klicka på knappen **Återställ lösenord.** Kontrollera att **läget** är inställt på **Att återställa lösenordet** och ange sedan ditt användarnamn och ett nytt lösenord. Klicka slutligen på knappen **Uppdatera:**
   
    ![Återställa användarautentiseringsuppgifterna i Azure-portalen](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Starta om den virtuella datorn**. Det här felsökningssteget kan korrigera eventuella underliggande problem som den virtuella datorn själv har.
   
    Välj din virtuella dator i Azure-portalen **Restart** och klicka på fliken **Översikt.**
   
    ![Starta om den virtuella datorn i Azure-portalen](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Distribuera om den virtuella datorn**. Det här felsökningssteget distribuerar om den virtuella datorn till en annan värd i Azure för att korrigera eventuella underliggande plattforms- eller nätverksproblem.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till avsnittet **Support + Felsökning** längst ned i listan. Klicka på knappen **Distribuera om** och sedan på **Omdedela:**
   
    ![Distribuera om den virtuella datorn i Azure-portalen](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    När den här åtgärden är klar går efemära diskdata förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn uppdateras.

9. **Verifiera routning**. Använd Network Watchers [nästa hopp-funktion](../../network-watcher/network-watcher-check-next-hop-portal.md) för att bekräfta att en rutt inte hindrar trafiken från att dirigeras till eller från en virtuell dator. Du kan också granska effektiva vägar för att se alla effektiva vägar för ett nätverksgränssnitt. Mer information finns i [Använda effektiva vägar för att felsöka vm-trafikflöde](../../virtual-network/diagnose-network-routing-problem.md).

10. Kontrollera att alla lokala brandväggar eller brandväggar på datorn tillåter utgående TCP 3389-trafik till Azure.

Om du fortfarande stöter på RDP-problem kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerade felsökningsbegrepp och steg för fjärrskrivbords-felsökning](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Felsöka med Azure PowerShell
Om du inte redan har gjort [det installerar och konfigurerar du den senaste Azure PowerShell](/powershell/azure/overview).

I följande exempel används `myResourceGroup`variabler som , `myVM`och `myVMAccessExtension`. Ersätt dessa variabelnamn och platser med dina egna värden.

> [!NOTE]
> Du återställer användarautentiseringsuppgifterna och RDP-konfigurationen med hjälp av Cmdleten [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. I följande exempel `myVMAccessExtension` är ett namn som du anger som en del av processen. Om du tidigare har arbetat med VMAccessAgent kan du få `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` namnet på det befintliga tillägget med hjälp av att kontrollera egenskaperna för den virtuella datorn. Om du vill visa namnet tittar du under avsnittet Tillägg i utdata.

Försök ansluta till den virtuella datorn igen efter varje felsökningssteg. Om du fortfarande inte kan ansluta provar du nästa steg.

1. **Återställ RDP-anslutningen**. Det här felsökningssteget återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller windows-brandväggsregler blockerar rdp, till exempel.
   
    I följande exempel återställs RDP-anslutningen `myVM` på `WestUS` en virtuell dator `myResourceGroup`som namnges på den plats och i resursgruppen med namnet :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Verifiera regler för nätverkssäkerhetsgruppen**. Det här felsökningssteget verifierar att du har en regel i nätverkssäkerhetsgruppen för att tillåta fjärrskrivbordstrafik. Standardporten för RDP är TCP-port 3389. En regel som tillåter RDP-trafik kanske inte skapas automatiskt när du skapar den virtuella datorn.
   
    Tilldela först alla konfigurationsdata för nätverkssäkerhetsgruppen till variabeln. `$rules` I följande exempel får du information `myNetworkSecurityGroup` om nätverkssäkerhetsgruppen som nämns i resursgruppen: `myResourceGroup`
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Visa nu de regler som har konfigurerats för den här nätverkssäkerhetsgruppen. Kontrollera att det finns en regel för att tillåta TCP-port 3389 för inkommande anslutningar enligt följande:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    I följande exempel visas en giltig säkerhetsregel som tillåter RDP-trafik. Du kan `Protocol` `DestinationPortRange`se `Access`, `Direction` , och är korrekt konfigurerade:
   
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
   
    Om du inte har någon regel som tillåter RDP-trafik [skapar du en regel för nätverkssäkerhetsgruppen](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tillåt TCP-port 3389.
3. **Återställ användaruppgifter**. I det här felsökningssteget återställs lösenordet för det lokala administratörskonto som du anger när du är osäker på eller har glömt autentiseringsuppgifterna.
   
    Ange först användarnamnet och ett nytt lösenord `$cred` genom att tilldela autentiseringsuppgifter till variabeln enligt följande:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Uppdatera nu autentiseringsuppgifterna på den virtuella datorn. I följande exempel uppdateras autentiseringsuppgifterna på en virtuell dator som namnges `myVM` på den `WestUS` plats och i resursgruppen : `myResourceGroup`
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Starta om den virtuella datorn**. Det här felsökningssteget kan korrigera eventuella underliggande problem som den virtuella datorn själv har.
   
    I följande exempel startas `myVM` om den `myResourceGroup`virtuella datorn som nämns i resursgruppen:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Distribuera om den virtuella datorn**. Det här felsökningssteget distribuerar om den virtuella datorn till en annan värd i Azure för att korrigera eventuella underliggande plattforms- eller nätverksproblem.
   
    I följande exempel distribueras den `myVM` virtuella `WestUS` datorn som namnges `myResourceGroup`på den plats och i resursgruppen :
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Verifiera routning**. Använd Network Watchers [nästa hopp-funktion](../../network-watcher/network-watcher-check-next-hop-portal.md) för att bekräfta att en rutt inte hindrar trafiken från att dirigeras till eller från en virtuell dator. Du kan också granska effektiva vägar för att se alla effektiva vägar för ett nätverksgränssnitt. Mer information finns i [Använda effektiva vägar för att felsöka vm-trafikflöde](../../virtual-network/diagnose-network-routing-problem.md).

7. Kontrollera att alla lokala brandväggar eller brandväggar på datorn tillåter utgående TCP 3389-trafik till Azure.

Om du fortfarande stöter på RDP-problem kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerade felsökningsbegrepp och steg för fjärrskrivbords-felsökning](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Felsöka virtuella datorer som skapats med den klassiska distributionsmodellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Efter varje felsökningssteg kan du försöka ansluta till den virtuella datorn igen.

1. **Återställ RDP-anslutningen**. Det här felsökningssteget återställer RDP-konfigurationen när fjärranslutningar är inaktiverade eller windows-brandväggsregler blockerar rdp, till exempel.
   
    Välj din virtuella dator i Azure-portalen. Klicka på **... Knappen Mer** klickar du sedan på **Återställ fjärråtkomst:**
   
    ![Återställa RDP-konfigurationen i Azure-portalen](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Verifiera molntjänstslutpunkter**. Det här felsökningssteget verifierar att du har slutpunkter i molntjänsterna för att tillåta fjärrskrivbordstrafik. Standardporten för RDP är TCP-port 3389. En regel som tillåter RDP-trafik kanske inte skapas automatiskt när du skapar den virtuella datorn.
   
   Välj din virtuella dator i Azure-portalen. Klicka på knappen **Slutpunkter** om du vill visa de slutpunkter som för närvarande är konfigurerade för den virtuella datorn. Kontrollera att det finns slutpunkter som tillåter RDP-trafik på TCP-port 3389.
   
   I följande exempel visas giltiga slutpunkter som tillåter RDP-trafik:
   
   ![Verifiera molntjänstslutpunkter i Azure-portalen](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Om du inte har en slutpunkt som tillåter fjärrskrivbordstrafik [skapar du en cloud services-slutpunkt](../windows/classic/setup-endpoints.md). Tillåt TCP till privat port 3389.
3. **Granska VM-startdiagnostik**. Det här felsökningssteget granskar vm-konsolloggarna för att avgöra om den virtuella datorn rapporterar ett problem. Alla virtuella datorer har inte startdiagnostik aktiverad, så det här felsökningssteget kan vara valfritt.
   
    Specifika felsökningssteg ligger utanför den här artikelns omfattning, men kan tyda på ett större problem som påverkar RDP-anslutningen. Mer information om hur du granskar konsolloggarna och skärmbilden av den virtuella datorn finns i [Startdiagnostik för virtuella datorer](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Kontrollera vm-resurshälsan**. Det här felsökningssteget verifierar att det inte finns några kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till avsnittet **Support + Felsökning** längst ned i listan. Klicka på knappen **Resurshälsa.** En felfri virtuell dator rapporterar som **tillgänglig:**
   
    ![Kontrollera vm-resurshälsa i Azure-portalen](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Återställ användaruppgifter**. I det här felsökningssteget återställs lösenordet för det lokala administratörskonto som du anger när du är osäker eller har glömt autentiseringsuppgifterna.  När du har loggat in på den virtuella datorn bör du återställa lösenordet för den användaren.
   
    Välj din virtuella dator i Azure-portalen. Bläddra nedåt i inställningsfönstret till avsnittet **Support + Felsökning** längst ned i listan. Klicka på knappen **Återställ lösenord.** Ange ditt användarnamn och ett nytt lösenord. Klicka slutligen på **knappen Spara:**
   
    ![Återställa användarautentiseringsuppgifterna i Azure-portalen](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Starta om den virtuella datorn**. Det här felsökningssteget kan korrigera eventuella underliggande problem som den virtuella datorn själv har.
   
    Välj din virtuella dator i Azure-portalen **Restart** och klicka på fliken **Översikt.**
   
    ![Starta om den virtuella datorn i Azure-portalen](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Kontrollera att alla lokala brandväggar eller brandväggar på datorn tillåter utgående TCP 3389-trafik till Azure.

Om du fortfarande stöter på RDP-problem kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerade felsökningsbegrepp och steg för fjärrskrivbords-felsökning](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Felsöka specifika RDP-fel
Du kan stöta på ett specifikt felmeddelande när du försöker ansluta till den virtuella datorn via RDP. Följande är de vanligaste felmeddelandena:

* [Fjärrsessionen kopplades från eftersom det inte finns några licensservrar för fjärrskrivbord tillgängliga för att tillhandahålla en licens](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Fjärrskrivbord kan inte hitta datorns "namn".](troubleshoot-specific-rdp-errors.md#rdpname)
* [Ett autentiseringsfel har uppstått. Det går inte att kontakta den lokala säkerhetsmyndigheten](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows-säkerhetsfel: Dina autentiseringsuppgifter fungerade inte](troubleshoot-specific-rdp-errors.md#wincred).
* [Den här datorn kan inte ansluta till fjärrdatorn](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Ytterligare resurser
Om inget av dessa fel uppstod och du fortfarande inte kan ansluta till den virtuella datorn via fjärrskrivbord läser du den detaljerade [felsökningsguiden för Fjärrskrivbord](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Felsökningssteg för åtkomst till program som körs på en virtuell dator finns i [Felsöka åtkomst till ett program som körs på en Virtuell Azure.](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Om du har problem med att använda Secure Shell (SSH) för att ansluta till en Virtuell Linux-dator i Azure läser [du Felsöka SSH-anslutningar till en Virtuell Linux-dator i Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


