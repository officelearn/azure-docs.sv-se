---
title: Skapa ett supportpaket för StorSimple 8000-serien | Microsoft Docs
description: Lär dig hur du skapar, dekryptera och redigera ett supportpaket för din enhet i StorSimple 8000-serien.
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
ms.openlocfilehash: dfc2d8d763a1eb64a37af73e03992f2d948a6856
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254374"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Skapa och hantera ett supportpaket för StorSimple 8000-serien

## <a name="overview"></a>Översikt

Ett supportpaket för StorSimple är en enkel att använda mekanism som samlar in alla relevanta loggar för att hjälpa Microsoft Support med felsökning av problem med StorSimple-enheten. Insamlade loggar krypterade och komprimerade.

Den här självstudien innehåller stegvisa instruktioner för att skapa och hantera supportpaket för din enhet i StorSimple 8000-serien. Om du arbetar med en StorSimple Virtual Array kan du gå till [skapa ett paket för log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Skapa ett supportpaket

I vissa fall måste du manuellt skapar supportpaket via Windows PowerShell för StorSimple. Exempel:

* Om du vill ta bort känslig information från dina loggfiler innan du delar med Microsoft Support.
* Om du har problem med laddar upp paketet på grund av anslutningsproblem.

Du kan dela din manuellt genererad supportpaket med Microsoft Support via e-post. Utför följande steg för att skapa ett supportpaket i Windows PowerShell för StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Skapa ett supportpaket i Windows PowerShell för StorSimple

1. Ange följande kommando för att starta en Windows PowerShell-session som administratör på den fjärrdator som används för att ansluta till din StorSimple-enhet:
   
    `Start PowerShell`
2. Anslut till konsolen SSAdmin för din enhet i Windows PowerShell-session:
   
   1. Skriv följande i kommandotolken:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. I dialogrutan som öppnas anger du lösenordet för enhetsadministratören. Standardlösenordet är _Password1_.
     
      ![Dialogrutan för PowerShell-autentiseringsuppgifter](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Välj **OK**.
   4. Skriv följande i kommandotolken:
     
      `Enter-PSSession $MS`
3. I sessionen som öppnas anger du lämpligt kommando.
   
   * För nätverksresurser som skyddas med lösenord, anger du:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Du uppmanas för ett lösenord och en krypteringslösenfras (eftersom supportpaketet krypteras). Ett supportpaket skapas i standardmappen (enhetsnamn läggas till med aktuellt datum och tid).
   * För resurser som inte är lösenordsskyddad, behöver du inte den `-Credential` parametern. Ange följande:
     
       `Export-HcsSupportPackage`
     
       Supportpaketet skapas för båda styrenheterna i standardmappen. Paketet är en krypterad, komprimerad fil som kan skickas till Microsoft Support för felsökning. Mer information finns i [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage cmdlet-parametrarna

Du kan använda följande parametrar med cmdleten Export-HcsSupportPackage.

| Parameter | Krävs/valfritt | Beskrivning |
| --- | --- | --- |
| `-Path` |Krävs |Använd för att ange platsen för den delade nätverksmappen där supportpaketet är placerad. |
| `-EncryptionPassphrase` |Krävs |Använd för att ange en lösenfras för att kryptera supportpaketet. |
| `-Credential` |Valfri |Använd det här alternativet om du vill definiera autentiseringsuppgifter för den delade nätverksmappen. |
| `-Force` |Valfri |Använd för att hoppa över bekräftelse-steget kryptering lösenfras. |
| `-PackageTag` |Valfri |Använd för att ange en katalog under *sökväg* i supportpaketet är placerad. Standardvärdet är [enhetsnamn]-[aktuellt datum och time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Valfri |Ange som **kluster** (standard) för att skapa ett supportpaket för båda styrenheterna. Om du vill skapa ett paket endast för den aktuella kontrollanten anger **Controller**. |

## <a name="edit-a-support-package"></a>Redigera ett supportpaket

När du har genererat ett supportpaket, kan du behöva redigera paket för att ta bort känslig information. Detta kan inkludera volymnamn, enhetens IP-adresser och säkerhetskopiering namn i loggfilerna.

> [!IMPORTANT]
> Du kan bara redigera ett supportpaket som genererades via Windows PowerShell för StorSimple. Du kan inte redigera ett paket som skapats i Azure portal med StorSimple Device Manager-tjänsten.

Om du vill redigera ett supportpaket innan du laddar upp den på webbplatsen Microsoft Support, först dekryptera supportpaketet, redigera filerna och krypterar den igen. Utför följande steg.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Så här redigerar du ett supportpaket i Windows PowerShell för StorSimple

1. Skapa ett supportpaket som beskrivs tidigare i [att skapa ett supportpaket i Windows PowerShell för StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Ladda ned skriptet](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på din klient.
3. Importera Windows PowerShell-modulen. Ange sökvägen till den lokala mappen där du har hämtat skriptet. Ange om du vill importera modulen:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Alla filer är *.aes* filer som komprimeras och krypteras. För att expandera och dekryptera filer, anger du:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observera att de faktiska filnamnstillägg nu visas för alla filer.
   
    ![Redigera supportpaket](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. När du uppmanas att ange den krypterade lösenfrasen som ska du ange lösenfrasen som du använde när supportpaketet skapades.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Bläddra till den mapp som innehåller filerna. Eftersom filerna är nu expanderas och dekrypteras, kommer dessa har ursprungliga filnamnstillägg. Ändra de här filerna för att ta bort eventuell kundspecifika information, till exempel namn och IP-adresser för enheten och spara filerna.
7. Stäng på filerna så att komprimeras med gzip och kryptera dem med AES-256. Det här är för hastighet och säkerhet i överföra supportpaketet via ett nätverk. För att komprimera och kryptera filer, anger du följande:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Redigera supportpaket](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. När du uppmanas, anger du en krypteringslösenfrasen för det ändrade supportpaketet.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Skriv ned den nya lösenfrasen så att du kan dela den med Microsoft Support vid begäran.

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

* Lär dig mer om den [information som samlas in i supportpaketet](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Lär dig hur du [använder supportpaket och enhetsloggar för att felsöka distributionen av enheten](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

