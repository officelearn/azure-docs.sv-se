---
title: "Skapa ett paket för stöd av StorSimple | Microsoft Docs"
description: "Lär dig hur du skapar, dekryptera och redigera ett supportpaket för din StorSimple-enhet."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: addfb01998271ee3a431d92bf2a6fec70f0577a6
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-manage-a-storsimple-support-package"></a>Skapa och hantera ett stödpaket för StorSimple
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [skapa och hantera ett stödpaket StorSimple](storsimple-8000-create-manage-support-package.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Ett supportpaket för StorSimple är ett enkelt att använda som samlar in alla relevanta loggar för att hjälpa Microsoft-supporten att felsöka eventuella problem för StorSimple-enhet används. Insamlade loggarna krypterade och komprimerade.

Vägledningen innehåller stegvisa instruktioner för att skapa och hantera support-paketet.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Skapa och ladda upp ett supportpaket i den klassiska Azure-portalen
Du kan skapa och ladda upp ett supportpaket till webbplatsen Microsoft Support via den **Underhåll** sidan tjänsten i den klassiska Azure-portalen.

> [!NOTE]
> Överföringen kräver en nyckel för support. Supportpersonalen bör ger det dig i ett e-postmeddelande.
> 
> 

Ett paket med stöd för krypterade och komprimerade (CAB-fil) skapas och överförs till webbplats för Support. Supportteknikern kan sedan Hämta paketet från webbplatsen Support för att felsöka problemet.

Utför följande steg i den klassiska portalen för att skapa ett supportpaket.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Skapa ett supportpaket i den klassiska Azure-portalen
1. Välj **enheter** > **Underhåll**.
2. I den **stödpaketet** väljer **skapa och ladda upp supportpaket**.
3. I den **skapa och ladda upp supportpaket** dialogrutan Gör följande:
   
    ![Skapa för stödpaket](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * I den **stöd nyckel** text ange nyckeln. Supportpersonalen Microsoft bör skicka den här nyckeln till dig via e-post.
   * Markera kryssrutan för att ge medgivande till automatiskt överföra support-paket till webbplatsen Microsoft Support.
   * Klicka på kryssikonen ![Kryssikon](./media/storsimple-create-manage-support-package/IC740895.png).

## <a name="manually-create-a-support-package"></a>Skapa ett supportpaket manuellt
I vissa fall måste du manuellt skapa stöd paketet via Windows PowerShell för StorSimple. Exempel:

* Om du behöver ta bort känslig information från loggfilerna innan du delar med Microsoft-supporten.
* Om du har problem med överföring av paket på grund av problem med nätverksanslutningen.

Du kan dela din manuellt genererad supportpaket Microsoft support via e-post. Utför följande steg för att skapa ett supportpaket i Windows PowerShell för StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Skapa ett supportpaket i Windows PowerShell för StorSimple
1. Ange följande kommando för att starta en Windows PowerShell-session som administratör på den fjärrdator som används för att ansluta till din StorSimple-enhet:
   
    `Start PowerShell`
2. Anslut till konsolen SSAdmin av enheten i Windows PowerShell-session:
   
   * Ange följande i Kommandotolken:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * I dialogrutan som öppnas anger du ditt administratörslösenord för enheten. Standardlösenordet är:
     
      `Password1`
     
      ![Dialogrutan för PowerShell-autentiseringsuppgifter](./media/storsimple-create-manage-support-package/IC740962.png)
   * Välj **OK**.
   * Ange följande i Kommandotolken:
     
      `Enter-PSSession $MS`
3. Ange ett kommando i sessionen som öppnas.
   
   * För nätverksresurser som är lösenordsskyddad, ange:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       Du uppmanas för ett lösenord, en sökväg till den delade nätverksmappen och krypteringslösenfrasen (eftersom paketet stöd krypteras). Ett supportpaket skapas i den angivna mappen.
   * För resurser som inte är lösenordsskyddad, behöver du inte den `-Credential` parameter. Ange följande:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       Stöd för paketet skapas för både domänkontrollanter i den angivna delade mappen. Det är en krypterad, komprimerad fil som kan skickas till Microsoft Support för felsökning. Mer information finns i [kontakta Microsoft Support](storsimple-contact-microsoft-support.md).

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
> Du kan bara redigera ett supportpaket som har genererats via Windows PowerShell för StorSimple. Du kan inte redigera ett paket som skapats i den klassiska Azure-portalen med StorSimple Manager-tjänsten.
> 
> 

Om du vill redigera ett supportpaket innan den skickas på webbplatsen Microsoft Support först dekryptera stöd för paketet, redigera filerna och kryptera den igen. Utför följande steg.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Så här redigerar du ett supportpaket i Windows PowerShell för StorSimple
1. Generera ett supportpaket som beskrivs ovan, [att skapa ett supportpaket i Windows PowerShell för StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Hämta skriptet](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på klientdatorerna.
3. Importera Windows PowerShell-modulen. Ange sökvägen till den lokala mappen där du har hämtat skriptet. Ange om du vill importera modulen:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Alla filer är *.aes* filer som är komprimerade och krypteras. För att expandera och dekryptera filer, ange:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observera att de faktiska filnamnstillägg som visas för alla filer.
   
    ![Redigera supportpaket](./media/storsimple-create-manage-support-package/IC750706.png)
5. Ange lösenordet som du använde när support paketet skapades när du uppmanas att ange krypteringslösenfrasen.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Bläddra till den mapp som innehåller loggfilerna. Eftersom filerna är nu packa upp och dekrypteras, kommer dessa har ursprungliga filnamnstillägg. Ändra de här filerna för att ta bort alla kundspecifik information, till exempel volymnamn och enheten IP-adresser och spara filerna.
7. Stäng filer om du vill komprimera dem med gzip och kryptera dem med AES 256. Detta är hastighet och säkerhet i överföra paketets stöd över ett nätverk. Om du vill komprimera och kryptera filer, anger du följande:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Redigera supportpaket](./media/storsimple-create-manage-support-package/IC750707.png)
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
* Lär dig hur du [använder paket och enhetsloggar för att felsöka enheten distributionen](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

