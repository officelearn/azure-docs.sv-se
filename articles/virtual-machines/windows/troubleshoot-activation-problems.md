---
title: "Felsöka problem med Windows virtuella aktivering i Azure | Microsoft Docs"
description: "Innehåller felsökning steg för att åtgärda Windows aktiveringsproblem för virtuell dator i Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 4f3a388e95d3689cafcd2462e821cb361c46989a
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Felsöka problem med Windows Azure virtuella aktivering

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Om du har problem när du aktiverar Azure Windows virtuell dator (VM som skapas från en anpassad avbildning) kan använda du informationen i det här dokumentet för att felsöka problemet. 

## <a name="symptom"></a>Symtom

När du försöker aktivera en Windows Azure-VM felmeddelandet meddelande som liknar följande exempel:

**Fel: 0xC004F074 programvara LicensingService rapporterade att datorn inte kunde aktiveras. Ingen nyckel ManagementService (KMS) kunde nås. Finns i programmets händelselogg för ytterligare information.**

## <a name="cause"></a>Orsak
I allmänhet inträffa Azure VM aktiveringsproblem om den virtuella Windows-datorn inte har konfigurerats med hjälp av lämplig KMS-klientinstallationsnyckel eller Windows VM har anslutningen till Azure KMS-tjänsten (kms.core.windows.net, port 1668). 

## <a name="solution"></a>Lösning

>[!NOTE]
>Om du använder en plats-till-plats-VPN och Tvingad tunneltrafik finns [Använd Azure anpassade vägar som ska aktiveras KMS-aktivering med Tvingad tunneltrafik](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Om du använder ExpressRoute och du har publicerat en standardväg, se [virtuella Azure-datorn kanske inte kan aktivera via ExpressRoute](http://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Steg 1 konfigurera lämplig KMS-klientinstallationsnyckel (för Windows Server 2016 och Windows Server 2012 R2)

För den virtuella datorn som skapas från en anpassad avbildning för Windows Server 2016 eller Windows Server 2012 R2, måste du konfigurera lämplig KMS-klientinstallationsnyckel för den virtuella datorn.

Det här steget gäller inte för Windows 2012 eller Windows 2008 R2. Den använder funktionen aktivering för Automation-virtuella datorer (AVMA) stöds endast av Windows Server 2016 och Windows Server 2012 R2.

1. Kör **slmgr.vbs/dlv** i en upphöjd kommandotolk. Kontrollera värdet på beskrivning i utdata och sedan avgöra om den har skapats från retail (RETAIL-kanal) eller volym (VOLUME_KMSCLIENT) licens media:
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Om **slmgr.vbs/dlv** visar RETAIL-kanal, kör följande kommandon för att ange den [KMS-klientinstallationsnyckel](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) som används för versionen av Windows Server och tvinga den att aktivera igen: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    För Windows Server 2016 Datacenter exempelvis skulle du köra följande kommando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Steg 2 Kontrollera anslutningen mellan VM och Azure KMS-tjänsten

1. Ladda ned och extrahera den [Psping](http:/technet.microsoft.com/en-us/sysinternals/jj729731.aspx) verktyget till en lokal mapp på den Virtuella inte aktiveras. 

2. Gå till Start, Sök på Windows PowerShell, högerklickar på Windows PowerShell och välj Kör som administratör.

3. Kontrollera att den virtuella datorn är konfigurerad för att använda rätt Azure KMS-servern. Det gör du genom att köra följande kommando:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    Kommandot ska returnera: nyckelhanteringstjänst datornamn har angetts till kms.core.windows.net:1688.

4. Kontrollera med hjälp av Psping att du är ansluten till KMS-servern. Växla till den mapp där du extraherade Pstools.zip hämtningen och kör sedan följande:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Kontrollera att du ser i den andra sista raden i utdata: skickas = 4, mottagna = 4, förlorad = 0 (0% förlust).

  Om förlorad är större än 0 (noll), har den virtuella datorn inte anslutning till KMS-servern. I det här fallet är om den virtuella datorn är i ett virtuellt nätverk och har en anpassad DNS-server anges, måste du se till att DNS-server kunna matcha kms.core.windows.net. Eller ändra DNS-servern till en som matchar kms.core.windows.net.

  Observera att om du tar bort alla DNS-servrar från ett virtuellt nätverk, virtuella datorer använder Azures interna DNS-tjänsten. Den här tjänsten kan matcha kms.core.windows.net.
  
Kontrollera också att gäst-brandväggen inte har konfigurerats på ett sätt som skulle blockera aktiveringsförsök.

5. När du har kontrollerat lyckad anslutning till kms.core.windows.net kör du följande kommando i Kommandotolken för att upphöjd Windows PowerShell. Detta kommando försöker aktivera flera gånger.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Aktiveringen returnerar informationen som liknar följande:

**Aktiverar Windows (r), ServerDatacenter edition (12345678-1234-1234-1234-12345678)... Produkten har aktiverats.**

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Jag har skapat Windows Server 2016 från Azure Marketplace. Behöver jag konfigurera KMS-nyckel för att aktivera Windows Server 2016? 
 
Nej. Bilden i Azure Marketplace har du lämplig KMS-klientinstallationsnyckel redan har konfigurerats. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Windows-aktivering fungerar på samma sätt oavsett om den virtuella datorn använder Azure Hybrid Använd förmånen (NAV) eller inte? 
 
Ja. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Vad händer om Windows-aktivering period har löpt ut? 
 
När respitperioden upphört och Windows fortfarande inte är aktiverad, visas ytterligare meddelanden om att aktivera i Windows Server 2008 R2 och senare versioner av Windows. Skrivbordsunderlägg förblir svart och Windows Update installerar säkerhet och viktiga uppdateringar, men inte valfria uppdateringar. I avsnittet meddelanden längst ned i den [licensiering villkor](http://technet.microsoft.com/en-us/library/ff793403.aspx) sidan.   

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
