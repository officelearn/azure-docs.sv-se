---
title: Skapa ett supportpaket för StorSimple 8000-serien | Microsoft Docs
description: Lär dig hur du skapar, dekryptera och redigera ett supportpaket för enheten StorSimple 8000-serien.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c83caae58e73d6f2d0fe086938e0bbc5cfdf80c4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743586"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Skapa och hantera ett stödpaket för StorSimple 8000-serien

## <a name="overview"></a>Översikt

Ett supportpaket för StorSimple är ett enkelt att använda som samlar in alla relevanta loggar för att hjälpa Microsoft-supporten att felsöka eventuella problem för StorSimple-enhet används. Insamlade loggarna krypterade och komprimerade.

Vägledningen innehåller stegvisa instruktioner för att skapa och hantera paketets stöd för enheten StorSimple 8000-serien. Om du arbetar med en virtuell StorSimple-matris, gå till [skapar ett paket i loggen](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Skapa ett supportpaket

I vissa fall måste du manuellt skapa stöd paketet via Windows PowerShell för StorSimple. Exempel:

* Om du behöver ta bort känslig information från loggfilerna innan du delar med Microsoft-supporten.
* Om du har problem med överföring av paket på grund av problem med nätverksanslutningen.

Du kan dela din manuellt genererad supportpaket Microsoft support via e-post. Utför följande steg för att skapa ett supportpaket i Windows PowerShell för StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Skapa ett supportpaket i Windows PowerShell för StorSimple

1. Ange följande kommando för att starta en Windows PowerShell-session som administratör på den fjärrdator som används för att ansluta till din StorSimple-enhet:
   
    `Start PowerShell`
2. Anslut till konsolen SSAdmin av enheten i Windows PowerShell-session:
   
   1. Ange följande i Kommandotolken:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. I dialogrutan som öppnas anger du ditt administratörslösenord för enheten. Standardlösenordet är _Password1_.
     
      ![Dialogrutan för PowerShell-autentiseringsuppgifter](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Välj **OK**.
   4. Ange följande i Kommandotolken:
     
      `Enter-PSSession $MS`
3. Ange ett kommando i sessionen som öppnas.
   
   * För nätverksresurser som är lösenordsskyddad, ange:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Du uppmanas för ett lösenord och krypteringslösenfrasen (eftersom paketet stöd krypteras). Ett supportpaket skapas i standardmappen (enhetsnamn läggas till med aktuellt datum och tid).
   * För resurser som inte är lösenordsskyddad, behöver du inte den `-Credential` parameter. Ange följande:
     
       `Export-HcsSupportPackage`
     
       Stöd för paketet skapas för både domänkontrollanter i standardmappen. Paketet är en krypterad, komprimerad fil som kan skickas till Microsoft Support för felsökning. Mer information finns i [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage cmdlet-parametrarna

Du kan använda följande parametrar med cmdleten Export-HcsSupportPackage.

| Parameter | Nödvändiga/valfria | Beskrivning |
| --- | --- | --- |
| `-Path` |Krävs |Använd för att ange platsen för den delade nätverksmappen där support-paketet är placerad. |
| `-EncryptionPassphrase` |Krävs |Använd för att ange en lösenfras för att kryptera support-paketet. |
| `-Credential` |Valfri |Använd för att ange autentiseringsuppgifter för den delade nätverksmappen. |
| `-Force` |Valfri |Använd för att hoppa över steg kryptering lösenfras bekräftelse. |
| `-PackageTag` |Valfri |Använd för att ange en katalog under *sökvägen* i paketets stöd är placerad. Standardvärdet är [enhetsnamn]-[aktuellt datum och time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Valfri |Ange som **klustret** (standard) för att skapa ett supportpaket för både domänkontrollanter. Om du vill skapa ett paket endast för den aktuella domänkontrollanten, ange **domänkontrollant**. |

## <a name="edit-a-support-package"></a>Redigera ett supportpaket

När du har genererat ett supportpaket kan behöva du redigera paket för att ta bort känslig information. Detta kan inkludera volymnamn, enhetens IP-adresser och säkerhetskopiering namn från loggfilerna.

> [!IMPORTANT]
> Du kan bara redigera ett supportpaket som har genererats via Windows PowerShell för StorSimple. Du kan inte redigera ett paket som skapats i Azure-portalen med Enhetshanteraren för StorSimple-tjänsten.

Om du vill redigera ett supportpaket innan den skickas på webbplatsen Microsoft Support först dekryptera stöd för paketet, redigera filerna och kryptera den igen. Utför följande steg.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Så här redigerar du ett supportpaket i Windows PowerShell för StorSimple

1. Generera ett supportpaket som beskrivs ovan, [att skapa ett supportpaket i Windows PowerShell för StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Hämta skriptet](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på klientdatorerna.
3. Importera Windows PowerShell-modulen. Ange sökvägen till den lokala mappen där du har hämtat skriptet. Ange om du vill importera modulen:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Alla filer är *.aes* filer som är komprimerade och krypteras. För att expandera och dekryptera filer, ange:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observera att de faktiska filnamnstillägg som visas för alla filer.
   
    ![Redigera supportpaket](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Ange lösenordet som du använde när support paketet skapades när du uppmanas att ange krypteringslösenfrasen.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Bläddra till den mapp som innehåller loggfilerna. Eftersom filerna är nu packa upp och dekrypteras, kommer dessa har ursprungliga filnamnstillägg. Ändra de här filerna för att ta bort alla kundspecifik information, till exempel volymnamn och enheten IP-adresser och spara filerna.
7. Stäng filer om du vill komprimera dem med gzip och kryptera dem med AES 256. Detta är hastighet och säkerhet i överföra paketets stöd över ett nätverk. Om du vill komprimera och kryptera filer, anger du följande:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Redigera supportpaket](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. När du uppmanas ange krypteringslösenfrasen för det ändrade stöd paketet.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Skriv ned den nya lösenfrasen så att du kan dela den med Microsoft-supporten när det behövs.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exempel: Redigera filer i ett supportpaket på en lösenordsskyddad resurs

I följande exempel visar hur du dekryptera, redigera och kryptera ett supportpaket.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [information som samlas in i paketet stöd](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Lär dig hur du [använder paket och enhetsloggar för att felsöka enheten distributionen](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

