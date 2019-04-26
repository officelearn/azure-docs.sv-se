---
title: Detaljerad felsökning i Azure för fjärrskrivbord | Microsoft Docs
description: Granska detaljerade felsökningsanvisningar för skrivbord fjärrfel där du kan inte till en Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: 'Det går inte att ansluta till fjärrskrivbord, Felsöka fjärrskrivbord genom att Fjärrskrivbord kan inte ansluta fjärrfel för fjärrskrivbord, felsökning: fjärrskrivbord, remote desktop problem'
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4b4d2e2099f0d49c7dd9a150ac659ffde62eaa21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506425"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detaljerade felsökningsanvisningar för problem med anslutning till fjärrskrivbord till Windows virtuella datorer i Azure
Den här artikeln innehåller detaljerade felsökningsanvisningar för att diagnostisera och åtgärda komplexa Remote Desktop-fel för Windows-baserade Azure-datorer.

> [!IMPORTANT]
> Om du vill göra allt vanligare Remote Desktop-fel, se till att läsa [grundläggande felsökningsartikeln för fjärrskrivbord](troubleshoot-rdp-connection.md) innan du fortsätter.

Du kan stöta på ett fjärrskrivbord felmeddelande som inte överensstämmer med någon av felmeddelandena som beskrivs i [grundläggande felsökningsguide för Remote Desktop](troubleshoot-rdp-connection.md). Följ dessa steg för att avgöra varför Remote Desktop (RDP)-klienten kan inte ansluta till RDP-tjänsten på Azure VM.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan alternativt kan du även skicka en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och klicka på **få Support**. Information om hur du använder Azure-supporten finns i [Microsoft Azure Support FAQ](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Komponenterna i en anslutning till fjärrskrivbord
Följande komponenter ingår i en RDP-anslutning:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Innan du fortsätter kan det vara användbart att mentalt granska vad som har ändrats sedan den senaste lyckade fjärrskrivbord-anslutningen till den virtuella datorn. Exempel:

* Offentliga IP-adressen för den virtuella datorn eller Molntjänsten som innehåller den virtuella datorn (även kallat virtuell IP-adress [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) har ändrats. RDP-fel kan bero på att din DNS-klientens cacheminne har fortfarande den *gamla IP-adressen* har registrerats för DNS-namn. Tömma DNS-klientens cache-minne och försök ansluta den virtuella datorn igen. Eller försök att ansluta direkt med den nya VIP.
* Du använder ett program från tredje part för att hantera dina anslutningar till fjärrskrivbord istället för att använda den anslutning som genereras av Azure-portalen. Kontrollera att programmets konfiguration innehåller rätt TCP-porten för Remote Desktop-trafik. Du kan kontrollera den här porten för en klassisk virtuell dator i den [Azure-portalen](https://portal.azure.com), genom att klicka på den Virtuella datorns inställningar > slutpunkter.

## <a name="preliminary-steps"></a>Preliminära steg
Innan du fortsätter till detaljerad felsökning

* Kontrollera status för den virtuella datorn i Azure-portalen för uppenbara problem.
* Följ den [snabbkorrigering steg för vanliga RDP-fel i grundläggande felsökningsguiden](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Kontrollera att din virtuella Hårddisk förbereds innan du överför den korrekt för anpassade avbildningar. Mer information finns i [förbereda en Windows-VHD eller VHDX för att överföra till Azure](../windows/prepare-for-upload-vhd-image.md).


Försök återansluta till den virtuella datorn via fjärrskrivbord efter de här stegen.

## <a name="detailed-troubleshooting-steps"></a>Detaljerade felsökningsanvisningar
Fjärrskrivbord-klienten kanske inte kan nå Fjärrskrivbordstjänsten på Azure-VM på grund av problem med följande källor:

* [Fjärrskrivbordsklienten dator](#source-1-remote-desktop-client-computer)
* [Organisation intranät edge-enhet](#source-2-organization-intranet-edge-device)
* [Tjänsteslutpunkt i molnet och få åtkomst till åtkomstkontrollistor (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Windows-baserad virtuell dator för Azure](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Källa 1: Fjärrskrivbordsklienten dator
Kontrollera att datorn kan göra fjärrskrivbordsanslutningar till en annan lokalt Windows-baserad dator.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Om det går inte att kontrollera följande inställningar på datorn:

* En inställning för lokal brandvägg som blockerar trafik för fjärrskrivbord.
* Lokalt installerat klientprogrammet för proxy som förhindrar anslutning till fjärrskrivbord.
* Lokalt installerad programvara som förhindrar anslutning till fjärrskrivbord för nätverksövervakning.
* Andra typer av säkerhetsprogram som antingen övervaka trafik eller tillåta/neka vissa typer av trafik som förhindrar anslutning till fjärrskrivbord.

I dessa fall kan du tillfälligt inaktivera programvaran och försök att ansluta till den lokala datorn via fjärrskrivbord. Om du hittar den faktiska orsaken det här sättet, tillsammans med nätverksadministratören för att korrigera inställningarna för programvara för att tillåta anslutningar till fjärrskrivbord.

## <a name="source-2-organization-intranet-edge-device"></a>Källan 2: Organisation intranät edge-enhet
Kontrollera att en dator som är direkt ansluten till Internet kan göra fjärrskrivbordsanslutningar till din Azure-dator.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Om du inte har en dator som är direkt ansluten till Internet, skapa och testa med en ny Azure-dator i en resurs eller tjänst. Mer information finns i [skapa en virtuell dator som kör Windows i Azure](../virtual-machines-windows-hero-tutorial.md). Du kan ta bort den virtuella datorn och resursgruppen eller Molntjänsten, efter att testet.

Om du kan skapa en anslutning till fjärrskrivbord med en dator som är direkt anslutna till Internet, kontrollera din organisation intranät edge-enhet för:

* En intern brandvägg blockerar HTTPS-anslutningar till Internet.
* En proxyserver som förhindrar anslutning till fjärrskrivbord.
* Intrångsidentifiering eller programvara som körs på enheter i nätverket edge som förhindrar anslutning till fjärrskrivbord för nätverksövervakning.

Arbeta med nätverksadministratören för att korrigera inställningarna för din organisation intranät edge-enhet för att tillåta HTTPS-baserade fjärrskrivbordsanslutningar till Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: Molnet tjänstslutpunkt och ACL
För virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen, kontrollerar du att en annan virtuell Azure-dator som är i samma molntjänst eller virtuella nätverk kan göra anslutningar till fjärrskrivbord till den virtuella Azure-datorn.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> För virtuella datorer som skapats i Resource Manager, gå vidare till [källa 4: Nätverkssäkerhetsgrupper](#source-4-network-security-groups).

Om du inte har en annan virtuell dator i samma molntjänst eller virtuella nätverk, skapa en. Följ stegen i [skapa en virtuell dator som kör Windows i Azure](../virtual-machines-windows-hero-tutorial.md). Ta bort den virtuella testdatorn när testet är klart.

Om du kan ansluta via fjärrskrivbord till en virtuell dator i samma molntjänst eller virtuella nätverk, söka efter de här inställningarna:

* Konfigurationen av slutpunkten för Remote Desktop-trafik på den Virtuella måldatorn: Privata TCP-porten för slutpunkten måste matcha den TCP-port som den Virtuella datorns Fjärrskrivbordstjänsten lyssnar (standard är 3389).
* ACL för slutpunkten för Remote Desktop-trafik på den Virtuella måldatorn: ACL: er kan du ange tillåts eller nekas inkommande trafik från Internet baserat på dess IP-källadressen. Felkonfigurerad ACL: er kan förhindra att inkommande trafik för fjärrskrivbord till slutpunkten. Kontrollera din ACL: er för att säkerställa att den inkommande trafiken från din offentliga IP-adresser för proxyservern eller andra edge-servern tillåts. Mer information finns i [vad är ett nätverk åtkomstkontrollistan (ACL)?](../../virtual-network/virtual-networks-acl.md)

Kontrollera om slutpunkten är orsaken till problemet genom att ta bort den aktuella slutpunkten och skapa en ny, välja en port slumpmässigt inom intervallet 49152 – 65535 externa portnummer. Mer information finns i [så här ställer du in slutpunkter till en virtuell dator](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Källa 4: Nätverkssäkerhetsgrupper
Nätverkssäkerhetsgrupper kan mer detaljerad kontroll över tillåten inkommande och utgående trafik. Du kan skapa regler med undernät och molntjänster i Azure-nätverk.

Använd [Kontrollera IP-flöde](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska reglerna för effektiva säkerhetsgrupper för att säkerställa att inkommande ”Tillåt” NSG-regeln finns och är prioriterad för RDP-porten (standard: 3389). Mer information finns i [med effektiva säkerhetsreglerna för felsökning av VM infrastrukturtrafiken rör](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Källa 5: Windows-baserad virtuell dator för Azure
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Följ instruktionerna i [i den här artikeln](../windows/reset-rdp.md). Den här artikeln återställer Fjärrskrivbordstjänsten på den virtuella datorn:

* Aktivera ”Remote Desktop” Windows-brandväggen Standardregeln (TCP-port 3389).
* Aktivera anslutning till fjärrskrivbord genom att ange registervärdet HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections till 0.

Testa anslutningen från datorn igen. Om du är fortfarande inte kan ansluta via fjärrskrivbord, kontrollera följande möjliga problem:

* Fjärrskrivbordstjänsten körs inte på den Virtuella måldatorn.
* Fjärrskrivbordstjänsten lyssnar inte på TCP-port 3389.
* Windows-brandväggen eller en annan lokal brandvägg har en utgående regel som hindrar Remote Desktop-trafik.
* Intrångsidentifiering eller programvara som körs på virtuella Azure-datorer för nätverksövervakning förhindrar anslutning till fjärrskrivbord.

För virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen, kan du använda en Azure PowerShell-fjärrsession till Azure-dator. Först måste du installera ett certifikat för den virtuella datorns moln-värdtjänst. Gå till [konfigurera säker PowerShell fjärråtkomst till Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) och ladda ned den **InstallWinRMCertAzureVM.ps1** skriptfil till din lokala dator.

Installera Azure PowerShell om du inte redan har gjort. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

Öppna en Azure PowerShell-kommandotolk och ändra den aktuella mappen till platsen för den **InstallWinRMCertAzureVM.ps1** skriptfilen. Om du vill köra ett skript för Azure PowerShell, måste du ange rätt körningsprincipen. Kör den **Get-ExecutionPolicy** kommando för att fastställa din aktuella principnivån. Information om att ställa in lämplig nivå finns i [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Sedan fylla i ditt Azure-prenumerationsnamn, molntjänstens namn och namnet på din virtuella dator (ta bort den < och > tecken), och kör sedan följande kommandon.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Du kan hämta rätt prenumerationens namn från den *SubscriptionName* egenskapen för visning av den **Get-AzureSubscription** kommando. Du kan hämta molntjänstens namn för den virtuella datorn från den *ServiceName* kolumn i visningen av de **Get-AzureVM** kommando.

Kontrollera om du har det nya certifikatet. Öppna en snapin-modulen för certifikat för den aktuella användaren och titta i den **betrodda rotcertifikatutfärdare\Certifikat** mapp. Du bör se ett certifikat med DNS-namnet på din molntjänst i kolumnen utfärdat till (exempel: cloudservice4testing.cloudapp.net).

Nu ska initiera en fjärrsession med Azure PowerShell med hjälp av dessa kommandon.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

När du har angett giltiga autentiseringsuppgifter, bör du se något som liknar följande Azure PowerShell-prompten:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Den första delen av det här meddelandet är din molntjänstens namn som innehåller den Virtuella, måldatorn som kan skilja sig från ”cloudservice4testing.cloudapp.net”. Nu kan du köra Azure PowerShell-kommandon för den här Molntjänsten att undersöka problem nämns och korrigera konfigurationen.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Att manuellt åtgärda Fjärrskrivbordstjänster som lyssnar på TCP-port
Kör det här kommandot i Kommandotolken remote Azure PowerShell-session.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Egenskapen PortNumber visar det aktuella portnumret. Om det behövs ändrar du fjärrskrivbordet port tillbaka till dess standardvärde (port 3389) med hjälp av det här kommandot.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Kontrollera att porten har ändrats till 3389 med hjälp av det här kommandot.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Avsluta remote Azure PowerShell-session med hjälp av det här kommandot.

```powershell
Exit-PSSession
```

Kontrollera att Remote Desktop-slutpunkten för den virtuella Azure-datorn också använder TCP-port 3398 som dess Intern port. Starta om den virtuella Azure-datorn och försök igen-anslutningen till fjärrskrivbordet.

## <a name="additional-resources"></a>Ytterligare resurser
[Återställa ett lösenord eller Fjärrskrivbordstjänsten för Windows-datorer](../windows/reset-rdp.md)

[Installera och konfigurera Azure PowerShell](/powershell/azure/overview)

[Felsöka Secure Shell (SSH)-anslutningar till en Linux-baserade Azure VM](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

