---
title: Detaljerad felsökning i Azure för fjärrskrivbord | Microsoft Docs
description: Granska detaljerad felsökning av remote desktop fel där du kan inte till en Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: 'Det går inte att ansluta till fjärrskrivbord, Felsöka fjärrskrivbord, fjärrskrivbord kan inte ansluta skrivbord fjärrfel, felsökning: fjärrskrivbord, remote desktop problem'
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2017
ms.author: genli
ms.openlocfilehash: 1485bc5ac7ae47df9a1a36c8b88d6515b5624360
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detaljerad felsökning av problem med anslutning till fjärrskrivbord till virtuella Windows-datorer i Azure
Den här artikeln innehåller detaljerad felsökning för att diagnostisera och åtgärda komplexa Remote Desktop-fel för Windows-baserade virtuella Azure-datorer.

> [!IMPORTANT]
> Se till att läsa om du vill korrigera vanliga fjärrskrivbord fel [grundläggande felsökningsartikel för fjärrskrivbord](troubleshoot-rdp-connection.md) innan du fortsätter.

Du kan stöta på ett felmeddelande som inte överensstämmer med någon av felmeddelandena som beskrivs i Fjärrskrivbord [grundläggande felsökningsguiden för fjärrskrivbord](troubleshoot-rdp-connection.md). Följ dessa steg för att avgöra varför RDP (Remote Desktop)-klienten kan inte ansluta till RDP-tjänsten på den virtuella Azure-datorn.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du även filen en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och på **Get Support**. Information om hur du använder Azure-supporten finns i [Microsoft Azure Support FAQ](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Komponenter i en fjärrskrivbordsanslutning
Följande komponenter ingår i en RDP-anslutning:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Innan du fortsätter kan det hjälpa att mentalt granska vad som har ändrats sedan den senaste lyckade fjärrskrivbordsanslutningen till den virtuella datorn. Exempel:

* Offentliga IP-adressen för den virtuella datorn eller den molntjänst som innehåller den virtuella datorn (kallas även den virtuella IP-adressen [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) har ändrats. RDP-fel kan bero på att din DNS-klientens cacheminne har fortfarande den *gamla IP-adressen* registrerats för DNS-namn. Rensa din DNS-klientens cacheminne och försök ansluta den virtuella datorn igen. Eller försök ansluta direkt med ny VIP-Adressen.
* Du använder ett program från tredje part för att hantera fjärrskrivbord-anslutningarna istället för den anslutning som genereras av Azure-portalen. Kontrollera att programmets konfiguration innehåller rätt TCP-port för Remote Desktop-trafik. Du kan kontrollera den här porten för en klassisk virtuell dator i den [Azure-portalen](https://portal.azure.com), genom att klicka på den Virtuella datorns inställningar > slutpunkter.

## <a name="preliminary-steps"></a>Preliminära steg
Innan du fortsätter till detaljerad felsökning

* Kontrollera status för den virtuella datorn i Azure-portalen för eventuella uppenbara problem.
* Följ den [snabbkorrigering steg för vanliga RDP-fel i grundläggande felsökningsguiden](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Kontrollera att den virtuella Hårddisken är korrekt förberedd innan du överför den anpassade avbildningar. Mer information finns i [förbereda en Windows-VHD eller VHDX för att överföra till Azure](prepare-for-upload-vhd-image.md).


Försök att ansluta till den virtuella datorn via fjärrskrivbord efter de här stegen.

## <a name="detailed-troubleshooting-steps"></a>Detaljerade felsökningsanvisningar
Fjärrskrivbordsklienten eventuellt inte att nå tjänsten fjärrskrivbord på virtuella Azure-datorn på grund av problem med följande källor:

* [Fjärrskrivbordsklient dator](#source-1-remote-desktop-client-computer)
* [Gränsenheten för organisationen intranät](#source-2-organization-intranet-edge-device)
* [Molnet tjänstslutpunkten och åtkomstkontrollista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Windows-baserade Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>: 1 fjärrskrivbordsklienten källdator
Kontrollera att datorn kan göra anslutningar till fjärrskrivbord till en annan lokal Windows-baserad dator.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Om det går inte att kontrollera följande inställningar på datorn:

* En inställning för lokal brandvägg som blockerar trafik för fjärrskrivbord.
* Lokalt installerat klientprogramvaran för proxy som hindrar anslutningar till fjärrskrivbord.
* Lokalt installerad programvara som förhindrar anslutning till fjärrskrivbord för nätverksövervakning.
* Andra typer av säkerhetsprogram som övervaka trafik eller tillåta/neka vissa typer av trafik som förhindrar anslutning till fjärrskrivbord.

I dessa fall kan du tillfälligt inaktivera programmen och försök att ansluta till en lokal dator via fjärrskrivbord. Om det här sättet kan ta reda på den faktiska orsaken kan fungera med administratören att åtgärda Programvaruinställningar för att tillåta anslutningar till fjärrskrivbord.

## <a name="source-2-organization-intranet-edge-device"></a>Källan 2: Gränsenheten organisation intranät
Kontrollera att en dator som är direkt ansluten till Internet kan göra fjärrskrivbordsanslutningar till din virtuella Azure-datorn.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Om du inte har en dator som är direkt ansluten till Internet, skapa och testa med en ny Azure virtuell dator i en resurs eller molnomfång tjänst. Mer information finns i [skapa en virtuell dator som kör Windows i Azure](../virtual-machines-windows-hero-tutorial.md). Du kan ta bort den virtuella datorn och resursgruppen eller Molntjänsten, när testet.

Om du kan skapa en fjärrskrivbordsanslutning till en dator som är direkt anslutna till Internet, kontrollera din organisation intranät insticksenhet för:

* En intern brandvägg blockerar HTTPS-anslutningar till Internet.
* En proxyserver som förhindrar anslutning till fjärrskrivbord.
* Intrångsidentifiering eller program som körs på enheter i nätverket kant som förhindrar anslutning till fjärrskrivbord för nätverksövervakning.

Arbeta med nätverksadministratören för att korrigera inställningarna för din organisation intranät gränsenheten för att tillåta HTTPS-baserade Fjärrskrivbord anslutningar till Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: Molnet tjänstslutpunkten och ACL
För virtuella datorer skapas med den klassiska distributionsmodellen, kontrollerar du att en annan virtuell Azure-dator i samma molntjänst eller virtuella nätverk kan göra anslutningar till fjärrskrivbord till den virtuella Azure-datorn.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> För virtuella datorer som skapats i Resource Manager, går du vidare till [källa 4: Nätverkssäkerhetsgrupper](#source-4-network-security-groups).

Om du inte har en annan virtuell dator i samma molntjänst eller virtuella nätverk, skapa en. Följ stegen i [skapa en virtuell dator som kör Windows i Azure](../virtual-machines-windows-hero-tutorial.md). Ta bort den virtuella testdatorn när testet är klart.

Om du kan ansluta via fjärrskrivbord till en virtuell dator i samma molntjänst eller virtuella nätverk, kontrollera inställningarna:

* Slutpunktskonfigurationen för Remote Desktop-trafik på mål-VM: privata TCP-port för slutpunkten måste matcha den TCP-port som den virtuella datorn via fjärrskrivbord tjänsten lyssnar (standard är 3389).
* ACL för fjärrskrivbord trafik slutpunkten på mål-VM: ACL: er kan du ange tillåts eller nekas inkommande trafik från Internet baserat på dess IP-adress. Felkonfigurerad ACL: er kan förhindra att inkommande trafik för fjärrskrivbord till slutpunkten. Kontrollera din ACL: er för att säkerställa att inkommande trafik från din offentliga IP-adresser för proxyservern eller andra gränsservern tillåts. Mer information finns i [vad är ett nätverk åtkomstkontrollista (ACL)?](../../virtual-network/virtual-networks-acl.md)

Om du vill kontrollera om slutpunkten är orsaken till problemet genom att ta bort aktuella slutpunkten och skapa en ny om du väljer en slumpmässigt vald port mellan 49152 – 65535 för det externa portnummer. Mer information finns i [hur du ställer in slutpunkter till en virtuell dator](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Datakällan 4: Nätverkssäkerhetsgrupper
Nätverkssäkerhetsgrupper kan mer detaljerad kontroll över tillåtna inkommande och utgående trafik. Du kan skapa regler som utsträckning undernät och molntjänster i Azure-nätverk.

Använd [Kontrollera IP-flöde](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska effektiva regler för nätverkssäkerhetsgrupper för att säkerställa inkommande ”Tillåt” NSG regel finns och prioriteras för RDP-porten (standard 3389). Mer information finns i [med effektiva säkerhetsregler för att felsöka VM infrastrukturtrafiken rör](../../virtual-network/virtual-network-nsg-troubleshoot-portal.md#using-effective-security-rules-to-troubleshoot-vm-traffic-flow).

## <a name="source-5-windows-based-azure-vm"></a>Källa 5: Windows-baserade Azure VM
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Följ instruktionerna i [i den här artikeln](reset-rdp.md). Den här artikeln återställs Remote Desktop-tjänsten på den virtuella datorn:

* Aktivera Standardregeln ”Remote Desktop” Windows-brandväggen (TCP-port 3389).
* Aktivera anslutning till fjärrskrivbord genom att ange registervärdet HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections till 0.

Försök igen från datorn. Om du är fortfarande inte kan ansluta via fjärrskrivbord, kontrollera följande möjliga problem:

* Remote Desktop-tjänsten körs inte på målet VM.
* Remote Desktop-tjänsten lyssnar inte på TCP-port 3389.
* Windows-brandväggen eller en annan lokal brandvägg har en utgående regel som hindrar Remote Desktop-trafik.
* Intrångsidentifiering eller program som körs på Azure-dator för nätverksövervakning förhindrar anslutning till fjärrskrivbord.

För virtuella datorer skapas med den klassiska distributionsmodellen kan använda du Azure PowerShell-fjärrsession till den virtuella Azure-datorn. Du måste först installera ett certifikat för den virtuella datorns värd tjänst i molnet. Gå till [konfigurera Secure PowerShell fjärråtkomst till virtuella datorer i Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) och ladda ned den **InstallWinRMCertAzureVM.ps1** skriptfil till den lokala datorn.

Installera Azure PowerShell om du inte redan har gjort. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

Öppna en Azure PowerShell-kommandotolk och ändra den aktuella mappen till platsen där den **InstallWinRMCertAzureVM.ps1** skriptfilen. Du måste ange rätt körningsprincipen för att köra en Azure PowerShell-skript. Kör den **Get-ExecutionPolicy** kommando för att fastställa din aktuella principnivån. Information om hur du anger rätt finns [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Sedan fylla i namnet på din Azure-prenumeration, molntjänstnamnet och namn på virtuell dator (att ta bort den < och > tecken), och kör sedan följande kommandon.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Du kan hämta rätt prenumerationsnamn från den *SubscriptionName* -egenskapen för visning av den **Get-AzureSubscription** kommando. Du kan hämta molntjänstnamnet för den virtuella datorn från den *ServiceName* kolumn i visningen av den **Get-AzureVM** kommando.

Kontrollera att du har det nya certifikatet. Öppna en snapin-modulen för certifikat för den aktuella användaren och titta i den **Trusted Root Certification Authorities\Certificates** mapp. Du bör se ett certifikat med Molntjänsten i kolumnen utfärdat till DNS-namn (exempel: cloudservice4testing.cloudapp.net).

Sedan initiera Azure PowerShell-fjärrsession med hjälp av dessa kommandon.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

När du har angett giltiga autentiseringsuppgifter, bör du se något som liknar följande Azure PowerShell-prompten:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Den första delen av det här meddelandet är din molntjänstnamnet som innehåller mål VM, vilket kan skilja sig från ”cloudservice4testing.cloudapp.net”. Nu kan du utfärda Azure PowerShell-kommandon för den här Molntjänsten att undersöka problem som anges och korrigera konfigurationen.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Att manuellt korrigera Fjärrskrivbordstjänster som lyssnar på TCP-port
Kör detta kommando i Kommandotolken remote Azure PowerShell-session.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Egenskapen PortNumber visar det aktuella portnumret. Om det behövs ändrar du fjärrskrivbordet port tillbaka till standardvärdet (port 3389) med hjälp av det här kommandot.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Kontrollera att porten har ändrats till 3389 med hjälp av det här kommandot.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Vill du avsluta Azure PowerShell-fjärrsession med hjälp av det här kommandot.

```powershell
Exit-PSSession
```

Kontrollera att Remote Desktop-slutpunkten för den virtuella Azure-datorn också använder TCP-port 3398 som dess Intern port. Starta om den virtuella Azure-datorn och försök fjärrskrivbord anslutningen igen.

## <a name="additional-resources"></a>Ytterligare resurser
[Hur du återställer ett lösenord eller tjänsten Remote Desktop för Windows-datorer](reset-rdp.md)

[Installera och konfigurera Azure PowerShell](/powershell/azure/overview)

[Felsökning av SSH (Secure Shell) anslutningar till en Linux-baserade Azure virtuella](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

