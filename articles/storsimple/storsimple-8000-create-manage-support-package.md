---
title: Skapa ett supportpaket för StorSimple 8000-serien
description: Lär dig hur du skapar, dekrypterar och redigerar ett supportpaket för din StorSimple 8000-serieenhet.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 9ca033f6f786c0142261dafa31b93b71a8b3336a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277074"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Skapa och hantera ett supportpaket för StorSimple 8000-serien

## <a name="overview"></a>Översikt

Ett StorSimple-supportpaket är en lättanvänd mekanism som samlar in alla relevanta loggar för att hjälpa Microsoft Support med felsökning av problem med StorSimple-enheten. De insamlade loggarna krypteras och komprimeras.

Den här självstudien innehåller steg-för-steg-instruktioner för att skapa och hantera supportpaketet för din StorSimple 8000-serieenhet. Om du arbetar med en StorSimple Virtual Array går du till för att [generera ett loggpaket](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Skapa ett supportpaket

I vissa fall måste du manuellt skapa supportpaketet via Windows PowerShell för StorSimple. Ett exempel:

* Om du behöver ta bort känslig information från loggfilerna innan du delar med Microsoft Support.
* Om du har problem med att ladda upp paketet på grund av anslutningsproblem.

Du kan dela ditt manuellt genererade supportpaket med Microsoft Support via e-post. Gör följande för att skapa ett supportpaket i Windows PowerShell för StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Så här skapar du ett supportpaket i Windows PowerShell för StorSimple

1. Om du vill starta en Windows PowerShell-session som administratör på fjärrdatorn som används för att ansluta till StorSimple-enheten anger du följande kommando:
   
    `Start PowerShell`
2. I Windows PowerShell-sessionen ansluter du till enhetens SSAdmin-konsol:
   
   1. Skriv följande i kommandotolken:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Ange lösenordet för enhetsadministratören i dialogrutan som öppnas. Standardlösenordet är _Password1_.
     
      ![Dialogrutan PowerShell-autentiseringsuppgifter](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Välj **OK**.
   4. Skriv följande i kommandotolken:
     
      `Enter-PSSession $MS`
3. Ange lämpligt kommando i sessionen som öppnas.
   
   * För nätverksresurser som är lösenordsskyddade anger du:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Du uppmanas att ange ett lösenord och en krypteringslösenfras (eftersom supportpaketet är krypterat). Ett supportpaket skapas sedan i standardmappen (Enhetsnamn läggs till med aktuellt datum och aktuell tid).
   * För resurser som inte är lösenordsskyddade `-Credential` behöver du inte parametern. Ange följande:
     
       `Export-HcsSupportPackage`
     
       Supportpaketet skapas för båda styrenheterna i standardmappen. Paketet är en krypterad, komprimerad fil som kan skickas till Microsoft Support för felsökning. Mer information finns i [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Cmdlet-parametrarna Export-HcsSupportPackage

Du kan använda följande parametrar med cmdleten Export-HcsSupportPackage.

| Parameter | Obligatorisk/valfri | Beskrivning |
| --- | --- | --- |
| `-Path` |Krävs |Används för att ange platsen för den nätverksdelade mapp där supportpaketet är placerat. |
| `-EncryptionPassphrase` |Krävs |Används för att tillhandahålla en lösenfras för att kryptera supportpaketet. |
| `-Credential` |Valfri |Används för att ange åtkomstautentiseringsuppgifter för den delade mappen för nätverket. |
| `-Force` |Valfri |Används för att hoppa över bekräftelsesteget för krypteringslösenfras. |
| `-PackageTag` |Valfri |Används för att ange en katalog under *Sökväg* där supportpaketet är placerat. Standardär [enhetsnamn]-[aktuellt datum och tid:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Valfri |Ange som **kluster** (standard) för att skapa ett supportpaket för båda styrenheterna. Om du bara vill skapa ett paket för den aktuella styrenheten anger du **Controller**. |

## <a name="edit-a-support-package"></a>Redigera ett supportpaket

När du har genererat ett supportpaket kan du behöva redigera paketet för att ta bort känslig information. Detta kan inkludera volymnamn, enhets-IP-adresser och säkerhetskopieringsnamn från loggfilerna.

> [!IMPORTANT]
> Du kan bara redigera ett supportpaket som genererades via Windows PowerShell för StorSimple. Du kan inte redigera ett paket som skapats i Azure-portalen med StorSimple Device Manager-tjänsten.

Om du vill redigera ett supportpaket innan du laddar upp det på Microsoft Support-webbplatsen dekrypterar du först supportpaketet, redigerar filerna och krypterar sedan om det. Utför följande steg.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Så här redigerar du ett supportpaket i Windows PowerShell för StorSimple

1. Generera ett supportpaket enligt beskrivningen ovan, i [Skapa ett supportpaket i Windows PowerShell för StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Ladda ner skriptet](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på din klient.
3. Importera Windows PowerShell-modulen. Ange sökvägen till den lokala mappen där du hämtade skriptet. Om du vill importera modulen anger du:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Alla filer är *.aes-filer* som är komprimerade och krypterade. Om du vill expandera och dekryptera filer anger du:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observera att de faktiska filtilläggen nu visas för alla filer.
   
    ![Redigera supportpaket](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. När du uppmanas att ange krypteringslösenfrasen anger du den lösenfras som du använde när supportpaketet skapades.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Bläddra till mappen som innehåller loggfilerna. Eftersom loggfilerna nu expanderas och dekrypteras kommer dessa att ha ursprungliga filnamnstillägg. Ändra dessa filer för att ta bort kundspecifik information, till exempel volymnamn och enhets-IP-adresser, och spara filerna.
7. Stäng filerna för att komprimera dem med gzip och kryptera dem med AES-256. Detta är för hastighet och säkerhet vid överföring av supportpaketet över ett nätverk. Om du vill komprimera och kryptera filer anger du följande:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Redigera supportpaket](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Ange en krypteringslösenfras för det ändrade supportpaketet när du uppmanas att göra det.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Skriv ned den nya lösenfrasen så att du kan dela den med Microsoft Support på begäran.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exempel: Redigera filer i ett supportpaket på en lösenordsskyddad resurs

I följande exempel visas hur du dekrypterar, redigerar och krypterar om ett supportpaket.

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

* Läs mer om informationen [som samlas in i supportpaketet](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Lär dig hur du [använder supportpaket och enhetsloggar för att felsöka enhetsdistributionen](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

