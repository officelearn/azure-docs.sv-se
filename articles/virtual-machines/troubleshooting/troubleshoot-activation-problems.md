---
title: Felsöka aktiveringsproblem för virtuella datorer i Windows i Azure| Microsoft-dokument
description: Innehåller felsökningssteg för att åtgärda aktiveringsproblem för Virtuella datorer i Windows i Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 5c84588290ce769b556002469b6a11c6950bb878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476560"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Felsöka aktiveringsproblem med virtuella Azure Windows-datorer

Om du har problem när du aktiverar virtuell Azure Windows-dator (VM) som skapas från en anpassad avbildning kan du använda informationen i det här dokumentet för att felsöka problemet. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Förstå Azure KMS-slutpunkter för Windows produktaktivering i Microsoft Azure Virtual Machines

Azure använder olika slutpunkter för KMS -aktivering (Key Management Services) beroende på den molnregion där den virtuella datorn finns. När du använder den här felsökningsguiden använder du lämplig KMS-slutpunkt som gäller för din region.

* Offentliga Azure-molnregioner: kms.core.windows.net:1688
* Nationella molnregioner i Azure China 21Vianet: kms.core.chinacloudapi.cn:1688
* Nationella molnregioner i Azure Germany: kms.core.cloudapi.de:1688
* Nationella molnregioner för Azure US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att aktivera en virtuell Azure Windows-dator visas ett felmeddelande som liknar fÃ¶1 fÃ¶1 fÃ¶1 fÃ¶rsÃ¶k:

**Fel: 0xC004F074 Software LicensingService rapporterade att datorn inte kunde aktiveras. Ingen Key ManagementService (KMS) kunde kontaktas. Mer information finns i programhändelseloggen.**

## <a name="cause"></a>Orsak

I allmänhet sker Azure VM-aktiveringsproblem om den virtuella Windows-datorn inte har konfigurerats med hjälp av den lämpliga konfigurationsnyckeln för KMS-klienten, eller om den virtuella Windows-datorn har problem med anslutningen till Azure KMS-tjänsten (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Lösning

>[!NOTE]
>Om du använder en VPN- och påtvingad tunnelning från plats till plats läser du [Använda anpassade Azure-vägar för att aktivera KMS-aktivering med påtvingad tunnel.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) 
>
>Om du använder ExpressRoute och du har en standardväg publicerad, se [Kan jag blockera Internet-anslutning till virtuella nätverk som är anslutna till ExpressRoute kretsar?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Steg 1 Konfigurera lämplig KMS-klientinställningsnyckel

För den virtuella datorn som skapas från en anpassad avbildning måste du konfigurera lämplig KMS-klientinställningsnyckel för den virtuella datorn.

1. Kör **slmgr.vbs /dlv** vid en upphöjd kommandotolk. Kontrollera beskrivningsvärdet i utdata och ta reda på om det skapades från butiks-(RETAIL-kanal) eller volym (VOLUME_KMSCLIENT) licensmedia:
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Om **slmgr.vbs /dlv** visar RETAIL-kanalen kör du följande kommandon för att konfigurera [KMS-klientens konfigurationsnyckel](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) för versionen av Windows Server som används och tvinga den att försöka aktivera igen: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    För Windows Server 2016 Datacenter kör du till exempel följande kommando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Steg 2 Verifiera anslutningen mellan tjänsten VIRTUELL OCH Azure KMS

1. Hämta och extrahera [PSping-verktyget](http:/technet.microsoft.com/sysinternals/jj729731.aspx) till en lokal mapp i den virtuella datorn som inte aktiveras. 

2. Gå till Start, sök i Windows PowerShell, högerklicka på Windows PowerShell och välj sedan Kör som administratör.

3. Kontrollera att den virtuella datorn är konfigurerad för att använda rätt Azure KMS-server. Gör detta genom att köra följande kommando:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Kommandot ska returneras: Maskinnamnet för nyckelhanteringstjänsten är inställt på kms.core.windows.net:1688.

4. Kontrollera att du är ansluten till KMS-servern med hjälp av Psping. Växla till den mapp där du extraherade Pstools.zip-nedladdningen och kör sedan följande:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   I den näst sista raden i utdata, se till att du ser: Skickat = 4, Mottaget = 4, Förlorat = 0 (0% förlust).

   Om Förlorad är större än 0 (noll) har den virtuella datorn inte anslutning till KMS-servern. I det här fallet om den virtuella datorn finns i ett virtuellt nätverk och har en anpassad DNS-server angiven, måste du se till att DNS-servern kan lösa kms.core.windows.net. Du kan också ändra DNS-servern till en som löser kms.core.windows.net.

   Observera att om du tar bort alla DNS-servrar från ett virtuellt nätverk använder virtuella datorer Azures interna DNS-tjänst. Den här tjänsten kan lösa kms.core.windows.net.
  
    Kontrollera också att den utgående nätverkstrafiken till KMS-slutpunkten med 1688-porten inte blockeras av brandväggen i den virtuella datorn.

5. Kontrollera att nästa hoppstyp med [Nätverkshanteraren](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) används med hjälp av Nästa hopp från den virtuella datorn i fråga till mål-IP 23.102.135.246 (för kms.core.windows.net) eller IP-adressen för lämplig KMS-slutpunkt som gäller för din region är **Internet**.  Om resultatet är VirtualAppliance eller VirtualNetworkGateway är det troligt att det finns en standardväg.  Kontakta din nätverksadministratör och samarbeta med dem för att fastställa rätt tillvägagångssätt.  Detta kan vara en [anpassad väg](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) om den lösningen är förenlig med organisationens principer.

6. När du har kontrollerat lyckad anslutning till kms.core.windows.net kör du följande kommando i den utökade Windows PowerShell-kommandotolken. Det här kommandot försöker aktivera flera gånger.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Aktiveringen returnerar information som liknar följande:
    
    **Aktivera Windows (R), ServerDatacenter utgåva (12345678-1234-1234-1234-12345678) ...  Produkten har aktiverats.**

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Jag skapade Windows Server 2016 från Azure Marketplace. Måste jag konfigurera KMS-nyckeln för att aktivera Windows Server 2016? 

 
Nej. Avbildningen på Azure Marketplace har rätt KMS-klientkonfigurationsnyckel som redan har konfigurerats. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Fungerar Windows-aktivering på samma sätt oavsett om den virtuella datorn använder Azure Hybrid Use Benefit (HUB) eller inte? 

 
Ja. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Vad händer om Windows-aktiveringsperioden löper ut? 

 
När respitperioden har gått ut och Windows fortfarande inte är aktiverat visas ytterligare meddelanden om hur du aktiverar Windows Server 2008 R2 och senare versioner av Windows. Skrivbordsunderlägget förblir svart och Windows Update installerar endast säkerhetsuppdateringar och viktiga uppdateringar, men inte valfria uppdateringar. Se avsnittet Meddelanden längst ned på sidan [Licensvillkor.](https://technet.microsoft.com/library/ff793403.aspx)   

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
