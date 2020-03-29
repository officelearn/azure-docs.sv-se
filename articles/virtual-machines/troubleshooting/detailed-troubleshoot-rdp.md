---
title: Detaljerad felsökning av fjärrskrivbord i Azure | Microsoft-dokument
description: Granska detaljerade felsökningssteg för fjärrskrivbordsfel där du inte kan till en virtuell Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: kan inte ansluta till fjärrskrivbord, felsöka fjärrskrivbord, fjärrskrivbord kan inte ansluta, fjärrskrivbordsfel, felsökning av fjärrskrivbord, problem med fjärrskrivbord
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea448b87f9e6954abecead2934bfb7f4ed04a9c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920152"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detaljerade åtgärder för felsökning av problem med fjärrskrivbordsanslutning till virtuella Windows-datorer i Azure
Den här artikeln innehåller detaljerade felsökningssteg för att diagnostisera och åtgärda komplexa fjärrskrivbordsfel för Windows-baserade virtuella Azure-datorer.

> [!IMPORTANT]
> Om du vill eliminera de vanligaste felen på fjärrskrivborden måste du läsa [den grundläggande felsökningsartikeln för Fjärrskrivbord](troubleshoot-rdp-connection.md) innan du fortsätter.

Ett felmeddelande om fjärrskrivbord kan visas som inte liknar något av de specifika felmeddelanden som omfattas av [den grundläggande felsökningsguiden för fjärrskrivbord](troubleshoot-rdp-connection.md). Följ dessa steg för att ta reda på varför RDP-klienten (Remote Desktop) inte kan ansluta till RDP-tjänsten på Den virtuella Azure-datorn.


Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Alternativt kan du också arkivera en Azure-supportincident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/) och klicka på **Hämta support**. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure Support](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Komponenter i en anslutning till fjärrskrivbord
Följande komponenter är involverade i en RDP-anslutning:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Innan du fortsätter kan det hjälpa att mentalt granska vad som har ändrats sedan den senaste lyckade fjärrskrivbordsanslutningen till den virtuella datorn. Ett exempel:

* Den offentliga IP-adressen för den virtuella datorn eller molntjänsten som innehåller den virtuella datorn (kallas även den virtuella IP-adressen [VIP)](https://en.wikipedia.org/wiki/Virtual_IP_address)har ändrats. RDP-felet kan bero på att DNS-klientcachen fortfarande har den *gamla IP-adressen* registrerad för DNS-namnet. Rensa DNS-klientcachen och försök ansluta den virtuella datorn igen. Eller prova att ansluta direkt till den nya VIP.
* Du använder ett program från tredje part för att hantera dina fjärrskrivbordsanslutningar i stället för att använda anslutningen som genereras av Azure-portalen. Kontrollera att programkonfigurationen innehåller rätt TCP-port för fjärrskrivbordstrafiken. Du kan kontrollera den här porten efter en klassisk virtuell dator i [Azure-portalen](https://portal.azure.com)genom att klicka på den virtuella datorns inställningar > slutpunkter.

## <a name="preliminary-steps"></a>Preliminära steg
Innan du fortsätter till den detaljerade felsökningen

* Kontrollera status för den virtuella datorn i Azure-portalen för eventuella uppenbara problem.
* Följ [snabbkorrigeringsstegen för vanliga RDP-fel i den grundläggande felsökningsguiden](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* För anpassade bilder kontrollerar du att din virtuella hårddisk är korrekt förberedd innan den överförs. Mer information finns i [Förbereda en Windows VHD eller VHDX för att ladda upp till Azure](../windows/prepare-for-upload-vhd-image.md).


Prova att återansluta till den virtuella datorn via Fjärrskrivbord efter dessa steg.

## <a name="detailed-troubleshooting-steps"></a>Detaljerade felsökningsanvisningar
Klienten för fjärrskrivbord kanske inte kan nå tjänsten Fjärrskrivbord på den virtuella Azure-datorn på grund av problem på följande källor:

* [Klientdator för fjärrskrivbord](#source-1-remote-desktop-client-computer)
* [Enheten för intranätskant för organisation](#source-2-organization-intranet-edge-device)
* [Slutpunkt för molntjänst och åtkomstkontrollista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Windows-baserad virtuell Azure-dator](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Källa 1: Klientdator för fjärrskrivbord
Kontrollera att datorn kan upprätta fjärrskrivbordsanslutningar till en annan lokal, Windows-baserad dator.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Om du inte kan kan du kontrollera följande inställningar på datorn:

* En lokal brandväggsinställning som blockerar fjärrskrivbordstrafik.
* Lokalt installerad klientproxyprogram som förhindrar fjärrskrivbordsanslutningar.
* Lokalt installerad programvara för nätverksövervakning som förhindrar fjärrskrivbordsanslutningar.
* Andra typer av säkerhetsprogram som antingen övervakar trafik eller tillåter/inte tillåter specifika typer av trafik som förhindrar fjärrskrivbordsanslutningar.

I alla dessa fall inaktiverar du programvaran tillfälligt och försöker ansluta till en lokal dator via Fjärrskrivbord. Om du kan ta reda på den verkliga orsaken på det här sättet kan du samarbeta med nätverksadministratören för att korrigera programinställningarna så att fjärrskrivbordsanslutningar tillåts.

## <a name="source-2-organization-intranet-edge-device"></a>Källa 2: Enheten för intranätskant för organisationen
Kontrollera att en dator som är direkt ansluten till Internet kan göra fjärrskrivbordsanslutningar till din virtuella Azure-dator.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Om du inte har en dator som är direkt ansluten till Internet skapar och testar du med en ny virtuell Azure-dator i en resursgrupp eller molntjänst. Mer information finns i [Skapa en virtuell dator som kör Windows i Azure](../virtual-machines-windows-hero-tutorial.md). Du kan ta bort den virtuella datorn och resursgruppen eller molntjänsten efter testet.

Om du kan skapa en anslutning till fjärrskrivbord med en dator som är direkt ansluten till Internet kontrollerar du om organisationens intranätenhet har:

* En intern brandvägg som blockerar HTTPS-anslutningar till Internet.
* En proxyserver som förhindrar fjärrskrivbordsanslutningar.
* Intrångsidentifiering eller nätverksövervakningsprogram som körs på enheter i edge-nätverket som förhindrar anslutningar till fjärrskrivbord.

Samarbeta med nätverksadministratören för att korrigera inställningarna för organisationens intranätkantsenhet så att HTTPS-baserade fjärrskrivbordsanslutningar till Internet tillåts.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: Slutpunkt för molntjänster och ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

För virtuella datorer som skapats med den klassiska distributionsmodellen kontrollerar du att en annan Virtuell Azure-dator som finns i samma molntjänst eller virtuellt nätverk kan göra fjärrskrivbordsanslutningar till din Virtuella Azure-dator.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> För virtuella datorer som skapats i Resource Manager går du till [Källa 4: Nätverkssäkerhetsgrupper](#source-4-network-security-groups).

Om du inte har en annan virtuell dator i samma molntjänst eller virtuellt nätverk skapar du en. Följ stegen i [Skapa en virtuell dator som kör Windows i Azure](../virtual-machines-windows-hero-tutorial.md). Ta bort den virtuella testdatorn när testet är slutfört.

Om du kan ansluta via Fjärrskrivbord till en virtuell dator i samma molntjänst eller virtuellt nätverk kontrollerar du om det finns följande inställningar:

* Slutpunktskonfigurationen för fjärrskrivbordstrafik på måldatorn: Slutpunktens privata TCP-port måste matcha den TCP-port där den virtuella datorns fjärrskrivbordstjänst lyssnar (standard är 3389).
* Med åtkomstkontrollistan för fjärrskrivbordstrafiken på måldatorn: Med åtkomstkontrollanter kan du ange tillåten eller nekad inkommande trafik från Internet baserat på dess käll-IP-adress. Felkonfigurerade åtkomstkontrollanter kan förhindra inkommande fjärrskrivbordstrafik till slutpunkten. Kontrollera dina åtkomstkontrollistor för att säkerställa att inkommande trafik från dina offentliga IP-adresser till din proxy eller annan kantserver är tillåten. Mer information finns i [Vad är en kontrolllista för nätverksåtkomst (ACL)?](../../virtual-network/virtual-networks-acl.md)

Om du vill kontrollera om slutpunkten är orsaken till problemet tar du bort den aktuella slutpunkten och skapar en ny och väljer en slumpmässig port i intervallet 49152–65535 för det externa portnumret. Mer information finns i [Så här konfigurerar du slutpunkter på en virtuell dator](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Källa 4: Nätverkssäkerhetsgrupper
Nätverkssäkerhetsgrupper tillåter mer detaljerad kontroll av tillåten inkommande och utgående trafik. Du kan skapa regler som spänner över undernät och molntjänster i ett virtuellt Azure-nätverk.

Använd [IP-flödeskontroll](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska gällande säkerhetsgruppsregler för att säkerställa att den inkommande NSG-regeln finns och prioriteras för RDP-port (standard 3389). Mer information finns i [Använda effektiva säkerhetsregler för att felsöka vm-trafikflödet](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Källa 5: Windows-baserad virtuell Azure-dator
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Följ instruktionerna i [den här artikeln](../windows/reset-rdp.md). I den här artikeln återställs tjänsten Fjärrskrivbord på den virtuella datorn:

* Aktivera standardregeln för Windows-brandväggen för Fjärrskrivbord (TCP-port 3389).
* Aktivera fjärrskrivbordsanslutningar genom att ange registervärdet HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections till 0.

Försök med anslutningen från datorn igen. Om du fortfarande inte kan ansluta via Fjärrskrivbord kontrollerar du följande möjliga problem:

* Tjänsten Fjärrskrivbord körs inte på måldatorn.
* Tjänsten Fjärrskrivbord lyssnar inte på TCP-port 3389.
* Windows-brandväggen eller en annan lokal brandvägg har en utgående regel som förhindrar fjärrskrivbordstrafik.
* Intrångsidentifiering eller nätverksövervakningsprogram som körs på den virtuella Azure-datorn förhindrar anslutningar till fjärrskrivbord.

För virtuella datorer som skapats med den klassiska distributionsmodellen kan du använda en fjärr-Azure PowerShell-session till den virtuella Azure-datorn. Först måste du installera ett certifikat för den virtuella datorns värdmolntjänst. Gå till [Konfigurera säker fjärråtkomst till virtuella Azure-datorer](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) och hämta skriptfilen **InstallWinRMCertAzureVM.ps1** till din lokala dator.

Installera sedan Azure PowerShell om du inte redan har gjort det. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

Öppna sedan en Azure PowerShell-kommandotolk och ändra den aktuella mappen till platsen för skriptfilen **InstallWinRMCertAzureVM.ps1.** Om du vill köra ett Azure PowerShell-skript måste du ange rätt körningsprincip. Kör kommandot **Get-ExecutionPolicy** för att fastställa din aktuella principnivå. Information om hur du ställer in lämplig nivå finns i [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Fyll sedan i ditt Azure-prenumerationsnamn, molntjänstnamnet och namnet på den virtuella datorn (ta bort < och > tecken) och kör sedan dessa kommandon.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Du kan få rätt prenumerationsnamn från egenskapen *SubscriptionName* för visningen av kommandot **Get-AzureSubscription.** Du kan hämta molntjänstnamnet för den virtuella datorn från kolumnen *ServiceName* i visningen av kommandot **Get-AzureVM.**

Kontrollera om du har det nya certifikatet. Öppna snapin-modulen Certifikat för den aktuella användaren och leta i mappen **Betrodda rotcertifikatutfärdare\Certifikat.** Du bör se ett certifikat med DNS-namnet på molntjänsten i kolumnen Utfärdat till (t.ex. cloudservice4testing.cloudapp.net).

Starta sedan en fjärr-Azure PowerShell-session med hjälp av dessa kommandon.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

När du har angett giltiga administratörsbehörighet bör du se något som liknar följande Azure PowerShell-prompt:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Den första delen av den här prompten är ditt molntjänstnamn som innehåller måldatorn, som kan skilja sig från "cloudservice4testing.cloudapp.net". Du kan nu utfärda Azure PowerShell-kommandon för den här molntjänsten för att undersöka de problem som nämns och korrigera konfigurationen.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Så här korrigerar du lyssning tcp-porten för fjärrskrivbordstjänster manuellt
Kör det här kommandot i snabbhet för fjärr-Azure PowerShell-session.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Egenskapen PortNumber visar det aktuella portnumret. Om det behövs ändrar du tillbaka portnumret för fjärrskrivbord till standardvärdet (3389) med det här kommandot.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Kontrollera att porten har ändrats till 3389 med det här kommandot.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Avsluta fjärr-Azure PowerShell-sessionen med det här kommandot.

```powershell
Exit-PSSession
```

Kontrollera att slutpunkten för fjärrskrivbord för Den virtuella Azure-datorn också använder TCP-port 3398 som intern port. Starta om den virtuella datorn för Azure och prova anslutningen till fjärrskrivbord igen.

## <a name="additional-resources"></a>Ytterligare resurser
[Återställa ett lösenord eller tjänsten Fjärrskrivbord för virtuella Datorer i Windows](../windows/reset-rdp.md)

[Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/overview)

[Felsöka SSH-anslutningar (Secure Shell) till en virtuell Virtuell Azure-dator med Linux](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

