---
title: Felsöka problem med Windows-dator aktivering i Azure | Microsoft Docs
description: Innehåller Felsök steg för att åtgärda problem med aktivering av Windows-dator i Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 3dc6466083a3dd6882933b8acdd7d64e4a8acd04
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006996"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Felsöka problem med Windows Azure VM-aktivering

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Om du har problem när du aktiverar Azure Windows virtuell dator (VM) som skapas från en anpassad avbildning kan använda du informationen i det här dokumentet för att felsöka problemet. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Förstå Azure KMS-slutpunkter för Windows Produktaktivering av Azure-datorer
Azure använder olika slutpunkter för KMS-aktivering beroende på området moln där den virtuella datorn finns. När du använder den här felsökningsguiden, använder du lämplig KMS-slutpunkt som gäller för din region.

* Offentliga Azure-molnregioner: kms.core.windows.net:1688
* Azure Kina nationella moln-regioner: kms.core.chinacloudapi.cn:1688
* Nationella molnregioner där Azure Germany: kms.core.cloudapi.de:1688
* Azure US Gov-regioner nationella: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Symtom

När du försöker aktivera en Windows Azure-dator, du får ett felmeddelande meddelande som liknar följande exempel:

**Fel: 0xC004F074 programvara LicensingService rapporterade att datorn inte kunde aktiveras. Ingen nyckel ManagementService (KMS) kunde nås. Finns i programmets händelselogg för ytterligare information.**

## <a name="cause"></a>Orsak
I allmänhet inträffa Azure VM aktiveringsproblem om den virtuella Windows-datorn inte har konfigurerats med hjälp av lämplig Konfigurationsnyckel för KMS-klienten eller Windows-VM har anslutningsproblem till Azure KMS-tjänsten (kms.core.windows.net, port 1668). 

## <a name="solution"></a>Lösning

>[!NOTE]
>Om du använder en plats-till-plats-VPN och Tvingad tunneltrafik, se [Använd Azure anpassade vägar för att aktivera KMS-aktivering med Tvingad tunneltrafik](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Om du använder ExpressRoute och du har en standardväg publiceras kan du se [virtuella Azure-datorn misslyckas med att aktivera via ExpressRoute](http://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Steg 1 konfigurera lämplig Konfigurationsnyckel för KMS-klient (för Windows Server 2016 och Windows Server 2012 R2)

För den virtuella datorn som har skapats från en anpassad avbildning av Windows Server 2016 eller Windows Server 2012 R2, måste du konfigurera lämplig Konfigurationsnyckel för KMS-klienten för den virtuella datorn.

Det här steget gäller inte för Windows 2012 eller Windows 2008 R2. Den använder funktionen aktivering för Automation-virtuella datorer (AVMA) stöds endast av Windows Server 2016 och Windows Server 2012 R2.

1. Kör **/ Slmgr.vbs/dlv** i en upphöjd kommandotolk. Kontrollera värdet på beskrivning i utdata och sedan avgör om den har skapats från detaljhandeln (detaljhandel channel) eller volym (VOLUME_KMSCLIENT) licens media:
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Om **/ Slmgr.vbs/dlv** visar butikskanalprestanda, kör följande kommandon för att ange den [KMS-klientinstallationsnyckel](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) som används för versionen av Windows Server och tvinga den att försök aktivera igen: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Till exempel kör Windows Server 2016 Datacenter, följande kommando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Steg 2 Kontrollera anslutningen mellan virtuella datorer och Azure KMS-tjänsten

1. Ladda ned och extrahera den [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) verktyget till en lokal mapp på den virtuella datorn som inte aktiveras. 

2. Gå till Start, söker du i Windows PowerShell, högerklickar du på Windows PowerShell och välj Kör som administratör.

3. Kontrollera att den virtuella datorn är konfigurerad för att använda rätt Azure KMS-servern. Gör detta genom att köra följande kommando:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    Kommandot ska returnera: nyckelhanteringstjänst datornamn har angetts till kms.core.windows.net:1688.

4. Kontrollera med hjälp av Psping att du är ansluten till KMS-servern. Växla till den mapp där du extraherade Pstools.zip nedladdningen och kör sedan följande:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Se till att du ser i den andra och sista raden i utdata: skickas = 4, mottagna = 4, förlorad = 0 (0% förlust).

  Om förlorad är större än 0 (noll), har den virtuella datorn inte anslutning till KMS-servern. I det här fallet är om den virtuella datorn är i ett virtuellt nätverk och har en anpassad DNS-server har angetts, måste du kontrollera att DNS-servern kan matcha kms.core.windows.net. Eller så ändrar du DNS-servern till ett som matchar kms.core.windows.net.

  Observera att om du tar bort alla DNS-servrar från ett virtuellt nätverk, virtuella datorer använder Azures interna DNS-tjänsten. Den här tjänsten kan lösa kms.core.windows.net.
  
Kontrollera också att gästdatorns brandvägg inte har konfigurerats på ett sätt som skulle blockera aktiveringsförsök.

5. När du har kontrollerat lyckad anslutning till kms.core.windows.net, kör du följande kommando i den utökade Windows PowerShell-Kommandotolken. Det här kommandot försöker aktivera flera gånger.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Aktiveringen returnerar information som liknar följande:

**Aktiverar Windows (r), ServerDatacenter edition (12345678-1234-1234-1234-12345678)... Produkten har aktiverats.**

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Jag har skapat Windows Server 2016 från Azure Marketplace. Behöver jag konfigurera KMS-nyckel för att aktivera Windows Server 2016? 
 
Nej. Avbildningen i Azure Marketplace har lämplig KMS-klientnyckeln inställningar redan har konfigurerats. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Windows-aktivering fungerar på samma sätt oavsett om Virtuellt datorn använder Azure Hybrid Använd förmånen (HUB) eller inte? 
 
Ja. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Vad händer om Windows aktiveringsperioden upphör att gälla? 
 
När respittiden har upphört att gälla och Windows fortfarande inte är aktiverad, visas ytterligare meddelanden om att aktivera i Windows Server 2008 R2 och senare versioner av Windows. Skrivbordsunderlägg förblir svart och Windows Update installerar säkerhet och viktiga uppdateringar, men inte valfria uppdateringar. Se avsnittet meddelanden längst ned på den [licensiering villkor](http://technet.microsoft.com/library/ff793403.aspx) sidan.   

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
