---
title: Felsök aktiverings problem i Windows för virtuella datorer i Azure | Microsoft Docs
description: Innehåller fel söknings stegen för att åtgärda aktiverings problem med virtuella Windows-datorer i Azure
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
ms.openlocfilehash: f3ad58c4094e9f39bcf9782b7b98e351e9d7809b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058133"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Felsöka aktiverings problem med Azure Windows Virtual Machine

Om du har problem med att aktivera den virtuella Azure Windows-datorn (VM) som skapas från en anpassad avbildning kan du använda informationen i det här dokumentet för att felsöka problemet. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Förstå Azure KMS-slutpunkter för Windows produkt aktivering av Azure Virtual Machines

Azure använder olika slut punkter för KMS-aktivering beroende på den moln region där den virtuella datorn finns. När du använder den här fel söknings guiden använder du lämplig KMS-slutpunkt som gäller din region.

* Azures offentliga moln regioner: kms.core.windows.net:1688
* Azure Kina 21Vianet National Cloud regionerna: kms.core.chinacloudapi.cn:1688
* Azure Tyskland Nationals moln regioner: kms.core.cloudapi.de:1688
* Azure US Gov national cloud regions: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Symtom

När du försöker aktivera en virtuell Azure Windows-dator får du ett fel meddelande som liknar följande exempel:

**Fel: 0xC004F074 program varan LicensingService rapporterade att datorn inte kunde aktive ras. Det gick inte att kontakta någon Key ManagementService (KMS). Mer information finns i program händelse loggen.**

## <a name="cause"></a>Orsak

I allmänhet uppstår problem med Azure VM-aktivering om den virtuella Windows-datorn inte har kon figurer ATS med hjälp av rätt konfigurations nyckel för KMS-klienten, eller om den virtuella Windows-datorn har ett anslutnings problem till Azure KMS-tjänsten (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Lösning

>[!NOTE]
>Om du använder en plats-till-plats-VPN och Tvingad tunnel trafik, se [Använd Azure-anpassade vägar för att aktivera KMS-aktivering med Tvingad tunnel trafik](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Om du använder ExpressRoute och har en standard väg publicerad, kan du läsa mer i [den virtuella Azure-datorn om du inte aktiverar över ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Steg 1 Konfigurera rätt konfigurations nyckel för KMS-klienten

För den virtuella datorn som skapas från en anpassad avbildning måste du konfigurera rätt KMS-klients installations nyckel för den virtuella datorn.

1. Kör **slmgr. vbs/dlv** i en upphöjd kommando tolk. Kontrol lera värdet för beskrivningen i utdata och ta reda på om det skapades från återförsäljar-eller volym mediet (ÅTERFÖRSÄLJARVERSION) eller volym (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Om **slmgr.vbs /dlv** visar RETAIL-kanalen kör du följande kommandon för att konfigurera [KMS-klientens konfigurationsnyckel](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) för versionen av Windows Server som används och tvinga den att försöka aktivera igen: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    För Windows Server 2016 Data Center kör du till exempel följande kommando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Steg 2 kontrol lera anslutningen mellan den virtuella datorn och Azure KMS-tjänsten

1. Hämta och extrahera [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) -verktyget till en lokal mapp på den virtuella datorn som inte aktive ras. 

2. Gå till Start, Sök på Windows PowerShell, högerklicka på Windows PowerShell och välj Kör som administratör.

3. Kontrollera att den virtuella datorn är konfigurerad för att använda rätt Azure KMS-server. Det gör du genom att köra följande kommando:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Kommandot bör returnera: Nyckel hanterings tjänstens dator namn har angetts till kms.core.windows.net:1688.

4. Verifiera genom att använda Psping som du har anslutning till KMS-servern. Växla till den mapp där du extraherade Pstools.zip-nedladdningen och kör sedan följande:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   Kontrollera att du ser följande i den andra och sista raden i dina utdata: Skickat = 4, mottaget = 4, borttappad = 0 (0% förlust).

   Om det förlorade värdet är större än 0 (noll) har den virtuella datorn inte någon anslutning till KMS-servern. I den här situationen, om den virtuella datorn finns i ett virtuellt nätverk och har en anpassad DNS-server angiven, måste du kontrol lera att DNS-servern kan matcha kms.core.windows.net. Eller ändra DNS-servern till en som löser kms.core.windows.net.

   Observera att om du tar bort alla DNS-servrar från ett virtuellt nätverk använder virtuella datorer Azures interna DNS-tjänst. Den här tjänsten kan lösa kms.core.windows.net.
  
    Se också till att den utgående nätverks trafiken till KMS-slutpunkten med 1688-porten inte blockeras av brand väggen på den virtuella datorn.

5. När du har verifierat anslutningen till kms.core.windows.net kör du följande kommando i den utökade Windows PowerShell-prompten. Det här kommandot försöker aktivera flera gånger.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Aktiveringen returnerar information som liknar följande:
    
    **Aktivera Windows (R), Server Data Center Edition (12345678-1234-1234-1234-12345678)...  Produkten har Aktiver ATS.**

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Jag skapade Windows Server 2016 från Azure Marketplace. Måste jag konfigurera KMS-nyckeln för att aktivera Windows Server 2016? 

 
Nej. Avbildningen i Azure Marketplace har rätt konfigurations nyckel för KMS-klienten redan konfigurerad. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Fungerar Windows-aktivering på samma sätt oavsett om den virtuella datorn använder Azure Hybrid Use Benefit (hubb) eller inte? 

 
Ja. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Vad händer om Windows-aktiverings perioden upphör att gälla? 

 
När Grace-perioden har upphört att gälla och Windows fortfarande inte har Aktiver ATS, kommer Windows Server 2008 R2 och senare versioner av Windows att visa ytterligare meddelanden om aktivering. Skriv bords underlägget är svart och Windows Update endast installera säkerhets uppdateringar och viktiga uppdateringar, men inte valfria uppdateringar. Se avsnittet meddelanden längst ned på sidan [licens villkor](https://technet.microsoft.com/library/ff793403.aspx) .   

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
